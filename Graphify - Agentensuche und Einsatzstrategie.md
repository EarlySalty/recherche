# Graphify: Agentensuche mit und ohne Wissensgraph

Stand: 28. November 2026. Recherche und technische Bestandsaufnahme des eigenen Multi-Agent-Setups. Entscheidung: Graphify behalten, strukturelle Suche verpflichtend machen, lokale Textsuche nicht pauschal blockieren.

## Ergebnis der öffentlichen Recherche

| Untersuchung | Ohne Graphify | Mit Graphify | Einordnung |
|---|---:|---:|---|
| Akon Labs, DeepSWE (113 gemeinsame Aufgaben, 1.157 Versuche je Arm) | 37,0 % bestanden | 54,0 % bestanden | +17 Prozentpunkte; Wettbewerber-Benchmark, kein Test einer First-Pflicht |
| Akon Labs, mittlere Modellkosten je Versuch | $0,663 | $0,636 | Indexierung und Infrastruktur nicht enthalten |
| coding-v1, Claude, 16 Retrieval-Fragen | 69.164 Tokens, 16,2 s | 139.809 Tokens, 29,3 s | Median-Korrektheit in beiden Armen 1,00; freie Hybridwahl: 78.854 Tokens, 14,4 s |
| TokenBench, Sonnet 5.5 | 124/142 bestanden | 123/142 bestanden | Kostenänderung über Aufgabengruppen −0,4 %, Intervall −7,3 % bis +6,9 %; keine gesicherte Gesamtersparnis |
| Graphify-eigener ERPNext-Test, sechs Fragen | 70,8 % Faktenabdeckung | 82,0 % | Hersteller-Test, kleine Stichprobe, keine vollständigen Implementierungsaufgaben |

Quellen (abgerufen 28.11.2026):
- [Akon Labs, DeepSWE, 26.11.2026](https://www.akonlabs.com/blog/deepswe-code-graph-benchmark): 625/1.157 gegenüber 428/1.157 bestandene Versuche. Wiederholungen derselben Aufgabe sind korreliert; die gepoolten Konfidenzintervalle sind entsprechend vorsichtig zu lesen.
- [coding-v1, Retrieval-Benchmark](https://fwornle.github.io/coding/benchmarks/coding-v1/): nur Claude-Arme mit technisch erzwungenem Tool-Angebot vergleichen. Der Hybridarm bietet auch CodeGraph; kein reiner Graphify-first-Test. Die Autoren korrigierten eine frühere Messung wegen eines fehlerhaften Indexes.
- [TokenBench, Findings](https://tokenbench.app/notes.html): kurze, verkettete und lange Aufgaben; gewichtete Kostenänderung, nicht bloße Token-Kompression.
- [Graphify, BENCHMARKS.md](https://github.com/Graphify-Labs/graphify/blob/v8/BENCHMARKS.md): eigener Benchmark.
- [Graphify, Projekt und Funktionsweise](https://github.com/Graphify-Labs/graphify).

Die Ergebnisse messen unterschiedliche Zielgrößen. Ein kleinerer Graphify-Output bedeutet nicht automatisch niedrigere End-to-End-Kosten. Graphify ist für Beziehungen und Kandidatensuche plausibel besonders nützlich; exakte Literale, YAML-Kommentare, Logs und noch nicht indexierte Änderungen gehören in die normale Suche. Keine der Quellen belegt einen pauschalen Vorteil einer Graphify-first-Pflicht in unserem konkreten Setup.

## Eigene Infrastruktur: Ist-Zustand am 28.11.2026

- Graphify 0.9.118; zentraler Graph mit 211.185.055 Bytes, zusammengeführt aus 19 Projektgraphen um 05:30.
- Täglicher Timer 05:15, heutiger Dienstlauf bis 05:33:35. Codeextraktion ohne LLM; Rust-Analyzer ergänzt semantische Kanten (z. B. 16.872 beim Twitch-Bot, 6.080 bei Deadlock-Bots laut Journal).
- Projektgraphen unter ~/.graphify/projects/<tag>/graphify-out, Links in kanonischen Checkouts, globaler Graph für projektübergreifende Kandidatensuche. Graphify-MCP und Architekturkarte vorhanden.
- Die globale Claude-Regel und der Skill code-suche verlangen Graphify zuerst. Der bisherige PreToolUse-Hook gibt rekursive Suche für 15 Minuten nach einer erkannten Graphify-Anforderung frei, selbst wenn diese fehlschlägt oder nur ein Skill geladen wurde. Kein Bezug zur konkreten Aufgabe oder zum Repository. Nach zwei Grep-Blockaden fällt das Grep-Tool durch; Bash nicht.
- Zwei geprüfte Feature-Worktrees (tb-brain-smalltalk-20261127, tb-collector-18) hatten keinen lokalen Graph-Link. Ein Main-Graph ist kein Nachweis für den aktuellen Branch. Keinen schreibbaren Link aus Feature-Worktrees auf den kanonischen Graphen legen.
- Die geprüften globalen Codex-Regeln und Hooks enthielten keine Graphify-Pflicht; Claude und Codex waren nicht gleich konfiguriert.
- Der Dienst meldete Erfolg trotz RepoLens-Fehler. Der konfigurierte RepoLens-Checkout fehlte auf diesem Host; der letzte vorhandene Graph kann veraltet sein. Globales Manifest vom 18.07.2026 listete nur 12 Projekte, obwohl der heutige Merge 19 Dateien verwendete.

Belege: lokale Dateien claude-config/hooks/graphify-first.py, skills/code-suche/SKILL.md, tools/graphify-site/refresh-all.mjs, repos.mjs; systemctl --user list-timers/show; journalctl --user -u graphify-refresh.service; stat; git worktree list; graphify global list.

## Zielstrategie

1. Bei neuer Architektur-/Bestandssuche, unbekannten Komponenten, Refactoring-Impact und projektübergreifenden Fragen Graphify zuerst. Der Manager übergibt Kandidatenpfade, Beziehungen und Graph-Stand an Worker; keine redundante Abfrage nur wegen der Uhrzeit.
2. Bei bekanntem Pfad, exakt bekanntem Literal, Compilerfehler, Konfiguration, Logs, Diff und aktueller Branch-Arbeit sind Read/rg/git grep direkt erlaubt. Auch bei fehlendem, veraltetem oder irrelevantem Graphen ist Textsuche der Fallback.
3. Graph-Ausgaben sind Hinweise, keine Wahrheit. Kandidaten im aktuellen Worktree öffnen, Pfade und Beziehungen verifizieren. Bei fehlendem lokalem Graphen explizit den kanonischen oder globalen Graphen als Baseline verwenden, nie ihn als Branch-Index ausgeben.
4. Keine manuelle globale Neu-Extraktion aus einem Worker-Checkout. Den kanonischen Refresh weiterhin zentral betreiben; Fehler, Quellen und Aktualität sichtbar machen.
5. Gleiche Semantik für Claude und Codex; technische Hinweise dürfen Worker nicht in Tool-Schleifen zwingen. Ein Hook kann Absicht und Relevanz nicht zuverlässig aus Shell-Syntax ableiten; die verbindliche Regel muss auf Aufgabenebene gelten.

## Eigener kontrollierter Vergleich

Vorab 12–20 repräsentative, bereits lösbare Aufgaben auswählen: exakte Suche, lokale Änderung, Architekturfrage, Abhängigkeiten, mehrdienstiger Bug, Refactoring. Je Aufgabe identischer Commit, Modell, Effort, Briefing, Berechtigungen, Zeitlimit und Tests, frische unabhängige Sessions; drei Arme: A ohne Graphify, B altes pauschales First-Gate, C selektive First-Strategie. Reihenfolge randomisieren und je Arm mindestens zwei Wiederholungen, Budget vorab festlegen. Keine Änderungen an Produktion.

Messen: bestandene Abnahmetests, relevante Fundstellen, Regressionen, Review-Runden, Input-/Output-/Cache-Tokens, tatsächliche Modellkosten, Toolcalls, Graph-Fehler, End-to-End-Laufzeit sowie amortisierte Refresh-Kosten. Pro Aufgabe gepaarte Differenzen und Streuung berichten; fehlgeschlagene Versuche im Nenner behalten. Ein lokaler Test wurde noch nicht durchgeführt; konkrete prozentuale Einsparungen sind daher unbekannt.

Entscheidung: selektives Graphify-first beibehalten, erst die Integrationsfehler beheben und dann den Nutzen messen. Keine allgemeine Tokenersparnis behaupten.
