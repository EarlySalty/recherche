# Zusatzrecherche: Delta-Analyse zum bestehenden Code-Review-Gate

## Kernaussage des Deltas

Die neue ZIP verändert die Grundrichtung der ersten Recherche **nicht**, aber sie verschiebt die Prioritäten deutlich. Die bisherige Empfehlung lautete im Kern: deterministische Checks vor LLM, riskobasiertes Review, hohe Blocking-Schwellen, Delta-Reviews nach Fixes und harte Budgets. Die neuen Laufzeitdaten zeigen jetzt, dass mehrere dieser Punkte bereits umgesetzt wurden und dass der verbleibende Hauptkostenblock viel konkreter ist: **Runde 1 selbst und die Art, wie jeder Reviewer-Prozess gestartet und vervielfacht wird.**

Das ist durch die neuen Messdaten wesentlich stärker belegt als zuvor. In 14 Tagen wurden 5.204 Claude-Sessions erfasst; 4.912 davon waren Gate-Reviewer. Nach dem mitgelieferten Kostenmaß entfielen **75 % des gesamten Verbrauchs auf Gate-Reviewer**. Der Median des ersten Reviewer-Turns lag bereits bei **60.195 Kontexttokens**, der Median des maximalen Session-Kontexts bei rund **190.536 Tokens**. Gleichzeitig gab es im Ledger 122 BLOCK- gegenüber nur 8 ALLOW-Urteilen bei 41 Branches; ein Urteil enthielt durchschnittlich 13,4 Areas und maximal 68. Das sind nun gemessene Betriebsdaten, nicht mehr nur eine aus dem Code abgeleitete Kostenhypothese.  
**Dateibefund:** `3-laufzeitdaten/usage-auswertung-14-tage.json:2–16, 75–87, 142–158`.

Damit würde ich eine Aussage aus der ersten Recherche präzisieren: **Das Gate sollte derzeit nicht neu gebaut werden.** Sein State-Modell, SHA-Bezug, read-only Reviewer-Clone, deterministisches Regelgate und die inzwischen echte Phase-2-Verifikation sind brauchbare Bausteine. Der größte kurzfristige Hebel liegt darin, die Reviewer-Runtime drastisch zu entschlacken und die Runde-1-Fan-out-Architektur zu ändern. Erst wenn diese beiden Maßnahmen gemessen sind, lohnt sich eine grundlegendere Neuentwicklung.

Die seit dem ersten Bericht hinzugekommenen Änderungen #20–#22 haben bereits drei dort empfohlene Probleme beseitigt. Seit #20 blockiert nur noch `high`; `low` wird zum Hinweis, und ein bloßes `input_class ok:false` erzeugt nicht mehr automatisch einen Merge-Blocker. Seit #21 bleibt ein Fix auf derselben Basis in Phase 2, statt bei Diff-Wachstum wieder einen Vollscan auszulösen. Seit #22 besteht Phase 2 aus **einem einzigen Verifikations-Reviewer**, der nur offene Findings und den Fixmechanismus prüft und keine erneute Unit-/Caller-/Boundary-/Security-Runde startet.  
**Dateibefund:** `1-gate-code/gpt-workers/CHANGELOG.md:1–26, 28–52, 54–71`.

Das bedeutet: Die frühere Empfehlung „macht Phase 2 wirklich inkrementell“ ist praktisch erledigt. **Der neue Schwerpunkt muss Runde 1 sein.**

## Neue Dateibefunde und was sie gegenüber dem ersten Bericht ändern

Die vielleicht wichtigste neue Zahl steht in Änderung #20. Vor dieser Änderung bestanden von 6.938 gespeicherten Blocking-Findings **37 % aus `low`-Findings und 30 % aus abgeleiteten Input-Class-Findings**. Diese Kategorien waren also ein erheblicher Teil des alten Blockierungsdrucks. Nach #20 wurden acht Branches erstmals unter der neuen Policy geprüft; zwei erreichten ALLOW, sechs blockierten weiterhin und speicherten zusammen **344 echte High-Blocker**, ohne Low- oder Input-Class-Blocker.  
**Dateibefund:** `1-gate-code/gpt-workers/CHANGELOG.md:30–36, 54–71`.

Das ist wichtig, weil es zwei Dinge gleichzeitig zeigt. Erstens war die alte Severity-Policy tatsächlich massiv zu streng. Zweitens reicht das Absenken der Severity **allein nicht**: Selbst nachdem Low-Noise entfernt wurde, erzeugt Runde 1 noch sehr viele High-Findings. Die nächste Optimierungsstufe darf daher nicht einfach „noch mehr Dinge von High zu Low umklassifizieren“ sein. Sie muss verhindern, dass ein Vollreview immer wieder jede denkbare Fehlerhypothese über jede einzelne Unit systematisch expandiert.

Der aktuelle Runde-1-Prompt tut genau das weiterhin. Er bezeichnet Runde 1 ausdrücklich als „exhaustive“, fordert für jede erforderliche Unit Invarianten, alle Caller und Callees sowie sämtliche Input-Klassen, zusätzlich Root-Cause, Security und Boundary-Prüfung. Obwohl nur `high` noch blockiert, bleibt das Suchverfahren selbst maximal breit.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:6318–6349`.

Besonders auffällig ist der Unterschied zwischen Runde 1 und Phase 2. Phase 2 ist inzwischen sehr nah an einer sauberen Verifikationsarchitektur: „Do not review the change again“, jedes alte Finding wird als `FIXED`, `STILL` oder `FALSE` bewertet, und neue Findings sind nur erlaubt, wenn der Fix selbst an einer geänderten Zeile einen Defekt einführt.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:6408–6424`.

Das ist genau die Architektur, die ich für alle Folgerunden beibehalten würde. Ich würde sie sogar noch stärker schützen: **Ein einmal vollständig reviewter Branch sollte nie allein wegen Rundenzahl wieder automatisch zum Vollreview zurückkehren.** Ein neuer Vollreview sollte nur durch einen semantischen Scope-Wechsel ausgelöst werden, etwa neue Subsysteme, neue externe Schnittstellen, Auth-/Security-Code, Datenmigration oder einen bewusst gesetzten Deep-Review-Trigger. Ein „drei Verifikationsrunden verbraucht, deshalb wieder alles scannen“ ist ökonomisch die falsche Rückfallstrategie.

Ein zweiter neuer, sehr belastbarer Kostenbefund liegt im Startkontext. Jeder Reviewer läuft derzeit als normaler `claude -p`-Prozess. Die Kommandoerzeugung begrenzt zwar Tools und Schreibrechte, isoliert aber **nicht die Claude-Code-Konfiguration**: Es gibt kein `--bare`, und die mitgelieferten Runtime-Daten zeigen, dass Reviewer die volle User-Konfiguration mit Skills, `CLAUDE.md`, Hooks und Plugins erben.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:5257–5274`; `3-laufzeitdaten/usage-auswertung-14-tage.json:142–158`.

Die Grundlast ist nicht klein: 105 Skill-Einträge mit knapp 30.000 Zeichen sowie ein globales `CLAUDE.md` mit rund 11.900 Zeichen; der gemessene erste Turn startet im Median bei 60.195 Tokens. Multipliziert mit 4.912 Reviewer-Sessions bedeutet das rechnerisch, dass allein die **ersten Turns** innerhalb von 14 Tagen rund 296 Millionen Kontexttoken-Instanzen enthielten. Das ist keine Abrechnungszahl, weil Cache-Reads und Cache-Writes anders gewichtet werden, aber es zeigt die Größenordnung des wiederholt geladenen Kontextes.  
**Dateibefund:** `3-laufzeitdaten/usage-auswertung-14-tage.json:13–16, 142–158`.

Anthropic dokumentiert inzwischen explizit einen Minimalmodus: `--bare` bzw. `CLAUDE_CODE_SIMPLE=1` schaltet unter anderem Hooks, Skills, Plugins, MCP-Server, Auto-Memory und automatische `CLAUDE.md`-Discovery ab und verwendet einen reduzierten Systemprompt. citeturn153389search0turn764842search1 Claude Code trennt außerdem User-, Projekt- und lokale Settings und erlaubt die Kontrolle darüber, welche Konfiguration eine Session erhält; Anthropic empfiehlt `/status`, um die tatsächlich geladenen Setting Sources zu überprüfen. citeturn628402search0turn764842search0

Daraus folgt für dieses Gate ein neuer **P0-Hebel**, den die erste Recherche mangels Laufzeitdaten nicht so hoch priorisieren konnte: Reviewer sollten in einer **minimalen, dedizierten Runtime** laufen, nicht in derselben reichen Claude-Code-Umgebung wie der Coding-Agent.

## Konkrete Änderung der Zielarchitektur

Die erste Zielarchitektur sollte deshalb nicht ersetzt, sondern in drei Punkten verschärft werden.

Erstens würde ich aus „Deep Review in Runde 1“ ein **fix budgetiertes Deep Review mit konstantem Fan-out** machen. Heute hängt der Fan-out an Areas; dein Ledger zeigt durchschnittlich 13,4 Areas und bis zu 68. Das bedeutet, dass größere Änderungen automatisch sehr viele unabhängige Opus-Sessions erzeugen. Ein Reviewer hat aber bereits vollständigen Repo-Zugriff über `Read`, `Grep`, `Glob` und lesende Git-Kommandos. Deshalb gibt es keinen zwingenden Grund, 53 oder 68 semantische Reviewer-Prozesse zu erzeugen, nur weil der Diff viele Units enthält.  
**Dateibefund:** `3-laufzeitdaten/usage-auswertung-14-tage.json:143–149`; `1-gate-code/gpt-workers/gate_hook.py:4886–4897`.

OpenAIs veröffentlichte Evaluation weist genau in die Gegenrichtung: Repo-Zugriff und Tool-Nutzung verbessern sowohl Recall als auch Precision gegenüber einer reinen Diff-Betrachtung; ihr produktiver Reviewer wird zugleich bewusst auf hohen Signalwert statt maximalen Recall eingestellt. citeturn965658view2 Das spricht für **wenige repo-fähige Reviewer mit klaren Aufgaben**, nicht für viele isolierte Mini-Reviewer, die denselben Kontext jeweils neu laden.

Für deinen Betrieb würde ich mit **zwei Runde-1-Reviewern als Standard und maximal drei** experimentieren: ein Correctness/Boundary-Reviewer und ein Security/Concurrency/State-Reviewer; nur bei sehr kleinen Changes kann einer reichen. Die Reviewer bekommen denselben eingefrorenen Branch-Scope, dürfen gezielt außerhalb des Diff nach Dependencies suchen, aber sie erhalten **keine Pflicht, jede Unit × jede Input-Klasse vollständig auszufüllen**. Große PRs werden nicht mehr nach 15 Units oder 160 KiB automatisch in zusätzliche Modellprozesse zerlegt; diese Grenzwerte können höchstens zum Erzeugen von Kontext-Hinweisen dienen. Die heutigen Konstanten `AREA_UNITS_MAX=15` und `AREA_DIFF_BYTES=160 KiB` sollten deshalb nicht mehr Fan-out steuern.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:5866–5869`.

Zweitens sollte das Gate Findings nach Runde 1 **einfrieren**. Runde 1 erzeugt eine Menge kanonischer Root-Cause-Findings. Danach sind nur noch vier Zustandsübergänge zulässig: `OPEN → FIXED`, `OPEN → FALSE`, `OPEN → STILL` und `FIX → NEW_REGRESSION`. Ein normaler Fix darf keine neue generelle Suchwelle eröffnen. Genau dieses Verhalten ist in #22 bereits weitgehend vorhanden und sollte zum dauerhaften Modell werden.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:6414–6424`.

Drittens sollten Contract-Fehler nicht noch einmal die gesamte Analyse auslösen. `method_review()` ruft bei Validierungsproblemen aktuell bis zu viermal `run_review()` auf; jede Reask enthält wieder den Review-Prompt plus die beste vorherige Antwort und Probleme.  
**Dateibefund:** `1-gate-code/gpt-workers/gate_hook.py:6768–6794`.

Dieser Mechanismus ist nach den neuen Laufzeitdaten zu teuer. Ein Schemafehler ist kein Grund, den Reviewer erneut Code lesen zu lassen. Der bessere Pfad ist: erste Analyse strukturiert speichern; bei syntaktischem JSON-Fehler einmal ein **kleines Format-Repair** ohne Repo-Tools ausführen; bei fehlendem Pflichtfeld entweder lokal aus dem vorhandenen Ergebnis reparieren oder den einzelnen fehlenden Nachweis gezielt nachfragen. Nach zwei technischen Fehlern sollte der Job nicht weiter exponentiell Context verbrennen, sondern als `REVIEW_INCONCLUSIVE` eskalieren.

Das resultierende Zielbild ist damit einfacher als im ersten Bericht:

```text
deterministische Checks
        ↓
Scope + Risk Classifier
        ↓
Runde 1: 1–3 feste Reviewer, minimaler Claude-Context
        ↓
Root-Cause-Dedupe + High-Signal-Validator
        ↓
BLOCK nur bestätigte High-Findings
        ↓
Fix
        ↓
1 Verifikations-Reviewer auf offene Findings + Fix-Diff
        ↓
FIXED / STILL / FALSE / NEW_REGRESSION
        ↓
ALLOW
```

Ein neuer Vollscan erfolgt nur bei `scope_epoch_changed=true`, nicht nach einer festen Rundenzahl.

## Was Claude Code, Codex und T3 Code jetzt zusätzlich nahelegen

Die aktuelle öffentliche Anthropic-Architektur bestätigt weiterhin das Prinzip **begrenzter Spezialisierung** statt Area-pro-Unit-Fan-out. Das öffentliche Claude-Code-Review-Plugin beschreibt vier parallele Spezialagenten: zwei für Projektregeln, einen für offensichtliche Bugs und einen für Git-History-Kontext; Findings werden anschließend nach Confidence gefiltert. Es überspringt außerdem triviale, geschlossene, Draft- oder bereits geprüfte PRs. citeturn341679search0 Das ist kein Beweis für Anthropics interne CI-Architektur, aber es ist ein belastbares öffentliches Designsignal: **fixed fan-out, eligibility filter, finding validation**.

Noch näher an deinem konkreten Problem ist OpenAI. OpenAI schreibt ausdrücklich, dass bei produktivem Code Review Präzision für Nutzbarkeit wichtiger sei als maximaler Recall und dass ein moderater Recall-Verlust akzeptiert werde, wenn dadurch Signalqualität und Vertrauen steigen. Gleichzeitig zeigen ihre Experimente, dass zusätzliche Inferenz nicht nur weitere Findings erzeugen muss; mehr Budget verbessert vor allem Kalibrierung und reduziert Fehlalarme. citeturn965658view2 Für dein Gate bedeutet das: Mehr Reviewer-Budget sollte künftig eher in die **Verifikation der wenigen gefährlichen Hypothesen** gehen als in zusätzliche Suchbreite.

OpenAIs 2026 veröffentlichte Custom-Review-Regeln verstärken diese Richtung. Regeln sollen klein, lokal gescopet und auf folgenreiche, nicht offensichtliche Invarianten beschränkt bleiben; mechanische Checks gehören ausdrücklich in Tests und Linters. In deren Eval erreichten regelgeführte Varianten sehr hohe Coverage, gleichzeitig wurde „Restraint“ an sicheren Gegenbeispielen separat gemessen. citeturn341679search3 Das passt direkt zu deinem `FEHLERKLASSEN.md`: Der Katalog sollte nicht automatisch als globale Checkliste in jeden Runde-1-Prompt injiziert werden. Er sollte als **Rule Router** dienen, aus dem pro Diff nur die relevanten Fehlerklassen aktiviert werden.

T3 Code liefert inzwischen ein besonders gutes Gegenmodell zu deinem heutigen Rundeneins-Fan-out. Die aktuelle `ui-consistency`-Konfiguration ist ein enger Spezialcheck mit `input: incremental`, nur bestimmten Dateimustern, `maxBudgetPerRun: 2`, `maxBudgetPerPR: 10` und `conclusion: neutral`. Der Prompt verlangt ausdrücklich nur geänderte Zeilen zu prüfen, nicht quer durch das Repository zu expandieren, und bei nicht offensichtlichen Verstößen **kein Finding** zu erzeugen. citeturn965658view1 Gleichzeitig läuft T3s `check:changed` deterministisch nur über betroffene Dateien, Packages und abhängige Pakete; die vollständigen Suites bleiben CI-Aufgabe. citeturn341679search5

Interessant ist außerdem, dass der aktuelle T3-Spezialreview selbst mit einem leistungsfähigen Modell arbeitet, aber nur **low reasoning**, engem Scope und einem harten Run-/PR-Budget. citeturn965658view1 Das ist für dein Wochenlimit vermutlich wichtiger als die Wahl „Opus versus kleineres Modell“: Die Architektur sollte zuerst verhindern, dass das starke Modell 10–50-mal denselben Branch neu aufrollt.

## Geänderte Prioritäten für dein Gate

Auf Basis der neuen Dateien würde ich die Umbaureihenfolge gegenüber dem ursprünglichen Bericht konkret ändern:

1. **Reviewer-Runtime isolieren.** Teste Runde-1- und Phase-2-Reviewer mit `--bare` beziehungsweise einer ebenso minimalen Claude-Code-Konfiguration, behalte nur `Read`, `Grep`, `Glob` und explizit freigegebene read-only Git-Kommandos. Anthropic dokumentiert, dass Bare Mode genau die heute mitgeladenen Kategorien wie Skills, Hooks, Plugins, MCP und `CLAUDE.md`-Discovery entfernt. citeturn153389search0turn764842search1 Miss dabei `first_turn_context`, Cache-Create, Cache-Read und maximale Context-Größe separat. **Erwartung:** sehr hohe Einsparwirkung bei sehr kleinem Qualitätsrisiko, weil deine Reviewer-Promptlogik die benötigte Review-Policy ohnehin explizit enthält. Der konkrete Prozentwert muss gemessen werden.

2. **Area-Fan-out vom PR-Umfang entkoppeln.** Ersetze „eine Session pro Area plus Integration“ durch eine feste Obergrenze von zunächst zwei oder drei Reviewer-Sessions pro Vollreview. Reviewer dürfen Repo-Kontext selbst nachladen. OpenAIs Ergebnis, dass Repo-Tooling Reviewqualität steigert, stützt diese Richtung. citeturn965658view2 **Erwartung:** wahrscheinlich der größte strukturelle Usage-Hebel nach Runtime-Isolation.

3. **Das exhaustive Unit-Matrix-Verfahren abschaffen, nicht die Tiefe.** Runde 1 darf weiter tief sein, aber Tiefe muss hypothesengetrieben werden. `concurrency` wird nur aktiviert, wenn Locks/Tasks/Channels/shared state berührt werden; `retry` nur bei Retry-/Queue-/Netzwerkpfaden; `null_missing` bei APIs/Serde/DB; Overflow bei Limits, Größen und Indizes. Damit bleibt das Wissen aus deinen sieben Input-Klassen erhalten, ohne jede Funktion siebenfach zu prüfen.

4. **#22 als endgültiges Folgerundenmodell festschreiben.** Keine automatische Rückkehr zum Vollreview aufgrund von Rundenanzahl. Eine neue `scope_epoch` entsteht nur bei einem semantischen Scope-Sprung. Bestehende Findings bleiben die einzige Review-Agenda des Fixers.

5. **Contract-Reasks auf lokales Repair umstellen.** Nie erneut Repositoryanalyse starten, nur weil JSON oder ein Pflichtfeld fehlerhaft ist. Ein kleiner schema-only Repair-Aufruf ist dafür ausreichend.

6. **Danach erst Modelle/Effort optimieren.** Der aktuelle Datensatz zeigt 90,2 % des gemessenen Verbrauchs auf Opus 5.5, aber dieser Wert ist mit 4.912 Reviewer-Sessions und riesigem Kontext vermischt.  
**Dateibefund:** `3-laufzeitdaten/usage-auswertung-14-tage.json:59–64`.  
Ein Wechsel auf Medium oder ein kleineres Modell vor dem Entfernen von Fan-out und Grundlast würde die falsche Variable optimieren. Anthropic selbst weist darauf hin, dass ein niedrigerer Effort Tokenverbrauch reduzieren kann; das sollte aber als zweite Optimierungsstufe A/B-getestet werden. citeturn476452search34

Die früher vorgeschlagene risk-based Gate-Stufe bleibt sinnvoll, ist aber jetzt **P2 statt P0**. Deine aktuelle Datenlage zeigt, dass selbst ein High-Risk-Review unnötig teuer wird, solange jeder Reviewer 60k Grundkontext mitbringt und der PR in 13, 30 oder 68 Sessions zerlegt werden kann. Erst nachdem dieser Multiplikator entfernt wurde, lohnt sich die feinere Frage, wann ein Reviewer ganz übersprungen werden darf.

## Was noch nicht belegt ist und wie die nächste Messung aussehen sollte

Die neue ZIP enthält noch **keine belastbare Post-#22-Messreihe**. Laut `usage-auswertung-14-tage.json` gingen #20–#22 am 18. November 2026 um 20:28 Uhr live; danach wurde das Wochenlimit erreicht. Deshalb wäre es falsch, aus den historischen 75 % Gate-Anteil direkt abzuleiten, dass die aktuelle Fassung weiterhin genauso teuer ist.  
**Dateibefund:** `3-laufzeitdaten/usage-auswertung-14-tage.json:150`.

Ebenso lässt sich aus den 37 % Low- und 30 % Input-Class-Findings nicht einfach „67 % Einsparung“ errechnen. Die Kategorien können sich überlappen, Finding-Zahl ist nicht proportional zu Tokenverbrauch, und Runde-1-Sucharbeit fällt auch dann an, wenn ein Finding später nicht blockiert. Diese Zahlen belegen **Noise**, nicht direkt Kostenreduktion.

Für die Zusatz-Evaluation würde ich deshalb keine neue große Benchmarkkampagne starten. Nimm etwa zehn repräsentative historische Branches aus dem Ledger, darunter drei kleine, drei mittlere, zwei große und zwei mit echten schweren Fehlern, und führe vier Shadow-Varianten auf exakt demselben SHA aus: heutiges Gate; heutiges Gate mit minimaler Reviewer-Runtime; minimale Runtime plus fester Zwei-Reviewer-Runde-1; und zusätzlich dieselbe Variante mit modularen statt exhaustiven Input-Klassen. Pro Variante reichen fünf Kennzahlen: bestätigte High-Findings, verpasste adjudizierte High-Findings, Reviewer-Sessions, Opus-Input-Äquivalent und `first_turn_context + max_context`.

Der wichtigste Abbruchtest ist nicht „findet die neue Variante weniger Findings?“, sondern **„verpasst sie einen bestätigten High-Defekt, den die alte Variante zuverlässig findet?“** OpenAIs veröffentlichte Reviewarbeit stützt genau diese Auswertung über High-Signal-Precision und kritischen Recall statt über rohe Finding-Zahl. citeturn965658view2

Die stärkste neue Schlussfolgerung lautet deshalb: **Deine letzten Änderungen #20–#22 gehen bereits in die richtige Richtung. Jetzt solltest du nicht noch mehr Regeln am Finding-Ende drehen, sondern die Kosten am Anfang des Systems entfernen.** Ein Reviewer sollte mit nahezu leerer Claude-Code-Runtime starten, ein Vollreview sollte nur wenige feste Agenten besitzen, und nach Runde 1 muss der Finding-Scope einfrieren. Wenn diese drei Punkte greifen, kann dein Grundprinzip „einmal tief, danach nur verifizieren“ tatsächlich funktionieren, ohne dass „tief“ weiterhin „Dutzende Opus-Sessions mit jeweils 60k Startkontext“ bedeutet.