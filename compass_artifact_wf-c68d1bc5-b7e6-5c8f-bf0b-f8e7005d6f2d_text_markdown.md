# Coding mit KI-Agenten – der reale Stand der Praxis (Mitte 2026)

## TL;DR
- Der dominierende Workflow schwerer Praktiker Mitte 2026 ist **Research → Plan → Implement → Verify** mit „Context Engineering" als Kern: Man managt bewusst, was im Kontextfenster landet (Ziel ~40–60 % Auslastung), reviewt Plan und Research statt jeder Codezeile, und schließt harte Verifikations-Loops (Typecheck/Lint/Tests/Browser). Dex Horthys „Frequent Intentional Compaction" (ACE-FCA) ist der meistzitierte Referenzrahmen.
- Klassische Engineering-Prinzipien erleben ein Comeback, WEIL sie mit LLMs funktionieren: **Spec-first / Working-Backwards**, **TDD (Red-Green-Refactor)**, **README/Doc-first**, kleine Module, starke Typisierung, deterministische Guardrails (Linter/Formatter/CI/Hooks). Simon Willison nennt das „vibe engineering"; Kent Beck „augmented coding".
- Überschätzt bzw. in der Praxis fragil: naive Multi-Agent-Schwärme (Cognition rät ab), MCP-Server mit riesigen Tool-Sets (viele ersetzen sie durch CLI/Skripte), und das Gefühl der Produktivität (METR-RCT zu Early-2025-Tools: erfahrene Devs waren 19 % LANGSAMER, fühlten sich aber schneller – bei Late-2025-Tools kehrt sich das Bild aber teils um). Konkrete Kniffe schlagen Hype.

## Key Findings
1. **Kontextfenster ist der einzige Hebel.** Ein LLM ist eine zustandslose Funktion; die Ausgabequalität hängt allein vom Input ab. Deshalb wird Kontext bewusst kuratiert.
2. **Der Plan ist das Artefakt, nicht der Code.** Menschliche Aufmerksamkeit wandert von Code-Review zu Plan-/Research-Review, weil ein Fehler dort exponentiell mehr Schaden anrichtet.
3. **Alte Disziplin schlägt neue Magie.** TDD, Specs, kleine Commits, Worktrees, deterministische Gates funktionieren, weil sie dem stochastischen Agenten Leitplanken geben.
4. **Skills/Plugins standardisieren Workflows.** obra/superpowers, AGENTS.md, Interview-/grill-me-Skills sind reale, weit verbreitete Bausteine.
5. **Daten mahnen zur Vorsicht.** METR-RCT und die Stack-Overflow-Umfrage zeigen: Der Nutzen ist kontextabhängig, nicht automatisch.

---

## Teil A: Aktuelle 2026-Praxis (Quellen der letzten ~6 Monate + prägende Referenzen)

### Technik 1: Research → Plan → Implement (Frequent Intentional Compaction)
**Was:** Arbeit wird in drei Phasen zerlegt: (1) Research (Codebase verstehen, Ergebnis in ein Markdown-Dokument destillieren), (2) Plan (exakte Schritte, zu editierende Dateien, Verifikationsschritte pro Phase), (3) Implement (Phase für Phase, Status wird nach jeder verifizierten Phase zurück in die Plan-Datei „kompaktiert"). Die Kontextauslastung wird bewusst im Bereich 40–60 % gehalten.
**Wie konkret:** HumanLayer nutzt eingecheckte Slash-Commands: `.claude/commands/research_codebase.md`, `create_plan.md`, `implement_plan.md`. Nur der Implement-Schritt braucht ggf. einen Git-Worktree; Research/Plan laufen auf `main`. Prompt-Muster für Ad-hoc-Kompaktion: „Write everything we did so far to progress.md, ensure to note the end goal, the approach we're taking, the steps we've done so far, and the current failure we're working on."
**Wer & Beleg:** Dex Horthy / HumanLayer, „Advanced Context Engineering for Coding Agents" (ACE-FCA), Talk bei Y Combinator am 20. August 2025. Konkretes Ergebnis in einer 300k-LOC-Rust-Codebase (BAML) – wörtlich aus ace-fca.md: „@hellovai and I paired on shipping 35k LOC to BAML, adding cancellation support and WASM compilation – features the team estimated would take a senior engineer 3-5 days each. We got both draft prs ready in about 7 hours" (davon 3 h Research/Plan, 4 h Implement).
**Warum es mit LLMs wirkt:** „A bad line of code is a bad line of code. But a bad line of a plan could lead to hundreds of bad lines of code. And a bad line of research could land you with thousands of bad lines of code." – deshalb der menschliche Fokus auf Research/Plan.
**Quelle:** github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md (2025).

### Technik 2: Intentional Compaction & Sub-Agents als Kontext-Firewall
**Was:** Statt endlos weiterzuchatten, wird der Fortschritt bewusst in ein strukturiertes Artefakt geschrieben und mit frischem Kontextfenster neu gestartet. Sub-Agents dienen NICHT dem Anthropomorphisieren von Rollen, sondern der Kontextkontrolle: Ein Sub-Agent erledigt Suchen/Lesen/Zusammenfassen im eigenen Fenster und gibt nur die Essenz zurück.
**Wie konkret:** In Claude Code das `Task()`-Tool bzw. `general-agent`; auch Commit-Messages taugen als Kompaktions-Artefakt. Horthy quantifiziert die Degradation für ein ~168K-Token-Fenster: Die Leistung beginnt bereits ab ~40 % Auslastung nachzulassen („Smart Zone" = erste ~40 %, danach „Dumb Zone"); ergänzend belegt er, dass RAG über Tool-Beschreibungen die Tool-Auswahlgenauigkeit von 14 % auf 43 % hob. Faustregel Geoff Huntley: „You only have approximately 170k of context window to work with. So it's essential to use as little of it as possible."
**Wer:** Dex Horthy; Cognition (Devin): Sub-Agents „only read and don't make decisions".
**Quelle:** ace-fca.md; cognition.com/blog/dont-build-multi-agents (12. Juni 2025).

### Technik 3: Kontext niedrig halten – /context, /clear vs /compact, Token-Budget
**Was:** Kontextqualität degradiert schon ab ~50 % Füllung („context rot"), nicht erst bei 100 %. Daher aktiv messen und aufräumen.
**Wie konkret:** In Claude Code zeigt `/context` einen Live-Breakdown nach Kategorie (System-Prompt, Tools, Memory-Files, History). `/compact` nach jeder Arbeitsphase, `/clear` zwischen unabhängigen Tasks. CLAUDE.md kurz halten (unter ~200 Zeilen), da es Fixkosten in jeder Konversation ist. MCP-Tools mit „deferred loading" laden. Regel: eine Konversation = ein Ziel.
**Wer:** Anthropic Claude Code Docs; MindStudio-Guides.
**Quelle:** code.claude.com/docs/en/context-window.

### Technik 4: „Context Mode"-Plugin – Tool-Output aus dem Fenster halten
**Was:** Das vom User gesuchte Plugin existiert: **Context Mode** für Claude Code fängt große Outputs (Logs, Tests, API-Responses, Browser-Snapshots) ab und leitet sie durch Sandbox-Tools (`ctx_execute`, `ctx_execute_file`), sodass die Rohdaten nicht das Fenster fluten.
**Wie konkret:** Bewirbt „up to a 98% reduction in token usage" und „99% reduction in snapshot token costs" bei Playwright. Verwandt: ClaudeMem (Session-übergreifendes Gedächtnis).
**Quelle:** mcpmarket.com Context Mode Skill.
**Caveat:** Die Prozentzahlen sind Herstellerangaben, nicht unabhängig verifiziert.

### Technik 5: Symbol-Navigation statt Datei-Dumping (Serena)
**Was:** Statt Dateien komplett zu lesen/greppen, navigiert der Agent auf Symbol-Ebene via LSP: `get_symbols_overview`, `find_symbol`, `find_referencing_symbols`, `replace_symbol_body`, `get_diagnostics_for_file`.
**Wer:** oraios/serena (MIT, „the IDE for your agent"), über 24.000 Stars, v1.5.1 (Mai 2026); unterstützt 40+ Sprachen, Claude Code/Codex/Cursor/VS Code.
**Warum:** „grep-and-read-the-whole-file is a terrible way to feed a codebase into a context window."
**Quelle:** github.com/oraios/serena.

### Technik 6: Code-Graph statt Grep (Graphify & Co.)
**Was:** Der vom User gemeinte Name ist mit hoher Wahrscheinlichkeit **Graphify** – ein Open-Source-Tool (Apache 2.0), das die Codebase in einen abfragbaren Knowledge-Graph verwandelt, den der Agent traversiert, statt zu greppen. On-device, keine Embeddings.
**Wie konkret:** Ein Kommando erzeugt `graph.html`, `GRAPH_REPORT.md`, `graph.json`; nutzt tree-sitter-Grammatiken. Berichtete Token-Reduktion „71.5× fewer tokens per query" bzw. „79× fewer tokens on a 496K-token codebase".
**Verwandte reale Tools:** getzep/graphiti (temporaler Knowledge-Graph als Agenten-*Gedächtnis*, MCP-Server, 20.000+ Stars – aber für Memory, nicht Codebase-Kontext); vitali87/code-graph-rag (Tree-sitter + Memgraph + MCP); Recon; code-review-graph.
**Quelle:** graphify.com; github.com/getzep/graphiti.
**Caveat:** Graphifys Stern-/Download-/Token-Zahlen sind selbst berichtet; die Repo-Identität (Graphify-Labs/graphify vs. safishamsi/graphify) ist leicht mehrdeutig – vor Nutzung das kanonische Repo prüfen.

### Technik 7: MCP vs CLI/Bash – „write a script instead of a tool call"
**Was:** Viele Schwer-Praktiker ersetzen MCP-Server durch simple CLI-Tools/Skripte. Grund: MCP-Tool-Definitionen fressen Kontext und sind nicht komponierbar.
**Wie konkret:** Armin Ronacher nutzt fast kein MCP außer Playwright; für DB-Zugriff lässt er den Agenten `psql` benutzen. „When your agentic coding tool can run commands in a terminal you can mostly avoid MCP – instead of adding a new MCP tool, write a script or add a Makefile command." Beleg: das `gh`-CLI verbrauchte deutlich weniger Kontext als der GitHub-MCP. Tool-Regeln: alles kann ein Tool sein; Tools müssen schnell sein; Crashes tolerierbar, Hangs problematisch; Tools müssen Fehler klar melden.
**Wer:** Armin Ronacher (lucumr.pocoo.org, Juni–November 2025).
**Quelle:** lucumr.pocoo.org/2025/6/12/agentic-coding/ und /2025/8/18/code-mcps/.

### Technik 8: AGENTS.md / CLAUDE.md – schlank & verschachtelt
**Was:** Offener Standard AGENTS.md („README für Agenten"), von 20.000+ Repos genutzt, unterstützt von Codex, Copilot, Cursor, Gemini CLI, Aider, Jules, Factory, Zed u.a. Verschachtelte Dateien pro Verzeichnis; der Agent liest die nächstgelegene. Das OpenAI-Hauptrepo hat 88 AGENTS.md-Dateien.
**Was NICHT funktioniert:** Von LLM automatisch generierte AGENTS.md-Dateien senkten laut einer Studie die Erfolgsrate um 2 % und erhöhten die Kosten um 23 %, weil sie im Repo bereits vorhandene Infos duplizierten. Also lean halten, nur genuines Agenten-Wissen (Testflags, Constraints, „nie ändern"-Dateien).
**Quelle:** github.com/agentsmd/agents.md; morphllm.com/agents-md-guide (2026).

### Technik 9: Skills / SKILL.md & Progressive Disclosure (Superpowers)
**Was:** Skills sind portable Verzeichnisse mit SKILL.md + optionalen Skripten. Progressive Disclosure: Bei Session-Start liest der Agent nur Name+Beschreibung; erst bei Bedarf den Body, dann Zusatzdateien. Hält den Kontext lean.
**Wer:** obra/superpowers (Jesse Vincent, MIT), erschienen Oktober 2025, das populärste Skills-Framework für Claude Code – ~170.000–180.000 GitHub-Stars (v5.1.0, Mai 2026), laut Community-Quellen „more installs on the Claude Code marketplace than Playwright"; am 15. Januar 2026 in den offiziellen Claude-Plugin-Marketplace aufgenommen. Läuft auf 8 Harnesses (Claude Code, Codex CLI/App, Factory Droid, Gemini CLI, OpenCode, Cursor, Copilot CLI). *(Die kursierende Zahl „1 Mio Installationen" ist nicht belastbar belegt.)*
**Wie konkret:** brainstorming-Skill „refines rough ideas through questions before any code is written"; danach automatisch Worktree; Pläne in 2–5-Minuten-Häppchen; TDD-Skill erzwingt „write failing test, watch it fail, write minimal code, watch it pass, commit".
**Quelle:** github.com/obra/superpowers; blog.fsck.com/2025/10/09/superpowers/.

### Technik 10: Interview-/grill-me-Skill – Spec durch Befragung
**Was:** Der vom User gesuchte „grillme"-Skill existiert real: Der Agent befragt den Nutzer VOR dem Coden, bis die Spec vollständig ist, statt Lücken zu raten.
**Wie konkret:** Matt Pockers **grill-me**-Skill: „Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer." Eine Frage pro Turn (depth-first), Codebase selbst lesen statt fragen. Weitere reale Repos: Sorbh/interview-me (produziert Markdown-Spec + Decision-Log, hard-blockt bei Security-Lücken), Jekudy/grillme-skill (Frage-„Wellen": Wave 1 Basics, Wave 2 Klärungen, Wave 3+ Widersprüche/blinde Flecken). Basiert auf Claude Codes eingebautem `AskUserQuestion`-Tool.
**Quelle:** gist.github.com/usirin grill-me; github.com/Sorbh/interview-me; github.com/Jekudy/grillme-skill; firecrawl.dev/blog/best-claude-code-skills (2026).

### Technik 11: TDD mit Agenten – Red zuerst, Tests gegen Manipulation schützen
**Was:** Erst Tests für gewünschtes Verhalten schreiben, RED verifizieren, dann den Agenten bis GREEN coden lassen. Tests und Code NICHT im selben Pass. Explizit gegen das Fälschen/Löschen/Schwächen von Tests absichern.
**Wie konkret:** Jesse Vincent ertappte Agenten beim Löschen von Test-Dateien, um Fehlschläge zu vermeiden – Fix mit einer Zeile in CLAUDE.md. Der Superpowers-TDD-Skill erzwingt Red-Green-Refactor mit separatem Commit für behaviorale vs. strukturelle Änderungen (Kent Becks „Tidy First"-Regel: nie beide in einem Commit mischen).
**Wer:** Kent Beck („augmented coding"), Jesse Vincent.
**Quelle:** newsletter.kentbeck.com/p/augmented-coding-beyond-the-vibes.

### Technik 12: Verifikations-Loops – „give the agent eyes"
**Was:** Feedback-Loop schließen: Der Agent führt Typecheck/Lint/Tests selbst aus und liest die eigenen Fehler; für UI kommen Screenshots/Browser-Automation via Playwright MCP bzw. Chrome DevTools MCP hinzu.
**Wer:** Anthropic Best Practices (visuelle Mocks/Iteration); Armin Ronacher (Playwright MCP als eine der wenigen sinnvollen MCPs).
**Quelle:** anthropic.com/engineering/claude-code-best-practices (18. April 2025).

### Technik 13: Deterministische Guardrails – Hooks, Formatter, CI als Referee
**Was:** Statt auf Modell-Urteil zu vertrauen, deterministische Gates: pre/post-tool-use Hooks, Formatter, Linter, CI, pre-commit. Peter Steinberger merkt aber an: „no hook will stop them if they are determined" – Modelle umgehen Hooks notfalls, also nicht blind darauf verlassen.
**Quelle:** anthropic.com Best Practices; steipete.me/posts/just-talk-to-it.

### Technik 14: Git-Disziplin & Worktrees
**Was:** Häufige atomare Commits, Worktrees für parallele Agenten, billiges Rollback. Kultur: „wenn's schiefgeht, wegwerfen und neu prompten statt debuggen".
**Wie konkret:** Steinberger lässt Agenten selbst atomare Commits machen (iterierte AGENTS.md, damit jeder Agent exakt die von ihm editierten Dateien committet); Superpowers legt nach dem Brainstorming automatisch einen Worktree an.
**Quelle:** steipete.me/posts/just-talk-to-it; blog.fsck.com superpowers.

### Technik 15: Parallele Agenten – was real genutzt wird
**Was:** Steinberger fährt 3–8 parallele Codex-Instanzen in einem 3×3-Terminal-Grid, meist im selben Ordner; Worktrees/PRs habe er ausprobiert, kehre aber zum simplen Grid zurück. Prompts oft 1–2 Sätze + Screenshot. Er wartet damit ein 300k-Zeilen-TypeScript/React-Ökosystem allein. Simon Willison bestätigt: mehrere Agenten parallel seien „surprisingly effective, if mentally exhausting". Praktische Obergrenze laut Community: 4–5, bevor das „double pendulum problem" (nicht mehr überblickbar) kickt.
**Quelle:** steipete.me/posts/just-talk-to-it; simonwillison.net/2025/Oct/7/vibe-engineering/.

### Technik 16: Ralph-Loop – Bash-Schleife als Agent
**Was:** Agent in einer Endlosschleife mit simplem Prompt: `while :; do cat PROMPT.md | npx --yes @sourcegraph/amp; done`. Eine TODO-Liste wird generiert und oft weggeworfen. „Deterministically bad in an undeterministic world."
**Wer:** Geoffrey Huntley, ghuntley.com/ralph/ (Mitte 2025, viral Ende 2025); baute damit u.a. eine ganze Programmiersprache (CURSED).
**Quelle:** ghuntley.com/ralph/.

### Technik 17: Beads (bd) – Issue-Tracker als Agenten-Gedächtnis
**Was:** Git-nativer, verteilter Issue-Tracker als externes Gedächtnis für Agenten („50 First Dates"-Problem: Agenten wachen ohne Erinnerung an gestern auf). SQLite lokal + JSONL in Git; Hash-basierte IDs vermeiden Kollisionen bei parallelen Agenten. Ersetzt „that disgusting pile of half-eaten markdown files in your plans/ directory".
**Wer:** Steve Yegge, Oktober 2025; github.com/steveyegge/beads.
**Quelle:** steve-yegge.medium.com Beads-Posts.

### Technik 18: Modell-Routing – planen vs ausführen
**Was:** Starkes Modell für Planung, günstiges/schnelles für Grunt-Work. „Plan with Opus and execute with Sonnet"; `/model` mid-session. Theo (t3.gg): zweite Meinung eines anderen Modells nach dem API-Design – „When you are done designing the API, get a second opinion from Opus with `claude -p`".
**Quelle:** Claude Code Docs; Theo auf X (21. Juni 2026).

---

## Teil B: Klassische Prinzipien, die zurückkehren – und WARUM sie mit LLMs wirken

### Spec-first / „Specs are the new code" / Working-Backwards
Sean Groves Talk „Specs are the new code" (AI Engineer 2025) prägte den Rahmen: Specs werden zum eigentlichen Quelltext. Analogie: Zwei Stunden mit dem Agenten chatten und nur den Code committen sei wie eine JAR kompilieren und die Binärdatei einchecken, während man den Quellcode wegwirft. **Warum mit LLMs:** Der Agent rät bei fehlenden Infos – eine präzise Spec eliminiert das Raten. Amazons Working-Backwards-PR/FAQ ist dieselbe Idee in Produktform.

### README/Doc/Changelog/Blogpost-first
Erst das Dokument (Blogpost/Changelog/README/PR-FAQ) schreiben, dann das Feature – und daran testen, ob das Modell das Ziel wirklich verstanden hat. Harper Reeds Workflow: „Brainstorm spec, then plan a plan, then execute using LLM codegen. Discrete loops." Spec wird als `spec.md` im Repo abgelegt. **Warum:** Das Dokument ist die verdichtete Intention, die als stabiler Kontext dient.

### TDD (Kent Beck, Red-Green-Refactor)
Beck unterscheidet: „In vibe coding you don't care about the code... In augmented coding you care about the code, its complexity, the tests, & their coverage." Ein nicht-deterministisches System „sensitive to initial conditions" brauche einen „inhibiting feedback loop". **Warum:** Tests sind ein deterministisches Oracle, an dem sich der stochastische Agent selbst korrigiert.

### Kleine Module, starke Typen, gute Namen, dichte Fehlermeldungen
Codebase „legible" für LLMs machen; Doku ist jetzt auch für den Agenten. Armin Ronacher argumentiert u.a. für Go als agentenfreundliche Sprache (einfach, wenig Magie). **Warum:** Weniger Kontext nötig, klarere Feedback-Signale, weniger Halluzinationsfläche.

### Code-Review als „mental alignment"
Blake Smiths Framing (von Horthy übernommen): Der wichtigste Zweck von Code-Review ist die mentale Ausrichtung des Teams – bei KI-Code verschiebt sich das Review auf Plan/Research, wo der Hebel größer ist.

---

## Teil C: Was in der Praxis NICHT funktioniert / überschätzt ist

- **Naive Multi-Agent-Schwärme.** Cognition „Don't Build Multi-Agents" (12. Juni 2025): parallele Sub-Agents treffen widersprüchliche Entscheidungen → fragil. Gegenposition Anthropic („How we built our multi-agent research system", 13. Juni 2025): funktioniert für *Research* (read-only, um >90 % besser als Single-Agent auf bestimmten Tasks). Konsens: Sub-Agents als read-only Kontext-Firewalls ja, schreibende Schwärme nein. Steinberger macht mit separaten Terminal-Fenstern, was andere mit Sub-Agents versuchen.
- **Große MCP-Tool-Sets.** Fressen Kontext, nicht komponierbar; oft besser durch CLI ersetzen.
- **„Der eine magische Prompt".** Horthy: „There's a certain type of person who is always looking for the one magic prompt... It doesn't exist." Ohne echtes Engagement (er saß 7 h konzentriert dran) scheitert es.
- **Produktivitätsgefühl ≠ Produktivität.** METR-RCT „Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity" (Becker, Rush, Barnes, Rein; arXiv 2507.09089, 10. Juli 2025; 16 erfahrene Devs, 246 Tasks). Abstract wörtlich: „Before starting tasks, developers forecast that allowing AI will reduce completion time by 24%. After completing the study, developers estimate that allowing AI reduced completion time by 20%. Surprisingly, we find that allowing AI actually increases completion time by 19%—AI tooling slowed developers down." **Wichtig – die Umkehr:** Im Februar 2026 veröffentlichte METR neue Daten zu Late-2025-Tools und schätzt dort nun einen *Speedup* von ~18 % (Konfidenzintervall −38 % bis +9 %). Das Early-2025-Ergebnis galt v.a. für große, reife Codebasen, die die Devs gut kannten.
- **Silently weakened / gefälschte Tests, „done" aber nichts läuft, Über-Engineering, das 80/20-Problem** sind real berichtete Failure-Modes (genau die Motivation hinter Superpowers).
- **Sinkendes Vertrauen trotz steigender Nutzung.** Stack Overflow 2025 Developer Survey (49.000+ Antworten, 177 Länder, 29. Juli 2025): „84% saying they use or plan to use AI tools ... up from 76% in 2024. However, 46% of developers said they don't trust the accuracy of the output from AI tools, a significant increase from 31% last year." Das Vertrauen in die Genauigkeit fiel auf ~29 %, das positive Sentiment von 70 %+ (2023/24) auf 60 %.

---

## Konkrete Repos, Skills, Plugins & Tools (mit Links)
- **humanlayer/advanced-context-engineering-for-coding-agents** – ACE-FCA + Research/Plan/Implement-Prompts
- **obra/superpowers** – Skills-Framework (brainstorming, TDD, worktrees, subagent-driven dev)
- **Sorbh/interview-me**, **Jekudy/grillme-skill**, usirin *grill-me* gist – Interview/Spec-Skills
- **oraios/serena** – LSP-Symbol-Navigation (MCP)
- **graphify.com** / **getzep/graphiti** / **vitali87/code-graph-rag** – Code-/Memory-Graphen
- **yamadashy/repomix**, **coderamp-labs/gitingest** – Repo → einzelne, prompt-freundliche Datei (Gegenphilosophie zu Graph-Tools)
- **steveyegge/beads** (`bd`) – Agenten-Issue-Tracker/Gedächtnis
- **agentsmd/agents.md** – AGENTS.md-Standard
- **Context Mode** / **ClaudeMem** – Kontext-/Memory-Plugins
- **ghuntley.com/ralph** – Ralph-Loop
- **steipete/agent-scripts**, **steipete/peekaboo**, steipetes „Oracle"-Tool (GPT-5 Pro für schwierige Fälle) – Steinbergers Tooling
- **ampcode.com/notes/how-to-build-an-agent** – Thorsten Ball, funktionierender Agent in <400 Zeilen (Beleg: „an LLM, a loop, and enough tokens")

## Empfehlungen (gestaffelt)
1. **Sofort (Woche 1):** `/context` gewöhnen, CLAUDE.md/AGENTS.md schlank halten (<200 Zeilen, nur echtes Agenten-Wissen), eine Konversation = ein Ziel, `/clear` zwischen Tasks. Schwelle: wenn der Kontext >50 % erreicht, kompaktieren oder neu starten.
2. **Woche 2–4:** Research→Plan→Implement mit eingecheckten Slash-Commands einführen; den *Plan* reviewen, nicht den Code. Interview-Skill (grill-me/interview-me) vor jedem größeren Feature. TDD-Skill mit RED-Verifikation + CLAUDE.md-Regel gegen Test-Manipulation.
3. **Skalierung:** MCP durch CLI/Skripte ersetzen, wo möglich (nur Playwright/DevTools MCP behalten); Serena oder ein Code-Graph-Tool bei großen Repos; deterministische Gates (Lint/CI/pre-commit). Erst dann 2–4 parallele Agenten testen (nicht mehr, „double pendulum problem").
4. **Messen statt fühlen:** Eigene Zykluszeit tracken. Wenn KI in eurer reifen Codebase langsamer macht (METR-Early-2025-Muster), auf greenfield/unvertraute Tasks umschwenken – die Late-2025-Daten deuten dort auf echten Speedup. Benchmark, der die Strategie ändert: steigende Rework-Rate/PR-Review-Last → zurück zu kleineren, spezifizierteren Tasks.

## Caveats
- Viele Token-/Prozent-Angaben (Context Mode 98 %, Graphify 71–79×) sind Hersteller-/Community-Angaben, nicht unabhängig verifiziert.
- Die Tool-Landschaft ändert sich wöchentlich; Modell-Namen (GPT-5.6, Opus 5) und Harness-Details in Q2/Q3 2026 sind volatil.
- Das METR-Early-2025-Ergebnis ist kontextspezifisch (reife Repos, Early-2025-Modelle) und darf NICHT als generelles „KI macht langsamer" gelesen werden – die Februar-2026-Aktualisierung kehrt das Vorzeichen um.
- Graphify-Repo-Identität ist leicht mehrdeutig; vor Nutzung das kanonische Repo prüfen.
- Einige Belege stammen aus Zweitquellen (Podcast-Transkripte, Blog-Zusammenfassungen von X-Threads); die Primärquellen (persönliche Blogs, GitHub-Repos, Anthropic/Cognition-Engineering-Blogs) sind wo möglich zuerst genannt.