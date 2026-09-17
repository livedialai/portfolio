# Thomas Barthel — Voice-AI-Architekt & Produktentwickler
**Bremerhaven · [weser-ai.de](https://weser-ai.de) · info@weser-ai.de**

---

## Über mich

Ich baue Sprach-KI-Produkte: vollständige, produktiv laufende Systeme für Telefonie, Call-Center, Behörden, Praxen und Vertrieb — von der SIP-Anbindung über den Sprachagenten bis zum SaaS-Portal mit Abrechnung, Deployment und Betrieb. Meine Arbeitsweise: selbst entwickelt, selbst getestet, selbst betrieben. Fertige Produkte statt Konzeptpapier.

## Kernaussage in einem Satz

> Ich entwickle KI-Telefonie, Voice-Agents und KI-Appliances entlang der gesamten Kette — Gesprächs-Architektur, Modell- und TTS-Auswahl, Telefonnetz-Anbindung (SIP/Asterisk/ViciDial/LiveKit), DSGVO-konformer Betrieb und Multi-Tenant-SaaS — als fertig lieferbare Produkte.

## Zahlen, die mein Portfolio belegen

| Kennzahl | Wert |
|---|---|
| Repositories | 147 eigenständige (von 154 gesamt: 67 GitHub + 92 Codeberg) |
| Quellcode gesamt | ~5,55 Mio. Zeilen (davon ~3,8 Mio. Zeilen eigene Entwicklung; Rest Upstream-Forks mit eigenen Anpassungen) |
| Projektfamilien | 19 (Versionsreihen je Familie) |
| Produktive Systeme | GoFonIA (SIP-Telefonanlage + LiveKit-Agent), GoDinIA (Reservierungs-SaaS mit WhatsApp-Bridge), FastCab (Taxi-Vermittlung), FanMall/FanVue (Creator-SaaS), Weser-AI-Appliances, LINGU-COP (Behörden-Dolmetscher), Star Food (Pizza-Lieferservice mit Telegram-Bestellbot) |
| Plattformen | On-Premise-Appliance · Eigene Server · Docker/PM2 · WordPress-Ökosystem · Cloud-RZ (ISO 27001) |
| Sprachen/Stack | Python, TypeScript/JS, Rust, PHP, Astro, React; SIP/RTP, LiveKit, ViciDial, Asterisk, OpenSIPS; PostgreSQL, MySQL, MariaDB, SQLite, Redis, MongoDB; LLM-APIs (OpenAI-kompatibel, DeepSeek, Mistral, Grok), STT (Deepgram, Parakeet), TTS (Inworld, Qwen3, ElevenLabs) |

## Positionierung für Bewerbungen

Gesucht: **Senior Voice-AI Engineer · AI-Telefonie-Architekt · Produktentwickler für KI-SaaS** — gerne hybrid, mit voller Architektur- und Betriebsverantwortung. Ich ersetze keine bestehende Rolle, ich baue das System dahinter auf. Anwendungsfälle: Sprachagenten in Vertrieb/Service, Telefonie-Automatisierung für Kanzleien/Praxen/Behörden, On-Premise-KI in regulierten Branchen, Aufbau von KI-Funktionsbereichen.

---

*Kontakt: Weser AI · Inhaber: Giacomo Steckhan · c/o SourceArt · Fritz-Thiele-Straße 3 · 28279 Bremen-Obervieland · [weser-ai.de](https://weser-ai.de) · info@weser-ai.de*

---

## Kernkompetenz: Voice-AI & Sprachsysteme

## Was ich vollständig selbst baue, betreibe und liefere

**KI-Sprachagenten für Telefonanlagen und Call-Center** — die komplette Kette:

- **Telefonie-Anbindung:** SIP/RTP von Grund auf — via LiveKit SIP-Bridge, OpenSIPS, Asterisk/ARI, ViciDial (Remote-Agent, Lead-Tracking) und einer eigenen, in Rust implementierten PBX (RustPBX DE, ~214.000 Zeilen; Voice-Gateway **ViciAI** als Single-Binary-SIP-Gateway, Rust, mit Lead-Qualifikation direkt über die ViciDial-API).
- **Gesprächs-Architektur:** Conversation Design, Warm-/Cold-Call-Transfer mit Ansage und Phasen-Statusmaschinen, Voicemail-Erkennung, Simultanübersetzung, Call-Timetable, CDR-Auswertung.
- **Pipeline-Integration:** STT (Deepgram Nova-3, Parakeet-TDT/ONNX, Whisper-Varianten) → LLM (OpenAI-kompatibel; DeepSeek, Mistral, xAI (Grok), OpenRouter; lokale Open-Source-Modelle) → TTS (Inworld, Qwen3-TTS, ElevenLabs, lokale Stimmen) — selbst gehostet oder API, per Adapter-Architektur austauschbar.
- **Voice UX & Emotion:** Erarbeitete Markup-Sprache für Emotion und Delivery-Style ([happy], [whispering], [breathe], [laughing], [sigh], [clear_throat]), kombiniert mit SSML-Pausen — nachweisbar gesteigerter CSAT (eigene KPI-Studie); Conversation-Design-Beratung unter voice-ux.de.
- **Wissensquellen:** RAG über pgvector (Firmenwissen, Doku, AGB), Kalender-Integration (CalDAV, Meetergo, Cal.com) für echte Terminbuchung im Gespräch.
- **Skalierung & Mandantenfähigkeit:** Multi-Tenant mit Mandanten-Auflösung, eigene DID-Nummern je Kunde, Tenant-Portale, Abrechnung (Mollie, SumUp, Stripe), API-Key-/Widget-Verwaltung.

## Belege (produktiv)

- **GoFonIA** — SaaS-SIP-Telefonanlage mit LiveKit-Voice-Agent: OpenSIPS → LiveKit-SIP-Bridge → Agent (v5, ~35.000 Zeilen aktuelle Version), Frontend React/TSX + FastAPI-Backend, 22 Router, Mollie-Billing, RAG, Voice-Clone, SIP-Debug. Vermarktet als Telefonanlage für Kanzleien, Praxen, Call-Center.
- **ViciAI / GoViCiA** — AI-Call-Center für ViciDial: Rust-SIP-Gateway, 6-Phasen-Warmtransfer auf eine Person, Agent-Maske im ViciDial-Stil, MongoDB-Backend, Supervisor-Dashboard; Deployment-Zuverlässigkeit inkl. 21 Rust-Testcases.
- **LINGU-COP** — neutraler KI-Simultanübersetzer für Polizei und Behörden: 105 Sprachen, lückenloses, zeitgestempeltes Protokoll (Original + Übersetzung), verknüpft mit Aktenzeichen, Schweizer Rechenzentrum (Infomaniak Genf, ISO 27001:2022).
- **MikroVox** — AI-Modul für MikoPBX: 6-Container-Stack (pgvector, Redis, LiveKit + SIP-Bridge, Agent), Asterisk-Loopback-Trunk, Groq-LLM, Deepgram, Inworld-TTS, Warm-/Cold-Call-Transfer — per ZIP in MikoPBX installierbar.

## Wo ich ansetze

Beratung, Architektur, Modell- und Stack-Vergleich (TTFT/Latenz, Kosten, Datenschutz), Implementierung inkl. Telefonnetz (SIP-Trunk/Nummern), Livegang, KPI-Messung und Betrieb. Deutsch- und englischsprachige Systeme (mehrere Produkte existieren zweisprachig).

---

## Kernkompetenz: KI-Appliances & Datensouveränität

## Produktlinie: Branchenspezifische KI-Appliances (Weser AI)

Ich habe ein Produktmodell entwickelt, das KI nicht als Cloud-Abo, sondern als **fertige, kundeneigene Hardware** ausliefert: die KI-Appliance (64 GB RAM, 150+ vorinstallierte Module) mit eigenen, auf Open-Source-Basis optimierten Sprachmodellen — **On-Premise, DSGVO-konform, §203 StGB-sicher**. Motto der Linie: *Fachwissen bleibt im Haus.*

**Branchen-Pakete (eigene Modul-Suiten):**

| Branche | Produkt(e) | Module |
|---|---|---|
| Recht & Kanzlei | SyltKI, Jurgo | 25 |
| Medizin & Praxis | HippoCube | 25 |
| Pflege & Soziales | CareCube | 44 |
| Bildung & Versicherung | LernCube, InsuCube | 25+ |
| Behörden/Polizei | LINGU-COP (Simultanübersetzer, 105 Sprachen) | 20+ |

**Deployment-Optionen:**
- **Appliance (Cube):** Mini-PC, Strom + Netzwerk, sofort erreichbar unter `firma-ai.local` — für Einzelstandorte.
- **Managed Server:** dedizierter Server im ISO-zertifizierten Rechenzentrum (Waller Kriegsbunker, Bremen).
- **Demoklauf:** kostenlose Demo-Cloud je Branche → individuelles Angebot → Lieferung/Go-live.

## Datenschutz als Verkaufsargument — und als Architekturprinzip

- Kein US-Cloud-Anbieter, kein Datentransfer ins Ausland; Modelle laufen ausschließlich auf Kundenhardware in Deutschland (optional Schweizer RZ, Tier III+, ISO 27001:2022, Venenerkennung statt Passwörter, N+1-Redundanz).
- SwornTranslate/Doctranslate-Erfahrung: strukturerhaltende KI-Dokumentenübersetzung (OCR + Übersetzungsbackend).
- §203-StGB-konforme Verarbeitung für Kanzlei- und Praxisdaten.
- Wi-Fi-souveräne Sonderthemen: meetergo-Sovereignty-Scan-Proxy (Landing-Analyse der Souveränität von Anbietern), Vertex-AI-OpenAI-Proxy (ADC), Locale-Erweiterungen (DE/EN/ES/RU).

## Beispiele komplexer Sonderprojekte

- **LINGU-COP** (lingucop.weser-ai.de): KI-Dolmetscher für Vernehmungen/Erstbefragungen — strukturell neutral (keine menschliche Agenda, kein Befangenheitsvorwurf): Protokoll mit Zeitstempel, Aktenzeichen-Verknüpfung, Dashboard, 105 Sprachen; Live-Demos für Polizei, Ausländerbehörden, Zoll, JVA, Rettungsdienste.
- **Voice-UX-KPIs:** messbare CSAT-Wirkung von Emotion-/Delivery-Styles in Voice-Dialogen (eigene KPI-Studien DE/EN, voice-ux.de).

## Warum das zählt

Ich beherrsche beides: die **technische Tiefe** (von SIP-Trunk bis Modell-Finetuning) und die **Produktlogik** (was Kanzleien, Praxen, Pflegeeinrichtungen kaufen, ohne IT-Abteilung). Daraus entstehen verkaufbare, lieferbare, wartbare Systeme — keine Labormuster.

---

## Kernkompetenz: SaaS- & Plattform-Engineering

## Ich entwickle Multi-Tenant-Produkte mit kompletten Zahlungs- und Betriebsflüssen

Neben der Telefonie-Achse stehen eigene SaaS-Produkte in Betrieb — jeweils mit Frontend, API, Abrechnung, WhatsApp-/E-Mail-Kanälen und Deployment:

**GoDinIA — Reservierungs-SaaS für Restaurants (produktiv, mit Kunden)**
- Tisch-/Zeitreservierung über WordPress-Plugin (Shortcode/Widget), Multi-Tenant, Mollie-Billing, Lizenz-Verwaltung (AES-256-Lizenzen).
- **WhatsApp-Bridge:** Benachrichtigungen laufen über eine eigene Node-Bridge (Bridge-Key-Auth) zur Waxum-Session (Backend auf eigener Infrastruktur) — im Produktivbetrieb mit echten Restaurantkunden verifiziert; Ziffern-Menü-Cockpit (Anmeldung, Status, Fahrtgast etc.).
- Kassensystem-Add-on (Bondrucker-Integration, OrderSprinter) und WP-Module (Reservierungs-Widget, Tischreservierung).

**FastCab — Vermittlungsplattform für Mietwagenfahrten (MVP live, fastcab.eu)**
- Fahrgast-Portal (PWA), Fahrer-App, WordPress-Backend als Admin-Schicht, Node-Express-Worker als API; Dispatch-Matching (Haversine-Radius + ETA-Sortierung + Stammfahrer-Priorität), WhatsApp-Angebote, Trip-Statusmaschine, Zahlung via Mollie/SumUp, km-Staffelpreise.

**GoFonIA — KI-Telefonie-SaaS** (s. Abschnitt „Voice-AI & Sprachsysteme“), inkl. Support-Tools: Tenant-Portal (Kundencenter), Affiliate-/Partner-System mit WP-Plugin, Betriebs- und Backup-Skripte.

**Creator-Stack (FanMall/FanVue)** — Plattform für Creator-Marketing: Webhooks, OAuth2+PKCE, HMAC-Signaturen, Autogramm-Rendering, Bild- und Vault-Verwaltung, PPV-Strategie-Engine, Telegram-Admin-Konsole (rollenbasierte Zugänge), mehrsprachige Landingpages (DE/EN/ES/FR/IT), Paketpreis-Modell 29/79/149 €.

## WordPress & Web-Ökosystem

- Umfangreiche **WP-Plugin-Suite** (eigene Produkte): Voice-Agent-Plugin mit DID-Zuweisung, Prompt-Editor, Billing; LiveKit-Voice-Widget; SIP-Webphone (SIP.js); Jambonz/mistral-Plugin; Grok-Speech-Plugin; Google-Reviews-Slider; Cloud-Plugin; CalDAV-Buchungs- und Kalender-Widgets (Infomaniak, Meet.bot); gobookme-Plugin-Suite (Buchungs- und Aktivierungs-Endpunkte, Affiliate-Tracking).
- **Öffentliche KI-Editoren für WordPress (GitHub):** [**WP AI Edit**](https://github.com/livedialai/wp-ai-edit) — KI-Chat direkt im WordPress-Backend: Seiten befüllen, Einstellungen und Plugins ändern, fremde Designs als Vorlage einlesen; inkl. Agentur-Fernzugriff über Anwendungspasswörter und Protokoll. [**WP Agency Edit**](https://github.com/livedialai/wp-agency-edit) — die Zentrale dazu: betreute Kunden-Websites hinterlegen und per KI-Chat von einer Stelle aus bearbeiten; das Sprachmodell läuft zentral.
- Frontend-Architektur: Astro/React/TSX, Alpine.js/Tailwind; static builds, mehrsprachig (Polylang-fähig, bis 10 Sprachversionen).

## DevOps & Betrieb — bis zum lauffähigen Betrieb

- Deployment: PM2 (Node/Python), Docker-Compose-Stacks, Nginx/Let's Encrypt, Apache-Setups, Systemd-Services, Cron, Backup-Skripte, Monitoring (SSE-Dashboards, CDR-Auswertung).
- Daten: PostgreSQL, MySQL/MariaDB (ViciDial/WordPress), Redis, SQLite, MongoDB, pgvector.
- Sicherheits-Hygiene: verschlüsselte Lizenzen, Secret-Handling per Env, HMAC-Verifikation, HTTPS-only, EU-Datenschutz-Fokus — inkl. einer geführten Sicherheitsprüfung des eigenen Codes (u.a. hartkodierte Keys gefunden & an Kunden-Agentur gemeldet).

## Arbeitsweise

Entwicklung mit KI-Beschleunigung (4–8×), aber mit eigener Architektur-Verantwortung: Datenmodelle, Migrationspfade, API-Verträge, Zahlungsflüsse und Betrieb werden entworfen, nicht zusammengeschraubt. Projekte werden mit 2 Geräten getestet (Desktop + mobil, PWA statt native App), Feature-Wünsche lieferfertig umgesetzt — inkl. Doku (deutsch/englisch).

---

## Nachweis & Handwerk — Fakten, Stack, Referenzen

## Leistungsnachweis (Stand 09/2026, aus eigenem Quellcode-Archiv gemessen)

- **147 eigenständige Repositories** (von 154 gesamt) auf GitHub + Codeberg, konsolidiert in **19 Projektfamilien** — die Dokumentation dazu liegt als Portfolio vor (PORTFOLIO.md).
- **~5,55 Mio. Zeilen Quellcode**, davon ~3,8 Mio. eigene Entwicklung und eigene Anpassungen (Rest: Upstream-Forks).
- **Produktivbetrieb:** SIP-Telefonanlage mit KI-Agent, Reservierungs-SaaS mit WhatsApp, Taxi-Vermittlung, Creator-SaaS, KI-Appliances, Behörden-Übersetzer — mehrere Systeme parallel live, mit echten Kunden und Zahlungsverkehr.
- **Sprachversionen der Systeme:** DE/EN/ES/RU; WP-Websites mehrsprachig.
- **Content-Systeme:** **pizzafamily.de** — 10 Sprachvarianten (de, en, fr, it, pl, ro, ru, tr, uk, ar), **~950 Seiten**, WordPress/Polylang, komplett im letzten Monat aufgebaut (per Sitemap verifiziert).

## Tech-Stack (Nachweis im Code, keine Verallgemeinerungen)

| Ebene | Technologien |
|---|---|
| **Sprachen** | Python (FastAPI/Flask), TypeScript/JavaScript (Node, Next.js, React, Astro, Alpine.js), Rust (PBX, SIP-Gateway), PHP (WordPress, Laravel-artige Backends), Shell |
| **Telefonie** | LiveKit (Server + SIP-Bridge), Asterisk/ARI, ViciDial, OpenSIPS, Jambonz, Fonoster, SIP.js/JsSIP, RustPBX (eigen, in Rust), SIP-Trunk-Anbindung |
| **AI/ML** | Optimierte Open-Source-LLMs (lokal, On-Premise), OpenAI-kompatible APIs (DeepSeek, Mistral, xAI/Grok, OpenRouter), RAG (pgvector), STT (Deepgram Nova-3, Whisper/Parakeet ONNX), TTS (Inworld, Qwen3, ElevenLabs), Emotion-/SSML-Markups, Simultanübersetzung, Voice-Clone |
| **Daten** | PostgreSQL, MySQL/MariaDB, MongoDB, Redis, SQLite, pgvector |
| **Betrieb** | PM2, Docker-Compose, Nginx/Let's Encrypt, Apache, Systemd, Cron, Backup-Automation, Monitoring (SSE-Dashboards), WordPress-Ökosystem (Polylang) |
| **Zahlung** | Mollie, SumUp, Stripe (Anbindungen live bzw. geprüft) |

## Referenzprojekte (Auswahl, alle produktiv bzw. mit Live-Beweis)

1. **GoFonIA** — Multi-Tenant-KI-Telefonanlage (OpenSIPS + LiveKit + Agent, RAG, Mollie-Billing, Tenant-Portal) → *Betrieb auf eigenen Servern, Kunden-Agentur-Vertrieb*.
2. **LINGU-COP** — Behörden-Simultanübersetzer (105 Sprachen, Protokoll, Schweizer RZ) → *Demo-Betrieb für Dienststellen*.
3. **GoDinIA** — Restaurant-Reservierung mit WhatsApp-Bridge (Waxum) and Bondrucker-Kasse → *Kunden-Betrieb (Restaurant)*.
4. **Weser-AI-Appliances** — Branchen-Cubes (Recht/Medizin/Pflege/Bildung) mit 25–44 Modulen, §203-StGB-konform → *Produktlinie [weser-ai.de](https://weser-ai.de)*.
5. **FastCab** — Mietwagen-Vermittlung (Dispatch, PWA, Zahlung) → *live unter fastcab.eu*.
6. **Star Food** (starfood.pizza) — Pizza-Lieferservice: WooCommerce-Shop mit entfernungsbasierten Lieferzonen (Geoapify-Plugin, 5 Zonen + Mindestbestellwert) und Telegram-Bestellbot (telegraf, WooCommerce-REST, COD + SumUp) → *live unter starfood.pizza*.

## Kontakt

**Weser AI · Inhaber: Giacomo Steckhan**
c/o SourceArt · Fritz-Thiele-Straße 3 · 28279 Bremen-Obervieland
📧 info@weser-ai.de · 🌐 [weser-ai.de](https://weser-ai.de)

*Vollständige Portfolio-Dokumentation: [PORTFOLIO.md](PORTFOLIO.md) · Unterlagen auf Anfrage.*