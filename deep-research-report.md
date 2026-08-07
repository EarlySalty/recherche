# AI-Coding mit Coding Agents im August 2026: Das Real-World-Playbook

## Forschungsrahmen und Kernergebnis

**Stand dieser Recherche ist der 7. August 2026.** Für Aussagen über heutige Tools, Workflows und Praktiken wurden Veröffentlichungen ab dem **7. Februar 2026**, laufend gepflegte offizielle Dokumentationen sowie aktuelle Repositories verwendet. Ältere Engineering-Prinzipien wie Test-Driven Development, README-first, kleine Änderungen, Code Review, Least Privilege und klare Schnittstellen werden nur dort behandelt, wo aktuelle Praktiker oder aktuelle Herstellerdokumentationen sie erneut einsetzen.

Die wichtigste Erkenntnis lautet:

> **Die aktuelle Norm ist nicht „Vibe Coding“, nicht ein magischer Prompt und auch nicht möglichst viele parallele Agenten. Die robuste Norm ist begrenzte Delegation: Der Mensch definiert Absicht, Produktverhalten, Architekturgrenzen und Abnahmekriterien; der Agent recherchiert und implementiert; Tests, Browser, Compiler und Reviewer liefern überprüfbare Beweise.**

Anthropics Analyse von rund 400.000 Claude-Code-Sitzungen mit etwa 235.000 Personen zeigt genau diese Arbeitsteilung: Menschen trafen im Durchschnitt ungefähr 70 Prozent der Planungsentscheidungen, während Claude ungefähr 80 Prozent der Ausführungsentscheidungen übernahm. Vereinfacht: **Der Mensch entscheidet, was und warum gebaut wird; der Agent entscheidet zunehmend, wie es umgesetzt wird.** citeturn18search2turn16view6

Das wird durch aktuelle empirische Forschung gestützt. Eine Untersuchung von 7.156 Pull Requests aus fünf Coding-Agent-Systemen fand keinen Agenten, der über alle Aufgabentypen hinweg am besten war. Der Aufgabentyp beeinflusste die Annahme stärker als der Produktname: Dokumentationsänderungen wurden beispielsweise wesentlich häufiger akzeptiert als neue Features. Die praktische Konsequenz ist, dass **Task Design, Kontext, Tests und Review-Prozess wichtiger sind als die dauernde Suche nach dem einen besten Modell.** citeturn15view12turn14academia21

Eine weitere aktuelle Studie über 278.790 Review-Unterhaltungen fand, dass menschliche Reviewer zusätzliche Hinweise zu Verständnis, Tests und Wissenstransfer lieferten, AI-Review-Vorschläge seltener übernommen wurden und übernommene AI-Vorschläge im Mittel stärkere Zunahmen von Codeumfang und Komplexität verursachten. AI-Reviewer eignen sich damit gut als skalierbarer zusätzlicher Filter, ersetzen aber nicht die Verantwortung eines Menschen für Architektur und Wartbarkeit. citeturn15view11turn14academia23

Die aus den aktuellen Quellen ableitbare Real-World-Norm besteht aus sieben Elementen:

| Element | Praktische Bedeutung |
|---|---|
| **Intent vor Implementation** | Ziel, Nutzerverhalten, Grenzen und „Done when“ werden vor dem Code geklärt. |
| **Research vor Änderungen in unbekanntem Code** | Der Agent muss Datenfluss, Konventionen und existierende Abstraktionen verstehen. |
| **Kleine verifizierbare Schritte** | Ein Milestone endet mit einem Test, Build, Screenshot oder anderem Nachweis. |
| **Kontext wird kuratiert** | Nicht alles wird in einen Chat gekippt; Ergebnisse werden verdichtet und neue Sessions gestartet. |
| **Tests sind ausführbare Spezifikationen** | Besonders bei Bugs wird das falsche Verhalten zuerst reproduziert. |
| **Unabhängiges Review** | Implementierer und Reviewer erhalten unterschiedliche Rollen und möglichst getrennten Kontext. |
| **Begrenzte Rechte** | Sandbox, isolierte Worktrees und Freigaben schützen Repository, Secrets und Infrastruktur. |

Diese Elemente erscheinen in unterschiedlichen Kombinationen bei Theo, Dex Horthy, David Ondrej beziehungsweise Matt Pocock sowie in den aktuellen Empfehlungen von OpenAI, Anthropic, GitHub und Cursor. citeturn16view0turn16view1turn16view4turn16view6turn16view7turn17view4turn17view9

## Was Theo, Dex Horthy und David Ondrej tatsächlich tun

**Theo – t3.gg: weniger Workflow-Theater, mehr Gespräch, Beispiele und visuelles Feedback.**

Theos aktueller Workflow vom Mai 2026 ist eine bewusste Gegenposition zu überladenen Agent-Setups. Er sagt ausdrücklich, dass er fast keine Skills installiert habe und dass sein „grill me“-Ablauf im Wesentlichen nur gespeichertes Markdown sei, das er per Voice-to-Text einfügt. Sein Punkt ist nicht, dass Skills nutzlos sind, sondern dass Entwickler häufig ein Toolproblem konstruieren, obwohl ein klares Gespräch mit einem leistungsfähigen Modell ausreichen würde. citeturn18search6turn16view11

Seine Prompts konzentrieren sich stärker auf **gewünschtes Verhalten, Ziel und konkrete Beispiele** als auf vorab vermutete Dateipfade oder Implementierungsdetails. Theo lässt den Agenten häufig selbst herausfinden, welche Dateien betroffen sind, weil eine voreilige menschliche Dateiauswahl den Agenten auch auf die falsche Spur setzen kann. Für komplizierte Anforderungen gibt er lieber ein minimales, reales Beispiel mit konkreten Inputs und dem gewünschten Nutzerergebnis als eine lange abstrakte Erklärung. citeturn17view0

Ein besonders praktischer Teil seines Setups ist die Verwendung von Bildern. Theo schätzt, dass ein Drittel bis die Hälfte seiner Prompts Screenshots enthält: Fehlermeldungen, Logs, aktuelle UI-Zustände, Referenzdesigns oder annotierte Screenshots mit Pfeilen. Das reduziert die Übersetzung zwischen visueller Beobachtung und Textbeschreibung. Bei Frontend-Arbeit ist dies oft wirksamer als eine Seite CSS-Anweisungen, weil das Modell direkt sieht, welches Element falsch wirkt. citeturn17view0

Für komplizierte Pläne lässt Theo den Agenten gelegentlich eine **HTML-Darstellung des Plans** erzeugen. Der Vorteil ist nicht HTML an sich, sondern die Review-Oberfläche: Er kann den Plan leichter lesen, visuell beurteilen, kommentieren und per Screenshot markieren. Sobald ein gutes Beispiel im Repository liegt, nutzen spätere Agenten dieses als Referenz. Das ist ein wichtiges Muster: **Ein korrektes Artefakt im Repository steuert zukünftiges Verhalten oft besser als eine abstrakte Prompt-Regel.** citeturn16view13turn17view0

Seine `AGENTS.md` ist ebenfalls nicht primär eine riesige technische Betriebsanleitung. Er beschreibt sie eher als Brief an den Agenten: Was ist das Produkt, welche Philosophie verfolgt es, warum existiert es und welche Richtung soll das Modell bevorzugen? In seinem Beispiel enthält sie kaum Dateipfade oder starre technische Entscheidungen. Das steht nicht im Widerspruch zu operativen Regeln; es zeigt vielmehr, dass eine Agent-Anweisung zwei Ebenen haben kann: **Produktweltbild oben, konkrete wiederkehrende Regeln darunter.** citeturn16view12

Theo startet außerdem sehr viele neue Threads. Bei einem Projekt berichtete er von mehr als 100 Threads in fünf Tagen, überwiegend seriell auf `main`, nicht von 15 gleichzeitig laufenden Worktrees. Sein Grund ist die menschliche Kontextlast: Selbst wenn Agenten parallel arbeiten können, muss der Mensch noch verstehen, welche Variante was geändert hat, welche Annahmen gelten und welche Branches noch relevant sind. Worktrees verwendet er gezielt, wenn sich eine Aufgabe während der Planung als größer oder riskanter erweist. citeturn17view0turn17view4

Bei großen, sicherheitsrelevanten oder Hosting-nahen Änderungen holt er zusätzliche Reviewer oder Review-Bots hinzu. Er lässt den Implementierungsagenten Review-Hinweise abarbeiten und erneut prüfen, bis keine relevanten Findings mehr erscheinen. Gleichzeitig warnt er vor PR-Spam und veralteten Branches: Ein Agent kann vor einer Rebase erst untersuchen, ob die Änderung auf `main` inzwischen bereits anders umgesetzt wurde. citeturn17view4

**Dex Horthy: Context Engineering, Research–Plan–Implement und menschliche Hebelpunkte.**

Dex Horthys gegenwärtige Position ist stark durch eine gescheiterte „Code schreiben lassen, nicht lesen“-Phase geprägt. Sein Team ließ über Monate Code entstehen, den kein Mensch wirklich verstand. Nach einem Produktionsproblem dauerte es Tage, die falsch durch die Codebasis geführte Primary-Key-Logik zu finden, und anschließend mehrere Wochen, das Team wieder in die eigene Codebasis einzuarbeiten. Seine heutige Schlussfolgerung ist nicht „Menschen müssen jede AI-Zeile tippen“, sondern: **Eine Organisation darf nicht den mentalen Besitz ihrer Architektur verlieren.** citeturn17view9turn16view14

Sein wichtigstes technisches Konzept ist die **„dumb zone“** eines Kontextfensters. Ein Modell mit einer Million Token Kontext wird nicht automatisch intelligenter, wenn man es mit einer Million Token füllt. Dex beobachtet bei kleineren Modellen Probleme ungefähr um 100.000 Token und bei großen Kontextfenstern häufig zwischen 300.000 und 400.000 Token. Das sind Erfahrungswerte, keine universellen Naturkonstanten. Entscheidend ist das Signal: Wenn der Agent plötzlich unsinnige Dateien verändert, bekannte Fakten ignoriert, Entscheidungen wiederholt oder sich in Zustimmungsschleifen bewegt, ist nicht automatisch ein detaillierterer Prompt nötig – oft ist eine neue Session nötig. citeturn16view15

Dex’ aktueller Ablauf für komplexe Brownfield-Aufgaben besteht aus separaten Kontexten:

1. Eine Session untersucht die Codebasis und erzeugt ein Research-Dokument.
2. Eine weitere Session formuliert Produkt- oder Designentscheidungen.
3. Eine frische Session erstellt daraus einen ausführbaren Plan.
4. Der Mensch reviewed insbesondere Research, Architektur und Plan.
5. Erst anschließend setzt ein Implementierungsagent die Schritte um.

Der ältere HumanLayer-Text nennt dieses Muster **Research–Plan–Implement** und „frequent intentional compaction“. Dex hat diesen Ansatz in seinem aktuellen Gespräch im Juli 2026 erneut beschrieben. Das zentrale Prinzip ist nicht die exakte Anzahl der Dateien, sondern die Trennung von Exploration, Entscheidung und Ausführung. citeturn16view15turn15view10

Das HumanLayer-Beispiel zeigt auch, warum Plan-Review einen höheren Hebel als ausschließliches Code-Review besitzt: Eine falsche Codezeile verursacht einen lokalen Fehler; eine falsche Annahme im Research kann Hunderte nachfolgende Entscheidungen vergiften. In einem dokumentierten Versuch erzeugte die Variante mit vorherigem Research einen Plan, der den Bug an einer passenderen Stelle und mit codebasiskonformeren Tests löste als die Variante ohne Research. Dex betont allerdings ausdrücklich, dass der Prozess nicht magisch ist und bei tiefen Abhängigkeitsproblemen scheitern kann, wenn weder Agent noch Mensch die Domäne ausreichend verstehen. citeturn15view10

Für „Loop Engineering“ bevorzugt Dex langsame, begrenzte Loops. HumanLayer startete zunächst nachts einen Agenten, der genau eine Qualitätsverbesserung vornahm und einen Pull Request öffnete. Später wurden daraus vier Agenten und vier PRs pro Nacht. Entscheidend: **Ein Mensch liest sie weiterhin vor dem Merge.** Das ist wesentlich näher an realer Produktionsarbeit als ein endloser Agent, der ohne Budget, Scope oder Merge-Gate beliebig viele Änderungen erzeugt. citeturn17view6turn17view9

**David Ondrej und Matt Pocock: Harness Engineering und Skills als wiederverwendbare Arbeitsabläufe.**

David Ondrejs aktuelles Skills-Repository zeigt eine stärker systematisierte Richtung. Die Skills sind in Agent-Orchestrierung, Skill-Erstellung, Research/Web, strukturiertes Denken und Dokumentation sowie Betrieb und Setup gruppiert. Die relevante Idee ist nicht, möglichst viele Skills zu installieren, sondern **wiederkehrende, bewährte Verfahren als selektiv ladbare Module zu speichern**, statt sie bei jedem Task neu zu erklären oder permanent ins Kontextfenster zu laden. citeturn19search0

Sein aktueller Austausch mit Matt Pocock dreht sich weniger darum, welcher Modellname diese Woche führt, sondern um **Harness Engineering**: Welche Tools kann der Agent verwenden? Welche Artefakte existieren? Wie erhält er Feedback? Wie werden Tests ausgeführt? Wie werden Aufgaben zerlegt? Welche Informationen werden dauerhaft oder nur bei Bedarf geladen? Der praktische Multiplikator entsteht aus Domänenwissen, guten Tests, modularer Architektur, API-Zugriff, Wiederverwendung und überprüfbaren Schleifen – nicht aus einem einzelnen cleveren Prompt. citeturn19search4turn19search8turn14search0

David experimentiert auch mit unterschiedlichen Multi-Agent-Strukturen. Ein öffentliches Beispiel unterscheidet unzuverlässige parallele Subagenten ohne gemeinsamen Kontext, parallele Agenten mit gemeinsamem Log, sequenzielle Agenten mit vollem Kontext und sequenzielle Agenten mit Kompression zwischen den Schritten. Dass die als zuverlässiger bezeichneten Varianten sequenziell arbeiten beziehungsweise Kontext verdichten, passt zu Theos Skepsis gegenüber unkontrollierter Parallelisierung und Dex’ Phasenmodell. citeturn19search5

**Der scheinbare Widerspruch zwischen Theo und David ist produktiv:** Theo warnt vor Skill-Cargo-Cult; David zeigt, wann Skills sinnvoll werden. Daraus ergibt sich eine belastbare Regel:

> Ein einmaliger Satz gehört in den Prompt. Eine dauerhafte Repository-Regel gehört in `AGENTS.md`. Ein komplexer, wiederholter Ablauf mit eigenen Ressourcen, Scripts oder Checklisten gehört in einen Skill.

## Der belastbare Ablauf von der Idee bis zum Merge

Der folgende Workflow ist die Synthese der aktuellsten Practitioner-Setups, offiziellen Dokumentationen und empirischen Ergebnisse. Nicht jede kleine Änderung benötigt alle Phasen. Für ein Text- oder Einzeilen-Fix kann der Ablauf stark verkürzt werden. Für Features, Datenmigrationen, Authentifizierung, Billing, Infrastruktur oder große Refactorings sollten die Phasen hingegen explizit bleiben.

### Die Absicht zuerst stabilisieren

Der Startprompt sollte vier Dinge enthalten: **Goal, Context, Constraints und Done when**. Genau diese Struktur empfiehlt auch die aktuelle Codex-Dokumentation. Ein Agent kann eine Aufgabe nur zuverlässig abschließen, wenn das Ziel nicht ausschließlich als Tätigkeit formuliert ist – „baue einen Filter“ –, sondern als beobachtbares Verhalten – „Nutzer können mehrere Status auswählen, die Auswahl bleibt in der URL erhalten und ein Reload rekonstruiert denselben Zustand“. citeturn16view0

Ein brauchbares Ausgangsformat ist:

```text
Ziel
Nutzer sollen ...

Motivation
Das Problem heute ist ...

Beobachtbares Verhalten
- Wenn ..., dann ...
- Wenn ..., dann ...
- Bei Fehler ..., dann ...

Nicht-Ziele
- ...
- ...

Randbedingungen
- Bestehende öffentliche APIs nicht brechen.
- Keine neue Dependency ohne Begründung.
- Keine Änderungen außerhalb des betroffenen Flows.

Fertig, wenn
- Akzeptanztests A, B und C bestehen.
- Typecheck, Lint und Build erfolgreich sind.
- Das Verhalten im Browser verifiziert wurde.
- Der Diff auf Scope Creep geprüft wurde.
```

**Für unscharfe Features folgt ein Interview, nicht sofort Code.** OpenAI empfiehlt inzwischen ausdrücklich, den Agenten den Nutzer interviewen und Annahmen angreifen zu lassen. Matt Pococks aktuelles `grill-me`-Muster fragt jeweils eine Frage, gibt eine empfohlene Antwort und soll die Codebasis selbst untersuchen, wenn die Antwort dort gefunden werden kann. citeturn16view2turn15view9

Eine verbesserte Version für echte Produktarbeit lautet:

```text
Interviewe mich zu diesem Feature, bevor du planst oder Code änderst.

Regeln:
- Stelle genau eine Frage pro Nachricht.
- Gib zu jeder Frage deine empfohlene Antwort und deren Konsequenzen an.
- Prüfe zuerst, ob die Antwort aus Code, Tests, Issues oder Dokumentation
  hervorgeht.
- Frage besonders nach Nutzergruppen, Fehlerfällen, Datenmodell,
  Berechtigungen, Migration, Kompatibilität, Telemetrie und Rollback.
- Führe eine Liste offener und entschiedener Punkte.
- Beende das Interview erst, wenn du eine vollständige Spec mit
  Akzeptanzkriterien erzeugen kannst.
```

Der Mehrwert liegt nicht in der Formulierung „grill me“, sondern darin, dass das Modell **fehlende Entscheidungen sichtbar machen muss, statt sie still zu erraten**.

### README-, Blogpost- oder Handbuch-first

Das vom Nutzer genannte „Blogpost zuerst“-Verfahren ist eine aktuelle Wiederbelebung von README-driven beziehungsweise narrative-first development. Dabei wird das Feature so beschrieben, als wäre es bereits fertig:

- Für wen ist es?
- Welches Problem löst es?
- Wie sieht das konkrete Nutzungsbeispiel aus?
- Welche Einschränkungen gelten?
- Was geschieht bei Fehlern?
- Wie migrieren bestehende Nutzer?
- Welche Behauptungen würde der Text über das Produkt aufstellen?

Eine aktuelle Beschreibung von README-driven Development verweist darauf, dass das Verfahren bereits 2010 formuliert wurde, heute aber gerade mit Coding Agents wieder nützlich ist: Die Dokumentation zwingt den Entwickler, die öffentliche Oberfläche und das gewünschte Verhalten zu klären, bevor der Agent interne Strukturen erzeugt. Simon Willison beschrieb 2026 ebenfalls einen Ablauf, bei dem er zuerst eine detaillierte README manuell formulierte und anschließend den Agenten die Implementation daraus bauen ließ. citeturn13search3turn13search35

Der konkrete Agent-Trick besteht darin, aus dem vorgeschriebenen Artikel anschließend Tests abzuleiten:

```text
Lies diesen vorab geschriebenen Release-Artikel.

Erstelle noch keinen Code.

Extrahiere:
1. alle überprüfbaren Produktbehauptungen,
2. alle impliziten Annahmen,
3. widersprüchliche oder unklare Aussagen,
4. nötige Akzeptanztests,
5. nicht beschriebene Fehler- und Grenzfälle.

Markiere jede Behauptung, die mit dem aktuellen Repository nicht
umsetzbar oder mit bestehenden Verträgen inkompatibel wäre.
```

Das Verfahren ist besonders für neue Produktfunktionen, APIs, CLIs und Nutzerflows geeignet. Für einen klar reproduzierbaren Bug wäre ein vorgeschalteter Blogpost unnötig; dort ist ein failing test der bessere erste Vertrag.

### Die Codebasis untersuchen, ohne sie sofort zu verändern

Bei unbekannten oder großen Repositories sollte der erste Agent **read-only** arbeiten. Sein Output ist kein Code, sondern ein Research-Paket:

```text
Untersuche diese Aufgabe im aktuellen Repository. Ändere keine Dateien.

Dokumentiere:
- relevanten Request-, Daten- und Kontrollfluss,
- öffentliche und interne Schnittstellen,
- existierende ähnliche Implementierungen,
- betroffene Tests und Testkonventionen,
- Architekturregeln und Invarianten,
- potenzielle Seiteneffekte,
- offene Fragen und Unsicherheiten,
- Dateien, die wahrscheinlich geändert werden müssen, mit Begründung.

Belege wichtige Aussagen mit Dateipfad und Symbol beziehungsweise
Zeilenbereich. Trenne Beobachtungen von Hypothesen.
```

Die Trennung zwischen Beobachtung und Hypothese ist wesentlich. Sobald ein Agent eine Vermutung in einem Research-Dokument als Tatsache formuliert, behandeln spätere Agenten sie häufig als sicheren Kontext. Dex nennt fehlerhafte Informationen im Kontext gefährlicher als bloße Kontextmenge. citeturn16view15turn15view10

Research sollte anschließend von einem Menschen oder einem zweiten Agenten adversarial geprüft werden:

```text
Überprüfe RESEARCH.md gegen das Repository.

Suche gezielt nach:
- übersprungenen Aufrufern,
- alternativen Datenpfaden,
- versteckten Seiteneffekten,
- Berechtigungs- und Mandantengrenzen,
- Migrationen,
- Hintergrundjobs und Caches,
- Tests, die die Analyse widerlegen.

Ändere keinen Code. Erzeuge FINDINGS.md mit Schweregrad,
Belegen und Korrekturvorschlägen.
```

### Einen ausführbaren Plan erstellen

Ein guter Agentenplan ist keine Liste wie „Backend ändern, Frontend ändern, Tests ergänzen“. Jeder Schritt muss klein genug sein, dass er abgeschlossen und unabhängig überprüft werden kann.

OpenAIs aktueller Long-Horizon-Leitfaden empfiehlt Milestones mit eigenen Akzeptanzkriterien, Validierungsbefehlen, einer Stop-and-fix-Regel und Entscheidungsnotizen, damit der Agent bei Schwierigkeiten nicht zwischen Ansätzen oszilliert. citeturn16view4turn16view5

Ein Plan-Schritt sollte ungefähr so aussehen:

```text
Milestone: Reproduktions-Test für den Cache-Key

Änderungen:
- tests/cache/test_project_scope.py
- gegebenenfalls Test-Fixtures, keine Produktionsdateien

Erwarteter Zwischenzustand:
- Der neue Test schlägt auf dem aktuellen main fehl.
- Er schlägt wegen des falschen Mandanten-Schlüssels fehl,
  nicht wegen Setup-, Netzwerk- oder Fixture-Problemen.

Validierung:
pytest tests/cache/test_project_scope.py -q

Stop-Regel:
Wenn der Test bereits auf main besteht, Implementation stoppen und
Research korrigieren.
```

Der Mensch reviewed an dieser Stelle insbesondere:

- Ist das richtige Problem identifiziert?
- Wird die Änderung an der richtigen Abstraktionsgrenze vorgenommen?
- Sind Scope und Nicht-Ziele klar?
- Bleibt das Datenmodell konsistent?
- Decken die Validierungen das Nutzerverhalten oder nur Implementierungsdetails ab?
- Kann jeder Milestone zurückgerollt oder separat geprüft werden?

### In kleinen vertikalen Scheiben implementieren

Der Implementierungsagent sollte nicht „den gesamten Plan umsetzen und am Ende testen“. Stattdessen bearbeitet er einen Milestone, führt dessen Validierungen aus, aktualisiert den Status und geht erst dann weiter.

```text
Implementiere genau den nächsten unvollständigen Milestone in PLAN.md.

Regeln:
- Ändere nur Dateien, die für diesen Milestone erforderlich sind.
- Mache die kleinstmögliche konsistente Änderung.
- Ändere keine Erwartungen in bestehenden Tests, nur damit sie bestehen.
- Führe die angegebenen Validierungen aus.
- Bei einem Fehler: Ursache analysieren und reparieren, bevor du fortfährst.
- Aktualisiere PLAN.md mit Resultat, Belegen, Abweichungen und offenen Risiken.
- Committe erst, wenn der Milestone verifiziert ist.
```

Das entspricht OpenAIs aktuellem Long-Horizon-Muster: klare Spec, checkpoint-basierter Plan, Runbook, kontinuierliche Tests beziehungsweise Lint, Typecheck und Build sowie ein laufendes Audit-Log. citeturn16view4

### Unabhängig verifizieren und reviewen

Nach der Implementation sollte nicht derselbe Chat gefragt werden: „Ist jetzt alles gut?“ Der Agent hat eine starke Fortsetzungsneigung, seine bisherige Arbeit zu rechtfertigen. Besser ist eine frische Session ohne den vollständigen Implementierungsdialog:

```text
Du bist der unabhängige Reviewer dieser Änderung.

Lies:
- ursprüngliche Spec,
- PLAN.md,
- finalen Diff,
- relevante Tests.

Prüfe:
- funktionale Korrektheit,
- fehlende Grenzfälle,
- Regressionen,
- Architekturverletzungen,
- unnötige Komplexität,
- Security- und Berechtigungsprobleme,
- Fehlerbehandlung,
- Datenmigration und Rückwärtskompatibilität,
- ob Tests tatsächlich die Spec prüfen.

Vertraue weder Commit-Message noch Implementierungszusammenfassung.
Belege jedes Finding mit Code und einem reproduzierbaren Szenario.
Ändere zunächst nichts.
```

AI-Reviewer sind besonders gut als zusätzlicher breiter Scanner. Die aktuelle Review-Forschung zeigt aber, dass Menschen mehr Kontext-, Test- und Wissenshinweise beitragen und AI-Vorschläge häufig nicht unverändert übernommen werden. Der finale Merge-Entscheid bleibt daher bei einem verantwortlichen Entwickler. citeturn14academia23turn15view11

## Context Engineering ohne Cargo Cult

Context Engineering bedeutet nicht, möglichst viel Kontext bereitzustellen. Es bedeutet, **für den aktuellen Entscheidungsschritt die kleinste vollständige und korrekte Informationsmenge bereitzustellen**.

Ein brauchbares Modell besteht aus vier Schichten.

**Hot Context** wird immer geladen. Dazu gehören Produktabsicht, zentrale Invarianten, Testbefehle, Sicherheitsregeln und wenige dauerhafte Konventionen. In Codex ist dies typischerweise `AGENTS.md`, in Claude Code `CLAUDE.md`, in Cursor Rules beziehungsweise entsprechende Projektdateien. Die aktuelle OpenAI-Dokumentation empfiehlt eine kurze, genaue `AGENTS.md`, die Repository-Struktur, Start-, Build-, Test- und Lint-Befehle, Konventionen, Verbote und die Definition von „fertig“ beschreibt. Wiederkehrende Fehler sollen nach realer Beobachtung ergänzt werden; bei wachsender Größe sollen Detaildokumente verlinkt statt permanent eingebettet werden. citeturn16view1turn16view3

**Cold Context** liegt im Repository und wird nur bei Bedarf geladen: Architekturentscheidungen, API-Verträge, Datenmodell, Security-Handbuch, Migrationsregeln, UI-Designsystem oder Domain-Glossar. Ein Authentifizierungsagent braucht das Auth-Handbuch, ein CSS-Fix aber nicht.

**Task Context** enthält ausschließlich die konkrete Spec, Research-Ergebnisse, den Plan, relevante Fehlerausgaben und aktuelle Entscheidungen.

**Ephemeral Context** sind große Toolausgaben wie vollständige Logs, Browser-DOMs, JSON-Antworten oder Testberichte. Diese sollten gesucht, gefiltert oder extern gespeichert werden, statt vollständig in den Chat zu gelangen.

### Die sinnvolle Struktur einer AGENTS.md

Theo und OpenAI betonen unterschiedliche, aber kombinierbare Aspekte. Theo verwendet die Datei zur Produkt- und Denksteuerung; OpenAI betont operative Kommandos und wiederkehrende Regeln. Daraus ergibt sich diese Zweiteilung:

```markdown
# Product intent

Dieses Produkt hilft [Nutzergruppe] bei [Problem].
Prioritäten:
1. ...
2. ...
3. ...

Bevorzuge einfache, explizite Lösungen.
Schütze insbesondere [zentrale Invarianten].

# Repository workflow

## Commands
- Install:
- Dev:
- Targeted tests:
- Full tests:
- Typecheck:
- Lint:
- Build:

## Change rules
- Keine öffentliche API ohne explizite Freigabe ändern.
- Keine neue Dependency ohne Begründung.
- Keine Refactorings außerhalb des Task-Scopes.
- Bei Bugfixes zuerst reproduzierenden Test erstellen.
- Bestehende Test-Erwartungen nicht anpassen, um fehlerhaften Code
  nachträglich zu legitimieren.

## Done means
- Akzeptanzkriterien erfüllt.
- Targeted und relevante Gesamttests bestanden.
- Typecheck, Lint und Build bestanden.
- Diff auf Scope Creep geprüft.
- Risiken, Migrationen und nicht gelöste Punkte dokumentiert.

## Escalation
Stoppe und frage nach beziehungsweise melde Blocker bei:
- widersprüchlicher Spec,
- Datenverlust- oder Migrationsrisiko,
- Änderungen an Auth, Billing, Secrets oder Produktionsinfrastruktur,
- zwei fehlgeschlagenen Reparaturzyklen mit derselben Ursache.
```

Die Datei sollte nicht vorsorglich mit Hunderten Stilregeln gefüllt werden. Die bessere Heuristik lautet: **Wenn derselbe relevante Fehler zweimal auftritt, Retrospektive durchführen und daraus eine knappe, überprüfbare Regel ableiten.** Diese Heuristik wird inzwischen auch offiziell von OpenAI empfohlen. citeturn16view3

### Fresh Context und Intentional Compaction

Ein häufiger Fehler ist, einen einzigen Chat über Research, Produktdiskussion, Implementation, Debugging und Review hinweg weiterzuführen. Der Chat enthält dann verworfene Ansätze, widersprüchliche Anweisungen, lange Logs und alte Fehlerannahmen.

Dex’ frequent intentional compaction löst dies, indem der aktuelle Zustand in ein strukturiertes Dokument geschrieben und anschließend eine neue Session gestartet wird. Das Dokument sollte mindestens Ziel, bestätigte Erkenntnisse, verworfene Ansätze, bisherige Änderungen, Testergebnisse, aktuelle Blocker und nächsten Schritt enthalten. citeturn16view15turn15view10

```text
Erzeuge HANDOFF.md für eine frische Agent-Session.

Enthalten sein müssen:
- unverändertes Endziel,
- bestätigte Fakten mit Belegen,
- getroffene Entscheidungen und Gründe,
- verworfene Ansätze und warum sie scheiterten,
- bereits geänderte Dateien,
- ausgeführte Tests und exakte Ergebnisse,
- aktueller Fehler,
- nächster kleinster Schritt,
- Unsicherheiten, die nicht als Fakten behandelt werden dürfen.

Füge keine komplette Chat-Zusammenfassung und keine irrelevanten Logs ein.
```

Ein Neustart ist besonders sinnvoll, wenn der Agent:

- dieselbe Lösung mehrfach wiederholt,
- nach Korrekturen reflexartig zustimmt, aber sein Verhalten nicht ändert,
- bereits verworfene Annahmen erneut verwendet,
- unerwartet unbeteiligte Dateien verändert,
- Ziele und Implementierungsdetails vermischt,
- große Teile des bisherigen Kontextes falsch zusammenfasst.

Dex bezeichnet Zustimmungssätze wie „You’re completely right“ nach wiederholten Korrekturen als Warnsignal für eine vergiftete Trajektorie. Dies ist keine mathematische Diagnose, aber eine nützliche operative Heuristik: **Nicht weiter in einen schlechten Gesprächsverlauf investieren; komprimieren und neu starten.** citeturn16view15turn17view6

### Subagenten als Kontextfilter

Subagenten sollten nicht primär als Rollenspielcharaktere wie „Senior Backend Wizard“ eingesetzt werden. Ihr sinnvollster Zweck ist **Kontextisolation**:

- Ein Subagent durchsucht 50 Dateien und liefert fünf relevante Erkenntnisse.
- Ein Subagent analysiert nur Datenmodell und Migration.
- Ein Subagent untersucht Tests und bestehende Konventionen.
- Ein Subagent wertet einen langen Build- oder CI-Log aus.
- Ein Subagent prüft einen Diff auf Security-Probleme.

Der Parent-Agent erhält strukturierte Ergebnisse statt sämtlicher Suchoperationen und Rohdaten. HumanLayer beschreibt genau diese Verwendung: frischer Kontext für Suche und Zusammenfassung, damit der Hauptagent nicht mit `grep`, Dateilesevorgängen und Logs überladen wird. citeturn15view10

Parallelisierung sollte nur dort erfolgen, wo die Untersuchungsbereiche tatsächlich unabhängig sind. Zwei Agenten, die gleichzeitig dasselbe Datenmodell verändern, erzeugen mehr Integrationsarbeit als Geschwindigkeit. Davids experimentelles Repository zeigt ebenfalls, dass parallele Agenten ohne passenden gemeinsamen Kontext unzuverlässiger sind als sequenzielle oder komprimierende Abläufe. citeturn19search5

### Context Mode

Das vom Nutzer erwähnte GitHub-Projekt **Context Mode** adressiert genau das Problem ungefilterter Toolausgaben. Das Projekt leitet Tool-Ergebnisse über MCP und Hooks um, speichert Inhalte lokal, indiziert sie unter anderem über SQLite FTS5 und gibt dem Agenten nur Zusammenfassungen oder relevante Suchtreffer zurück. Dadurch sollen beispielsweise große Browser-Snapshots, `curl`-Antworten oder Logs nicht vollständig in das Kontextfenster gelangen. citeturn15view6

Das Repository wirbt mit Einsparungen von ungefähr 98 Prozent bei Plattformen mit Hooks und ungefähr 60 Prozent bei ausschließlich anweisungsbasierter Weiterleitung. Diese Zahlen sind **projektseitige Messwerte und keine unabhängige Garantie**. Besonders wertvoll ist jedoch die qualitative Erkenntnis: Eine Regel wie „Bitte verwende Context Mode“ wird vom Modell nicht zuverlässig bei jedem Toolaufruf eingehalten. Hooks oder technisch erzwungenes Routing sind wesentlich robuster als bloße Prompt-Anweisungen. citeturn16view8turn16view9

Context Mode ist sinnvoll, wenn:

- Browser-, Test- oder Build-Tools sehr große Outputs erzeugen,
- lange Sessions durch Logmengen unbrauchbar werden,
- wiederholte Suche in bereits gelesenen Dokumenten nötig ist,
- lokale Speicherung der Toolausgaben mit den Sicherheitsanforderungen vereinbar ist.

Es ist weniger wichtig bei kleinen Repositories, kurzen Tasks oder Agenten, deren Harness bereits Toolausgaben begrenzt. Vor einer Teamstandardisierung sollten reale Aufgaben mit und ohne das Plugin verglichen werden: Erfolgsquote, benötigte Korrekturen, Laufzeit, Tokenverbrauch und Zahl übersehener Details.

### Graphify

**Graphify** baut aus Code, Dokumenten, Papers und Diagrammen einen abfragbaren Knowledge Graph. Die Idee ist, nicht vollständige Dateien in den Agentenkontext zu laden, sondern einen relevanten Subgraphen aus Symbolen, Beziehungen, Datenflüssen und Dokumentenverweisen abzurufen. Das Projekt nennt Unterstützung für mehrere Coding-Agent-Umgebungen und kombiniert unter anderem Parser beziehungsweise Tree-sitter-basierte Analyse mit Graphabfragen. citeturn15view7

Das ist besonders interessant für:

- große polyglotte Monorepos,
- Abhängigkeiten über mehrere Services,
- Systeme mit umfangreicher Architektur- und Domain-Dokumentation,
- Impact-Analysen wie „Welche Consumer hängen an diesem Event?“,
- Codebasen, in denen Textsuche Beziehungen nur unzureichend sichtbar macht.

Graphify ist im August 2026 jedoch eher **experimentelle Infrastruktur als allgemeine Norm**. Ein Knowledge Graph kann veraltete oder unvollständige Beziehungen enthalten; generierte Kanten müssen weiterhin gegen den tatsächlichen Code geprüft werden. Der richtige Einsatz ist daher zunächst read-only: Research und Impact-Analyse unterstützen, aber keine Architekturentscheidung allein auf Graphdaten stützen.

Die robuste Reihenfolge lautet:

1. Normale symbolische Code-Suche und Tests verwenden.
2. Bei großen oder schwer navigierbaren Systemen Graph-Retrieval pilotieren.
3. Treffer stets mit konkreten Dateien, Symbolen und Laufzeitbelegen rückprüfen.
4. Gegen eine Baseline messen, ob weniger Kontext tatsächlich zu besseren Ergebnissen führt.

## Tests, Reviews und Techniken zur Bug-Reduktion

### TDD wird durch Coding Agents wertvoller, nicht überflüssig

Der Kern von TDD – **Red, Green, Refactor** – ist für Coding Agents besonders nützlich, weil ein Test aus einer sprachlichen Behauptung eine maschinell prüfbare Bedingung macht.

Bei einem Bugfix sollte der Ablauf lauten:

1. Das fehlerhafte Verhalten in einem möglichst kleinen Test reproduzieren.
2. Sicherstellen, dass der Test auf dem unveränderten Stand fehlschlägt.
3. Sicherstellen, dass er **aus dem erwarteten Grund** fehlschlägt.
4. Die kleinste Produktionsänderung implementieren.
5. Den Reproduktionstest bestehen lassen.
6. Relevante Nachbartests und anschließend die breitere Suite ausführen.
7. Den Diff auf unerwünschte Nebenänderungen prüfen.

Der Schritt „aus dem erwarteten Grund fehlschlagen“ ist entscheidend. Ein roter Test wegen einer falschen Fixture, eines fehlenden Imports oder einer nicht gestarteten Datenbank beweist den Produktbug nicht.

Ein guter Prompt dafür ist:

```text
Reproduziere den beschriebenen Bug zuerst in einem automatisierten Test.

Noch keine Produktionsdateien ändern.

Der Test muss:
- auf dem aktuellen main fehlschlagen,
- das beobachtbare falsche Verhalten prüfen,
- an einer öffentlichen oder stabilen Systemgrenze testen,
- wegen des beschriebenen Bugs fehlschlagen,
- ohne unnötige Implementation-Details auskommen.

Führe den Test aus und zeige:
- den exakten Befehl,
- die relevante Fehlermeldung,
- warum dieser Fehler den Bug reproduziert.

Stoppe danach zur Prüfung.
```

OpenAI empfiehlt heute ausdrücklich, Tests zu erzeugen oder anzupassen, die relevanten Suites auszuführen, Lint, Formatierung und Typecheck zu prüfen und den finalen Diff auf Regressionen zu reviewen. Der aktuelle Long-Horizon-Leitfaden verlangt außerdem eine Stop-and-fix-Regel: Ein Agent soll bei fehlgeschlagener Validierung nicht einfach zum nächsten Milestone wechseln. citeturn16view1turn16view4

### Tests und Implementation organisatorisch trennen

Ein Coding Agent kann Tests nach seiner eigenen Implementation so schreiben, dass sie genau seine Lösung bestätigen. Das reduziert den Wert des Tests als unabhängigen Vertrag.

Robustere Varianten sind:

- Der Mensch oder ein separater Test-Agent formuliert die Akzeptanztests.
- Der Implementierungsagent darf diese Tests zunächst nicht verändern.
- Änderungen an Test-Erwartungen erfordern eine explizite Begründung.
- Der Reviewer prüft, ob der Test auch mit einer absichtlich falschen Implementation fehlschlagen würde.
- Bei kritischen Invarianten werden zusätzliche Contract-, Integration- oder End-to-End-Tests verwendet.

GitHub zeigt in seiner aktuellen Dokumentation ein spezialisiertes Test-Agent-Muster, bei dem der Agent sich auf deterministische Unit-, Integrations- und End-to-End-Tests konzentriert und Produktionscode nicht eigenmächtig ändert. Das illustriert die sinnvolle Aufgabentrennung zwischen Spezifikation beziehungsweise Test und Implementation. citeturn8search14

Für neue Features ist striktes Test-first nicht in jedem Detail sinnvoll. Bei explorativer UI kann zunächst ein Prototyp oder Screenshot-Review nötig sein. Aber auch dort sollten vor der finalen Implementation beobachtbare Abnahmekriterien existieren:

- Welche Elemente sind sichtbar?
- Welche Tastatur- und Screenreader-Flows funktionieren?
- Welche URL oder welcher Zustand entsteht?
- Wie verhält sich die UI bei Laden, Fehler und leerem Ergebnis?
- Welche Browser- oder Viewport-Größen sind relevant?

### Den Agenten mit echten Feedbackkanälen ausstatten

Ein Agent kann nur Fehler korrigieren, die er beobachten kann. Ein Repository ohne ausführbare Validierung zwingt ihn zum Raten.

Ein produktionsfähiges Harness sollte nach Aufgabenart mindestens einige dieser Kanäle anbieten:

| Aufgabenart | Notwendige Rückmeldung |
|---|---|
| Bibliothek oder Backend | Unit- und Integrationstests, Typecheck, Lint, Logs |
| API | Contract-Tests, Beispielrequests, Schema-Prüfung, Auth-Test |
| Frontend | Browserzugriff, Screenshots, Konsole, Netzwerk-Requests, E2E |
| Datenbank | Migrationsprüfung, Constraints, Testdaten, Rollback-Test |
| Infrastruktur | Plan/Diff, Policy-Checks, isolierte Testumgebung |
| Performance | Benchmark oder Profiling mit festgelegtem Ausgangswert |
| Security | Threat Model, statische Checks, Berechtigungstests, menschliches Review |

Theo nutzt Screenshots und visuelle Annotationen besonders stark; OpenAIs aktuelle Empfehlungen verlangen explizite Tests und Review; die empirische Forschung zu agentischem Review zeigt, dass menschliche Kontextprüfung weiterhin nötig bleibt. citeturn17view0turn16view1turn14academia23

### Invarianten statt nur Beispieltests

Beispieltests prüfen einzelne Fälle. Invarianten beschreiben, was niemals verletzt werden darf:

- Ein Nutzer darf nie Daten eines anderen Mandanten sehen.
- Eine wiederholte Webhook-Zustellung darf keine zweite Zahlung auslösen.
- Eine Migration darf bestehende IDs nicht neu zuordnen.
- Ein fehlgeschlagener Vorgang darf keinen halbfertigen Zustand hinterlassen.
- Eine öffentliche API darf bei neuen optionalen Feldern alte Clients nicht brechen.

Coding Agents profitieren stark von solchen Regeln, weil sie über viele Implementierungsvarianten hinweg gelten. Sie gehören je nach Reichweite in Spec, Tests, Architekturvertrag oder `AGENTS.md`.

Ein Review-Prompt für Invarianten lautet:

```text
Ignoriere zunächst, ob die vorhandenen Tests bestehen.

Leite aus Spec, Datenmodell und bestehendem Verhalten die wichtigsten
Systeminvarianten ab. Prüfe anschließend den Diff gegen jede Invariante.

Suche insbesondere nach:
- Mandanten- und Berechtigungslecks,
- nicht atomaren Zustandsänderungen,
- fehlender Idempotenz,
- Race Conditions,
- falschen Cache-Keys,
- Zeit- und Zeitzonenfehlern,
- fehlerhafter Fehlerbehandlung,
- inkompatiblen Schema- oder API-Änderungen.
```

### Kleine Diffs und kleine Pull Requests

Die Agenten können heute schneller Code produzieren, als Menschen ihn sinnvoll reviewen können. Dadurch wird Diff-Größe zu einem Qualitäts- und nicht nur Komfortproblem.

Kleine PRs reduzieren:

- Zahl gleichzeitig zu verstehender Entscheidungen,
- Wahrscheinlichkeit versteckter Scope-Erweiterung,
- Merge-Konflikte,
- Kosten eines Rollbacks,
- Zeit zwischen Änderung und Feedback,
- Risiko, dass ein Review-Bot relevante Findings zwischen Stilhinweisen verliert.

Dex beschreibt 2.000-Zeilen-PRs als kaum nachhaltig lesbar und verlagert menschliche Aufmerksamkeit deshalb stärker auf Research und Pläne. Theo warnt gleichzeitig davor, dass sehr einfache Agent-PR-Erstellung zu PR-Bloat und veralteten Branches führen kann. citeturn15view10turn17view4

Eine sinnvolle Regel ist nicht eine absolut feste Zeilenzahl, sondern: **Ein PR sollte genau eine zusammenhängende Produkt- oder Architekturentscheidung enthalten.** Wenn die Beschreibung mehrere „und außerdem“ enthält, sollte er wahrscheinlich geteilt werden.

### Reviewer nicht blind reparieren lassen

Ein Review-Agent sollte zunächst Findings erzeugen, nicht sofort Code verändern. Sonst besteht die Gefahr, dass er einen korrekten Trade-off „repariert“, Tests umschreibt oder neue Probleme erzeugt.

Der Ablauf sollte sein:

1. Read-only Findings.
2. Findings nach Schweregrad, Reproduzierbarkeit und Relevanz sortieren.
3. Mensch oder Lead-Agent entscheidet, welche Findings gültig sind.
4. Implementierungsagent behebt jeweils kleine Gruppen.
5. Tests und Review erneut ausführen.
6. Loop mit klarer Exit-Bedingung beenden.

Theo verwendet Review-CLI-Loops, bis keine relevanten Findings mehr vorliegen. Der entscheidende Zusatz für Team- und Produktionsarbeit lautet: Der Loop benötigt ein Turn- beziehungsweise Kostenlimit und muss bei wiederholten oder widersprüchlichen Findings eskalieren. citeturn17view4

```text
Bearbeite nur bestätigte Findings aus REVIEW.md.

Nach jedem Finding:
- implementiere die kleinste Korrektur,
- führe den Reproduktionstest aus,
- führe relevante Regressionstests aus,
- markiere das Finding mit Belegen als erledigt.

Stoppe, wenn:
- dasselbe Finding nach zwei Versuchen wieder erscheint,
- die Behebung die Spec verändern würde,
- Tests widersprüchliche Erwartungen zeigen,
- ein Fix eine neue Architekturentscheidung benötigt.
```

### Rechte, Sandbox und Secrets

Coding Agents sollten im Normalbetrieb nicht uneingeschränkten Zugriff auf Host, Produktionsdaten, Secrets und Netzwerk erhalten.

Anthropic berichtet, dass Benutzer 93 Prozent der manuellen Permission-Prompts akzeptierten. Das zeigt das Problem der Freigabeermüdung: Ein Dialogfeld ist kein starker Schutz, wenn es routinemäßig bestätigt wird. Anthropic empfiehlt daher isolierte Sandboxes oder risikobasierte Automatisierung; das vollständige Überspringen aller Berechtigungen wird als unsicher für die meisten Situationen bezeichnet. citeturn16view7

Auch OpenAI empfiehlt, mit engen Standardberechtigungen und Sandbox zu beginnen und Rechte nur für vertrauenswürdige Repositories oder spezifische Abläufe zu erweitern. citeturn16view1

Besonders schützenswerte Aktionen benötigen explizite Gates:

- Lesen oder Schreiben von Produktionsdaten,
- Deployment,
- Datenmigration,
- Löschen oder Überschreiben von Ressourcen,
- Änderungen an Authentifizierung und Berechtigungen,
- Zugriff auf Secrets,
- Installation unbekannter Pakete,
- Netzwerkzugriff zu nicht freigegebenen Hosts,
- Merge in geschützte Branches.

## Ein minimaler, tatsächlich brauchbarer Setup-Blueprint

Ein gutes Setup beginnt nicht mit 40 Plugins. Es beginnt mit einem Agenten, einem verifizierbaren Repository und einem klaren Arbeitsvertrag.

### Baseline-Stack

**Agent-Harness:** Ein aktueller Coding Agent mit Dateizugriff, Shell, Git-Diff, Tests und möglichst Browser beziehungsweise Bildinput. Der konkrete Anbieter ist sekundär; aktuelle Daten zeigen, dass kein Agent alle Aufgabentypen dominiert. citeturn14academia21

**Versionskontrolle:** Jeder Task beginnt auf sauberem Git-Status. Größere oder parallele Tasks erhalten eigenen Branch und Worktree. Ein Agent darf nicht unbemerkt bestehende lokale Änderungen überschreiben.

**Repository-Anweisungen:** Eine kurze `AGENTS.md` beziehungsweise äquivalente Datei mit Produktabsicht, Kommandos, Invarianten, Scope-Regeln und Definition of Done. citeturn16view1

**Verifikation:** Ein einzelner dokumentierter Befehl sollte die wichtigsten Checks ausführen, beispielsweise:

```bash
./scripts/verify-change.sh
```

Dieser kann intern Typecheck, Lint, relevante Tests und Build ausführen. Der Agent muss nicht jedes Mal raten, welche Kombination gültig ist.

**Review:** Frische Agent-Session oder Review-Bot plus menschliche Freigabe. Bei sicherheits-, daten- oder architekturrelevanten Änderungen ist ein verantwortlicher menschlicher Reviewer Pflicht. citeturn14academia23

**Sandbox:** Lokale oder ephemeral Umgebung ohne Produktions-Secrets und mit begrenztem Netzwerkzugriff. citeturn16view7turn16view1

### Eine kleine sinnvolle Skill-Bibliothek

Die folgende Sammlung ist kein verpflichtender Produktstandard, sondern eine aus den untersuchten Workflows abgeleitete Minimalbibliothek. Jeder Skill sollte nur bei Bedarf geladen werden.

| Skill | Aufgabe |
|---|---|
| `grill-me` | Unscharfe Anforderungen durch sequenzielles Interview klären |
| `research-codebase` | Read-only Datenfluss, Konventionen und Risiken untersuchen |
| `write-plan` | Milestones mit Validierung und Stop-Regeln erzeugen |
| `reproduce-bug` | Erst einen korrekt fehlschlagenden Test erstellen |
| `review-diff` | Spec-basiertes, read-only Review durchführen |
| `verify-ui` | Browser, Screenshots, Konsole und Netzwerk prüfen |
| `security-review` | Berechtigungen, Datenfluss und Threat Model prüfen |
| `handoff` | Kontext in ein belastbares Übergabedokument komprimieren |
| `retro-rules` | Wiederholte Fehler analysieren und gezielt Regeln aktualisieren |

Das `grill-me`-Muster ist real und sehr klein: eine Frage nach der anderen, empfohlene Antworten, Codebasis selbst untersuchen, wenn möglich. Theo zeigt gleichzeitig, dass dafür nicht zwingend ein Plugin nötig ist; bei gelegentlicher Nutzung genügt ein Text-Snippet. citeturn15view9turn16view11

Davids Skills-Repository demonstriert den anderen Fall: Orchestrierung, Research, Dokumentation und Betriebsabläufe, die regelmäßig verwendet werden, profitieren von versionierten, wiederverwendbaren Skills. citeturn19search0

### Skills sollten ausführbare Teile enthalten

Ein guter Coding-Skill besteht nicht nur aus zehn Absätzen Prompttext. Er kann enthalten:

- ein knappes Trigger-Kriterium,
- klare Inputs und Outputs,
- eine Checkliste,
- Referenzbeispiele,
- deterministische Scripts,
- erlaubte und verbotene Aktionen,
- Stop- und Eskalationsbedingungen,
- eine eigene Evaluation.

Beispielsweise sollte ein `verify-ui`-Skill nicht nur sagen „prüfe die UI“, sondern festlegen:

```text
1. Starte die Anwendung mit dem dokumentierten Command.
2. Öffne die Zielroute.
3. Prüfe Browser-Konsole und fehlgeschlagene Requests.
4. Teste Loading, Success, Empty und Error State.
5. Teste Tastaturnavigation.
6. Erzeuge Screenshots für Desktop und Mobile.
7. Vergleiche sichtbares Verhalten mit den Akzeptanzkriterien.
8. Melde Abweichungen, bevor du weitere Änderungen machst.
```

Der Skill wird dadurch zu einem kleinen Harness und nicht zu einer Persona.

### Task-Auswahl nach Risikoklasse

Nicht jede Aufgabe sollte gleich autonom laufen.

| Klasse | Beispiele | Geeigneter Modus |
|---|---|---|
| **Niedrig** | Docs, Typfehler, lokale Tests, kleine interne Refactorings | Agent kann implementieren, testen und PR öffnen |
| **Mittel** | begrenztes Feature, UI-Flow, neue interne API | Spec, Plan, Tests, unabhängiges Review |
| **Hoch** | Auth, Billing, Mandantentrennung, Migration, Infrastruktur | menschliche Architekturentscheidung, kleine Milestones, Sandbox, verpflichtendes Expertenreview |
| **Kritisch** | Produktionsdaten, Secrets, irreversible Migration, Sicherheitskontrollen | keine unbeaufsichtigte Ausführung; explizite Freigaben und Rollback-Probe |

Die aktuelle Usage-Forschung zeigt zwar wachsende Agentenautonomie bei der Ausführung, aber weiterhin überwiegend menschliche Kontrolle über Planungsentscheidungen. Die Sicherheitsdokumentationen empfehlen außerdem enge Standardrechte. citeturn16view6turn16view7turn16view1

### Ein kompletter täglicher Ablauf

Für ein normales Feature kann der reale Arbeitstag so aussehen:

**Am Anfang:** Der Entwickler schreibt Ziel, Nutzerbeispiel und Nicht-Ziele oder erstellt einen README-/Blogpost-Entwurf.

**Danach:** Ein Grill-Interview deckt offene Produkt-, Daten- und Berechtigungsfragen auf.

**Research:** Ein read-only Agent untersucht das Repository und erzeugt `RESEARCH.md`.

**Planung:** Eine frische Session erstellt `PLAN.md` mit kleinen Milestones, Tests und Stop-Regeln.

**Review:** Der Entwickler liest Research und Plan. Bei großen Aufgaben wird hier mehr Zeit investiert als später in einzelne Codezeilen.

**Implementation:** Der Agent setzt einen Milestone nach dem anderen um und dokumentiert Validierungen.

**Visuelle Prüfung:** Frontend-Änderungen werden im Browser mit Screenshots, Konsole und Netzwerk geprüft.

**Review:** Ein frischer Agent oder Review-Bot untersucht den Diff read-only. Der Mensch bewertet die Findings.

**Merge:** Erst nach erfolgreichen Checks, verständlichem Diff, dokumentierten Risiken und menschlicher Freigabe.

**Retrospektive:** Tritt derselbe Agentenfehler wiederholt auf, wird eine kleine Regel, ein Test, ein Script oder ein Skill ergänzt – nicht automatisch ein größerer globaler Prompt.

Dieser Ablauf kombiniert die gegenwärtigen Herstellerempfehlungen mit den aktuellen Praktiken von Theo und Dex: klares Ziel, Interview, Research, Plan, kleine verifizierbare Schritte, frische Kontexte, visuelles Feedback, Review und bewusste Vereinfachung. citeturn16view0turn16view2turn16view4turn17view0turn17view4turn16view15

## Was heute Norm ist, was situativ hilft und was noch Hype bleibt

### Praktisch etablierte Norm

**Klare Akzeptanzkriterien vor der Implementation** sind inzwischen keine Spezialtechnik mehr. Sie tauchen in den aktuellen OpenAI-Leitfäden, in Dex’ Research–Plan–Implement-Verfahren und in realen Practitioner-Workflows auf. citeturn16view0turn16view4turn16view15

**Kurze Repository-Anweisungen statt gigantischer Prompt-Handbücher** sind ebenfalls weitgehend etabliert. Dauerhafte Regeln sollen korrekt, konkret und hierarchisch sein; task-spezifisches Wissen wird verlinkt oder als Skill geladen. Theo warnt vor überladenen Dateien, während OpenAI ausdrücklich kurze, praktische `AGENTS.md`-Dateien empfiehlt. citeturn16view12turn16view1

**Tests, Lint, Typecheck, Build und Diff-Review als Completion Gate** sind heute die zentrale Zuverlässigkeitsschicht. Ein Agent, der „fertig“ sagt, liefert noch keinen Beweis; die Toolausgaben liefern den Beweis. citeturn16view1turn16view4

**Frische Sessions nach Phasen oder bei schlechter Trajektorie** sind eine verbreitete Real-World-Technik. Theo startet viele kurze Threads; Dex komprimiert und startet neu, bevor das Kontextfenster qualitativ kippt. citeturn17view0turn16view15

**Menschen behalten Produkt-, Architektur- und Merge-Verantwortung.** Das zeigen sowohl die Nutzungsdaten als auch aktuelle Review-Forschung. citeturn16view6turn14academia23

**Sandbox und Least Privilege** sind für professionelle Umgebungen Norm, insbesondere bei Agenten mit Shell-, Netzwerk- und Schreibzugriff. citeturn16view7turn16view1

### Situativ sehr wirksam

**README- oder Blogpost-first** ist hervorragend für neue Features, APIs, CLIs und Nutzerflows, aber unnötig für kleine, klar reproduzierbare Bugs. Es zwingt zur Produktklarheit und erzeugt eine Quelle für Akzeptanztests. citeturn13search3turn13search35

**HTML-Pläne und annotierte Screenshots** sind besonders bei visuellen Features, langen Plänen und Remote-Arbeit nützlich. Sie sind kein allgemeines Agentenprinzip, aber ein sehr konkreter Theo-Trick zur Verringerung von Missverständnissen. citeturn16view13turn17view0

**Review-Bot-Loops** sind für große oder riskante Diffs nützlich, benötigen jedoch validierte Findings, Limits und menschliche Endentscheidung. citeturn17view4turn14academia23

**Parallele Agenten und Worktrees** lohnen sich für unabhängige Tasks, alternative Research-Ansätze oder langsame Hintergrundarbeit. Sie lohnen sich nicht, wenn der Mensch anschließend 15 überlappende Branches rekonstruieren muss. Theo bevorzugt überwiegend serielle Threads; Dex nutzt begrenzte nächtliche Parallelität mit menschlichem Merge-Gate. citeturn17view0turn17view6

**Context Mode** kann bei großen Tooloutputs erhebliche Einsparungen bringen. Die projektseitigen Prozentangaben müssen jedoch im eigenen Harness verifiziert werden; technisch erzwungene Hooks sind glaubwürdiger als reine Prompt-Compliance. citeturn16view8turn16view9

### Noch keine allgemeine Norm

**Knowledge Graphs wie Graphify** sind vielversprechend für sehr große und heterogene Systeme, aber noch keine Standardvoraussetzung für gutes AI-Coding. In vielen Repositories reichen symbolische Suche, gezielte Research-Agenten, Tests und gute Dokumentation. citeturn15view7

**Dutzende spezialisierte Personas oder Agentenrollen** lösen nicht automatisch Kontext- und Qualitätsprobleme. Subagenten sind am wertvollsten als Kontextisolatoren oder unabhängige Prüfer, nicht als Theaterstück mit „Architekt“, „Guru“ und „Ninja“. citeturn15view10turn19search5

**Unbegrenzte autonome Loops** sind kein Qualitätsverfahren. Ohne Tests, Stop-Regeln, Scope, Kostenbudget und menschliches Merge-Gate können sie denselben falschen Ansatz nur schneller wiederholen. Der aktuelle OpenAI-Ansatz verlangt Checkpoints und Stop-and-fix-Regeln; Dex’ produktive Loops erzeugen begrenzte PRs, die Menschen vor dem Merge lesen. citeturn16view4turn17view6

**„Den ganzen Code nicht mehr lesen“** ist keine allgemein belastbare Norm. Es gibt Teams, die menschliche Aufmerksamkeit stärker auf Specs, Tests, Pläne und kritische Diffs verlagern. Das ist aber etwas anderes, als den mentalen Besitz des Systems aufzugeben. Dex’ gescheiterter Versuch und die aktuelle Review-Forschung sprechen klar gegen vollständig verantwortungsloses Mergen. citeturn17view9turn14academia23

**Model-Hopping als Hauptoptimierung** ist ebenfalls überschätzt. Die aktuelle PR-Studie zeigt unterschiedliche Stärken je Aufgabentyp, aber keinen universellen Sieger. Ein gutes Harness mit klarer Spec, Tests, Browser, Git, Review und Kontextkontrolle überlebt den nächsten Modellwechsel; ein schlechter Prozess bleibt auch mit dem besten Modell schlecht. citeturn14academia21

Die älteren Engineering-Regeln, die durch Coding Agents heute besonders wichtig werden, sind damit erstaunlich konventionell:

- **Schreibe zuerst auf, welches Problem du löst.**
- **Mache implizite Anforderungen explizit.**
- **Bevorzuge kleine, reversible Änderungen.**
- **Reproduziere einen Bug, bevor du ihn reparierst.**
- **Nutze Tests als Vertrag, nicht als nachträgliche Dekoration.**
- **Trenne Erzeuger und Prüfer.**
- **Automatisiere wiederkehrende Qualitätskontrollen.**
- **Halte Dokumentation und Code synchron.**
- **Gib Prozessen und Programmen nur die Rechte, die sie benötigen.**
- **Stoppe bei Unsicherheit, statt auf einer falschen Annahme weiterzubauen.**

Coding Agents verändern vor allem die Produktionsgeschwindigkeit. Sie heben die klassischen Regeln nicht auf; sie erhöhen den Preis dafür, sie zu ignorieren. Je schneller ein Agent hunderte korrekte wirkende Codezeilen erzeugen kann, desto wichtiger werden Spezifikation, Kontextqualität, kleine Integrationsschritte, ausführbare Beweise und menschliche Architekturverantwortung. citeturn14academia25turn16view6turn14academia23