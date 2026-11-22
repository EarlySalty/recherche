# Code Review ohne Usage-Explosion: Analyse deines Gates und eine kosteneffiziente Zielarchitektur

## Kernergebnis

Dein älteres Gate hat kein primäres Qualitätsproblem, sondern ein **Optimierungsproblem**: Es ist auf maximale Vollständigkeit konstruiert, während ein produktives Review-System auf **hohen erwarteten Nutzen pro Finding** optimiert werden sollte. Genau diese Trennung formuliert OpenAI inzwischen ausdrücklich: Für einen produktiv eingesetzten Reviewer sei Präzision wichtiger als maximaler Recall; OpenAI akzeptiere bewusst einen moderaten Recall-Verlust, wenn dadurch Signalqualität und Entwicklervertrauen steigen. Der Reviewer soll nicht „alles finden“, sondern Findings liefern, deren erwarteter Nutzen größer ist als Verifikationsaufwand und Schaden durch Fehlalarme. citeturn741340search0

Das ist bemerkenswert nah an deinem Problem. Dein Gate enthält in der untersuchten Version dagegen explizit die Anweisung, Runde eins müsse **„exhaustive“** sein, weil spätere Runden neue Probleme außerhalb des Fix-Scopes nicht mehr hochstufen sollen. Für jede geänderte Unit fordert es Invarianten, sämtliche Caller und Callees, sieben Input-Klassen, Root-Cause-/Twin-Suche, Security und Boundary-Analyse. Selbst Wartbarkeitsprobleme werden standardmäßig als blockierend behandelt; bei Zweifeln zwischen NIT und BLOCKING soll das Modell ausdrücklich BLOCKING wählen. Das erzeugt systematisch hohen Recall, aber ebenso systematisch viel Kontextarbeit, viele Findings und viele Folgerunden. [Untersuchte ZIP-Datei](sandbox:/mnt/data/gpt-workers-gate-live-20261115.zip), insbesondere `gate_hook.py:5834–5941`.

Die stärksten öffentlich belegten Gegenbeispiele verfolgen inzwischen fast alle eine **Funnel-Architektur**. Anthropic prüft zuerst, ob ein PR überhaupt reviewwürdig ist, beschränkt Bug-Agenten auf den eingeführten Diff, verlangt „HIGH SIGNAL“ und validiert gefundene Probleme anschließend separat. OpenAI hält mechanisch ausdrückbare Regeln in Tests/Linters, gibt dem LLM nur wenige, lokal gescopte Regeln und betrachtet Code Review ausdrücklich nur als zusätzliche Verteidigungsschicht. T3 Code macht zunächst Contribution-Triage, führt betroffene deterministische Checks selektiv aus und hat spezialisierte Agent-Checks mit expliziten Budgets und neutralem statt blockierendem Ergebnis. Große OSS-Projekte wie Chromium und Kubernetes lösen das Problem zusätzlich über **Ownership und Scope**: die richtige Person für den betroffenen Bereich, gezielte Tests und getrennte Freigabe-Rollen statt eines universellen „prüfe alles“-Reviewers. citeturn741340search2turn741340search1turn167462search0turn167462search1turn167462search4turn813159search7

Die wichtigste Architekturänderung für dich lautet deshalb:

> **Nicht den Reviewer schwächer machen, sondern seine Arbeit später, seltener und gezielter einsetzen.**  
> Deterministische Checks zuerst. Dann Risiko klassifizieren. Dann nur den betroffenen Diff plus gezielt nachgeladenen Kontext prüfen. Nur potenziell blockierende Findings unabhängig validieren. Deep Review und Security als separate, risikogesteuerte Pfade. Fix-Runden nur auf den neuen Delta-SHA, mit harten PR-Budgets.

Damit verlierst du nicht zwangsläufig die Fähigkeit, schwere Fehler zu finden. Im Gegenteil: Du verschiebst Budget von flächendeckender Vollständigkeitsarbeit zu den Stellen, an denen ein tiefes Modell tatsächlich Mehrwert hat. OpenAI berichtet aus seinen eigenen Evaluationen, dass Repo-Zugriff und Ausführung dem Reviewer erlauben, mehr kritische Probleme bei gleichzeitig weniger Fehlalarmen zu finden; zusätzlicher Inferenzaufwand müsse deshalb nicht primär in „noch mehr Findings“, sondern in bessere Kalibrierung investiert werden. citeturn741340search0

## Was dein hochgeladenes Gate tatsächlich macht

Ich konnte die ZIP-Datei vollständig lesen und den tatsächlichen Code untersuchen. Die nachfolgenden Aussagen beziehen sich auf `/mnt/data/gpt-workers-gate-live-20261115.zip`; die konkrete produktive `~/.t3/userdata/pyramide.json`, deine aktuellen Claude-Hook-Einstellungen außerhalb des Archivs, Laufzeit-State und historische Usage-Logs sind **nicht** enthalten. Deshalb kann ich die Architektur dieser älteren Version präzise beschreiben, aber nicht behaupten, dass dein heutiger Betrieb exakt dieselben Modelle, Efforts oder Kosten hat.

| Befund aus dem Code | Konsequenz |
|---|---|
| Fallback-Konfiguration ist `claude-opus-5/high` → `gpt-6-astra/high`; produktiv kann `pyramide.json` diese Reihenfolge überschreiben. Das zweite Modell wird nur bei technischem „kein Urteil“ aufgerufen, nicht bei einem inhaltlichen BLOCK. `gate_hook.py:116–134`, `1676–1824`, `4311–4380` | Positiv: Kein „Reviewer-Shopping“ nach einem BLOCK. Negativ: technische Vertrags-/Timeout-Probleme können einen kompletten zweiten teuren Reviewer-Lauf auslösen. |
| Vor dem LLM läuft bereits ein mechanisches `regelgate`; Doku-only und leere Diffs werden übersprungen. `gate_hook.py:1704–1724`, `7029–7034` | Gute Basis. Das Problem ist nicht, dass keinerlei billiger Filter existiert, sondern dass nach dem Filter der teure Pfad sehr aggressiv wird. |
| Runde eins: „must be exhaustive“. Jede Unit muss vollständig behandelt werden; spätere Runden dürfen neue Findings nur innerhalb Fix-Code und Caller hochstufen. `gate_hook.py:5871–5879` | Dadurch zwingst du das Modell, in Runde eins maximal breit zu suchen. Das ist genau die falsche Default-Zielfunktion für ein produktives Gate. |
| Für **jede** erforderliche Unit werden Pre-/Post-/State-Invarianten, alle Caller, Callees und sieben Input-Klassen verlangt: `empty`, `null_missing`, `max_overflow`, `error_path`, `retry`, `concurrency`, `stale_state`. `gate_hook.py:5458–5472`, `5799–5809`, `5897–5904` | Die Kosten wachsen ungefähr mit Anzahl geänderter Units × geforderter Prüfdimensionen × notwendigem Repository-Browsing. Viele dieser Dimensionen sind für normale Änderungen irrelevant. |
| Root-Cause-Prüfung verlangt Mechanismus, Ursache, „why unnoticed“ und Twin-Suche im Repository. Nicht abgedeckte Twins werden vom Hook selbst zu BLOCKING Findings. `gate_hook.py:5901`, `6276–6312` | Sehr wertvoll für echte Bugfixes oder Incident-Fixes, aber viel zu teuer als universelle Pflicht für jede Änderung. |
| Eine vom Modell selbst mit `ok:false` markierte Input-Klasse wird mechanisch in ein BLOCKING Finding verwandelt. `gate_hook.py:6315–6341` | Ein konservatives oder missverständlich antwortendes Modell kann damit selbst neue Hard Gates erzeugen. |
| Selbst Wartbarkeit, zusätzliche Latenz, redundante Limits, irreführende Kommentare, falsche Sprache oder „production code without a production caller“ sind BLOCKING; „when in doubt … it is BLOCKING“. `gate_hook.py:5904–5907` | Das erklärt einen großen Teil deiner Fix-Last. „Blockiert Deployment“ ist semantisch viel breiter als „Release-Risiko“. |
| Bis 160 KiB Diff und 15 Units gibt es einen Reviewer-Job. Darüber wird nach Bereichen gesplittet; bei mehreren Bereichen kommt **zusätzlich** ein Integration-Reviewer hinzu. Maximal drei Jobs laufen gleichzeitig. `gate_hook.py:5460–5462`, `5748–5778`, `6468–6522` | Parallelisierung verkürzt die Wandzeit, reduziert aber nicht den Modellverbrauch. Große PRs erzeugen mehr Reviewer, und der Integration-Reviewer repliziert einen Teil der Analyse. |
| Jeder Reviewer-Job darf nach Vertragsfehlern bis zu drei Mal erneut gefragt werden, also maximal vier Modellaufrufe für denselben Job. `gate_hook.py:5458`, `6211–6273` | Das ist ein versteckter multiplikativer Kostenfaktor. Ein JSON-/Citation-/Coverage-Problem kann zu einer vollständigen erneuten Modellrunde führen, obwohl die eigentliche Codeanalyse schon erledigt war. |
| Findings werden nur per exakt gleichem `(location, defect)` dedupliziert. `gate_hook.py:6525–6548` | Zwei Reviewer können dieselbe Ursache anders formulieren oder an verschiedenen Symptomen verorten und dadurch mehrere Findings erzeugen. |
| Phase 2 ist bereits inkrementell: wenn State frisch ist, der alte Head Ancestor bleibt, höchstens drei neue Dateien hinzukommen, der Diff nicht >1,5× wächst und offene Blocker existieren, wird die Fix-Runde gewählt. Nach drei Phase-2-Runden geht es wieder zum Vollreview. `gate_hook.py:2282–2287`, `4166–4193`, `4414–4473` | Das ist eine gute Idee, aber der automatische erneute Vollreview nach `PHASE2_MAX=3` kann genau dann wieder viel Usage erzeugen, wenn ein PR bereits teuer war. |
| Es gibt einen SHA-/Tree-basierten ALLOW-Cache. `gate_hook.py:7050–7056` | Ebenfalls sinnvoll. Der Cache sollte in der neuen Architektur auf eine explizite Review-Key-Struktur mit Policy-Version erweitert werden. |
| README: Push/Merge zu `main`/`master` löst Review aus; Feature-Branch-Push nicht. Timeout 2400 s, Job-Wartezeit 2100 s, Hang-Wache 1800 s. Ein explizites Zeitbudget gibt es nicht. `README.md:169–200`, `gate_hook.py:130–134` | Du begrenzt technische Laufzeit, aber nicht ökonomischen Verbrauch pro PR. |
| Die Doku behauptet an einer Stelle `METHOD_RETRIES = 6`, der Code enthält tatsächlich `3`. `docs/merge-gate-zweiphasig.md:115`, `gate_hook.py:5458` | Konkreter Hinweis auf Policy-/Dokumentationsdrift. Bei einem komplizierten Gate ist das selbst ein Wartbarkeitsrisiko. |

Der zentrale Kostenmultiplikator lässt sich direkt aus dem Code herleiten. Bei `A` gesplitteten Bereichen entstehen bei `A > 1` **A Area-Jobs plus ein Integration-Job**. Jeder davon kann durch `METHOD_RETRIES = 3` insgesamt bis zu vier Modellaufrufe verursachen. Für zehn Bereiche sind das theoretisch `11 × 4 = 44` Modellaufrufe innerhalb **eines** Reviewer-Modells und einer Gate-Runde. Falls dieser Reviewer technisch kein Urteil liefert und die Kette auf ein zweites Modell fällt, kann derselbe strukturelle Aufwand erneut entstehen. Das ist ein Worst Case, keine gemessene Durchschnittszahl deines Systems; trotzdem zeigt die Rechnung, warum Agent-Fanout × Contract-Retries × Fix-Loops gefährlicher ist als ein einzelner großer Prompt.

Noch wichtiger ist die semantische Multiplikation. Dein Prompt sagt dem Modell nicht nur „finde Bugs“, sondern zwingt es, jede Unit entlang eines fixen Testkatalogs zu beweisen, alle Caller zu finden, Cross-Boundaries zu verfolgen, Twins zu suchen und Unsicherheit zugunsten eines Blocks aufzulösen. Das bedeutet: **Mehr Modellintelligenz macht das Gate unter dieser Policy nicht automatisch billiger.** Ein intelligenteres Modell kann im Gegenteil mehr echte Randprobleme entdecken und damit mehr Arbeit auslösen.

Ein weiterer Unterschied zwischen gemessener und gefühlter Usage ist relevant: Dein README protokolliert zwar pro Codex-Run `input/cached/output` in `job.json.usage_summary`, enthält aber im Gate selbst keinen globalen Token-, Call- oder Kosten-Deckel pro PR. Das Gate kennt Rundenlimits, aber kein ökonomisches Budget. Damit kann „noch eine formal zulässige Runde“ weit teurer sein als die eigentliche Codegenerierung.

## Wie Anthropic, OpenAI, T3 Code und große OSS-Projekte das Problem begrenzen

### Anthropic / Claude Code

Die aktuell öffentlich sichtbare `/code-review`-Implementierung im Claude-Code-Repository macht einige Dinge, die deinem Ansatz bewusst entgegengesetzt sind. Zuerst prüft ein Haiku-Agent, ob der PR geschlossen, Draft, trivial beziehungsweise offensichtlich korrekt oder schon durch Claude reviewed ist. Ein zweiter Haiku-Agent ermittelt nur die relevanten `CLAUDE.md`-Dateien, ein Sonnet-Agent fasst den PR zusammen. Erst danach laufen vier Reviewer parallel: zwei Sonnet-Agenten auf lokale `CLAUDE.md`-Compliance und zwei Opus-Agenten auf Bugs. citeturn741340search2

Entscheidend ist aber die **Review-Policy** der beiden Bug-Agenten. Einer soll nur den Diff untersuchen und ausdrücklich keinen zusätzlichen Kontext öffnen, wenn das Finding ohne diesen Kontext nicht validiert werden kann. Beide sollen ausschließlich signifikante, eindeutige Probleme in eingeführtem Code melden. Die Implementierung sagt sinngemäß: keine Style-/Quality-Nits, keine state- oder inputabhängigen Spekulationen, keine subjektiven Verbesserungsvorschläge, keine Dinge, die ein Linter findet, keine bereits existierenden Probleme. Unsichere Findings sollen überhaupt nicht gemeldet werden. Außerdem sind Duplikate explizit verboten. citeturn741340search2

Danach wird nicht einfach alles gepostet: Die aktuelle Implementierung startet für einzelne Kandidaten weitere Validierungs-Subagents und filtert nicht validierte Issues aus. Das ältere README beschreibt noch ein 0–100-Confidence-Scoring mit Schwelle 80 und einen Git-History-Agenten; ein Anthropic-Maintainer bestätigte am **16. August 2026**, dass dieses README eine ältere Pipeline beschreibt und die aktuelle Implementierung stattdessen validierungsbasiert ist. Deshalb wäre es falsch, Anthropic heute eine feste 80er-Konfidenzschwelle als aktuellen Mechanismus zuzuschreiben. citeturn307385search0turn307385search1

Das ist für dich eine wichtige Lektion: Anthropic nutzt zwar ebenfalls Agent-Fanout, aber **vorne sitzt ein Eligibility Filter und hinten ein Validator**. Noch wichtiger: Die Bug-Suche wird absichtlich kastriert, was Scope und Spekulation betrifft. Die Architektur akzeptiert also bewusst, dass ein Review nicht alle denkbaren Dinge findet.

Anthropic warnt auch in seinen eigenen Prompting-Dokumenten vor unnötiger Subagent-Nutzung. Neuere Claude-Modelle delegieren relativ aggressiv; Anthropic empfiehlt Subagents für parallelisierbare, isolierte, unabhängige Workstreams, aber bei einfachen, sequentiellen oder Single-File-Aufgaben direkt zu arbeiten. Zusätzlich weist Anthropic darauf hin, dass hohe Effort-Stufen und übermäßig gründliche Prompts Thinking- und Tool-Kosten aufblähen können. citeturn307385search3

Das bedeutet allerdings **nicht**, dass Anthropic öffentlich dokumentiert hätte, wie sein gesamtes internes Produktions-CI oder Sicherheits-Gating funktioniert. Öffentlich belegbar ist hier der Claude-Code-Workflow beziehungsweise dessen Repository-Konfiguration, nicht eine geheime Anthropic-interne Merge-Architektur.

### OpenAI / Codex Code Review

OpenAI beschreibt die Designentscheidung noch expliziter. Im Beitrag vom **1. Dezember 2025** heißt es, ein Deployment-Reviewer müsse anders optimiert werden als ein hypersensitiver Trainings-Verifier: Für Nutzbarkeit sei Präzision wichtiger als Recall. OpenAI habe einen moderaten Recall-Verlust bewusst akzeptiert, um hohes Signal und Entwicklervertrauen zu erreichen. Ein langsamer oder lauter Reviewer werde sonst umgangen. citeturn741340search0

Interessanterweise bedeutet „high precision“ bei OpenAI nicht zwangsläufig „nur Diff geben“. OpenAI berichtet, dass Repo-weite Tools und Codeausführung gegenüber reinem Diff-Kontext sowohl das Erkennen kritischer Probleme als auch die Präzision verbessert hätten. Der entscheidende Unterschied ist also **selektiver Zugriff statt vollständig vorab eingespeister Kontext**: Der Reviewer darf bei Bedarf ins Repository gehen, muss aber nicht für jede geänderte Funktion systematisch alle Caller, Callees und sieben Input-Kategorien materialisieren. citeturn741340search0

OpenAI gibt außerdem einen nützlichen Produktionsindikator an: Wenn der eingesetzte Reviewer einen Kommentar hinterlasse, führe dieser in 52,7 % der Fälle zu einer Codeänderung des Autors. OpenAI beschreibt diesen Wert als Signal für Actionability, nicht als vollständige Qualitätsmetrik, und warnt ausdrücklich davor, einen sauberen Review als Sicherheitsgarantie zu betrachten. Code Review sei eine Verteidigungsschicht, nicht der Ersatz für Tests oder menschliches Urteil. citeturn741340search0

Die aktuellere Codex-Dokumentation vom **20. Juli 2026** verstärkt die Scope-Idee. Repository-spezifische Review-Regeln gehören in `AGENTS.md`, aber OpenAI empfiehlt **kleine, lokal gescopte Regelsets**. Breite Anweisungen hätten in Evaluationen zu Noise geführt. Als Einstieg werden zwei oder drei folgenreiche, nicht triviale Regeln empfohlen; für jede solle man einen echten Verstoß, einen sicheren Gegenfall und eine irrelevante Änderung testen. Mechanische Format-/Lint-Regeln sollen in CI bleiben. citeturn741340search1

OpenAI berichtet für diese Custom-Rule-Evaluation, dass regelgeführte Varianten in der primären Suite 98 % der geforderten Custom Findings wiederfanden gegenüber 58,3 % ohne diese Regeln. Das ist kein allgemeiner „98 % Bug Recall“-Wert; es bezieht sich auf die evaluierten Repository-Regelverletzungen. Entscheidend für deine Architektur ist vielmehr, dass die Regeln eng gescopt wurden und explizit auch sichere Gegenbeispiele enthalten waren. citeturn741340search1

Auch in einem öffentlichen OpenAI-Repository findet sich die Trennung verschiedener Review-Tiefen. `openai/openai-go` schreibt für normale exhaustive PR-/Commit-Range-Reviews einen spezialisierten Review-Skill vor; **security-sensitive** Änderungen verlangen zusätzlich einen exakten Security-Diff-Scan, während breite Trust-Boundary-Änderungen oder explizit vollständige Security-Audits einen separaten Deep-Security-Scan erfordern. Das ist ein öffentliches Repository-Regelwerk und keine Aussage darüber, dass jedes interne OpenAI-Repository genauso aufgebaut ist. citeturn631831search4

OpenAI bietet Security Review zudem als eigenen fokussierten Pfad an: Er betrachtet PR-Diff plus relevanten Repository-Kontext, erzeugt Findings mit Severity und Evidenz und kann abhängig von einer gewählten Severity-Schwelle einen Check fehlschlagen lassen. Das stützt die Architekturidee, **Security nicht in einem universellen allgemeinen Reviewer zu verstecken**, sondern separat nach Risiko und Schweregrad zu behandeln. citeturn631831search1

### T3 Code

T3 Code ist besonders interessant, weil das Repository dein Problem fast wörtlich formuliert: In `CONTRIBUTING.md` wird begrenzte Review-Kapazität explizit als Engpass behandelt. Beiträge können vor einem tieferen Review wegen fehlender Problemdefinition, zu breitem Scope oder unzureichender Verifikation aussortiert werden; mehrere unabhängige Fixes sollen getrennt werden. „Passing triage“ ist ausdrücklich noch **keine** Korrektheits-, Security- oder Merge-Freigabe. citeturn741340search3

Die dazugehörige öffentlich sichtbare Contribution-Triage-Pipeline trennt Eligibility und Code Review technisch. Sie soll nur genug Code lesen, um Scope und Evidenz zu beurteilen, und die volle Korrektheits-, Security- und Performance-Prüfung dem späteren Review überlassen. Erst ein geeigneter PR wird per `macroscope-review` zum tieferen Review weitergereicht; ein einmal bestandener Triage-Lauf schafft keinen dauerhaften Trust. citeturn167462search2

Auf der deterministischen Seite arbeitet T3 selektiv. `vp run check:changed` formatiert und lintet geänderte Dateien, typecheckt betroffene Packages und deren Dependents, führt angrenzende Tests und bestimmte weitere change-sensitive Checks aus. Wenn der Dateisatz exakt dem letzten erfolgreichen Lauf entspricht, wird dessen Ergebnis wiederverwendet statt alles erneut auszuführen; jede relevante Veränderung invalidiert diesen Cache. citeturn167462search1turn630082search1

Das CI selbst enthält ebenfalls explizite Change Detection, damit teure Jobs nicht auf PRs laufen, die sie nicht beeinflussen können. Wenn die Changed-File-Ermittlung unvollständig oder nicht vertrauenswürdig ist, fällt ein Beispiel-Check bewusst auf „run it“ zurück statt einen notwendigen Check versehentlich auszulassen. Das ist ein gutes Muster für ein risk-basiertes Gate: **selektiv im Normalfall, konservativ bei Unsicherheit**. citeturn630082search0

Am deutlichsten ist die Macroscope-Konfiguration für `ui-consistency`. Sie gilt nur für ausgewählte Web-TSX/CSS-Dateien, arbeitet mit inkrementellem Input, beantwortet genau drei spezifische Review-Fragen und sagt explizit: keine breite Codebase-Verfolgung, keine Builds, keine Screenshots; wenn der Diff eine Verletzung nicht offensichtlich macht, gibt es kein Finding. Dazu stehen in der Konfiguration `maxBudgetPerRun: 2`, `maxBudgetPerPR: 10` und `conclusion: neutral`. Das ist fast das Gegenteil deines alten Gates: ein spezialisiertes, budgetiertes, **advisory** Agent-Gate. citeturn167462search0

Wichtig ist die Grenze dessen, was man daraus schließen darf. T3 Code benutzt öffentlich Macroscope und beschreibt den beabsichtigten Handoff, aber die eigene Contribution-Dokumentation warnt, dass Teile der Automatisierung beziehungsweise Trigger-Konfiguration verifiziert werden müssen und dass die Beschreibung nicht automatisch einen vollständig ausgerollten privaten Workflow belegt. Wir können also die Repository-Policies und Check-Konfigurationen analysieren, aber nicht einen unbekannten privaten Maintainer-Prozess erfinden. citeturn741340search3turn167462search2

### Große Open-Source-Projekte

Chromium arbeitet stark mit **lokaler Ownership**. Für geänderte Bereiche sollen möglichst lokale `OWNERS` gewählt werden, weil diese den notwendigen Kontext besitzen; Gerrit/Commit Queue verbindet menschliches Review mit automatischen Tests. Chromium empfiehlt außerdem, nicht unnötig viele Reviewer als Vollreviewer zu behandeln. citeturn167462search4turn167462search5

Kubernetes trennt ebenfalls Rollen: Reviewer und Approver werden anhand der Owners betroffener Dateien zugewiesen; `/lgtm` und `/approve` sind getrennte Signale. Für den dokumentierten Kubernetes-Workflow müssen außerdem die Tests passen. Das ist organisatorisch dasselbe Muster wie technisch spezialisierte Reviewer: **nicht jeder prüft alles, und nicht jedes Signal hat dieselbe Autorität**. citeturn813159search7turn813159search9turn813159search10

GitHub selbst unterstützt diese Staffelung über required status checks, erforderliche Reviews und Code-Owner-Reviews. Neue Commits können alte Approvals invalidieren oder eine Freigabe der letzten Änderung verlangen. Das erlaubt ein Gate, in dem deterministische Checks und menschliche Ownership hart blockieren, während ein KI-Review nur unter definierten Bedingungen ein zusätzliches Hard Gate wird. citeturn813159search0turn813159search3

Es gibt in diesen Quellen **keinen Beleg**, dass Chromium oder Kubernetes jeden PR mit einem allumfassenden LLM-Hard-Gate ähnlich deinem alten Gate prüfen. Der öffentlich belegbare gemeinsame Nenner ist vielmehr: betroffene Bereiche bestimmen, Ownership und spezialisierte Checks darauf routen, harte und weiche Signale trennen und nicht jede mögliche Qualitätsdimension in einen einzigen Reviewer pressen.

## Warum dein Kostenprofil so stark eskaliert

LLM-Code-Review wird nicht primär teuer, weil ein einzelner Diff „zu lang“ ist. Teuer wird es durch **Multiplikation**:

\[
\text{Kosten} \approx
\text{Review-Runden}
\times
\text{Reviewer-Jobs}
\times
\text{Versuche pro Job}
\times
(\text{Kontextbeschaffung}+\text{Reasoning}+\text{Output})
\]

Bei deinem Gate sind alle vier Faktoren erhöht. Große Änderungen erzeugen mehrere Areas plus Integration. Contract-Verletzungen können einen Job vollständig wiederholen. Runde eins ist maximal gründlich. Jeder BLOCK erzeugt einen Fix und damit potenziell eine neue Review-Runde. Nach mehreren inkrementellen Fix-Runden kann wieder ein Vollreview folgen. Wenn ein Reviewer technisch ausfällt, kann ein zweites Modell übernehmen.

Zusätzlich entsteht ein **Finding-to-Work Amplifier**. Ein Modell-Review kostet nicht nur Modell-Tokens. Jedes Finding kostet auch Entwickler- oder Agentenzeit, Fix-Generierung, Tests, neue Diffs und erneutes Review. Deshalb ist ein echtes, aber unwichtiges Finding nicht kostenlos. OpenAI modelliert diese Idee explizit als erwarteten Nutzen eines Findings unter Einbeziehung von menschlichem Verifikationsaufwand und Kosten falscher Alarme. citeturn741340search0

Dein altes Severity-Modell verstärkt genau diesen Effekt: Fast alles mit irgendeiner Konsequenz für Verhalten, Betrieb **oder Maintenance** wird blockierend. Ein System, das eine unnötige doppelte Konstante, ein echtes Edge-Case-Risiko und einen Auth-Bypass alle in denselben „du darfst nicht deployen“-Kanal steckt, zwingt den Autor rationalerweise, alles sofort zu bearbeiten. Dadurch wird Review-Recall unmittelbar zu Fix-Loop-Usage.

Ein zweiter versteckter Faktor ist **Kontext-Rekonstruktion**. Wenn zehn Area-Agenten unabhängig Caller/Callees suchen und danach noch ein Integration-Agent dieselben Boundaries untersucht, ist Parallelität zwar schnell, aber nicht billig. Anthropic weist allgemein darauf hin, dass übermäßige Subagent-Nutzung und hohe Effort-Stufen unnötige Exploration und Thinking-Tokens verursachen können. citeturn307385search3

Ein dritter Faktor ist der Unterschied zwischen **Validierung und Wiederholung**. Wenn ein Modell eine fast korrekte, aber schema-invalid JSON-Antwort geliefert hat, ist es ökonomisch ungünstig, die komplette Codeanalyse neu zu starten. Dein `METHOD_RETRIES` macht genau das. Eine bessere Architektur trennt die teure semantische Analyse vom billigen Output-Repair: Syntax reparieren beziehungsweise fehlende Felder gezielt nachfordern, ohne den gesamten Reviewer neu auf das Repository loszulassen.

Und schließlich optimierst du aktuell standardmäßig auf **universelle Coverage statt Risiko**. Die sieben Input-Klassen sind für Parser, Queueing, Retry-Logik oder State Machines hervorragend; für eine statische CSS-Anpassung, einen konstanten Rename oder viele normale Business-Logic-Änderungen sind Teile davon reine Review-Steuer. T3s UI-Agent zeigt das Gegenprinzip sehr schön: drei für genau diesen Codebereich relevante Fragen statt eines universellen Qualitätskatalogs. citeturn167462search0

## Zielarchitektur: ein adaptives, budgetiertes Review-Funnel

Ich würde dein Gate nicht „locker“ und auch nicht einfach „weniger gründlich“ machen. Ich würde es in **sechs voneinander unabhängige Verantwortlichkeiten** zerlegen:

```text
             ┌─────────────────────────┐
Diff/SHA ───►│ Deterministic Preflight │
             └────────────┬────────────┘
                          │ pass
                          ▼
             ┌─────────────────────────┐
             │ Risk + Scope Classifier │
             └───────┬──────┬──────────┘
                     │      │
                  low│   medium/high
                     │      │
                     ▼      ▼
                 ALLOW   Targeted LLM Review
                    +         │
                 advisory     ▼
                         Candidate Findings
                              │
                              ▼
                     semantic dedupe
                              │
                  ┌───────────┴────────────┐
                  │ only potential blocker │
                  ▼                        │
             Independent Validation       │ advisory
                  │                        ▼
                  ▼                   report only
        confirmed high/critical?
             │             │
            yes            no
             │             │
             ▼             ▼
          BLOCK           ALLOW
             │
             ▼
       delta-only recheck
             │
     max loop/budget reached
             │
             ▼
      HUMAN REVIEW REQUIRED

High-risk path in parallel:
auth / secrets / crypto / migration / payment /
destructive data / trust boundary / dependency
             │
             ▼
   deterministic security + targeted security review
```

Der wichtigste Unterschied ist: **„deep“ ist ein Modus, keine Default-Eigenschaft jedes Reviews.** Dein heutiger exhaustive Prompt bleibt wertvoll. Ich würde ihn nicht löschen, sondern in `deep-review` beziehungsweise `incident-review` verschieben. Er wird automatisch nur bei hochriskanten Änderungen oder manuell mit einem Label/Flag aktiviert.

Die Prioritäten für den Umbau wären:

1. **P0 – Default-Exhaustiveness entfernen.** Normales Review darf nicht mehr „jede Unit, jeden Caller und jede Input-Klasse“ verlangen. Dein bestehender Prompt wird zum Deep-Review-Profil. Gleichzeitig wird „when in doubt → BLOCKING“ gestrichen; bei Unsicherheit gilt im normalen Gate „nicht blockieren, gegebenenfalls advisory oder Validator anfordern“.
2. **P0 – ein echtes PR-Budget einführen.** Nicht nur vier Block-Runden, sondern maximale LLM-Calls, Input-/Output-Tokens oder Provider-Credits pro Review-Key. Budgeterschöpfung darf nicht automatisch einen neuen Reviewer triggern; Ergebnis ist `MANUAL_REVIEW_REQUIRED`, nicht endloses Re-Review.
3. **P0 – Findings erst nach unabhängiger Validierung blockieren lassen.** Das Primärmodell erzeugt Kandidaten. Nur `high/critical` beziehungsweise dein Äquivalent werden von einem zweiten, fokussierten Validator überprüft. Low/Medium bleiben advisory.
4. **P1 – deterministische Checks vor das LLM ziehen und nach Betroffenheit routen.** Typecheck, Lint, relevante Tests, Secret-/Dependency-/SAST-Checks und bekannte Repository-Regeln laufen ohne LLM. Das entspricht der von OpenAI empfohlenen Trennung mechanischer CI-Regeln von LLM-Urteil und dem selektiven T3-Ansatz. citeturn741340search1turn167462search1
5. **P1 – Review-once-per-diff statt Review-again-per-fix.** Cache-Key aus Base-SHA, Head-SHA, Policy-Version und Review-Profil. Nach einem Fix wird nur der neue Delta-Diff plus Status der alten bestätigten Blocker geprüft. Ein Vollreview wird nur bei geänderter Risikoklasse, relevant veränderter Basis, erheblicher Scope-Ausweitung oder explizitem Deep-Review erneut ausgelöst.
6. **P1 – semantische Deduplizierung und Finding-Lifecycle.** Nicht `(location, defect-text)`, sondern etwa `(root_cause_class, symbol/boundary, invariant)` als Fingerprint. Ein Finding kann `candidate → validated → open → fixed/false` durchlaufen. Mehrere Symptome derselben Ursache erzeugen einen Blocker mit mehreren Evidence-Locations.
7. **P2 – Security separat halten.** Auth, Permissions, Secrets, Crypto, SQL-/Schema-Migrationen, Destructive Operations und Trust-Boundary-Änderungen bekommen einen unabhängigen Security-Pfad. Das erlaubt dir, den generischen Reviewer leiser zu machen, ohne bei Security nachzugeben. OpenAIs öffentliches Security-Review-Design und das `openai-go`-Repository verwenden ebenfalls getrennte Security-Review-Ebenen. citeturn631831search1turn631831search4

Ein sinnvolles Risk Routing könnte so aussehen:

| Tier | Beispiele | Standard-Review |
|---|---|---|
| **Low** | Doku, generated-only, klar mechanische Refactors, triviale Testdaten | deterministische Checks; kein LLM oder nur advisory Cheap Review |
| **Medium** | normale Business Logic, UI-Verhalten, gewöhnliche APIs | 1 präziser Diff-Reviewer mit Repo-Browse-on-demand |
| **High** | Concurrency, Persistenz, öffentliche Contracts, komplexer State, Deployment-Konfiguration | 1 starker Repo-aware Reviewer; nur blocker candidates werden separat validiert |
| **Critical** | AuthN/AuthZ, Secrets, Crypto, Payments, Destructive Data, SQL-/Schema-Migration, Trust Boundaries | High-Pfad + unabhängiger Security-Check + Human/Codeowner |

Der Risk Classifier muss dafür kein LLM sein. Pfadmuster, geänderte Dependencies, Dateitypen, Symbol-/API-Marker und Diff-Metadaten reichen zunächst. Bei Unsicherheit solltest du wie T3s Changed-File-Gate **fail toward deeper checking**, nicht in einen stillen Skip. citeturn630082search0

Eine konkrete Konfiguration könnte so aussehen:

```yaml
review_policy:
  version: 2

  cache_key:
    - merge_base_sha
    - head_sha
    - policy_version
    - risk_profile

  deterministic:
    always:
      - changed_lint
      - changed_typecheck
      - affected_tests
    security:
      - secret_scan
      - dependency_policy
      - sast_changed_code

  risk:
    critical_paths:
      - "**/auth/**"
      - "**/permissions/**"
      - "**/crypto/**"
      - "**/migrations/**"
      - "**/payments/**"
    high_signals:
      - public_api_changed
      - schema_changed
      - concurrency_changed
      - deployment_config_changed
      - destructive_operation_changed

  budgets:
    low:
      llm_calls: 0
    medium:
      primary_calls: 1
      validator_calls: 1
      max_input_tokens: 45000
      max_output_tokens: 3000
    high:
      primary_calls: 1
      validator_calls: 2
      max_input_tokens: 90000
      max_output_tokens: 6000
    critical:
      primary_calls: 1
      validator_calls: 2
      security_calls: 1
      max_input_tokens: 140000
      max_output_tokens: 8000

  findings:
    blocking:
      min_severity: high
      require_validation: true
      require_evidence: true
    advisory:
      min_severity: medium
    suppress:
      - style
      - subjective_refactor
      - deterministic_linter_issue
      - preexisting_issue

  rereview:
    max_fix_loops: 2
    delta_only: true
    full_review_when:
      risk_tier_increased: true
      base_materially_changed: true
      new_critical_path: true
      diff_growth_ratio: 1.5
      new_files: 3

  deep_review:
    manual_label: deep-review
    automatic_for:
      - critical_risk
      - incident_fix
      - broad_trust_boundary_change

  budget_exhausted:
    result: manual_review_required
```

Die konkreten Token-Zahlen sind **Startwerte für deine Evaluation, keine branchenweit richtigen Schwellen**. Wichtiger als die Zahl selbst ist, dass der Scheduler vor jedem weiteren Agent-Call entscheiden kann: „Passt dieser Call noch ins PR-Budget und erhöht er erwartbar die Entscheidungsqualität?“

Der Kernalgorithmus kann sehr klein bleiben:

```python
def gate(base, head, policy):
    key = review_key(base, head, policy.version)

    if cached_final_decision(key):
        return cached_final_decision(key)

    diff = build_diff(base, head)

    deterministic = run_affected_checks(diff)
    if deterministic.has_hard_failure:
        return BLOCK(deterministic.failures)

    risk = classify_risk(diff)

    if risk == "low":
        return ALLOW(advisories=deterministic.advisories)

    budget = Budget(policy.budgets[risk])

    candidates = primary_review(
        diff=diff,
        context=on_demand_repo_context(),
        instructions=high_signal_policy(risk),
        budget=budget,
    )

    candidates = semantic_dedupe(candidates)

    blocker_candidates = [
        f for f in candidates
        if f.severity >= HIGH
        and f.evidence_is_concrete
    ]

    validated = []
    for cluster in cluster_by_root_cause(blocker_candidates):
        if not budget.can_validate(cluster):
            return MANUAL_REVIEW_REQUIRED(
                reason="review budget exhausted",
                unresolved=cluster,
            )

        result = validate_candidate(cluster)
        if result.confirmed:
            validated.append(result)

    if risk == "critical":
        security = run_independent_security_path(diff, budget)
        validated.extend(security.confirmed_blockers)

    if validated:
        save_open_findings(key, validated)
        return BLOCK(validated)

    return ALLOW(
        advisories=[
            *deterministic.advisories,
            *nonblocking(candidates),
        ]
    )
```

Eine Fix-Runde sollte anschließend **nicht** wieder `gate(base, new_head)` als Vollreview aufrufen. Stattdessen:

```python
def recheck(old_review, new_head):
    delta = diff(old_review.reviewed_head, new_head)

    if risk_boundary_crossed(old_review, delta):
        return gate(old_review.base, new_head, policy)

    statuses = verify_previous_blockers(
        findings=old_review.confirmed_blockers,
        delta=delta,
        necessary_context_only=True,
    )

    new_candidates = review_only_newly_changed_behavior(delta)

    if confirmed_new_high_risk(new_candidates):
        return validate_and_decide(new_candidates)

    if any(status == "STILL" for status in statuses):
        return BLOCK(statuses)

    return ALLOW()
```

Damit behältst du die gute Idee deiner Phase 2, beseitigst aber den problematischen Vertrag „Runde eins muss deshalb alles finden“ und den automatischen Vollreview als Routine-Eskalation.

## Severity, False Positives und die richtige Blocking-Policy

Der wichtigste Policy-Schritt ist, **Codequalität und Merge-Blockierung nicht mehr gleichzusetzen**. Ein sauberer Codebestand braucht durchaus Hinweise auf Wartbarkeit, Testqualität und Design. Aber nicht jedes reale Problem rechtfertigt einen zusätzlichen Deploy-Zyklus.

Ich würde vier semantische Severity-Stufen verwenden:

| Severity | Bedeutung | Gate-Verhalten |
|---|---|---|
| Critical | unmittelbares Security-, Datenverlust-, Auth-/Safety- oder massives Produktionsrisiko | blockiert nach unabhängiger Bestätigung; Security/Human-Pfad |
| High | klare falsche Funktion, Crash, Contract-Break, Race, relevante Regression | blockiert nach unabhängiger Bestätigung |
| Medium | reales Problem, aber begrenzter Impact oder gute Workaround-/Containment-Situation | advisory; optional Owner-Eskalation |
| Low/Nit | Wartbarkeit, Cleanup, Naming, Style, hypothetische Verbesserung | advisory oder unterdrücken |

Zusätzlich zu Severity brauchst du **Confidence/Evidence**, aber nicht zwingend als vom LLM erfundene Zahl. Anthropic ist gerade von einem dokumentierten numerischen Confidence-Modell zu separater Validierung übergegangen; das ist für dein Gate robuster. citeturn307385search0

Für einen Blocker würde ich deshalb drei Bedingungen gleichzeitig verlangen:

```text
impact >= HIGH
AND evidence == concrete
AND independent_validation == CONFIRMED
```

„Concrete evidence“ heißt beispielsweise: eine konkrete Input-/State-Sequenz, ein verletzt nachweisbarer Invariant, ein Test/Repro oder ein eindeutig gebrochener Contract. „Das könnte unter Umständen problematisch sein“ gehört nicht in den Blocking-Kanal.

Deine sieben Input-Klassen würde ich nicht wegwerfen. Ich würde daraus **Review-Module** machen. `retry` und `concurrency` werden automatisch bei Queue-/Worker-/Lock-/Async-Code aktiviert; `null_missing` und `error_path` bei Parsing/Serialization/API-Boundaries; `max_overflow` bei Integer-, Buffer-, Pagination- und Limit-Logik; `stale_state` bei Cache/State/Distributed-System-Code. Ein normaler Diff bekommt nur die einschlägigen Module. So bewahrst du deine gründliche Methodik, ohne sie siebenfach auf jede Funktion anzuwenden.

Dasselbe gilt für die ausgezeichnete Root-Cause-/Twin-Idee in deinem Gate. Für einen Commit, der klar ein Production-Incident oder eine Bugklasse behebt, ist Twin Search extrem sinnvoll. Für eine neue UI-Komponente muss sie nicht automatisch laufen. T3s spezielle UI-Konfiguration illustriert genau dieses Prinzip: enge Fragestellung dort, wo sie relevant ist. citeturn167462search0

Auch die Codex-Regel-Empfehlung passt hier: wenige folgenreiche, nicht offensichtliche Invarianten in lokal gescopten `AGENTS.md`-Regeln statt eines globalen Katalogs. Wenn eine Regel mechanisch prüfbar ist, gehört sie in CI. citeturn741340search1

Für deine deduplizierten Findings würde ich einen stabilen Fingerprint speichern, etwa:

```text
fingerprint =
    defect_family
  + affected_contract_or_symbol
  + root_cause_invariant
```

Damit werden „DB connection bleibt beim Error offen“ in drei Call Sites nicht drei unabhängige Fix-Loops. Ein Finding enthält stattdessen `evidence_locations[]`. Wenn mehrere Reviewer dasselbe Root Cause sehen, steigt Confidence, nicht die Anzahl der Tickets.

## Messung, Evaluation und stufenweise Migration

Die neue Architektur sollte nicht anhand von „fühlt sich viel billiger an“ ausgerollt werden, sondern anhand einer **Pareto-Kurve aus kritischem Recall, Blocking-Präzision und Kosten**. Genau die Kombination aus Coverage und „Restraint“ ist auch in OpenAIs Custom-Rule-Evaluation explizit Teil des Testdesigns gewesen. citeturn741340search1

Deine wichtigsten Metriken wären:

| Metrik | Was sie beantwortet |
|---|---|
| **Critical/High Recall** | Wie viele adjudizierte schwere Bugs erkennt das Gate? |
| **Blocking Precision** | Wie viele Blocker waren nach menschlicher Prüfung tatsächlich merge-relevant? |
| **False-positive Rate, blocking** | Wie oft erzeugt das Gate unnötige Fix-Loops? |
| **Finding action rate** | Wie oft führt ein Finding zu einer sinnvollen Codeänderung? OpenAIs veröffentlichte 52,7 % können als interessanter externer Referenzpunkt dienen, aber nicht als Zielvorgabe für dich. citeturn741340search0 |
| **Kosten pro PR p50/p95** | Input-, Cached-, Reasoning-/Output-Tokens beziehungsweise dein Planverbrauch |
| **Calls pro PR** | Zeigt Fanout und Retry-Multiplikation |
| **Kontextbytes pro PR** | Zeigt, ob unnötig ganze Repos mehrfach gelesen werden |
| **Fix-Loops pro PR** | Direktes Maß deines jetzigen Schmerzpunkts |
| **Zeit bis Merge p50/p95** | Quality Gate darf Delivery nicht dominieren |
| **Budget-exhausted Rate** | Zeigt, ob Budgets zu eng oder PRs zu groß sind |
| **Escaped High/Critical Defects** | Was wurde nach ALLOW später als Hotfix/Incident gefunden? |
| **Deep-review activation rate** | Prüft, ob „Deep“ wirklich Ausnahme bleibt |
| **Cache hit rate** | Zeigt Nutzen von Review-once-per-diff |

Für das Eval-Set würde ich drei Quellen kombinieren: historische echte Fehler/Incidents aus deinem Repository, künstlich oder per Mutation eingebaute schwere Defekte und reale „clean counterexamples“, die bewusst **nicht** geblockt werden dürfen. OpenAIs Regel-Evaluation verwendet ebenfalls Verstöße plus sichere Gegenbeispiele, gerade um Noise zu messen. citeturn741340search1

Jedes Finding braucht anschließend menschliche Adjudikation auf Root-Cause-Ebene: `real/not real`, `severity`, `blocking-worthy`, `duplicate`, `already caught deterministic`, `would escape without LLM`. Nur damit kannst du erkennen, ob dein altes Gate zwar „mehr“ findet, aber bei schweren Bugs tatsächlich einen höheren Recall hat oder hauptsächlich zusätzliche Medium-/Maintenance-Funde produziert.

Der Migrationspfad sollte inkrementell sein. Zuerst instrumentierst du die **alte** Pipeline genauer: pro Area, Modell, Versuch, Phase und Finding die Usage speichern; besonders wichtig sind `attempt_index`, `context_bytes`, `reason_for_retry`, `finding_origin`, `severity`, `later_fixed`, `duplicate_cluster`. Damit wirst du wahrscheinlich schnell sehen, ob die Hauptkosten aus Exhaustiveness, Contract-Reasks, Bereichssplitting oder Fix-Loops kommen.

Danach läuft die neue Policy im **Shadow Mode** parallel, ohne Merge-Entscheidungen zu treffen. Beide Systeme sehen denselben PR/SHA; die Findings werden root-cause-dedupliziert und adjudiziert. Du vergleichst nicht nur Anzahl der Findings, sondern insbesondere: „Welche High/Critical-Probleme findet Alt, die Neu übersieht?“ und umgekehrt „Welche Alt-Blocker hätte Neu korrekt nicht blockiert?“

Im nächsten Schritt stellst du Low- und Medium-Risk-PRs vollständig auf das neue Funnel um, während High/Critical noch dual bewertet werden. Erst wenn dein Eval zeigt, dass der schwere Recall stabil bleibt, schaltest du auch diese Klassen um. Der alte exhaustive Reviewer bleibt zunächst als manuell auslösbarer `deep-review` bestehen.

Erst danach würde ich die alte Retry-/Phasen-Komplexität löschen. Besonders `METHOD_RETRIES` sollte nicht mehr bedeuten „analysiere alles erneut“. JSON- oder Schema-Reparatur muss ein lokaler, kleiner Repair-Schritt sein. Und `PHASE2_MAX` sollte nicht mehr automatisch bedeuten „jetzt wieder das gesamte Ding prüfen“, sondern eher: „zu viele Iterationen → Human/Deep Review erforderlich“.

Das gewünschte Zielbild ist nicht „weniger Review“. Es ist **weniger redundante Review-Arbeit pro vermiedenem schweren Defekt**. Deine ideale Kennzahl wäre deshalb etwas wie:

\[
\text{Review Efficiency}
=
\frac{\text{bestätigte High/Critical Defects vor Merge}}
{\text{LLM-Kosten} + \lambda\cdot\text{unnötige Fix-Loops}}
\]

Wenn die neue Pipeline beispielsweise 97 % statt 99 % deines Critical/High-Recalls erreicht, dafür Blocking-Fehlalarme um 70 % und Usage um 80 % reduziert, kann sie für ein reales Deployment-System deutlich sicherer sein, weil Entwickler das Gate nicht ständig umgehen wollen und du Budget für wirkliche Hochrisikoänderungen übrig behältst. Die konkreten Prozentwerte wären erst durch deine Evaluation zu bestimmen; die zugrunde liegende Präzisions-vor-Maximal-Recall-Logik entspricht jedoch ausdrücklich der von OpenAI beschriebenen Deployment-Erfahrung. citeturn741340search0

## Was wir sicher wissen – und was nicht

Stand der öffentlichen Quellen ist **22. November 2026**. OpenAIs grundlegender Verifikationsbeitrag stammt vom **1. Dezember 2025**, die Dokumentation zu Custom Review Rules vom **20. Juli 2026**. Die untersuchten Anthropic-, T3-, Chromium-, Kubernetes- und GitHub-Repositories beziehungsweise Dokumentationen wurden in ihrem aktuell öffentlich verfügbaren Stand abgerufen. citeturn741340search0turn741340search1

Bei Anthropic ist öffentlich der konkrete Claude-Code-Review-Prompt mit Eligibility-Check, vier spezialisierten Reviewern, High-Signal-Regeln, Duplikatverbot und nachgeschalteter Finding-Validierung sichtbar. Sicher **nicht** daraus ableitbar ist Anthropic-internes CI für sämtliche eigenen Produkt-Repositories. Außerdem ist das noch öffentlich sichtbare Confidence-Scoring im Plugin-README veraltet; Anthropic hat das selbst bestätigt. citeturn741340search2turn307385search0

Bei OpenAI ist öffentlich dokumentiert, dass der produktive Code Reviewer auf hohe Präzision optimiert wird, Repo-Tools und Ausführung verwenden kann und intern jeder PR automatisch reviewed wird. Ebenfalls öffentlich ist die Empfehlung, Repository-Regeln klein und gescopt zu halten und deterministische Dinge in CI zu belassen. Wie jedes einzelne interne OpenAI-Repository seine Branch Protection, Security-Schwellen oder Human Approvals konfiguriert, ist damit nicht vollständig offengelegt. citeturn741340search0turn741340search1

Bei T3 Code können wir die öffentliche Triage-Policy, selective `check:changed`-Logik, CI-Change-Detection und konkrete Macroscope-Agent-Konfiguration inklusive `maxBudgetPerRun: 2`, `maxBudgetPerPR: 10` und `conclusion: neutral` belegen. Wir sollten daraus aber keinen nicht dokumentierten privaten Review-Prozess erfinden; das Repository selbst weist darauf hin, dass Automation/Trigger separat verifiziert werden müssen. citeturn167462search0turn167462search1turn167462search2turn741340search3

Bei deinem Gate sind Architektur, Prompts, Schwellen und Schleifen in der hochgeladenen alten Version direkt belegbar. Nicht enthalten sind die aktuelle `pyramide.json`, reale State-/Usage-Historie und deine inzwischen gelockerte Fassung. Deshalb ist die Aussage „dies sind deine historischen Kostentreiber“ eine **codebasierte Diagnose**, während die konkrete Frage „welcher Faktor hat wie viel Prozent deiner echten Usage verursacht?“ erst mit deinen Laufzeitdaten beantwortbar wäre.

Die zentrale Schlussfolgerung ist trotzdem ungewöhnlich klar: **Dein Gate hat bereits viele gute Einzelideen – deterministische Vorprüfung, SHA-Cache, inkrementelle Fix-Runden, Area-Splitting, Integration-Review, Root-Cause/Twin-Analyse und technische Fallbacks. Das Problem ist, dass fast jede dieser Ideen gleichzeitig und standardmäßig auf maximale Gründlichkeit geschaltet ist.** Der Umbau sollte deshalb nicht bei „besserem Prompting“ anfangen, sondern bei Scheduling, Severity und ökonomischer Kontrolle: billige Filter zuerst, tiefe Analyse nach Risiko, Validierung nur für Blocker, Security separat, exaktes Diff-Caching und ein harter Budgetdeckel pro PR. Das ist der stärkste gemeinsame Nenner der öffentlich dokumentierten Ansätze von OpenAI, Anthropic, T3 Code und etablierten OSS-Review-Prozessen. citeturn741340search0turn741340search2turn167462search0turn167462search1turn167462search4turn813159search7