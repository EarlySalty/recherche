# KI‑Videoschnitt für Unternehmen und Gaming: professionelles Produktionssystem ohne KI‑Slop

**Stand der Recherche: 1. Oktober 2026.**

## Kernergebnis und Zielbild

Die sinnvollste Art, heute mit KI professionellen Videoschnitt zu skalieren, ist **nicht** „Rohvideo rein → KI macht viralen Clip raus“. Genau dieses Vorgehen erzeugt meist den austauschbaren Auto-Caption-/Stock-B‑Roll-/Zoom-Look, den du als KI‑Slop vermeiden willst.

Das bessere System ist:

> **KI findet, analysiert, strukturiert, transkribiert, schlägt vor, baut Varianten und automatisiert technische Arbeit. Ein festes Brand‑ und Editing‑System entscheidet, wie das Ergebnis aussehen darf. Ein Mensch kontrolliert Story, Timing, Geschmack, Fakten, Rechte und den finalen Export.**

Das ist auch strategisch wichtig: YouTube hat seine Monetarisierungsregeln 2025 ausdrücklich präzisiert. Generische, massenproduzierte, repetitive und wie aus einer austauschbaren Vorlage wirkende Inhalte können unter „inauthentic content“ fallen. Wiederkehrende Intros, Layouts und Formate sind dagegen nicht grundsätzlich ein Problem, solange der eigentliche Inhalt substanziell variiert und echten kreativen, informativen oder unterhaltenden Mehrwert bietet. citeturn18view0

Damit würde ich dein System in drei Ebenen aufteilen:

| Ebene | Aufgabe | KI darf | Mensch entscheidet |
|---|---|---|---|
| **Brand System** | Wie sieht Content dieses Kunden aus? | Referenzen analysieren, Design-Spec erzeugen | Stil, Positionierung, No-Gos |
| **Editorial System** | Was wird aus dem Rohmaterial? | Transkribieren, Highlights suchen, Hooks/Varianten vorschlagen | Story, Humor, Timing, Aussage |
| **Production System** | Wie wird es reproduzierbar produziert? | Reframing, Captions, Rough Cuts, Metadaten, Render-Vorbereitung | Final Cut, Thumbnail, Rechte, QC |

Das Entscheidende ist, **nicht einen „MrBeast-Style“ zu klonen**, sondern die dahinterliegenden Mechanismen zu abstrahieren: klare Erwartung durch Thumbnail/Titel, sofortige Bestätigung dieser Erwartung, frühe interessante Momente, offene Informationsschleifen, sichtbare Stakes und ein befriedigender Payoff. YouTube misst bei längeren Videos ausdrücklich, wie viele Zuschauer nach den ersten 30 Sekunden noch dabei sind, und nennt als positive Faktoren unter anderem, dass der Anfang zur Erwartung aus Titel und Thumbnail passt; interessante spätere Momente sollen nach Möglichkeit früher gebracht werden. citeturn14view0

Das bedeutet praktisch:

**schlechter Bait**

> „Das ist komplett verrückt …“  
> 12 Sekunden Effekte  
> Logo  
> Kontext  
> irgendwann kommt die interessante Szene.

**guter Hook**

> „Ich hatte noch einen Versuch – und dieser Boss hatte genau 2 % HP.“  
> [0,8 Sekunden kritischer Gameplay-Moment]  
> „Dafür musste ich aber erst dieses Problem lösen …“

Oder im Unternehmensbereich:

> „Unsere Supportzeit fiel um 38 % – aber nicht wegen eines neuen Chatbots.“  
> [Dashboard/Produkt als Beweis]  
> „Der eigentliche Unterschied war diese Änderung im Workflow.“

Dabei gilt selbstverständlich: Zahlen und Claims nur verwenden, wenn sie im Kundenmaterial tatsächlich belegt sind.

Der Hook **verspricht**, der Hauptteil **beweist**, der Payoff **liefert**. Nicht: Hook verspricht und Video verschleppt.

YouTube unterstützt diese Denkweise inzwischen auch direkt mit A/B-Tests: Bis zu drei Titel beziehungsweise Titel-/Thumbnail-Kombinationen können getestet werden, und YouTube bewertet den Gewinner nach dem **Anteil an der Wiedergabezeit**, nicht bloß nach der höchsten Klickrate. Das ist ein wichtiger Hinweis gegen billigen Clickbait: Ein Thumbnail, das zwar klickt, aber die falsche Erwartung erzeugt, ist langfristig keine optimale Verpackung. Die Funktion gilt derzeit für Longform, nicht für Shorts. citeturn14view1

**Meine Kernempfehlung für dein Produkt wäre deshalb:**

> **„AI-assisted Social Video Operating System“ statt „AI Video Generator“.**

Du verkaufst nicht „KI macht Clips“, sondern ein individuell eingerichtetes **Content-System pro Kunde**, das aus zwei oder drei Referenzclips, einem Interview und dem vorhandenen Brand Kit eine maschinenlesbare Editing-Spezifikation erstellt. Danach kann der Agent immer wieder Rohmaterial in Vorschläge, Clips, Titel, Thumbnails, Render und Testvarianten umsetzen.

## Werkzeug-Stack für professionelle Produktion

Ich würde für einen kommerziellen Workflow **nicht versuchen, alles in einem Tool abzubilden**. Gute Ergebnisse entstehen eher aus einem Stack mit klar verteilten Rollen.

**Adobe Premiere Pro – meine erste Wahl als kontrollierbarer Master-Editor für Agentur-/Unternehmensarbeit.** Premiere besitzt inzwischen natürlichsprachliche Mediensuche über Media Intelligence, textbasierten Videoschnitt, automatische Transkription, Erkennung beziehungsweise Massenentfernung von Pausen und Füllwörtern sowie weitere AI-gestützte Audio-, Maskierungs- und Reframing-Funktionen. Generative Extend kann kurze Video- oder Audiobereiche verlängern. citeturn15view4turn15view5turn15view6

Der interessante Punkt daran ist nicht Generative AI. Es ist **Media Intelligence + Transcript + normale Timeline**. Bei einem zweistündigen Gaming-VOD oder einer langen Unternehmensaufnahme willst du beispielsweise suchen können nach:

> „Moment, wo Spieler fast stirbt und danach lacht“

oder:

> „Stelle, an der CEO über Kosten spricht“

und anschließend **manuell** entscheiden, welche fünf Sekunden davor und danach dramaturgisch notwendig sind.

**DaVinci Resolve Studio – stärkste Alternative für ein eigenes skalierbares Produktionssystem.** Stand Oktober 2026 ist Resolve 21 aktuell; die kostenlose Version ist umfangreich, Resolve Studio wird auf der deutschen Blackmagic-Seite derzeit für 305 Euro angeboten. Studio erweitert Resolve unter anderem um die DaVinci Neural Engine, zusätzliche AI-Funktionen und Automatisierungs-/Scripting-Möglichkeiten über Python und Lua. citeturn14view5

Meine Entscheidung wäre:

**Premiere**, wenn Kunden bereits Adobe verwenden, Motion Graphics/Adobe-Ökosystem wichtig sind und Editor-Kompatibilität zählt.

**Resolve**, wenn du selbst eine Produktionspipeline aufbaust, Color/Audio/Finishing in einer Anwendung haben möchtest und langfristig stärker automatisieren willst.

**Descript – sehr gut für Corporate Talking Heads, Podcasts, Interviews und schnelle Social Repurposing-Arbeit.** Descript lässt Video über das Transkript bearbeiten; Underlord kann auf Anweisungen hin Rohmaterial straffen, Pausen/Füllwörter entfernen, Audio verbessern und Captions beziehungsweise Visuals ergänzen. Studio Sound bietet AI-basierte Sprach-/Noise-Aufbereitung. citeturn17view5turn17view6turn17view7

Für hochdynamisches Gaming würde ich Descript dagegen eher als **Transcript-/Rough-Cut-Layer** behandeln, nicht als kreativen Endeditor.

**CapCut – gut als schneller Social-Layer, nicht als alleinige kreative Intelligenz.** CapCuts Auto-Editor bietet Long-to-Short, automatische Szenenerkennung, Untertitel, vertikale Clips und Vorlagen; CapCut selbst beschreibt den automatischen Output ausdrücklich als Ausgangspunkt, der anschließend hinsichtlich Text, Timing, Framing und Stil überprüft werden sollte. citeturn16view2

Genau so würde ich es einsetzen: Geschwindigkeit ja, **Template-Ästhetik nein**.

Für deine eigentliche Produktidee werden die Open-Source-Bausteine noch interessanter:

**`SYSTRAN/faster-whisper`** eignet sich für lokale Transkription und unterstützt Wort-Zeitstempel. Das Repository beschreibt es als CTranslate2-Reimplementierung von Whisper und veröffentlicht eigene Benchmarks zu höherer Geschwindigkeit beziehungsweise geringerem Speicherbedarf. citeturn17view0turn17view1

**`WyattBlue/auto-editor`** kann lange Videos anhand von Audio-Lautstärke und Bewegung vorsortieren, Dead Space entfernen und die entstehenden Schnitte anschließend unter anderem für Premiere und Resolve exportieren. Das Repository enthält inzwischen sogar eine Agent-Skill-Installation. citeturn16view3

Das ist für Gaming ziemlich interessant:

```text
4-Stunden-VOD
        ↓
Proxy / Lossless Pre-Cut
        ↓
Speech + Gameplay Activity Detection
        ↓
Transkript mit Timecodes
        ↓
LLM analysiert Highlights
        ↓
Kandidaten mit In/Out-Timecodes
        ↓
Premiere/Resolve/Remotion
        ↓
Human QC
```

**`mifi/lossless-cut`** ist besonders für sehr große VOD-Dateien praktisch, weil Material verlustarm beziehungsweise ohne unnötiges komplettes Neurendern zerlegt werden kann. Das Projekt kann unter anderem Schnittlisten als CSV-EDL importieren und exportieren. citeturn17view2turn17view3

**Remotion** ist wahrscheinlich der wichtigste Baustein, wenn du aus der Dienstleistung später ein wirklich skalierbares Produkt machen willst. Remotion definiert Videos programmatisch über React-Komponenten und Frame-/Videoeigenschaften. Dadurch können Caption-Systeme, Lower Thirds, CTA-Karten, Facecam-Layouts, Progress Bars, Statistik-Karten oder Endcards **als richtige Komponenten statt als lose Templates** gebaut werden. citeturn16view7

Beispiel:

```text
Client A
├── CaptionStyle
├── FacecamFrame
├── GameplayCrop
├── CTA
├── StatCard
└── MemeReaction

Client B
├── CaptionStyle
├── ProductDemo
├── LowerThird
├── QuoteCard
├── CTA
└── EndCard
```

Dann sagt die KI nicht mehr:

> „Mach Untertitel schön.“

Sondern:

```json
{
  "component": "CaptionStyle",
  "variant": "gaming-emphasis",
  "start": 12.4,
  "end": 15.8,
  "emphasis_words": ["ONE HP", "CLUTCH"]
}
```

Das ist ein gewaltiger Unterschied hinsichtlich Reproduzierbarkeit.

Für Agenten existiert inzwischen außerdem ein offenes **Agent-Skills-Format**: Ein Skill besteht mindestens aus einer `SKILL.md`; optional sind `scripts/`, `references/` und `assets/` vorgesehen. citeturn14view6 Remotion veröffentlicht selbst ein `remotion-dev/skills`-Repository mit Agent Skills für seine Video-Workflows. citeturn17view4

Damit hast du ziemlich genau die Architektur, nach der du gefragt hast.

**Mein bevorzugter Stack für dein Vorhaben wäre daher:**

| Bedarf | Empfehlung |
|---|---|
| Professioneller Final Cut | Premiere oder Resolve |
| Lokale Transkription | faster-whisper |
| Riesige Gaming-VODs vorbereiten | LosslessCut + auto-editor |
| AI-Analyse / Story / Clip-Auswahl | LLM mit eigenem Skill |
| Reproduzierbare Brand-Overlays | Remotion |
| schnelle Talking-Head-Produktion | Descript |
| schnelle Social-Varianten | CapCut als optionaler Secondary Editor |
| Render-/Media-Automation | FFmpeg |
| finale Qualitätskontrolle | Mensch |

**Der Skill selbst ist dein eigentliches geistiges Eigentum.** Das Schnittprogramm kann jeder kaufen. Wertvoll wird dein Angebot durch die Kombination aus Brand-Spec, Hook-System, Scoring, Vorlagen, QA-Regeln und Performance-Historie des Kunden.

## Schnittsystem für Hook, Watchtime und Conversion

Die zentrale Denkweise sollte sein:

> **Jeder Clip ist eine Mini-Geschichte und gleichzeitig ein Funnel.**

TikTok selbst empfiehlt für Werbe-Creatives eine klare **Hook → Body → Close/CTA**-Struktur sowie vertikales 9:16-Material, ausreichend hohe Auflösung und Freiraum für die TikTok-UI. TikTok nennt Musik, Bewegung, Übergänge und Text-Overlays als mögliche Attention Trigger. citeturn15view2turn15view3

Das ist aber **kein Argument für permanentes visuelles Feuerwerk**. Die Frage bei jedem Eingriff lautet:

> „Was macht dieses Element für das Verständnis oder die Emotion?“

Ich würde nur fünf legitime Gründe für ein zusätzliches Element zulassen:

| Rolle | Beispiel |
|---|---|
| **Proof** | Gameplay, Dashboard, Produktdemo, echte Statistik |
| **Context** | Name, Karte, vorherige Situation |
| **Attention** | Reframe, Punch-in, Schnitt, Motion |
| **Emotion/Joke** | Reaction, Meme, SFX |
| **Navigation** | Caption, Progress Indicator, CTA |

Kann ein Zoom, Meme oder B‑Roll keinem dieser Zwecke zugeordnet werden, wird es entfernt.

**Für deine „MrBeast-Häppchen“-Idee würde ich folgende Version verwenden:**

```text
IMPACT TEASER
     ↓
KLARE FRAGE / STAKES
     ↓
KONTEXT NUR SO VIEL WIE NÖTIG
     ↓
PROGRESSION
     ↓
MICRO-PAYOFF
     ↓
NEUE KOMPLIKATION
     ↓
FINALER PAYOFF
     ↓
KONTEXTUELLER CTA
```

Nicht:

```text
laute Musik
→ random Zoom
→ Meme
→ Pfeil
→ Untertitel
→ Explosion
→ nochmal Zoom
```

Ein guter **Gaming-Short** könnte als Startvorlage so funktionieren:

```text
0:00  stärkstes visuelles Signal / ungewöhnliche Situation
0:01  "Ich hatte genau EINEN Versuch."
0:03  kurze Vorschau des Problems – Ergebnis noch nicht verraten
0:05  Ausgangssituation
0:10  erster Fortschritt
0:17  Komplikation
0:23  fast gescheitert
0:28  Payoff
0:32  "Den kompletten Run findest du im verlinkten Video."
```

Diese Zeiten sind **kein Plattformgesetz**, sondern ein Starttemplate, das anhand realer Retention-Daten verändert werden sollte. Für YouTube ist die wichtige offizielle Information, dass die ersten 30 Sekunden als Intro-Retention separat betrachtet werden und spätere Top-Momente gegebenenfalls früher platziert werden sollten. citeturn14view0

Bei Gaming funktioniert inhaltlich besonders gut:

**Stakes:** Was kann verloren/gewonnen werden?

> „Wenn ich jetzt sterbe, sind drei Stunden Run weg.“

**Constraint:**

> „Kann man den Boss ohne Heilung besiegen?“

**Knowledge Gap:**

> „Dieser Build sieht komplett falsch aus – bis man dieses Item versteht.“

**Unexpected Event:**

> „Ich wusste nicht, dass dieses NPC-Verhalten überhaupt möglich ist.“

**Social Conflict:**

> „Mein Teammate sagte, der Spot sei unmöglich.“

Die KI sollte dafür **keine künstliche Drama-Sprache erfinden**, sondern im Transcript nach echten Situationen suchen, die bereits Stakes besitzen.

Für **Unternehmenscontent** ist das Äquivalent weniger „Hype“ und mehr **konkrete Relevanz**:

> „Wir haben unsere Demo um sechs Minuten gekürzt – und genau dieser Teil war schuld.“

> „Fast jedes Team automatisiert diesen Prozess an der falschen Stelle.“

> „Das ist der Unterschied zwischen unserem alten und neuen Workflow.“

Danach möglichst früh einen echten Beleg zeigen: Produkt, UI, Code, Vorher/Nachher, Prozess oder verifizierte Zahl.

Die richtige Unternehmensstruktur ist meist:

```text
Problem
↓
konkreter Proof
↓
warum es passiert
↓
Lösung / Mechanismus
↓
Ergebnis
↓
nächster sinnvoller Schritt
```

Nicht:

> „In der heutigen schnelllebigen digitalen Welt ist KI wichtiger denn je …“

Genau solche austauschbaren Einstiege gehören in deiner Anti-Slop-Spec ausdrücklich verboten.

**CTA und Funnel sollten ebenfalls kontextbezogen sein.**

„Join our Discord“ ist schwach.

Besser:

> „Die komplette Build-Liste liegt im Discord.“

> „Wir testen den Build heute Abend live – Link im Profil.“

> „Im Discord sind die Scrim-Termine und der Download.“

Damit wird nicht die Plattform gepitcht, sondern **der Grund für den Klick**.

Für YouTube Shorts gibt es dabei einen wichtigen technischen Punkt: Ein Short kann mit einem **Related Video** aus dem eigenen Kanal verbunden werden; der Link erscheint im Shorts-Player klickbar unter dem Kanalnamen und kann auf ein Video, einen Short oder Live-Content führen. citeturn19view2

Deshalb würde ich den Gaming-Funnel auf YouTube so bauen:

```text
SHORT
"Dieser Build sollte eigentlich nicht funktionieren."
        ↓
RELATED VIDEO
kompletter Run / Guide / Stream Highlight
        ↓
KANAL / BESCHREIBUNG / PROFIL
        ↓
DISCORD oder TWITCH
```

Das ist besser, als darauf zu hoffen, dass ein Nutzer aus einem Short eine komplizierte URL abtippt.

Bei TikTok kann ein Website-Link im Profil derzeit ab mindestens 1.000 Followern oder bei einem verifizierten Business Account hinzugefügt werden; TikTok empfiehlt selbst direkte Links mit möglichst wenigen Zwischenschritten. citeturn20search1

Daraus ergibt sich:

```text
TikTok
↓
"Build im Profil"
↓
direkte Landingpage
├── Discord
├── Twitch
└── Full Guide
```

Nicht sieben Social-Icons und zehn Möglichkeiten. Pro Clip **eine Primäraktion**.

Die KI sollte daher schon bei der Clip-Auswahl wissen:

```yaml
primary_goal: discord_join
offer: "vollständige Build-Datei"
cta_phrase: "Build im Discord"
funnel_destination: profile_link
```

und nicht erst am Ende irgendein „Follow for more“ ankleben.

## Universeller KI-Workflow für Kunden

Hier liegt meiner Meinung nach der größte Business-Wert deiner Idee.

Statt jedem Kunden einen generischen Prompt zu geben, baust du einen **zweistufigen Agenten**.

**Erste Stufe: einmaliges Style-Onboarding.**

Die KI bekommt zwei bis drei Referenzclips und soll **nicht den Creator kopieren**, sondern Eigenschaften extrahieren:

```text
Schnittfrequenz
Caption-Dichte
Typografie-Hierarchie
Farbverwendung
Facecam-Größe
Gameplay-Verhältnis
Kamerabewegung
Punch-in-Verhalten
B-Roll-Häufigkeit
Meme-Dichte
SFX-Dichte
Musikrolle
Humor
CTA-Stil
Informationsdichte
Hook-Arten
```

Danach stellt sie gezielt Fragen.

Ich würde folgende Onboarding-Fragen standardisieren:

| Bereich | Frage |
|---|---|
| Ziel | Was soll der Content primär bewirken: Reichweite, Lead, Discord, Twitch, Verkauf, Reputation? |
| Publikum | Wer soll stoppen und warum interessiert diese Person das Thema? |
| Plattform | YouTube Shorts, TikTok, Reels, Longform, LinkedIn? |
| Persönlichkeit | Seriös, technisch, trocken, laut, ironisch, edgy? |
| Referenzen | Was gefällt dir **konkret** an den Beispielclips? Was nicht? |
| Branding | Logo, Fonts, Farben, Lower Thirds, vorhandene Templates? |
| Captions | Minimal, wortweise, Satzweise, Schwerpunktwörter? |
| Gaming | Facecam? HUD? Welche UI-Elemente dürfen niemals verdeckt werden? |
| Memes | Wie viel Meme-Humor? Welche Arten sind tabu? |
| Audio | Welche Musik/SFX sind erlaubt und lizenziert? |
| CTA | Wohin soll ein Zuschauer nach dem Clip? Warum sollte er das tun? |
| Grenzen | Claims, Themen, Wörter, Personen oder Effekte, die niemals benutzt werden dürfen? |

Anschließend schreibt die KI eine **Brand Video Spec**, etwa:

```yaml
client: example_gaming

audience:
  primary: "competitive players"
  knowledge_level: "intermediate"
  language: "de-DE"

objective:
  primary: "discord_join"
  secondary: "twitch_viewer"

voice:
  tone:
    - direct
    - knowledgeable
    - dry_humor
  forbidden:
    - fake_hype
    - corporate_ai_language
    - exaggerated_claims

visual_system:
  aspect_ratio: "9:16"
  facecam:
    enabled: true
    default_position: "top"
    maximum_height_percent: 28
  gameplay:
    priority: true
    protected_regions:
      - minimap
      - health
      - inventory
  captions:
    max_lines: 2
    sentence_case: true
    emphasis_words_max_per_phrase: 2
    emoji_policy: "rare"
  zooms:
    use_only_for:
      - reaction
      - proof
      - gameplay_focus
    minimum_spacing_seconds: 5

editing:
  pace: "high-but-readable"
  silence_policy: "remove only non-dramatic dead air"
  meme_density: "low-medium"
  transition_style: "hard cuts; motivated motion only"
  b_roll_policy: "proof before decoration"

hook:
  preferred:
    - stakes
    - unexpected_event
    - challenge
    - counterintuitive_fact
  forbidden:
    - "you won't believe"
    - "this changes everything"
    - fake countdowns

cta:
  primary: "Discord"
  value_exchange: "full build + patch updates"
  style: "one sentence, contextual"

anti_slop:
  no_random_stock_broll: true
  no_generated_faces: true
  no_word_by_word_rainbow_captions: true
  no_emoji_spam: true
  no_unverified_statistics: true
```

Das ist viel besser als „Prompt dir den Stil jedes Mal neu“.

**Zweite Stufe: Produktionslauf pro Video.**

Die Verarbeitung würde ich so gestalten:

```text
INGEST
↓
TECHNISCHE ANALYSE
↓
TRANSKRIPTION
↓
SEMANTISCHE ANALYSE
↓
CLIP-KANDIDATEN
↓
EDITORIAL SCORING
↓
HOOK-VARIANTEN
↓
EDIT PLAN / EDL
↓
DRAFT
↓
HUMAN REVIEW
↓
PACKAGING
↓
RENDER + QC
↓
PUBLISH
↓
PERFORMANCE-DATEN ZURÜCK IN CLIENT-PROFIL
```

Lokale Transkription mit Wort-Zeitstempeln lässt sich beispielsweise mit faster-whisper aufbauen; Auto-Editor und LosslessCut können davor beziehungsweise danach technische Vorsortierung und Schnittlisten übernehmen. citeturn17view0turn16view3turn17view3

**Wichtig: Lass die KI nicht einfach „Virality Score: 94/100“ behaupten.**

Das ist Pseudogenauigkeit.

Gib ihr stattdessen ein **relatives Editorial Scorecard-System**. Mein Vorschlag:

| Dimension | Gewicht |
|---|---:|
| Hook sofort verständlich | 20 |
| eigenständig verständlich | 15 |
| Stakes / Spannung / Nutzen | 15 |
| sichtbarer Proof oder Action | 15 |
| klarer Payoff | 15 |
| Brand Fit | 10 |
| natürlicher CTA-Übergang | 10 |

Dann:

```text
85/100 = sehr starker Kandidat innerhalb DIESES Rohmaterials
72/100 = brauchbar
61/100 = benötigt Umbau
```

Aber ausdrücklich:

> **Der Wert ist eine interne Priorisierung, keine Vorhersage von Views.**

Vor dem Scoring existieren Hard Gates:

```text
[ ] Aussage sachlich korrekt?
[ ] Kontext ausreichend?
[ ] Rechte am verwendeten Material geklärt?
[ ] Keine vertraulichen Informationen?
[ ] Keine irreführende synthetische Darstellung?
[ ] Kein Payoff, den der Clip später nicht liefert?
```

Das Produktionssystem sollte außerdem **nicht sofort einen Clip erzeugen**, sondern zunächst Kandidaten liefern:

```text
Candidate A
00:14:32.400 – 00:15:08.100
Reason:
- instantly understandable failure
- real emotional reaction
- strong visual proof
- clean payoff
Hook possibility:
"Ich habe gerade den dümmsten Weg gefunden, diesen Boss zu besiegen."

Candidate B
00:42:01.200 – 00:42:38.500
...
```

Erst nach Auswahl entstehen Edit Decision List und Assets.

Damit verhinderst du, dass zehn schlechte Clips automatisch zehnmal professionell gerendert werden.

**Die Lernschleife ist anschließend entscheidend.**

Pro Clip speicherst du nicht nur Views, sondern:

```json
{
  "content_type": "gaming_challenge",
  "hook_type": "stakes",
  "hook_visual": "boss_1hp",
  "caption_variant": "minimal",
  "cta": "discord_build",
  "duration_seconds": 34,
  "result": {
    "retention": "...",
    "completion": "...",
    "shares": "...",
    "profile_actions": "...",
    "discord_joins": "..."
  }
}
```

Welche Kennzahlen verfügbar sind, hängt von Plattform und Account ab. YouTube stellt auf Videoebene unter anderem detaillierte Retention-Kurven mit Dips, Spikes und Top-Momenten bereit. citeturn14view0

Damit kannst du nach genügend Veröffentlichungen fragen:

> „Funktionieren beim Kunden echte Gameplay-Cold-Opens besser als Facecam-Hooks?“

statt:

> „Was sagt irgendein allgemeiner Viral-Guide im Internet?“

Das macht das System mit jedem Kunden besser.

## Prompt- und Skill-Vorlage zum direkten Einsatz

Für deine Idee würde ich einen Agent Skill namens beispielsweise `professional-social-video-editor` bauen. Das offene Agent-Skills-Format ist genau für solche wiederverwendbaren Instruktionspakete ausgelegt: `SKILL.md` ist verpflichtend, weitere Scripts, Referenzen und Assets können modular ergänzt werden. citeturn14view6

Die Ordnerstruktur:

```text
professional-social-video-editor/
│
├── SKILL.md
│
├── references/
│   ├── onboarding.md
│   ├── brand-video-spec.md
│   ├── hook-library.md
│   ├── corporate-editing.md
│   ├── gaming-editing.md
│   ├── anti-slop.md
│   ├── thumbnail-system.md
│   ├── metadata-system.md
│   ├── render-specs.md
│   └── qc-checklist.md
│
├── assets/
│   ├── captions/
│   ├── lower-thirds/
│   ├── cta/
│   ├── sfx/
│   ├── logos/
│   └── remotion-components/
│
└── scripts/
    ├── probe_media.py
    ├── transcribe.py
    ├── make_proxies.py
    ├── candidate_clips.py
    ├── render_social.py
    └── qc_media.py
```

Remotion pflegt selbst Agent Skills für AI-Coding-Agenten, wodurch sich dieser Skill-Ansatz besonders gut mit programmatischen Video-Komponenten kombinieren lässt. citeturn17view4turn16view7

Deine `SKILL.md` könnte ungefähr so aussehen:

```markdown
---
name: professional-social-video-editor
description: >
  Turns corporate, gaming, stream, interview, and long-form footage
  into brand-consistent social-video edit plans and deliverables.
  Use for clip discovery, hooks, captions, layout decisions,
  thumbnails, metadata, CTAs, render specifications, and QC.
---

# Mission

Create professional, brand-specific social video.
AI assists production; it must never replace editorial judgment.

The output must not look mass-produced, generic, templated,
or obviously AI-generated.

# Operating modes

There are two modes:

A. CLIENT_SETUP
B. VIDEO_PRODUCTION

If no valid Brand Video Spec exists, run CLIENT_SETUP first.

# CLIENT_SETUP

Inspect all supplied reference clips.

Do not blindly imitate a creator.
Extract abstract design and editorial properties:

- pacing
- shot duration
- hook construction
- typography hierarchy
- caption density
- framing
- facecam/gameplay ratio
- transition vocabulary
- sound design
- meme density
- CTA style
- thumbnail composition
- title patterns

Then ask only questions required to establish:

- audience
- primary objective
- platforms
- brand voice
- desired pacing
- fonts
- colors
- logo rules
- caption style
- framing
- safe areas
- meme policy
- music policy
- CTA
- forbidden elements
- factual/claim restrictions
- approval requirements

Produce BRAND_VIDEO_SPEC.yaml.

Do not begin mass production before the spec is accepted.

# VIDEO_PRODUCTION

For every source video:

1. Validate media.
2. Obtain a timestamped transcript.
3. Identify topics, stories, conflicts, proofs, reactions,
   jokes, useful statements, and visual moments.
4. Generate clip candidates.
5. Score candidates comparatively.
6. Reject candidates without a payoff.
7. Propose three materially different opening hooks.
8. Produce an edit plan.
9. Produce an asset list.
10. Produce captions.
11. Produce CTA.
12. Produce packaging suggestions.
13. Produce render specifications.
14. Run QC.

# Editorial rules

Every clip needs:

HOOK -> CONTEXT -> PROGRESSION -> PAYOFF

A CTA is optional.
A payoff is not.

Never manufacture stakes that do not exist.

Never invent:
- quotations
- numbers
- customer results
- gameplay events
- product features
- reactions
- footage

If evidence is unavailable, mark:
[EVIDENCE REQUIRED]

# Hook rules

Prefer:
- clear stakes
- visible proof
- unanswered question
- conflict
- challenge
- unexpected event
- counterintuitive claim
- transformation

Avoid:
- generic hype
- empty adjectives
- vague "you won't believe this"
- misleading teasers
- long logo intros

The first visual must have a purpose.

# Anti-slop rules

Never add an effect solely because engagement is desired.

Do not:
- zoom constantly
- highlight every caption word
- use random stock B-roll
- use emoji spam
- add irrelevant memes
- generate fake faces
- generate fake screenshots
- invent UI
- use five fonts
- use excessive transitions
- use generic corporate AI phrasing
- create clickbait the video does not deliver

Each additional element must perform at least one function:

PROOF
CONTEXT
ATTENTION
EMOTION
NAVIGATION

Otherwise remove it.

# Gaming rules

Preserve important gameplay information.

Do not crop:
- health
- minimap
- objective markers
- inventory
- crosshair
- relevant opponent information

unless explicitly allowed by the Brand Video Spec.

Automatic reframing is a draft only.
Manually define exceptions.

# Corporate rules

Prioritize:
1. verified proof
2. clarity
3. brand credibility
4. pacing

Never make unsupported performance claims.

Do not make the content sound like an AI marketing article.

# Output contract

Return:

A. SOURCE SUMMARY
B. CLIP CANDIDATES WITH TIMECODES
C. CLIP SCORES
D. HOOK VARIANTS
E. EDIT DECISION LIST
F. OVERLAY / B-ROLL / MEME PLAN
G. CAPTION PLAN
H. SOUND PLAN
I. CTA
J. TITLE OPTIONS
K. DESCRIPTION
L. HASHTAGS
M. THUMBNAIL CONCEPTS
N. RENDER SPEC
O. QA REPORT

# Human approval gates

Human approval is mandatory before:

- factual claims are published
- generated realistic imagery is used
- an individual's appearance or voice is synthesized
- third-party copyrighted assets are added
- final thumbnails are published
- client material is uploaded to an unapproved cloud service
- final publishing
```

Dann kommt der eigentliche **Master-Prompt**, den ein Nutzer zusammen mit zwei bis drei Beispielen verwenden kann:

```text
Du bist mein Senior Social Video Director und Video Editor.

Deine Aufgabe ist NICHT, möglichst viele AI-Effekte zu erzeugen.
Deine Aufgabe ist, aus meinem Material ein wiederholbares,
professionelles Content-System zu entwickeln, das zu meiner
Marke und Zielgruppe passt.

Wir arbeiten in zwei Phasen.

PHASE A – STYLE SETUP

Ich gebe dir:
1. zwei bis drei Referenzclips,
2. vorhandenes Branding,
3. Informationen über meinen Content.

Analysiere die Referenzen NICHT danach, welchen Creator du
kopieren kannst.

Analysiere stattdessen:
- Hook-Struktur
- Story-Struktur
- Schnittgeschwindigkeit
- durchschnittliche Informationsdichte
- Facecam-/Gameplay-/B-Roll-Verteilung
- Text-Hierarchie
- Caption-Stil
- Font-Charakter
- Farbsystem
- Zoom-Verhalten
- Bewegungen
- Übergänge
- Meme-Einsatz
- Sounddesign
- Musik
- CTA
- Thumbnail-Logik
- Titel-Logik

Stelle mir danach die minimal notwendige Anzahl an Fragen, um
einen eindeutigen Brand Video Style Guide zu erstellen.

Frage insbesondere nach:
- Zielgruppe
- primärem Conversion-Ziel
- Plattformen
- gewünschter Tonalität
- Elementen der Referenzen, die mir gefallen
- Elementen, die mir NICHT gefallen
- Brand-Farben
- Fonts
- Logos
- Caption-Stil
- Pacing
- Humor
- Meme-Regeln
- Musikrechten
- Gaming-HUD-Safe-Zones
- CTA und Zielseite
- verbotenen Claims
- verbotenen visuellen Elementen

Erstelle danach eine strukturierte BRAND_VIDEO_SPEC.

Stoppe und lasse die Spezifikation bestätigen.

PHASE B – VIDEO

Wenn eine bestätigte BRAND_VIDEO_SPEC existiert:

Analysiere zuerst das gesamte Rohmaterial bzw. Transkript.

Suche nicht nur nach lauten Momenten.
Suche nach:
- starken Aussagen
- Konflikt
- Stakes
- Problemen
- Lösungen
- Überraschungen
- echten Reaktionen
- Fehlern
- Erfolgen
- Demonstrationen
- sichtbarem Proof
- verständlichen Mini-Stories
- Stellen mit natürlichem Payoff

Erstelle zuerst Kandidaten, bevor du einen Schnitt auswählst.

Pro Kandidat:
- Start-Timecode
- End-Timecode
- Kernstory
- Hook-Potenzial
- erwarteter Payoff
- notwendiger Kontext
- visuelles Material
- Risiken
- CTA-Eignung

Bewerte Kandidaten relativ zueinander,
aber behaupte niemals, Views oder Viralität vorhersehen zu können.

Für den besten Kandidaten erzeuge drei unterschiedliche Hooks:
A. Stakes
B. Curiosity
C. Proof/Result

Jeder Hook muss vom Video eingelöst werden.

Erstelle anschließend den finalen Edit Plan sekundengenau.

Für jede Änderung nenne:
- Timecode
- Videoquelle
- Schnitt
- Crop/Reframe
- Caption
- Overlay
- B-Roll
- Meme
- SFX
- Musikaktion
- Zweck der Änderung

Der Zweck muss einer Kategorie entsprechen:
PROOF / CONTEXT / ATTENTION / EMOTION / NAVIGATION.

Kannst du keinen Zweck nennen, entferne das Element.

Verwende kein AI-Slop.

Insbesondere:
- keine zufälligen Zooms
- keine zufällige B-Roll
- keine Emoji-Flut
- keine überladenen Captions
- keine generischen KI-Floskeln
- keine Fake-Screenshots
- keine Fake-Statistiken
- keine künstlichen Reaktionen
- keine erfundenen Aussagen
- keine fünf unterschiedlichen visuellen Stile
- keine Templates, die wichtiger wirken als der Content

Am Ende liefere:
1. Edit Plan
2. Captions
3. Audio/SFX Plan
4. CTA
5. Titelvarianten
6. Beschreibung
7. relevante Hashtags
8. Thumbnail-Konzepte
9. Render Settings
10. QC-Checkliste

Titel, Beschreibungen und Thumbnails müssen wie von einem
erfahrenen menschlichen Editor/Creator wirken, nicht wie
generisches AI-Marketing.
```

Das ist die Basis, die du anschließend branchenspezifisch erweitern kannst.

Dann brauchst du nicht **einen** riesigen Prompt, sondern:

```text
CORE SKILL
│
├── corporate
├── gaming
├── podcast
├── founder
├── ecommerce
└── streamer
```

plus:

```text
CLIENT SPEC
│
├── Kunde A
├── Kunde B
└── Kunde C
```

Das Ergebnis ist:

> **Core Editing Knowledge × Vertical Rules × Client Style × aktuelles Rohmaterial**

Genau diese Trennung macht das System universell.

## Qualitätsstandard gegen KI-Slop

Der wichtigste Teil deines gesamten Angebots sollte meiner Meinung nach ein **Anti-Slop-QA-Gate** sein.

YouTube prüft bei monetarisierbaren Kanälen ausdrücklich auch Titel, Thumbnails und Beschreibungen und beschreibt generische beziehungsweise massenproduzierte AI-Inhalte als problematisch, wenn sie wie unoriginelle Template-Massenware wirken. citeturn18view0

### Thumbnails

Meine wichtigste Regel:

> **AI sollte bevorzugt das Thumbnail planen und bearbeiten helfen – nicht automatisch ein komplett synthetisches Thumbnail erfinden.**

Für Personen oder Gaming-Creator würde ich möglichst mit **echten Frames beziehungsweise echten Fotos** arbeiten.

Die KI bekommt beispielsweise:

```text
SOURCE SUBJECT: creator_face_03.png
SOURCE GAMEPLAY: boss_frame_17.png
BRAND FONT: ...
BRAND COLORS: ...
VIDEO PROMISE: ...
```

und den Prompt:

```text
Develop three thumbnail concepts.

Do NOT regenerate the creator's face.
Do NOT change facial identity.
Do NOT invent gameplay.
Do NOT add fake UI.
Do NOT add objects not supported by the video.

Use the supplied source images as immutable evidence.

Each concept may contain:
- one primary subject
- one secondary visual idea
- at most one short text element if genuinely necessary

Prioritize:
1. immediate readability at mobile size
2. clear visual hierarchy
3. one unanswered question
4. strong subject/background separation
5. accurate representation of the video

Avoid:
- AI glow
- plastic skin
- impossible lighting
- excessive arrows
- excessive circles
- meaningless facial exaggeration
- tiny details
- fake 3D text
- generic YouTube clickbait composition

For each concept provide:
- composition
- crop
- background treatment
- expression/frame selection
- text if any
- reason it creates curiosity
- exact Photoshop/Canva editing instructions

Do not generate the final image unless explicitly requested.
```

Dann erzeugst du drei **inhaltlich unterschiedliche** Varianten, nicht:

```text
Version A: Gesicht 3 % größer
Version B: Gesicht 5 % größer
Version C: Gesicht 7 % größer
```

YouTube empfiehlt bei seinen A/B-Tests ebenfalls echte Unterschiede zwischen den Varianten; bis zu drei Kombinationen lassen sich gegeneinander testen. citeturn14view1

Gute Konzepte wären zum Beispiel:

**Proof:** tatsächlicher Endzustand sichtbar.

**Stakes:** unmittelbar sichtbares Problem.

**Curiosity:** ungewöhnliche Situation ohne vollständige Auflösung.

### Titel

Die Formel sollte nicht „maximal emotional“, sondern sein:

> **konkret + Spannung + wahr**

Beispiel Gaming:

**Slop:**

> „DIESER BUILD IST ABSOLUT INSANE 🤯🔥 YOU WON'T BELIEVE THIS“

**besser:**

> „Dieser 1‑HP‑Build hat den Boss tatsächlich überlebt“

Oder:

> „Ich habe den Boss ohne Heilung versucht“

Corporate-Slop:

> „Unlocking the Power of AI: A Game-Changing Revolution“

besser:

> „Warum wir unseren KI‑Workflow wieder vereinfacht haben“

oder:

> „Diese Automatisierung hat unseren Support langsamer gemacht“

YouTube erlaubt Titel mit bis zu 100 Zeichen; wichtiger als das Ausreizen dieses Limits ist aber, dass Thumbnail, Titel und tatsächlicher Einstieg dieselbe Erwartung erzeugen. Das wird auch durch YouTubes Retention- und A/B-Test-Dokumentation gestützt. citeturn14view0turn14view1

Eine sinnvolle Titel-KI sollte deshalb beispielsweise zwölf Varianten nicht einfach nach „Catchiness“, sondern nach verschiedenen Hypothesen gruppieren:

```text
4 × Outcome
4 × Stakes
4 × Curiosity
```

und bei jedem Titel angeben:

```text
Promise:
Evidence in video:
Potential mismatch:
Thumbnail complement:
```

Das letzte Feld ist besonders gut, weil Titel und Thumbnail **nicht exakt dasselbe sagen müssen**.

Beispiel:

```text
Thumbnail:
"1 HP"

Title:
"Der schlechteste Build, mit dem ich je gewonnen habe"
```

statt:

```text
Thumbnail:
"1 HP BUILD"

Title:
"DER 1 HP BUILD IST KOMPLETT KRASS"
```

### Beschreibung und Hashtags

Auch hier: kein Text aufblasen.

Eine gute Social-/YouTube-Beschreibung beginnt eher so:

```text
Wir wollten testen, ob der Build ohne Heilung überhaupt
durch den Bossfight kommt. Das Ergebnis war knapper als gedacht.

Kompletter Build + Updates:
[CTA]

Full Run:
[...]

Music / Credits:
[...]
```

statt:

> „Willkommen zu einem weiteren aufregenden Video! Heute tauchen wir tief in die faszinierende Welt von …“

Tags spielen laut YouTube gegenüber Titel, Thumbnail und Beschreibung nur eine untergeordnete Rolle und sind insbesondere bei häufig falsch geschriebenen Begriffen hilfreich. Auch übermäßiges Hashtagging ist keine sinnvolle Strategie; YouTube ignoriert bei sehr starkem Hashtag-Spam sogar sämtliche Hashtags eines Inhalts. citeturn9search7turn9search0

Deshalb würde ich standardmäßig **nur wenige, eindeutig relevante Hashtags** generieren und jedes einzelne begründen lassen.

```text
#GameName      – direkte Themenzuordnung
#ChallengeRun  – Format
#BossName      – konkrete Suchintention
```

Nicht:

```text
#viral #fyp #trending #video #gaming #fun #cool #wow
#explore #mustwatch #contentcreator ...
```

### Captions

Captions sind eine **Lesbarkeitsschicht**, keine Weihnachtsbeleuchtung.

Regeln:

```text
maximal 1–2 Zeilen gleichzeitig
↓
sinnvolle Phrase statt jedes einzelne Wort
↓
nur Schlüsselwort hervorheben
↓
keine fünf Farben
↓
Text nicht über Gameplay-HUD oder Gesicht
↓
Interpunktion und Eigennamen prüfen
```

Die AI-Transkription muss bei Gaming insbesondere Namen prüfen:

```text
Champions
Items
Maps
Weapons
Abilities
Player names
Game-specific slang
```

Dafür sollte pro Game ein Wörterbuch existieren:

```yaml
game_dictionary:
  canonical_terms:
    - ...
  player_names:
    - ...
  abbreviations:
    - ...
```

Das ist ein erstaunlich effektiver Qualitätsunterschied zwischen billigem Auto-Captioning und professioneller Produktion.

### Render und technische Ausgabe

Für ein universelles Social-Master würde ich als Ausgangspunkt **1080 × 1920, 9:16** nutzen und anschließend Plattformvarianten ableiten. TikTok empfiehlt vertikales 9:16, mindestens 720p und ausreichend Abstand zur UI. citeturn15view3

Für YouTube empfiehlt Google unter anderem MP4, H.264, progressive Verarbeitung, AAC-LC/Opus-Audio mit 48 kHz und das Beibehalten der Frame Rate des aufgenommenen Materials. Ein mit 30 fps aufgenommenes Video sollte also nicht bloß des Marketings wegen künstlich auf 60 fps gebracht werden. citeturn18view6

Eine vernünftige Social-Vorgabe:

```yaml
delivery:
  resolution: 1080x1920
  aspect_ratio: "9:16"
  codec: "H.264"
  container: "MP4"
  scan: "progressive"
  audio_sample_rate: "48 kHz"
  frame_rate: "same as source unless editorially justified"

captions:
  burned_in: true
  sidecar_srt: true

qc:
  inspect_full_video: true
  mobile_safe_zones: true
  spelling_check: true
  hud_check: true
  audio_check: true
  rights_check: true
  metadata_check: true
```

YouTube Shorts können inzwischen bei vertikalem beziehungsweise quadratischem Format bis zu drei Minuten lang sein. citeturn18view7 Das bedeutet allerdings nicht, dass jeder Short drei Minuten dauern sollte: Die Story bestimmt die Länge.

### Musik, Memes und Rohmaterial

Bei Musik solltest du niemals den Agenten einfach „such irgendeinen trending Song“ machen lassen.

Für YouTube stellt die YouTube Audio Library Produktionsmusik und Soundeffekte bereit, die YouTube als copyright-safe für YouTube bezeichnet; manche Creative-Commons-Titel erfordern Attribution. YouTube weist zugleich ausdrücklich darauf hin, dass es keine entsprechende Garantie für beliebige „royalty-free“-Bibliotheken Dritter gibt. citeturn15view7

Für kommerzielle TikTok-Inhalte existiert die Commercial Music Library; TikTok beschreibt diese Bibliothek als für kommerzielle TikTok-Nutzung vorab geklärt, während Songs aus der allgemeinen Musikbibliothek nicht automatisch für Brand-/Business-Content freigegeben sind. citeturn20search2turn20search5

Deshalb würde ich Musikrechte **plattformbezogen** speichern:

```yaml
asset:
  id: music_023
  source: "..."
  license_owner: "..."
  allowed:
    youtube: true
    tiktok: false
    instagram: true
    paid_ads: false
    client_commercial: true
  attribution_required: false
  proof_of_license: "licenses/music_023.pdf"
```

Nicht:

```text
folder/
  cool_music_final2.mp3
```

Das Gleiche gilt für Memes.

Für Unternehmen würde ich keine zufällig von Google/TikTok heruntergeladenen Memeclips zur Standardbibliothek machen. Besser ist ein **eigenes Reaction-/Meme-Vocabulary**:

```text
freeze frame
hard cut
record scratch
dramatic silence
UI error
camera punch
known client reaction
text card
game-specific visual gag
```

Damit bekommst du Meme-Energie, ohne jedes Video mit fremdem viralen Footage vollzukleben.

Pexels ist beispielsweise eine Option für ergänzendes Stockmaterial; dessen Lizenz gestattet grundsätzlich kostenlose Nutzung und Bearbeitung, enthält aber Einschränkungen etwa hinsichtlich irreführender Endorsement-Darstellungen erkennbarer Personen. Für Kundenarbeit sollte die Lizenz des konkreten Assets trotzdem dokumentiert werden. citeturn6search2turn6search8

### AI-Kennzeichnung

AI-unterstützter Schnitt bedeutet auf YouTube **nicht automatisch**, dass ein Video als AI-generiert gekennzeichnet werden muss. YouTube nennt unter anderem AI-Unterstützung für Outline, Skript, Thumbnail, Titel, Captions, Schärfung und Audio-Reparatur als Beispiele für Hilfstätigkeiten beziehungsweise kleinere Bearbeitungen, die für sich keine Offenlegung verlangen. Realistische, wesentlich synthetisch erzeugte oder veränderte Darstellung – etwa wenn eine reale Person etwas zu sagen scheint, was sie nie gesagt hat – muss dagegen offengelegt werden. citeturn19view3turn19view4

TikTok verlangt ebenfalls die Kennzeichnung realistischer AI-generierter Inhalte und fordert Creator auf, vollständig generierte beziehungsweise wesentlich per AI veränderte Inhalte entsprechend zu markieren; automatische Kennzeichnungen können unter anderem anhand von Content Credentials erfolgen. citeturn20search0

Das führt zu einer einfachen internen Regel:

```text
AI hat gesucht, geschnitten, transkribiert,
reframed oder Text vorgeschlagen?
→ normalerweise Produktionsassistenz.

AI lässt eine echte Person etwas tun/sagen,
was nicht passiert ist?
→ Kennzeichnungs-/Freigabeprüfung.

AI erzeugt realistisch wirkende Ereignisse?
→ Kennzeichnungs-/Freigabeprüfung.
```

### Datenschutz bei Firmenkunden

Für deutsches beziehungsweise europäisches B2B würde ich zudem eine klare **„approved AI tools“-Liste** führen. Kundeninterviews, Mitarbeiteraufnahmen, interne Screens, noch nicht veröffentlichte Produkte und NDA-Material gehören nicht automatisch in irgendeinen Consumer-AI-Dienst.

Wo ein externer Dienst personenbezogene Daten als Auftragsverarbeiter verarbeitet, bestehen nach dem europäischen Datenschutzrahmen entsprechende Anforderungen an die vertragliche Ausgestaltung und Garantien; für bestimmte internationale Übermittlungen können unter anderem Standardvertragsklauseln relevant sein. citeturn11search7turn11search4

Operativ heißt das:

```text
LOCAL
faster-whisper
FFmpeg
lokale Proxies
lokale Asset-Analyse

APPROVED CLOUD
nur freigegebene SaaS-Anbieter
AVV/DPA dokumentiert
Kundenfreigabe
Retention-Regeln

FORBIDDEN
NDA-Footage einfach in irgendein neues AI-Webtool ziehen
```

Gerade damit kannst du dich als professioneller Anbieter von „ein Typ mit CapCut und ChatGPT“ unterscheiden.

## Geschäftsmodell und praktische Umsetzung

Ich würde **nicht Videoschnitt pro KI-Minute verkaufen**.

Die Automatisierung ist dein interner Effizienzvorteil. Der Kunde bezahlt für:

> **zuverlässige Content-Produktion + konsistenten Brand-Look + bessere Auswahl des Materials + schnelle Iteration + Plattform-Fitness + messbares Lernen.**

Daraus lassen sich drei Produkte bauen.

**Brand Video System Setup**

Einmaliges Setup:

```text
Kickoff
+ 2–3 Referenzvideos
+ Brand Assets
+ Zielgruppenanalyse
+ Funnel
+ Brand Video Spec
+ Caption System
+ Overlay System
+ CTA System
+ Thumbnail System
+ Prompt/Skill
+ QC-System
```

Das ist der wichtigste Teil, weil er nachfolgende Produktionsarbeit standardisiert.

**Content Engine / Clip Factory**

Aus beispielsweise Streams, Podcasts, Interviews oder Gaming-VODs:

```text
Ingest
→ Transcript
→ Highlight Mining
→ Auswahl
→ X Clips
→ Captions
→ Sound
→ Plattformexport
→ Titel
→ Beschreibung
→ Thumbnail falls erforderlich
→ QC
```

Dabei sollte der Vertrag sehr eindeutig definieren:

```text
Input-Stunden pro Monat
Anzahl fertiger Clips
Plattformvarianten
Revisionen
Thumbnail-Anzahl
Untertitelsprachen
Publishing ja/nein
Musiklizenzen
Rohprojektübergabe ja/nein
Analytics-Auswertung ja/nein
```

**Performance Retainer**

Das ist langfristig wahrscheinlich das wertvollste Produkt.

Nicht nur:

> „Hier sind deine 20 Clips.“

Sondern:

```text
20 Clips
+
Hook-Experimente
+
Thumbnail-/Titel-Tests
+
Retention Review
+
CTA-Tracking
+
monatliche Anpassung des Brand Video Systems
```

YouTubes native Titel-/Thumbnail-A/B-Tests können hierfür bis zu drei Varianten vergleichen und wählen die erfolgreiche Variante nach Watch-Time-Anteil. citeturn14view1

Der Kunde kauft damit ein lernendes System.

**Die Kalkulation würde ich nicht von irgendeinem Fiverr-Marktpreis ableiten**, sondern von deiner tatsächlichen Produktion:

```text
Kundenkosten =
Editorzeit
+ Creative Direction
+ Review
+ Projektmanagement
+ SaaS/API
+ GPU
+ Assets/Lizenzen
+ Revision-Risiko
+ Reporting
```

Dann:

```text
Preis – Kundenkosten = Deckungsbeitrag
```

AI soll vor allem folgende Kostenblöcke drücken:

```text
manuelles Sichten
Transkribieren
Rohschnitt
Versionierung
Captions
Reframing
Media Search
Metadatenentwürfe
Render-Automatisierung
```

**nicht**:

```text
Geschmack
Storyverständnis
Humor
Markenpositionierung
Faktenprüfung
Final QC
```

Das ist auch genau der Teil, bei dem du dich am Markt differenzieren kannst.

Für ein Gaming-Angebot würde ich beispielsweise nicht schreiben:

> „Wir erstellen 30 AI Shorts pro Monat.“

Sondern:

> **„Wir verwandeln deine Streams in ein Social-Content-System: Wir finden die stärksten Stories aus jedem VOD, schneiden plattformfertige Clips in deinem Stil und optimieren Hooks und CTAs anhand deiner tatsächlichen Performance.“**

Für Unternehmenscontent:

> **„Aus Webinaren, Founder-Aufnahmen, Produktdemos und Interviews entsteht ein wiederholbares Short-Form-System mit verbindlichem Brand Design, Freigabeprozess und plattformfertigem Output.“**

Das klingt auch nicht nach Billig-Automation.

Der strategisch stärkste Endzustand wäre folgende Architektur:

```text
                        CLIENT
                          │
                  Brand / Audience
                          │
                          ▼
                 BRAND VIDEO SPEC
                          │
              ┌───────────┴────────────┐
              │                        │
              ▼                        ▼
        RAW MEDIA                  PERFORMANCE
              │                        │
              ▼                        │
     PROXY + TRANSCRIPTION             │
              │                        │
              ▼                        │
        CONTENT ANALYSIS               │
              │                        │
              ▼                        │
        CLIP CANDIDATES                │
              │                        │
              ▼                        │
       EDITORIAL SCORING               │
              │                        │
              ▼                        │
        HUMAN SELECTION                │
              │                        │
              ▼                        │
   ┌──────────┼────────────┐           │
   │          │            │           │
   ▼          ▼            ▼           │
Premiere   Resolve      Remotion       │
   │          │            │           │
   └──────────┴─────┬──────┘           │
                    ▼                  │
               HUMAN QC                │
                    │                  │
                    ▼                  │
            SOCIAL DELIVERIES          │
                    │                  │
                    ▼                  │
            TITLES / THUMBNAILS        │
                    │                  │
                    ▼                  │
                 PUBLISH               │
                    │                  │
                    ▼                  │
                ANALYTICS ─────────────┘
```

Und daraus ergibt sich auch die sinnvollste **GitHub-/Skill-Basis** für dein Projekt:

| Baustein | Nutzen |
|---|---|
| `agentskills/agentskills` | Grundformat für deinen eigenen Editing Skill; `SKILL.md`, Scripts, References und Assets sind im Format vorgesehen. citeturn14view6 |
| `remotion-dev/skills` | offizielles Beispiel für Agent Skills rund um programmatische Remotion-Videoproduktion. citeturn17view4 |
| Remotion | Brand-Komponenten, Captions, CTAs, Motion Graphics und reproduzierbare Renderings über React. citeturn16view7 |
| `SYSTRAN/faster-whisper` | lokale Transkription inklusive Wort-Zeitstempeln. citeturn17view0 |
| `WyattBlue/auto-editor` | automatische technische Rough Cuts nach Audio/Bewegung und Export in professionelle Editoren. citeturn16view3 |
| `mifi/lossless-cut` | schnelles Vorsortieren großer VODs und EDL-basierte Schnittübergabe. citeturn17view2turn17view3 |
| FFmpeg | technische Medienpipeline hinter Proxies, Crops, Audio, Encoding und Batch-Rendering; für YouTube kann der finale Encoder an dessen H.264/MP4-/48-kHz-Vorgaben ausgerichtet werden. citeturn18view6 |

Community-Skills würde ich dabei **als Code behandeln, nicht als vertrauenswürdige Prompt-Dateien**: Scripts vor Ausführung lesen, Abhängigkeiten prüfen und Versionen beziehungsweise Commits festschreiben.

Die sinnvollste Reihenfolge zum Aufbau ist daher nicht „erst 50 AI-Tools testen“, sondern:

```text
Brand Spec definieren
        ↓
Anti-Slop-Regeln definieren
        ↓
Hook-/Story-System definieren
        ↓
Clip-Kandidaten-System bauen
        ↓
Transkript + Timecodes automatisieren
        ↓
Premiere/Resolve als sicheren Final Layer etablieren
        ↓
Captions/CTA/Overlays als Komponenten bauen
        ↓
SKILL.md daraus erstellen
        ↓
mit 2–3 echten Kunden-/Teststilen kalibrieren
        ↓
Performance zurückspeisen
        ↓
erst dann stärker automatisieren
```

Der zentrale Wettbewerbsvorteil ist am Ende **nicht, dass deine KI schneiden kann**. Automatisches Schneiden, Transkription, Long-to-Short, Auto-Captions und Reframing sind inzwischen Standardfunktionen in mehreren Produkten. Premiere bietet AI-Suche und Text-Based Editing, Descript transcriptbasierten Schnitt und einen AI-Co-Editor, CapCut Long-to-Short und Auto-Captions, Resolve AI-Funktionen und Scripting. citeturn15view4turn17view5turn16view2turn14view5

Der verteidigbare Teil ist vielmehr:

> **Du weißt, was die KI schneiden soll, warum sie es schneiden soll, wie der jeweilige Kunde aussehen und klingen soll, welche Elemente verboten sind, wie der Clip in einen Funnel führt und wie die nächsten Clips aufgrund realer Performance besser werden.**

Genau daraus lässt sich ein professionelles, kundenfähiges KI‑Videoschnitt-System bauen, das **nicht nach KI aussieht**, obwohl ein großer Teil der langweiligen Produktionsarbeit von KI und Automation übernommen wird.