# GoFonIA / livedialai — Entwicklungs-Portfolio

**Stand:** 2026-09-02 · **Erstellt:** Hermes Agent · **Umfang:** alle Repositories von Giacomo (GitHub `livedialai` + Codeberg `gofonia`)

## Überblick

| Kennzahl | Wert |
|---|---|
| Repositories gesamt | 154 (67 GitHub, davon 29 privat · 92 Codeberg) |
| Eigenständige, analysierte Repos | 147 (Dubletten-/Versionsreihen zusammengefasst) |
| Codezeilen gesamt (LOC, Quellcode) | 5,555,206 |
| Plattformen | SIP-Telefonie · KI-Sprachagenten · Voice-AI · E-Commerce/Buchungs-SaaS · Bots |

## Inhaltsverzeichnis

1. **GoFonIA Telefonie-Plattform (Kernprodukt)**
2. **GoFonIA Web/Frontends & Landings**
3. **LiveDial & VICIdial-Installation**
4. **ViciAI / Vicidial-LiveKit-Agents**
5. **Dograh AI-Sprachagent**
6. **FanVue / Goddess-Nora Creator-Stack**
7. **FanMall Creator-SaaS**
8. **GoDinIA Restaurant-Booking-Suite**
9. **ToMeetIA / GoMeetMe Meeting-Suite**
10. **WordPress-Telefonie-Plugin-Suite**
11. **RustPBX & HiPIA-Appliance**
12. **Capellia AI Call Center**
13. **Forks & lokale Deployments externer OSS**
14. **MikroVox MikoPBX-Modul**
15. **TTS/STT & Speech-Infrastruktur**
16. **Bots, Scraper & Automatisierung**
17. **LiveKit-Agenten & Voice-Werkzeuge**
18. **CallID — AI Call Center Plattform**
19. **FastCab Taxi-Vermittlung**
20. Anhang A: Weitere Forks externer OSS-Projekte

---

## 1. Methodik
- Alle Repos wurden über GitHub-API (Token) und Codeberg-API erfasst und lokal geklont (`git clone --depth 1`).
- Codeumfang gemessen mit **pygount 3.2** (nur `sourceCount` = Quellcode-Zeilen; Markdown/Leerzeichen/Dokumentation nicht mitgezählt).
- Funktionen wurden aus README, Quellcode, Verzeichnisstruktur und Deployment-Artefakten abgeleitet — viele Repos sind **Server-Backups/Snapshots ohne README**; dort dient der tatsächliche Inhalt als Quelle.
- Dubletten & Versionsreihen wurden je Familie zusammengeführt; beschrieben wird jeweils die **am weitesten entwickelte** Version, die übrigen sind als Versionen/Snapshots tabelliert.

---

# GoFonIA Telefonie-Plattform (Kernprodukt)

**Kategorie:** Eigenentwicklung (Produktkern inkl. Versions-/Backup-Snapshots)
**Plattform(en):** GitHub livedialai (private Hauptentwicklung) und Codeberg gofonia (Snapshot-Ableger)
**Kanonisches Repository:** `20260801` (Codeberg) — GoFonIA v5, Server-Backup vom 01.08.2026
**Umfang kanonisch:** 35.174 LOC, 607 Dateien (Quelle: loc_results.json)

## GoFonIA v5 (20260801) — 1-Satz-Kurzcharakteristik
GoFonIA ist eine SaaS-Telefonieplattform, auf der KI-Sprachagenten (LiveKit Agents) eingehende SIP-Anrufe selbstständig annehmen: firmenwissen-basiert Auskunft geben, Meetergo-Termine buchen und Anrufe per Warm-/Blind-Transfer mit Ansage an Menschen durchstellen — Anrufverarbeitung komplett über OpenSIPS-SIP-Proxy → LiveKit SIP Bridge → LiveKit Server, ohne Asterisk.

**Standort:** Codeberg (Snapshot-Repo `20260801`, codeberg.org/gofonia) · **Typ:** Eigenentwicklung
**Sprachen:** Python, TSX, CSS, Markdown · **Codeumfang:** 35.174 LOC, 607 Dateien · **Letzter Push:** 01.08.2026 (einzelner Backup-Commit „Add OpenSIPS documentation + update README for v5 (Asterisk removed) + config files")

**Funktionsumfang:**
- **Telefonie v5:** OpenSIPS 3.6 als SIP-Proxy (Port 5080) vor der LiveKit SIP Bridge (5070) und dem LiveKit Server (7880) — laut `docs/OPENSIPS.md` wird Asterisk nicht mehr benötigt; der Asterisk-Container sowie `asterisk/` mit 594 Dateien (musiconhold.conf, voicemail-sender.sh, Dockerfile) sind Altbestand aus der v4-Linie.
- **KI-Sprachagent** (`enhanced_agent.py`, 50 KB, LiveKit Agents): AgentSession, Silero-VAD, STT Deepgram (nova-3), TTS Inworld/Voxtral mit Emotions-Voice-Mapping (optional Cartesia, Mistral, OpenAI), OpenAI-kompatibles LLM (Default `qwen3`); Redis-Call-Sessions, JWT, Caller-ANI/DID-Parsing.
- **Agent-Tools (aus Code):** `firmenwissen` (RAG-Wissensabfrage, pgvector-Vektorsuche über OpenAI-kompatible Embedding-API, standardmäßig Mistral `mistral-embed`), `check_meetergo_availability`/`book_meetergo_appointment` (Terminbuchung), `weiterleitungen_abrufen` (Rufumleitungen), `verbinden` (Transfer mit Ansage), `durchstellen` (Blind-Transfer), `execute_api_tool` (Mandanten-API-Tools), `generate_summary`/`send_summary_email` (Anruf-Zusammenfassung per E-Mail).
- **Warm Transfer Controller v4.0** (`warmtransfer/controller_v3.py`): Single-Room-Phasenmodell (caller, AI-Agent, Music-Bot und Chef in einem LiveKit-Room), Publisher-/Receiver-Subscription-Matrix; REST-API `POST /v1/transfer`, `/v1/briefing-complete`, `/v1/cancel`, `/v1/room-end` sowie LiveKit-Webhook-Receiver; dazu Music-on-Hold-Bot (`warmtransfer/music_bot.py`, `music_hold.wav`) und System-/Verkaufs- und Entscheider-Prompts.
- **Backend (FastAPI, 22 Router in `backend/main.py`):** Auth (JWT), Users, SIP-Peers, Trunks, Inbound-Routes, Dashboard, CDR, Voicemail, Call-Forwarding, Contacts, Settings, Audit, SIP-Debug, Agent-Config, Company-Knowledge, Integrations, **Tenants (Multi-Tenant-SaaS)**, **Billing/Mollie**, **SaaS-Admin**, **Affiliates**, **Widget — Multi-Tenant-fähige Vertriebs-/Partner-Architektur inkl. Zahlungsanbindung (Mollie), Widget-Tokens und Affiliate-System** (`models_affiliate.py`).
- **Frontend (React/TSX, 22 Seiten):** Dashboard, Nebenstellen/Extensions (+Detail), Trunks (+Detail), Inbound-Routes, CDR, Voicemail (Player/Statistik), Kontakte, Weiterleitungen, Integrationen, Wissenspflege (Knowledge), Agent-Prompts, Einstellungen, Benutzer, Billing (Mollie), SaaS-Admin, API-Keys, SIP-Debug, Voice-Clone, AssistentV2, FAQ, Login.
- **Betrieb/Deployment:** `install.sh`/`install-cloud.sh`/`install-agent-v3.sh` (Debian 12, Docker Stack, PM2-Prozesse agent-v3/controller-v3, Nginx-Config in `nginx/gofonia.conf`, Let's-Encrypt, PostgreSQL-Backup-Cron), `scripts/` (ensure-livekit-sip-rules.py, backup-db.sh), MIGRATION.md, DEPLOYMENT.md, QUICKSTART.md, CHANGELOG.md, DOKUMENTATION.md (GonoPBX-Doku).
- **Sonstiges im Backup:** `website-astro/` (Marketing-Website), `partner-frontend/` (statisches Partner-Interface), `dashboard/`, `v41/` (älterer Repo-Snapshot, siehe unten), `releases/`, Angebots-/Verkaufs-Textdateien.

**Aufbau/Module (aus Verzeichnisstruktur):**
- `config/opensips`, `config/livekit`, `config/sip-bridge` = Kernkonfiguration v5 (OpenSIPS-Proxy, SIP-Bridge, LiveKit); `docs/OPENSIPS.md` = Architektur-/Troubleshooting-Doku
- `enhanced_agent.py` = LiveKit Voice Agent (Tools, Provider-Auswahl, Emotions-TTS)
- `warmtransfer/` = Warm-Transfer-Controller v4.0, versionierte Controller-Stände, Music-on-Hold-Bot, Prompts
- `backend/` = FastAPI-API mit `routers/` (22 Router, u. a. tenants, billing, mollie, saas, affiliates, widget, knowledge), `ami_client.py` (Asterisk AMI, Altbestand), `scripts/mollie_poll.py`
- `frontend/` = React/Vite-WebUI; `partner-frontend/` = statisches Partner-Frontend; `nginx/gofonia.conf` = Reverse-Proxy
- `database/init.sql` = Schema (users, sip_peers, extensions, cdr, system_settings, …); `asterisk/` = Altbestand aus v4
- `server.js` = WebUI-/Dashboard-Server; `install*.sh` = Installer; `v41/` = Version-Snapshot (v4-Linie) im Backup enthalten

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| `gofoniav3` | GitHub livedialai (privat) | Ältere Version: GoFonia v3 — LiveKit-WebUI-Installer mit Warmtransfer, enthielt zusätzlich Pipecat-/Jambonz-Bots und VICIdial-Installer (31.228 LOC, Push 24.05.2026) |
| `gofoniav4` | GitHub livedialai (privat) | Ältere Version: GoFonIA v4 — Asterisk-basierter Stack, Jambonz/Pipecat/VICIdial entfernt (28.022 LOC, Push 25.05.2026) |
| `gofoniasaas` | GitHub livedialai (privat) | Ältere Version: SaaS-Variante mit externer OpenAI-kompatibler Embedding-API (29.353 LOC, 21.05.2026) |
| `gofoniasaasv2` | GitHub livedialai (privat) | Ältere Version: SaaS-Variante, aktualisiert (pgvector, ANI-Durchreichung) (31.471 LOC, 22.05.2026) |
| `gofoniaai` | GitHub livedialai (privat) | Ältere Version: Variante mit pgvector-Wissenssuche (29.235 LOC, 20.05.2026) |
| `gofonia-latest` | Codeberg | v4-Snapshot „latest" (27.579 LOC, Stand 06.07.2026: Wildcard-Dialplan, docker network externalize) |
| `4.3` | Codeberg | v4-Snapshot mit OAuth-Social-Login (Google, LinkedIn, Infomaniak) (28.077 LOC, 30.05.2026) |
| `4.4` | Codeberg | Umstrukturierte Enterprise-Version v4.4: flache Struktur mit `agent_v3.py`/`agent_v33.py`/`controller_v3.py`/`music_bot.py`/`version.txt` („v4.00 PRODUKTION — Cerebras 120b, blocking transfer") (23.424 LOC, 13.06.2026) |
| `4.5` | Codeberg | v4-Snapshot mit ausgegliedertem `agent/`-Ordner (agent_v3, controller_v3, music_bot, version.sh) (30.351 LOC, 22.06.2026) |
| `5.0` | Codeberg | Snapshot der v4-Kodebasis (README noch „v4"), enthält Agent-Zeitzonen-Fix (29.047 LOC, 02.08.2026) |
| `5.1wp` | Codeberg | Snapshot der v4-Kodebasis (LiveKit-1.6.5-Agent-Fixes, Controller-Phasen-Stabilisierung, AMI-Passwort-Fix) (28.285 LOC, 23.07.2026) |
| `v4` | Codeberg | Leerer Snapshot (nur `.git`, 0 LOC) |
| `v4.1` | Codeberg | Leerer Snapshot (nur `.git`, 0 LOC) |
| `v41` | Codeberg | Snapshot der OpenSIPS-Variante (README: „GoFonIA v4.2" mit OpenSIPS + LiveKit SIP Bridge, Tenants, Booking, Widget-Token, SaaS-Admin); Updates bis 17.08.2026 (29.282 LOC) |
| `v4.2` | Codeberg | Snapshot „v4.2" (Asterisk/OpenSIPS-Mischlinie, Tenants, Mollie, Forwards); letzter Commit 23.08.2026 (Produktionsstand) (29.122 LOC) |
| `v4.4` | Codeberg | v4-Snapshot (28.124 LOC, 04.06.2026) |
| `es` | Codeberg | Sprachvariante: „GoFonIA v4.4 — Español" (SaaS-PBX mit IA, komplett in Spanisch) (29.011 LOC, 15.06.2026) |
| `gofonia-api` | GitHub livedialai (privat) | Ältere Vorgänger-Plattform: GoFonia.de Phone AI Platform (FreePBX/Asterisk + LiveKit + OpenAI, PHP-Frontends, TTS-Proxy), 17.05.2026 (5.977 LOC) |
| `gofonia_en_v1` | Codeberg | US-MVP: komplettes US-System in einem Repo (Agent + Backend + English-only-Kundencenter-Frontend + DB-Schema/Seed + Deploy), Stand 09.08.2026 (18.841 LOC) |
| `gofonia-livekit-agent` | Codeberg | Auskopplung: LiveKit Voice Agent v3.3 (agent_v3.py, controller_v3.py, music_bot.py, cdr-writer, SIP-Anbindung, Warm-/Blind-Transfer, Meetergo/CalDAV-Buchung, Tischreservierung), Stand 05.08.2026 (3.062 LOC) |
| `kundencenter` | Codeberg | Teil der Plattform: Tenant-Portal-Frontend (React 19/Vite, `package.json`-Name newapp.gofonia.de, Seiten auth/operator/tenant, Mollie-Zahlung, Forwardings), Stand 05.08.2026 (5.792 LOC) |
| `godialia` | Codeberg | **Verwandt, aber eigenständiges Produkt** (keine Version der Plattform): GoDialIA — AI-Voice-Gatekeeper für ViciDial/GOautodial-Callcenter, PHP-Frontend + Rust-Binary-Voice-Agent (343.312 LOC); s. u. |

*Begründung kanonisch:* `20260801` (GoFonIA v5) ist die am weitesten entwickelte Version: größter Codeumfang (35.174 LOC, 607 Dateien — gut 10 % mehr als der nächste Kandidat gofoniasaasv2 mit 31.471 LOC), einziger Snapshot mit OpenSIPS-Konfiguration und v5-Architektur (Asterisk entfernt, docs/OPENSIPS.md), vollständigstes SaaS-Featureset (Tenants, Mollie-Billing, SaaS-Admin, Affiliates, Widget/API-Keys, Voice-Clone, Wissens-RAG, Agent-Prompt-Pflege, SIP-Debug) und neuester Stand (Backup vom 01.08.2026, enthält zusätzlich partner-frontend, Nginx-Config, Website-Astro und den v41-Snapshot als Beleg für die direkte Abstammung). Alle GitHub-Repos (v3/v4/SaaS-Varianten) sind ältere Entwicklungsstände vom Mai 2026; die Codeberg-Repos sind Snapshots dieser Linie plus Sprachvariante (es) und Auskopplungen (Agent, Kundencenter, US-MVP).

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### gofonia-api — GoFonia.de Phone AI Platform (Vorgänger)
- Standort GitHub livedialai (privat) · Sprachen: Python, HTML+PHP, PHP, YAML · 5.977 LOC, 100 Dateien
- Ursprüngliche Platform für gofonia.de: FreePBX/Asterisk + LiveKit SIP Bridge + LiveKit Server, LiveKit-Agent (`livekit_agent/`), PHP-Frontend/Admin (`php_frontend/`, `php_admin/`), TTS-Proxy (`tts_proxy/`), App-Verzeichnis; Docker-Compose-Dev-Setup. Initial-Commit 17.05.2026; ältere Codebasis als die v3/v4-Linie.

### gofonia_en_v1 — GoFonIA US-MVP
- Standort Codeberg · Sprachen: Python, TSX, Transact-SQL, TypeScript · 18.841 LOC, 129 Dateien
- „Komplettes US-System in einem Repo": `agent/` (agent_v3, controller_v3, music_bot, cdr_writer), `backend/` (FastAPI, Docker-Image), `frontend/` (Vite/React-Kundencenter, English-only), `database/` (schema.sql, Seed, anonymize_seed.py), `deploy/`, `setup.sh`, `deploy.sh`, livekit.yaml/sip.yaml. Amerikanischer Ableger der Plattform (US-Rufnummern, EN-Benutzeroberfläche), Stand 09.08.2026.

### gofonia-livekit-agent — LiveKit Voice Agent v3.3 (Auskopplung)
- Standort Codeberg · Sprachen: Python · 3.062 LOC, 7 Dateien
- Eigenständiger KI-Telefonagent auf LiveKit Agents mit SIP-Anbindung, Warm-/Blind-Transfer über eigenen Controller, Silent-DTMF-Steuerung, Terminbuchung (Meetergo/CalDAV), Tischreservierung und mandantenfähigen API-Tools; `cdr-writer` ersetzt das Asterisk-AMI-Tracking (CDR-/Minuten-Tracking aus LiveKit-SIP-Bridge-Logs), PM2-Dienst. Aktuellste Agent-Auskopplung (v3.3, 05.08.2026).

### kundencenter — Tenant-Portal (Frontend-Komponente)
- Standort Codeberg · Sprachen: TSX, TypeScript, CSS · 5.792 LOC, 60 Dateien
- Das interaktive Kundencenter der SaaS-Plattform (React 19/Vite): Seiten für auth/operator/tenant, i18n, Mollie-Zahlungsablauf (Zahlung im selben Tab + Polling nach Rückkehr), Forwardings-Endpoint-Anpassungen, Index-no-cache-Fix. In der v4.2-Doku als „Tenant-Portal (Kundencenter), statisch deploybar" beschrieben; Entwicklungsstand 05.08.2026 — gehört zur Plattform.

### godialia — GoDialIA (verwandtes, eigenständiges Produkt)
- Standort Codeberg · Sprachen: PHP (251k), HTML+PHP, Rust (18k), JavaScript · 343.312 LOC, 1437 Dateien
- „AI voice gatekeeper for ViciDial / GOautodial call centers": verwandelt beliebige ViciDial-Kampagnen in eine KI-Rezeption (ein SIP-Endpunkt, ein Rust-Binary-Voice-Agent `viciai.service`, ein FastAPI-Backend `godiaia.db`/`godiaia_server.py`, ein AI-Dashboard, legacy-admin), Kampagnen = Mandanten, Transfers (do_refer) intern im Rust-Binary. Kein Version/Snapshot der GoFonIA-Plattform — eigenständige Produktlinie für ViciDial-Callcenter (wird daher nicht zum Kernprodukt gezählt; Stand 23.08.2026).

## Quellen & Methode
Geprüft wurden: README.md je Repo (Titel/Architektur), `docs/OPENSIPS.md`, CHANGELOG.md, git-log (letzte Commits sämtlicher Repos), `config/opensips|livekit|sip-bridge`, `enhanced_agent.py` (Tool-Funktionen, Provider-Env), `warmtransfer/controller_v3.py` (API v1), `backend/main.py` (Router-Liste), `backend/routers/`, `database/init.sql`, `docker-compose.yml`, `DOKUMENTATION.md` (Kernfunktionen), `frontend/src/pages/`, `kundencenter` (package.json, src/pages), `godialia` (README, agent/ Rust, backend/), `gofonia-api/gofonia_de`, `gofonia_en_v1` (Struktur), Verzeichnisstrukturen aus famdata sowie LOC-/Sprachzahlen aus `/root/portfolio/data/loc_results.json` (jeweils Feld `loc`).

---

# GoFonIA Web/Frontends & Landings

**Kategorie:** Eigenentwicklung; die vier GitHub-Repos sind Erweiterungen/Anpassungen auf Basis des MIT-Projekts GonoPBX (ankaios76/gonopbx, im Code referenziert)
**Plattform(en):** GitHub `livedialai` (4 Produkt-Repos) sowie Codeberg `gofonia` (3 Web-Repos)
**Kanonisches Repository:** `gofoniav2` (LiveDial-Installationslinie) bzw. `foniaai` (FoniaAI-Linie) — zwei Versionsreihen innerhalb der Familie
**Umfang kanonisch:** gofoniav2: 25.237 Codezeilen, 280 Dateien · foniaai: 21.046 Codezeilen, 257 Dateien (Quelle: loc_results.json)

## gofoniav2 — LiveDial-Installationssuite (VICIdial + LiveKit) für Debian 12, inkl. Web-GUI & Astro-Website
**Standort:** GitHub: https://github.com/livedialai/gofoniav2 (öffentlich) · **Typ:** Fork/Anpassung (Basis GonoPBX, MIT)
**Sprachen:** Python 9.554 · TSX 9.151 · Bash 1.597 · **Codeumfang:** 25.237 LOC, 280 Dateien · **Letzter Push:** 2026-05-11
*Klärung vorab: entgegen der Annahme ist dieses Repo (wie auch gofonia_en) **keine reine Landingpage**, sondern das vollständige Installations-/Betriebsprojekt der „Livedial"-Suite. „Livedial" ist der README-Titel/Projektname dieser VICIdial-&-LiveKit-Installation — nicht der Name einer Landingpage. Die im Repo enthaltene Astro-Website ist die Projektwebsite **GonoPBX (gonopbx.de)** mit Produktseiten, Blog und Handbuch (DE/EN).*

**Funktionsumfang:**
- Installationsskripte für Debian 12: `install.sh` (VICIdial-Komplettinstallation: Asterisk 18 VICIdial-Fork, MariaDB, Apache, PHP 7.4, astguiclient, Cron-Jobs), `install-livekit.sh` (LiveKit Server/CLI/SIP-Bridge, Docker, Redis, SIP-Trunk zum lokalen Asterisk), `install-agent.sh`/`install-enhanced-agent.sh` (Python-Voice-Agent via PM2)
- „Enhanced Agent": ViciDial-API-Integration (Lead-Status nach jedem Anruf), Decision-LLM (POSITIV/NEGATIV/UNKLAR), Hangup-Tool, Redis-Persistenz, Express-Dashboard (Anruferverlauf, Suche, Timeline, Transkripte, Login-Schutz)
- Web-GUI (React/TSX): Dashboard, Nebenstellen, Anrufverlauf, IVR, Gruppen/Weiterleitungen, Voicemail, Benutzerzweige & Wissen, API-Keys, Prompt-Editor
- Python-Backend (FastAPI/Router-Paket) plus Asterisk-Konfigurations-Module (`gonopatch`) und Docker-Compose-Deployment
- `website-astro/`: GonoPBX-Produktwebsite (Astro, `site: https://gonopbx.de`, Sitemap) — Landing, Feature-Seiten (Dashboard, Voicemail, Nebenstellen, Anrufverlauf, Docker-Deployment, Home Assistant, Outbound-Caller-ID, Release-Notes), Blog mit Release-Posts, Handbuch, Impressum/Datenschutz; zweisprachig (DE + `en/`)
- Dokumentation: README, QUICKSTART, DEPLOYMENT, ADMIN_HANDBUCH, BENUTZERHANDBUCH, DOKUMENTATION, DOKU_UNABHAENGIGKEIT, COMPETITIVE_CLAIM_SHEET (+ englische Fassungen in `docs-en/`), CHANGELOG, `releases/` (v0.1.x–v2.1.2)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `install*.sh`, `setup.sh`, `deploy*.sh`, `release.sh` — Installer & Deployment
- `enhanced_agent.py`, `server.js` — Voice-Agent (Python) und Express-Dashboard (Node)
- `agent.py` (Basic), `jambonz-bot/` und `pipecat-bot/` — frühere/alternative Agenten-Implementierungen
- `backend/` — FastAPI-Backend mit Routen (users, peers, ivr, groups, forwards, config, contacts, knowledge, integrations, settings) und AMI/MQTT/Chat/Datenbank-Clienten
- `frontend/` — React-Web-GUI (58 Dateien, Tailwind, Nginx-Config)
- `asterisk/` — Asterisk-Konfigurationsbaum (592 Dateien, u. a. `config/`, Dockerfile, voicemail-sender.sh) und `gonopatch/` — Dialplan-/Trunk-/PJSIP-Patches
- `website-astro/` — Astro-Website gonopbx.de; `database/` — init.sql; `doks/` — Produkt-PDFs (IA-Pfonie)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**
| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| gofoniav2 | GitHub livedialai (öffentlich) | **kanonisch**: neueste & umfangreichste Version der LiveDial-Installationslinie (25.237 LOC, 280 Dateien, Push 2026-05-11, zusätzlich Doku: Handbuch, Claims-/Unabhängigkeitsdoku, docs-en) |
| gofonia_en | GitHub livedialai (öffentlich) | Vorgängerversion / englische Übersetzung: „Initial commit: English translation of gofoniaaimain" (25.171 LOC, 276 Dateien, Push 2026-05-10) — gleiche Struktur, etwas weniger Doku |
| foniaai | GitHub livedialai (privat) | eigene deutsche Produktlinie „FoniaAI — Multi-Tenant KI-Telefonassistent" (21.046 LOC, 257 Dateien, Push 2026-04-30); auf Basis von GonoPBX (MIT) erweitert |
| foniaPBX | GitHub livedialai (privat) | Lite-Variante der FoniaAI-Linie („KI-Telefonassistent für kleine Unternehmen und Privatkunden", 20.388 LOC, 256 Dateien, Push 2026-04-29) — Codebasis identisch bis auf ~15 betroffene Dateien, u. a. fehlt der Multi-Tenant-Router `backend/routers/tenants.py` |

*Begründung kanonisch: `gofoniav2` ist die am weitesten entwickelte Version der LiveDial-Linie — höchster LOC-Stand (25.237 vs. 25.171), jüngster Commit (2026-05-11), zusätzliche Dokumentation (Benutzerhandbuch, Unabhängigkeits-/Wettbewerbs-Analysen, englische Doku-Seite) und verbesserte Installer-Reliability (Commit-Message). `foniaai` ist kanonisch für die deutsche FoniaAI-Linie: jüngerer Push und mehr Umfang als foniaPBX sowie der einzige Multi-Tenant-Ausbau (eigene API-Keys/Prompts/Stimmen/Wissensdatenbanken pro Kunde, `tenants.py`).*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### gofonia-website (Codeberg gofonia)
- Standort https://codeberg.org/gofonia/gofonia-website · Sprachen: HTML/JS · 3.831 LOC, 14 Dateien · Letzter Commit 2026-06-04 (Repo-Anlage 2026-06-05)
- Statische Landing-/Produktseiten für **GoFonIA.de** („KI-Telefonassistent mit echter Sekretärinnen-Funktion"): index, warmtransfer, sovereignty-check (DSGVO & Souveränität mit Meetergo-Scan), technologie, ueber-uns, login (Token-basierter Login), Impressum/Datenschutz/AGB, Auftragsverarbeiter sowie zwei Pitchdeck-Seiten.
- `scan-server/scan-server.js` (Express, Port 3050): **Meetergo Sovereignty-Scan-Proxy** — HTTPS-Proxy an scan.meetergo.com, parst OG-Meta-Tags und rendert das Ergebnis im GoFonIA-Design; Deployment via PM2 unter `scan.gofonia.de` (README).

### foniaai (GitHub livedialai, privat)
- Standort https://github.com/livedialai/foniaai · Sprachen: TSX 8.667 · Python 7.577 · 21.046 LOC, 257 Dateien · Push 2026-04-30
- **FoniaAI — Multi-Tenant KI-Telefonassistent** („Die digitale Zentrale"): KI-Telefonat in Echtzeit via LiveKit + SIP Bridge; STT (Deepgram, Mistral Voxtral, Whisper, Groq), TTS (Voxtral 7 Emotionsstimmen, Inworld, Cartesia, OpenAI); stimmungsabhängige Sprachausgabe über Emotion-Tags; Tool-Funktionen (Anruf beenden, Firmenwissen, API-Integrationen, Meetergo-Terminbuchung); Warm-/Kaltwechsel und Rückkehr (Sekretärinnen-Feature, Ziel-Teilnehmer als zweiter SIP-Participant im selben LiveKit-Room); Tenant-Erkennung über angerufene DID; enthält dieselbe GonoPBX-Web-GUI, das Asterisk-Konfigurationspaket und die Astro-Website gonopbx.de. Basislizenz: „Portions based on GonoPBX by ankaios76 (MIT)".

### foniaPBX (GitHub livedialai, privat)
- Standort https://github.com/livedialai/foniaPBX · Sprachen: TSX 8.653 · Python 7.102 · 20.388 LOC, 256 Dateien · Push 2026-04-29
- **FoniaPBX** — abgespeckte Einzelkunden-Variante von FoniaAI für kleine Unternehmen/Privatkunden: KI-Assistent, Web-GUI (Dashboard, Anrufverlauf, API-Keys, Prompt-Editor), beliebig viele SIP-Trunks, frei wählbare LLM/STT/TTS, E-Mail-Zusammenfassung nach jedem Anruf, Voice-Cloning (Mistral Voxtral), Firmenwissen (Web-Crawler/Upload + Ollama-Embeddings), Call-Forwarding mit SIPBridge-Outbound, Docker-Installation; ohne Multi-Tenant-Ausbau (kein `tenants.py`).

### greviews (Codeberg gofonia)
- Standort https://codeberg.org/gofonia/greviews · Sprachen: HTML 169 · JS 72 · PHP 55 · 362 LOC, 6 Dateien · Letzter Commit 2026-07-14
- **Google-Reviews-Slider** für beliebige Websites: PHP-Backend (`reviews.php`) holt 5-Sterne-Bewertungen über serper.dev (Google Reviews API), cached 24 h in `reviews.json` (kein Cron, keine externen Abhängigkeiten außer PHP + Vanilla JS); `greviews.js`/`greviews.css` für responsiven Auto-Slider (Pause bei Mouse-Over); `finder.html` als Place-ID-Finder-Tool. Live-Demo: pizzafamily.de.

### affiliate (Codeberg gofonia)
- Standort https://codeberg.org/gofonia/affiliate · Sprachen: CSS 883 · PHP 608 · HTML 408 · Python 346 · 2.634 LOC, 12 Dateien · Letzter Commit 2026-06-21
- **GoFonia Affiliate-/Partner-System** (partner.gofonia.de): FastAPI-Backend (`backend/routers/affiliates.py`, SQLAlchemy-Modelle) mit Public-API (Signup, Login, Stats, Referral-Tracking, Conversion) und Admin-API (Affiliate-Status, Provisionen, Auszahlungen via Mollie-Webhook); statisches Frontend (Landing + Signup, Login, Partner-Dashboard, Admin-Panel, Dark-Theme im gofonia.de-Stil); WordPress-Plugin (`wordpress-plugin/`: Auto-Signup mit Domain-Erkennung, Brevo-Mailversand, Admin-UI, CalDAV-Buchung).

## Quellen & Methode
Geprüft: famdata-Export F2 (README-Auszüge, Top-Level-Strukturen), loc_results.json (LOC/Dateien/Zahlen), README.md aller 7 Repos, `git log`/`git remote -v` (letzte Commits, Origin-URLs), `diff -rq` foniaai↔foniaPBX und gofonia_en↔gofoniav2 (Dubletten-Nachweis), `website-astro/astro.config.mjs` (site: gonopbx.de) und `src/pages/` (GonoPBX-Seiten), `website/index.html` (GoFonIA-Landing), `scan-server/scan-server.js`, `greviews/reviews.php`, `affiliate/frontend/`+`backend/`, CHANGELOG (foniaai).

---

# LiveDial & VICIdial-Installation

**Kategorie:** Eigenentwicklung
**Plattform(en):** GitHub livedialai
**Kanonisches Repository:** gofoniaaiv2
**Umfang kanonisch:** 27.577 Codezeilen, 283 Dateien (Quelle: loc_results.json)

## gofoniaaiv2 — GoFonia + VICIdial: Produktions-Installationspaket mit GoFonia-Plattform
**Standort:** GitHub: https://github.com/livedialai/gofoniaaiv2 · **Typ:** Eigenentwicklung
**Sprachen:** Python, TSX, Bash · **Codeumfang:** 27.577 LOC, 283 Dateien · **Letzter Push:** 15.05.2026

**Funktionsumfang:**
- **Drei Installer**: `vicidial-install.sh` (Standalone-VICIdial-Installation, „Produktionsversion", mit Download-Fallback-Logik), `install.sh` (Standalone-GoFonia-Installation), `vicidial_gofonia_install.sh` (Kombi-Installer: VICIdial und GoFonia in einem Lauf, bettet den VICIdial-Installer ein, erzeugt einen Jambonz-Agent v4.2 mit PM2-Start und SIP-Trunk-Funktionen)
- **VICIdial Core**: Asterisk 18 (ViciDial-Variante inkl. Kompilierung mit LibPRI, systemd-Service, ViciDial-Dialplan), MariaDB-Schema (`database/init.sql`), astguiclient-Prozesse (Keepalive, Updater, Manager-Listener — mit Wartungshinweisen im README), Asterisk-Config-Sammlung (`asterisk/`, 592 Dateien), gängige Quell-Tarballs (Asterisk 18.21.0-vici, astguiclient)
- **GoFonia Platform**: FastAPI-Backend mit Asterisk-AMI-Integration (`backend/`: ami_client, mqtt_client, database, auth, dialplan/pjsip_config, queue/voicemail/email-Konfiguration, Audit, Router), React/TSX-Frontend (`frontend/`), Docker-Compose, Astro-Website (`website-astro/`)
- **Livekitonly/**: LiveKit Voice Agent v3.0 für Warm-/Cold-Call-Transfers ohne Pipecat (Track-Permission-Transfers: `controller.py` als Regie mit Phasen-Management IDLE/MUSIC/BRIEFING/CONNECTED/FAILED, `livekit_agent.py` mit VAD→Deepgram STT→LLM→Azure TTS, `music_bot.py` für WAV-Loop, `run.sh`)
- **KI-Bots & Prompts**: `enhanced_agent.py`, `jambonz-bot/` (Node.js Voice-AI-Agent), `pipecat-bot/`, `entscheiderprompt.txt`/`verkaufsprompt.txt`
- **Dokumentation**: README + README_VICIDIAL/README_GOFONIA, DOKUMENTATION.md, ADMIN_HANDBUCH.md, admin-handbuch.pdf, QUICKSTART.md, PLAN_VICIDIAL_GOFONIA.md, Release-Notizen (`releases/`)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `vicidial-install.sh` = VICIdial-Einzelinstallation; `vicidial_gofonia_install.sh` = Kombi-Installer (beide Systeme, Jambonz-Agent); `install.sh` = nur GoFonia
- `backend/` = GoFonia Backend API (FastAPI, Asterisk-AMI/MQTT, Auth, Dialplan-Configs), `frontend/` = React/TSX-GUI
- `Livekitonly/` = LiveKit-Agent v3.0 mit Transfer-Regie, `dashboard/` = Dashboard-HTML
- `asterisk/` + `database/` + `agc/` = Asterisk-Konfig, Datenbank-Schema, Agent-Assets

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| livedial2 | GitHub | Vorgänger-Version: produktionsreifes LiveDial-Debian-12-Paket (3.231 LOC), Stand 27.04.2026 |
| livedial | GitHub | frühere LiveDial-Variante inkl. openSUSE-Adaption und Jambonz-/Pipecat-Bots (4.382 LOC), Stand 06.05.2026 |
| gofoniaaiv2. | GitHub | leerer Platzhalter (nur README.md, keine Dateien) |

*Begründung kanonisch: gofoniaaiv2 hat mit Abstand die meisten Codezeilen (27.577 vs. 3.231/4.382), den jüngsten Push (15.05.2026) und den größten Funktionsumfang. Es übernimmt die livedial2-Komponenten nahezu unverändert (server.js, package.json, dashboard.html, Prompts per Diff byte-identisch) und erweitert sie um Produktions-VICIdial-Installer, GoFonia-Plattform (FastAPI-Backend + React-Frontend) und LiveKit-Agent v3.0. livedial2 ist demzufolge die Vorgängergeneration „LiveDial", die in „GoFonia" aufging — die famdata-Notiz („kanonisch wahrscheinlich livedial2") bestätigt sich damit nicht; die Weiterentwicklung liegt in gofoniaaiv2.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### livedial2
- Standort GitHub: https://github.com/livedialai/livedial2 · **Sprachen:** Bash, HTML/Genshi, Python · **LOC:** 3.231, 14 Dateien · **Letzter Push:** 27.04.2026
- Produktionsreifes Debian-12-Installationspaket „LiveDial — VICIdial + LiveKit + KI-Agent" (deutsches README mit Architektur-/Call-Flow-Diagramm, DSL, Ports, API-Endpunkten): `install.sh` setzt VICIdial (Asterisk 18 ViciDial-Fork, MariaDB, Apache, PHP 7.4 via sury.org, astguiclient, Crontab, Screen-Session, Logger-Config) auf; `install-livekit.sh` installiert LiveKit Server 1.11.0 + CLI 2.16.2, Redis (passwortgeschützt), Docker mit livekit/sip-Bridge (Port 5070), Nginx-Reverse-Proxy, Let's-Encrypt-Zertifikate für 4 Domains sowie Asterisk-SIP-Trunk `[livekit-sip]` inkl. Dialplan `[livekit-outbound]`/`[livekit-inbound]`; `install-enhanced-agent.sh` richtet KI-Agent + Express-Dashboard ein (interaktive ViciDial-Zugangsdaten und Status-Mappings SALE/XFER, PM2, API-Key-Verwaltung und Prompt-Editor über das Dashboard). Letzter Commit: Revert „dynamic provider selection" — Zweig scheint eingestellt.

### livedial
- Standort GitHub: https://github.com/livedialai/livedial · **Sprachen:** Bash, Python, JavaScript · **LOC:** 4.382, 33 Dateien · **Letzter Push:** 06.05.2026
- Englischsprachige frühere Variante derselben Installerskripte (install.sh, install-livekit.sh, install-agent.sh, install-enhanced-agent.sh, agent.py, enhanced_agent.py, Dashboard). Zusatzinhalte: `jambonz-bot/` (Jambonz Voice AI Bot mit TTS-Voice-Override pro Tenant, PM2-Prozesse „jambonz-tts"/„jambonz-working"), `pipecat-bot/`, `opensuse/` (vollständige openSUSE-Adaption der Installer — auch der Root-`install-livekit.sh` ist als „openSUSE-Adapted" kommentiert, die Debian-LiveKit-Variante mit Nginx/Let's Encrypt liegt in livedial2). Der Code überschneidet sich mit livedial2 (agent.py, enhanced_agent.py identisch), wurde aber separat weitergeführt (letzter Commit: Timeout-Zähler im Bot); README weist auf Platzhalter-API-Keys hin.

### gofoniaaiv2.
- Standort GitHub: https://github.com/livedialai/gofoniaaiv2. · **Sprachen:** nur Markdown · **LOC:** 0, 1 Datei · **Letzter Push:** 07.05.2026
- Leeres Repo mit ausschließlich `README.md` (Inhalt: „# gofoniaaiv2.") — Platzhalter ohne jeglichen Inhalt; vermutlich versehentlich angelegt.

## Quellen & Methode
Geprüft wurden: README.md und README_VICIDIAL.md/README_GOFONIA.md (gofoniaaiv2), livedial2/README.md (vollständig, 243 Zeilen: Architektur, Install-Schritte, Dashboard-Features, Ports), livedial/README.md, Kopfzeilen und Kernpassagen von install.sh, install-livekit.sh, install-enhanced-agent.sh (beide Repos), vicidial-install.sh, vicidial_gofonia_install.sh, install.sh, setup.sh sowie Livekitonly/README.md und backend/main.py (FastAPI). Zusätzlich Dateivergleichs-Diffs (server.js, package.json, dashboard.html, enhanced_agent.py, Prompts), Git-Logs (je 1 Commit — shallow) und LOC-Werte aus loc_results.json.

---

# ViciAI / Vicidial-LiveKit-Agents

**Kategorie:** Eigenentwicklung + Fork/Anpassung (Voice-AI-Call-Center-Suite für ViciDial; Kernagent viciai basiert auf Fork von miuda-ai/active-call)
**Plattform(en):** GitHub livedialai und Codeberg gofonia (beide)
**Kanonisches Repository:** viciai
**Umfang kanonisch:** 50.795 Codezeilen, 304 Dateien (Quelle: loc_results.json)

## viciai — Single-Binary-Voice-Gateway für ViciDial (Rust)

**Standort:** Codeberg: https://codeberg.org/gofonia/viciai · **Typ:** Fork/Anpassung (Basis: miuda-ai/active-call, Rust, MIT — `Cargo.toml`: package `active-call` v0.3.78)
**Sprachen:** Rust (23.083), Python (10.122), TSX (8.856) · **Codeumfang:** 50.795 LOC, 304 Dateien · **Letzter Push:** 2026-08-21

**Funktionsumfang:**
- **ViciDial-Remote-Agent:** Das Asterisk-Dialplan-Muster `_800493.` packt `<anrufer>_<lead_id>_<vorname>_<nachname>` in die Caller-ID (per `agi-set_variables.agi` + `Set(CALLERID(num)=...)`); der Agent löst jeden Anruf über SIP-From-URI (`{{ caller }}`) und To-URI (`{{ callee }}`) auf — **ohne Binary-Umbau** (Built-ins des playbook_handler.rs)
- **Lead-Qualifizierung** über die ViciDial Non-Agent-API: Tools `get_lead_info` (→ `lead_search`) und `update_lead` (Status NEW/CALLBK/SALE/DEC/DNC) als OpenAI-Function-Schemas in SQLite; reproduzierbarer Seed via `scripts/seed_viciai_lead_tools.py` (liest API-Passwort zur Laufzeit aus der ViciDial-DB, keine Secrets im Repo)
- **Voice-Pipeline mit BYOK:** ASR (Deepgram, zusätzlich Offline-Modus `offline`/`ort`+VAD), TTS (Deepgram oder Inworld), LLM (beliebiger OpenAI-kompatibler Endpunkt); native SIP-Terminierung (rustrtc/rsipstack) + WebRTC — **kein LiveKit, kein Media-Server-Cluster, keine GPU, kein Docker-Zwang**: ein Binary, SQLite, WebUI-Wizard
- **HiPIA-WebUI-Wizard** (`/admin`): SIP-Trunk/DID, Forwarding-Ziele (warm/blind), System-Prompt, Kalender-Booking, Custom Tools (OpenAI-Function-Schemas → HTTP), ASR/TTS/LLM-Provider mit Stimmen-Loader; SQLite (`config/appliance.db`), argon2-Auth, Passwortwechsel-Pflicht
- **Supervisor-Dashboard** (`dashboard/viciai_dashboard.py`, Port 8090): Live-Transkripte über SSE (`/events/{id}`, AsrDelta/AsrFinal), Verlauf + Statistiken (SQLite), Endpunkte `/api/stats`, `/api/calls`, `/api/transcript`, `/api/live`; per Installer wahlweise standalone (systemd `viciai-dashboard`) **oder** in die ViciDial-UI eingebettet (iframe + Sidebar-Menüpunkt), idempotent mit Backups
- **Integrations-Installer:** `scripts/vicidial-install.sh` (komplette ViciDial-Migration auf Debian 12, Produktionsversion mit Fallback-Downloads und Konsolen-Logging); funktioniert für Inbound- und Outbound-Kampagnen (Remote-Agent-Mechanik)
- Cross-Kompilierung (Cross.toml, Dockerfile.cross-x86_64/aarch64), systemd-Service (`deploy/active-call.service`), 21 Rust-Tests (SIP-Hold, SSRC-Wechsel, Autohangup, WebRTC-VAD, Anrufaufzeichnung u. a.)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `src/` (104 Dateien): `main.rs`, `app.rs`, `call/`, `media/`, `playbook/` (Playbook-Engine), `transcription/`, `synthesis/`, `offline/` (lokale ASR), `useragent/playbook_handler.rs` (liefert `{{ caller }}`/`{{ callee }}`), `admin/` (WebUI-Wizard), `callrecord/`
- `backend/` (46 Dateien, **GoFonIA-Zusatz**): FastAPI-Backend ("GoFonIA — Backend API") mit Asterisk-AMI-Client (`ami_client.py`), SQLAlchemy-DB, PJSIP-Konfigtools (`pjsip_config.py`, `clean_/fix_pjsip_config.py`), Dialplan-Erzeugung, Voicemail-Tabelle, Auth, SaaS-Utilities, `routers/`
- `frontend/` (57 Dateien): React/Vite/Tailwind-Dashboard (`App.tsx`, nginx.conf); `dashboard/viciai_dashboard.py` (Supervisor-View)
- `config/` (Telnyx-Beispiel, `office.wav`, `fillers.txt`), `docs/` (56 Dateien, u. a. `viciai-lead-qualifizierung.md`, `playbook_*`, `api.md`, Telnyx/Twilio-Integrationen, Architektur-Diagramme), `scripts/` (Installer + Seeds), `deploy/`, `release/active-call` (vorgebautes Binary, 68,7 MB)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| viciai | Codeberg gofonia | **kanonisch** — aktuellste Architektur (2026-08-21), Quellrepo mit Integration + Dashboard + Installern |
| vicidial-activecall | GitHub livedialai | Deployment-/Bootstrap-Repo desselben Produkts: idempotenter `install.sh` (lädt active-call-Binary nach `/opt/hpia/`, systemd-Dienst, patcht `admin.php`/`admin_header.php` → ViciAI-Menü **in** ViciDial, mit Backups) + mitgeliefertes Binary; kein Code-Duplikat |
| vicidial-agenten | Codeberg gofonia | aktuelle Version der **LiveKit-Python-Linie** (2026-08-20): Nachfolger von edgewrapper (+ `get_lead_info.py` mit `number_to_german_text`, `docs/`, Dockerfile/Compose, voices) |
| edgewrapper | Codeberg gofonia | Zwischenversion derselben LiveKit-Linie (2026-06-07): top-level Agent, Edge-TTS-Wrapper, Parakeet-STT + Silero-VAD-Zweig |
| vicidial-agents | Codeberg gofonia | ältere Ursprungsversion derselben Linie (2026-06-04/10): Jambonz-Agent (Node.js) **und** LiveKit-Agent (Python) nebeneinander, ohne Docker/docs/Edge-TTS |
| govicia | Codeberg gofonia | volle GoViCiA-Call-Center-Plattform (2026-06-15): FastAPI+MongoDB, Next.js-Frontend mit Vicidial-Style-Agent-Maske — Nachfolger/Erweiterung von govici |
| govici | Codeberg gofonia | ältere, schmalere Komponenten-Version desselben Systems (2026-06-15, reiner Agent + 6-Phasen-Warmtransfer + Dashboard, 2.223 LOC) |
| vicidial-phone-bot | GitHub livedialai (privat) | Legacy-Vorläufer (2026-05-17): Node.js-FastAGI-Phone-Bot von dialer.advocube.de, vor der LiveKit-Architektur |
| goautodial_de | Codeberg gofonia | Infrastruktur: GoAutoDial-CE-4.0-Web-Frontend (Vendor-Code, 571.200 LOC) + deutsche UI-Übersetzung |
| vicidialdocker | GitHub livedialai | Infrastruktur: ViciDial-Docker-Setup (Asterisk 18 + ConfBridge) |
| vicidial-alma-scratch-install | Codeberg gofonia | Infrastruktur: Bare-Metal-Build-Installer für ViciDial/GoAutoDial auf AlmaLinux 9 |

*Begründung kanonisch: viciai ist die einzige Implementierung ohne LiveKit-Infrastruktur (Single-Binary mit direkter SIP/WebRTC-Anbindung), zuletzt gepflegt (2026-08-21), mit 50.795 LOC und 304 Dateien der mit Abstand größte Codeumfang sowie das vollständigste Ökosystem (ViciDial-Dialplan-Integration, Lead-Qualifizierung, Dashboard, ViciDial-Admin-Einbettung, 21 Rust-Tests, Cross-Builds). Die LiveKit-Python-Agenten (vicidial-agents → edgewrapper → vicidial-agenten; govici → govicia) sind ältere bzw. komplementäre Implementierungen derselben Aufgabe.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### vicidial-agenten (aktuelle LiveKit-Implementierung)
- Standort Codeberg gofonia · Python 2017, JS 1280 · 3650 LOC, 27 Dateien · letzter Push 2026-08-20
- GoFonIA-LiveKit-Agent: Vicidial → LiveKit-SIP-Bridge → Python-Agent mit Dual-LLM (Verkaufs-LLM "Lisa" + Entscheider-LLM), Deepgram-EU-STT, Microsoft-Edge-TTS über lokalen OpenAI-kompatiblen Wrapper (:5050), Redis + CSV für Daten/Transkripte, Dashboard (Port 9000), Vicidial-Non-Agent-API für Lead-Statusupdates, Meetergo-Kalender, `get_lead_info.py` (Lead-Abfrage mit Zahlwort-Übersetzung); produktiver Pfad laut README. *(Vorstufen: edgewrapper — Parakeet-TDT/Silero-VAD-Erweiterung; vicidial-agents — Ursprungsversion mit Jambonz- und LiveKit-Variante in Unterordnern sowie SIP-BRIDGE-SETUP.md)*

### govicia (GoViCiA AI Call Center)
- Standort Codeberg gofonia · Python 7447, TSX 7161 · 15.575 LOC, 99 Dateien · letzter Push 2026-06-15
- LiveKit-Call-Center mit KI-Agent, 6-Phasen-Warmtransfer (CALLER_AI → CALLER_WAITING → AI_BRIEFING → CHEF_DECIDING → CONNECTED → FALLBACK) und Vicidial-Style-Agent-Maske: `warmtransfer_controller.py` v4.0 (Single-Room, Publisher-/Receiver-seitige Subscription-Matrix, API `/v1/transfer`/`/v1/briefing-complete`/`/v1/cancel`/`/livekit/webhook`), `main.py` (FastAPI, MongoDB, Routen für Projekte/Agenten/Kampagnen/Analytics/Contacts), Next.js-14-Frontend `livekit-call-center-frontend` (Agent-Maske mit AI-Briefing-Audio, Anrufer-Kontext, Kommentarfeld, Transfer-Queue, Dispositionen, Scripts, Transkripten); im Root viele experimentelle Agent-Varianten (ai_agent, multimodal_agent, realtime_agent, proper_agent, dialer_worker, …) als Entwicklungsartefakte. *(Vorstufe govici: gleicher Agent mit warmtransfer/controller.py a. Port 9100, dashboard.py a. 9000, setup_sip.py, email_summary.py, Outbound-Trunk d.vsip.eu)*

### vicidial-phone-bot (Legacy)
- Standort GitHub livedialai (privat) · JavaScript 2593 · 2613 LOC, 21 Dateien · letzter Push 2026-05-17 · kein README
- Node.js-FastAGI-Phone-Bot aus dem ViciDial-Betrieb dialer.advocube.de: `PhoneBotCore` mit Fireworks-AI-LLM (qwen3), Whisper-Service (`phone_whisper_service.js`), Voice-Service, Brevo-SMS-Service, SumUp-Zeitpakete (`bot_sumup_zeitpakete.js`); viele Iterationsdateien (fastagi_phone_bot**.js) — früher Vorläufer der KI-Anrufagenten statt LiveKit.

### goautodial_de (GoAutoDial DE Web-Frontend)
- Standort Codeberg gofonia · JavaScript 181.064, JS+Lasso 132.778, XML+PHP 98.997 · 571.200 LOC, 2022 Dateien · letzter Push 2026-08-24
- Komplettes GoAutoDial-CE-4.0-Web-Frontend (PHP 7.4, Apache, AdminLTE) einschließlich deutscher Übersetzung `lang/de_DE` (71 KB, alle UI-Texte) sowie en_US/es/fr/it/ru/zh; inkl. goAPIv2-API (398 Dateien) und `docs/goAPIv2-api.md`; Backend (Asterisk, MariaDB, astguiclient, Kamailio/RTPengine) nicht im Repo — Deployment nach `/var/www/html`. Vendor-Code mit eigener Sprachfassung, keine GoFonIA-Code-Eingriffe im PHP gefunden.

### vicidialdocker (ViciDial Docker)
- Standort GitHub livedialai · Transact-SQL 5809, Bash 252 · 6350 LOC, 17 Dateien · letzter Push 2026-05-17
- Fertiges ViciDial-Docker-Setup per `docker compose up -d`: MariaDB (Image `livedial/vicidial-db`, DB-Schema mit 344 Tabellen im Image gebacken), Asterisk 18 (`livedial/vicidial-asterisk`, PJSIP + ConfBridge 9600000–96000009), Apache (`livedial/vicidial-apache`, Port 3300; Entrypoint setzt SERVER_IP, legt ConfBridge-Konferenzen an, Keepalives); dazu build.sh/install.sh und PATCHES.md.

### vicidial-alma-scratch-install (AlmaLinux-9-Installer)
- Standort Codeberg gofonia · Bash 834 · 883 LOC, 4 Dateien · letzter Push 2026-08-22
- Produktionsnahe Source-Build-Installer für AlmaLinux 9: `install-vicidial-alma.sh` (DAHDI 3.4.0-Kernmodules, libpri 1.6.1, libsrtp 2.1.0, Asterisk 18.21.0-vici, astguiclient SVN, Apache-UI) und `install-goautodial-alma.sh` (GoAutoDial CE 4.0 mit Kamailio/RTPengine + deutschem goautodial_de-Frontend); SIP-only, ohne Kamailio für die ViciDial-Variante, volles Konsolen-Logging.

## Quellen & Methode
Geprüft wurden: famdata-JSON `F4_viciai_agents.json`, `loc_results.json`, Git-Logs aller 11 Repos (Data/Commits), READMEs (viciai, vicidial-agenten, edgewrapper, vicidial-agents, govici, govicia, vicidial-activecall, goautodial_de, vicidialdocker, vicidial-alma-scratch-install), `Cargo.toml` (Fork-Nachweis miuda-ai/active-call), `viciai/README.md` komplett, `src/`-Struktur, `backend/main.py`, `dashboard/README.md`, `docs/viciai-lead-qualifizierung.md`, `scripts/vicidial-install.sh`, `agent.py`-Diff (edgewrapper ↔ vicidial-agenten), `SIP-BRIDGE-SETUP.md`, `govicia/main.py` + `warmtransfer_controller.py` + `frontend/package.json`, `govici/agent.py`, `vicidial-phone-bot/package.json` + `phone_bot_core.js`, `vicidialdocker/docker-compose.yml`, `lang/de_DE` (Datei, 71 KB).

---

# Dograh AI-Sprachagent

**Kategorie:** Eigenentwicklung (auf Basis eines öffentlichen Open-Source-Forks)
**Plattform(en):** GitHub livedialai und Codeberg gofonia (beide)
**Kanonisches Repository:** dograh-miniV3
**Umfang kanonisch:** 1.872 Codezeilen, 26 Dateien (Quelle: loc_results.json, sourceCount)

Die Familie umfasst vier Linien rund um die Dograh-Voice-AI: die vollständige,
selbst gehostete Voice-Agent-Plattform (Fork **Dobrahv2**), die minimale
Headless-Voice-Agent-Versionsreihe **dograh-mini V1–V3** (= die eigentliche
Dublettenreihe, kanonisch V3), die **LiveKit**-Telefonie-Variante und die
PBX-Web-GUI **dobrah-pbx** mit nativer Dograh-ARI-Anbindung.

## dograh-miniV3 — Headless-Voice-Agent (neueste Generation, Warm- und Kalttransfer)

**Standort:** Codeberg: https://codeberg.org/gofonia/dograh-miniV3 · **Typ:** Eigenentwicklung
**Sprachen:** Python (1.600), Markdown (146), Bash (111) · **Codeumfang:** 1.872 LOC, 26 Dateien · **Letzter Push:** 2026-08-10

**Funktionsumfang:**
- Headless Sprach-Agent für eingehende Anrufe: SIP/PJSIP → Asterisk 22 → ARI/Stasis → External-Media-WebSocket → Pipecat (kein GUI, keine Datenbank, kein Docker-Stack nötig)
- Pipecat-Pipeline mit Deepgram STT, OpenRouter-LLM (Default `google/gemini-3.1-flash-lite`), Inworld TTS, Silero VAD; `AsteriskFrameSerializer` für ulaw 8 kHz
- Multi-Tenant: Runtime-Auflösung der tatsächlich gerufenen DID über die GoFonIA-Tenant-API (`tenant_resolution.py`, Retry mit `00`-Präfix für Legacy-Route-Matching)
- Tenant-spezifische Auswahl von Prompt, Provider, Modell, API-Key, Sprache und Stimme (`business_context.py`)
- Kalt- UND Warmtransfer (`transfer_resolver.py` + `warm_transfer.py`): Phasen `CALLER_AI` → `CALLER_WAITING` → `AI_BRIEFING` → `TARGET_DECISION` → `CONNECTED` → `FALLBACK` → `ENDED`, Zielauflösung über GoFonIA-Resolver, ARI Bridge-Swap
- Vorbereitete Ausgehend-Routing über den bestehenden SIPLoad-PJSIP-Endpoint, Redis nur für kurzlebige Call-Korrelation
- Betrieb: FastAPI `/ws/ari` + `/healthz`, Systemd-Unit, secret-freie Asterisk-Templates, Build-/Konfigurationsskripte, installierbare Tests (5 Dateien), Deutsche Operations-Doku

**Aufbau/Module (aus Verzeichnisstruktur):**
- `app/main.py` (984 Z.) = FastAPI-ARI-Event-Loop, External-Media-Bridge, Pipecat-Pipeline (`CallState`, `SpeechCompletionProcessor`, `ARIClient`)
- `app/warm_transfer.py` (279 Z.) = `WarmTransferCoordinator` mit Phasen-Statusmaschine und ARI-Protokoll
- `app/tenant_resolution.py` = Tenant-Auflösung, DID-Normalisierung
- `app/transfer_resolver.py` = Ziel-/Variablen-Rendering und Timeouts für Transfers
- `app/business_context.py` = Tenant-Kontext (Prompt, Sprache, Stimme) · `app/call_identity.py` = Anruf-Identität (called-DID)
- `deploy/` = systemd-Unit, `build-asterisk.sh`, `configure-asterisk.sh`, Asterisk-Templates · `docs/` = ARCHITECTURE/OPERATIONS/TENANT-CONFIGURATION · `tests/` = 5 Testdateien

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| dograh-mini | GitHub livedialai | Leere Erstanlage ohne jeden Commit (nur `.git`) — Vorgänger-/Fehlversuch der Codeberg-Reihe |
| dograh-mini-en | Codeberg gofonia | **V1** (ältere Version): 481 LOC, 336-Z.-`main.py`, ohne Tenant-/Transfer-Logik; heute neuere/durchgängig englische Doku |
| dograh-miniV2 | Codeberg gofonia | **V2** (Vorläufer): 1.144 LOC, 565-Z.-`main.py`, Kalttransfer + Tenant-Auflösung, aber kein Warmtransfer |
| dograh-miniV3 | Codeberg gofonia | **Kanonisch — neueste Generation**: + Warmtransfer-Modul, 984-Z.-`main.py`, 5 Tests |
| dogrH-miniv2 | Codeberg gofonia | Leere Anlage ohne Commit (Rechtschreibvariante "dogrH") — Zweck nicht erkennbar (keine Metadaten) |

*Begründung kanonisch: dograh-miniV3 ist der letzte Stand der Reihe (letzter Commit 2026-08-10 15:48 Uhr vs. V2 03:47 Uhr), mit der meisten Codebasis (1.872 vs. 1.144 LOC), dem größten Hauptmodul (984 vs. 565 Zeilen), dem einzigen Warmtransfer-Modul (279 Zeilen), 5 statt 4 Tests sowie erweiterter Doku (Warmtransfer als Bestandteil des ARI-Normalpfads, Logmarker- und Deployment-Konventionen). Der README-Titel "dograh-miniV2" ist eine Kopie aus dem V2-Repo — reines Artefakt der Iteration.*

## Sonstige Einzelrepos dieser Familie (kurz)

### Dobrahv2
- **Standort:** https://github.com/livedialai/Dobrahv2 · **Sprachen:** Python, TSX, TypeScript · **Umfang:** 144.644 LOC, 1.417 Dateien (größtes Repo der Familie) · **Letzter Push:** 2026-07-27 · Version 1.43.0
- Öffentlicher Fork der Open-Source-Plattform „Dograh" (Upstream-Verweise in README/Dockerfiles auf `dograh-hq/dograh`, „open-source alternative to Vapi & Retell"): visueller Workflow-Builder (Start-/Agenten-/QA-Knoten, Tools, Knowledge Bases, Webhooks), Browser-Test (Test Audio/Test Chat), Telefonie über Twilio/Vonage/Telnyx/Plivo/Vobiz/Cloudonix/Asterisk ARI, eingebauter Asterisk-PBX (Multi-Tenant: Trunks, Extensions, DID-Routing, CDR, Codecs, Fail2Ban, IP-Whitelist), MCP-Server für Coding-Agenten, Python-/Node-SDKs, evals/, Docs-Portal; Backend FastAPI (`api/`: routes, services, tasks, mcp_server, native, alembic), Frontend Next.js 15/React 19 (`ui/`, 3,5 MB), Postgres + Redis(ARQ) + MinIO, selbst-hostbar per Docker (One-Command), deutschsprachige PBX/SIP-Verwaltung im README ergänzt (letzter Commit).

### dograh-livekit
- **Standort:** https://codeberg.org/gofonia/dograh-livekit · **Sprachen:** Python (911), Markdown, INI, YAML · **Umfang:** 1.185 LOC, 11 Dateien · **Letzter Push:** 2026-08-01 (eigener Architekturpfad, älter als mini-V3)
- Deutsche Dokumentation; verbindet Dograh-Flow-Builder mit LiveKit als SIP/WebRTC-Backend: OpenSIPS als stateless SIP-Proxy (Trunk-Registrierung + Auth), LiveKit SIP-Bridge (Docker `livekit/sip`), LiveKit-Server (Rooms, WebRTC), Pipecat über nativen `LiveKitTransport` — kein Bridge-Code. Enthält `warmtransfer/controller_v3.py` („GofonIA Warm Transfer Controller v4.0", Single-Room mit caller/ai_agent/music_bot/chef-Teilnehmern, REST `/v1/transfer`, `/v1/briefing-complete`, `/v1/cancel`), `providers/telephony_config_reference.py` (Provider-Konfig-Schemas inkl. LiveKit als Provider) und fertige `config/` (opensips.cfg, livekit.yaml, sip.yaml).

### dobrah-pbx
- **Standort:** https://github.com/livedialai/dobrah-pbx · **Sprachen:** TSX (7.565), Python (5.400), Bash · **Umfang:** 16.074 LOC, 237 Dateien (davon asterisk/-Assets 21 MB) · **Letzter Push:** 2026-07-27
- „Open-Source Web GUI for Asterisk PBX with native Dograh ARI integration" (gonopbx.de): Extensions, Telefonbuch (CSV), SIP-Trunks, DID-Routing, Rufumleitung, Ring-Gruppen, mehrstufiges IVR, Voicemail mit E-Mail-Zustellung (SMTP), Home-Assistant/MQTT, native Dograh-ARI-Integration (erzeugt `ari.conf`/`websocket_client.conf`, remote Dograh-Instanz übernimmt als AI-Sprachagent die Anrufe), IP-Whitelist, Fail2Ban, CDR, Audit-Log, JWT-Rollen, Realtime-Dashboard, deutschsprachige Asterisk-Sounds, DE/EN-UI, Docker-Deployment. Backend FastAPI (`backend/`: dialplan, pjsip/ari/ami/mqtt/queue/acl/voicemail-Konfig), Frontend React+Vite+Tailwind (`frontend/`), Marketing-Website in Astro (`website-astro/`), Admin-Handbuch (PDF) und `DOKUMENTATION.md`/`QUICKSTART.md` auf Deutsch.

## Quellen & Methode
Geprüft: README.md aller 6 befüllten Repos (Dobrahv2: vollständig; mini-en/V2/V3: vollständig; livekit: Architecture + Warmtransfer; dobrah-pbx: Features-/Dograh-ARI-Sektion), CHANGELOG.md und ui/package.json (Dobrahv2 v1.43.0), `app/`-Module aller mini-Generationen (main.py 336/565/984 Zeilen; warm_transfer.py, tenant_resolution.py, transfer_resolver.py, business_context.py, call_identity.py), tests/, docs/ARCHITECTURE.md, deploy/-Skripte, warmtransfer/controller_v3.py und providers/ des LiveKit-Repos, backend/-Struktur des PBX, `git log -1` jedes Repos, loc_results.json. Dobrahv2-README enthält Upstream-Verweise (dograh-hq/dograh, docs.dograh.com), daher Fork-Einstufung; `asterisk/`-Assets (595–596 Dateien, u. a. musiconhold.conf, voicemail-sender.sh) sind bei Dobrahv2 und dobrah-pbx identisch. Die leeren Repos (dograh-mini, dogrH-miniv2) enthalten nur `.git` ohne Commits.

---

# FanVue / Goddess-Nora Creator-Stack

**Kategorie:** Eigenentwicklung (API-Referenz + Chatbot-Stack) · Daten-Assets (private Exporte)
**Plattform(en):** Codeberg gofonia (Stack) und GitHub livedialai (private Daten-Assets)
**Kanonisches Repository:** fanvue-livekit-signature
**Umfang kanonisch:** 3.763 LOC Codezeilen, 105 Dateien (Quelle: loc_results.json)

## fanvue-livekit-signature — Fanvue-Chatbot (Autogramm, Wunschfoto, PSTN-Rückruf, App-Billing)
**Standort:** Codeberg: https://codeberg.org/gofonia/fanvue-livekit-signature · **Typ:** Eigenentwicklung
**Sprachen:** JavaScript/Node.js (ESM) · HTML (Web-GUI) · Python (LiveKit-Agent) · **Codeumfang:** 3.763 LOC, 105 Dateien · **Letzter Push:** 30.08.2026

**Funktionsumfang:**
- **Chat-Automatisierung:** nimmt Fanvue-Webhooks entgegen, generiert Antworten per LLM (DeepSeek) und sendet sie zurück — vollautomatisch.
- **Vision-Support** (`deepseek-v4-flash-vision-exp`): beschreibt/bewertet Fan-Fotos (z. B. Rating-Wünsche).
- **PPV-Strategie-Engine:** Preisgrenzen, Angebots-Timing und Guardrails pro Prompt-Konfiguration; PPV-Preise werden validiert und auf den konfigurierten Bereich geklemmt.
- **PPV-Medienauswahl:** das LLM wählt das passendste Vault-Medium (über Fanvues AI-Beschreibungen/Tags) aus und hängt es an PPV-Angebote — Bildzuordnung per `mediaUuids`, keine URLs nötig.
- **Personalisierte Signatur (Autogramm):** Triggerwort (z. B. „Autogramm") startet einen Dialog nach dem Vornamen; der Bot rendert den Namen auf ein zufälliges Basisfoto aus `signature_photos/` (node-canvas, 50 Handschrift-Fonts) und liefert es kostenlos; signierte Fotos werden nach `SIGNATURE_RETENTION_HOURS` (Standard 48 h) automatisch aus dem Fanvue-Treasure gelöscht.
- **Custom Wunschfoto:** Fans beschreiben einen Wunsch; der Wish wird per DeepSeek prompt-verstärkt und von Seedream 4.5 Edit auf die Referenzfotos in `custom_fotos/` gerendert (konsistenter Charakter), dann in den Chat geliefert.
- **Voice-Callback (PSTN):** erscheint eine Telefonnummer im Chat, wählt der Bot den Kunden per LiveKit/SIP-Bridge an (ASR Deepgram · LLM DeepSeek · TTS Inworld, Persona „Goddess Nora"); Steuerung über `livekit/`-Stack (Callback-Controller + LiveKit-Agent, Python/livekit-agents).
- **Fanvue App-Store-Billing:** `app.*`-Webhooks (subscription.activated/cancel/payment), Entitlement-Check `GET /apps/{uuid}/subscription/me`, stündlicher Reconcile-Cron (`src/appBilling.js`).
- **Admin-API:** REST unter `/api` mit Bearer-Token (Creators, Konversationen, Prompt-CRUD + Aktivierung, Settings, Inworld-Voice-Konfig, LLM-/Voice-/STT-/Webhook-Testendpunkte, Statistiken).
- **Web-GUI** (Alpine.js + Tailwind, ohne Build-Schritt): Dashboard (Stats, letzte Konversationen), Prompt-Editor (mehrere Konfigurationen je Creator), messenger-artiger Conversations-Viewer mit PPV-Status, Settings (OAuth-Infos, LLM-Key, Admin-Token, Webhook-Test).
- **OAuth 2.0 + PKCE** mit Refresh-Token-Rotation und Shared-Promise-Lock gegen parallele Refreshes; **Webhook-Verifikation** via HMAC-SHA256 (5-Minuten-Toleranz) mit Event-Dedup über `eventId`.
- **Multi-Step-S3-Media-Upload** (mit Retry) für PPV-Inhalte; `mailer.js` für E-Mail-Versand.
- Technik: Node.js ≥ 18 (ESM), Express 4, SQLite via better-sqlite3 (WAL, keine externe DB), Inworld-TTS, PM2, Apache + Let's Encrypt; komplette Schritt-für-Schritt-Installation in `INSTALL.md`.

**Aufbau/Module (aus Verzeichnisstruktur):**
- `src/index.js` = Express-Start, `src/db.js` = SQLite-Schema/Queries, `src/tokenManager.js` = OAuth-PKCE, `src/fanvueApi.js` = API-Wrapper (Auto-Refresh, 401/429-Retry), `src/webhookHandler.js` = Signaturprüfung/Dedup, `src/chatbot.js` = LLM-Integration + PPV-Logik
- `src/signature.js` + `src/render.js` = Autogramm-Dienst (canvas-Rendering, `multifonts/` 50 Schriften, `GreatVibes-Regular.ttf`), `src/custom.js` = Wunschfoto, `src/appBilling.js` = App-Store-Billing, `src/mailer.js` = E-Mail, `src/mediaUpload.js` = S3-Upload, `src/inworld.js` = TTS
- `src/routes/` = `auth.js` (OAuth), `api.js` (REST), `webhooks.js` (POST /webhooks/fanvue)
- `livekit/` = Voice-Callback-Stack: `callback-agent/agent.py` + `controller.py` (Python), `config/`, `sip-bridge/`, `ecosystem.config.js`
- `public/` = Web-GUI (index, prompts, conversations, settings) · `signature_photos/` + `custom_fotos/` = Bildvorlagen · `seed/system-prompt.md` = Persona-Prompt „Goddess Nora"

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| fanvuebot | Codeberg | ältere Version: Chatbot-Standalone (Node, 2.058 LOC) ohne LiveKit-Anteil |
| fanvue-livekit | Codeberg | Zwischenversion: Chatbot (Unterordner `fanvue-chatbot/`) + LiveKit-/PSTN-Stack, 2.905 LOC |

*Begründung kanonisch: fanvue-livekit-signature ist die jüngste (zuletzt gepusht 30.08.2026) und mit Abstand größte Version (3.763 LOC, 105 Dateien) der Reihe; sie enthält den kompletten LiveKit-Voice-Stack aus fanvue-livekit (livekit/ mit callback-agent, config, sip-bridge) **und** zusätzliche Feature-Module (Autogramm, Wunschfoto, App-Billing, Mailer) sowie INSTALL.md und app-store-listing.md. Die famdata-Notiz nannte fanvue-livekit als kanonisch — der Vergleich von LOC, Push-Datum und Funktionsumfang (Feature-Scan: signature/custom/appBilling-Webhooks nur in signature) zeigt aber eindeutig, dass signature die weiterentwickelte Version ist; fanvue-livekit ist deren unmittelbarer Vorläufer. fanvuebot ist die früheste, nur-Chatbot-Version.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### fanvue
- Standort Codeberg: https://codeberg.org/gofonia/fanvue · Markdown 570 LOC, 7 Dateien · Push 26.08.2026
- Vollständige Entwickler-Referenz der offiziellen Fanvue REST API (extrahiert aus der OpenAPI-Spezifikation api.fanvue.com/docs/openapi.json): `README.md` (Grundkonzepte, App-Typen, Auth/Scopes) + `api-endpoints.md` (243 Operationen auf 183 Pfaden) + `docs/` mit Praxis-Beispielen (PPV-Generier-Pipeline, Voice-Messages/TTS, Free-Trial-Membership, Persona-Spezifikation, System-Prompt „Goddess Nora"). Reine Doku — kein Code.

### goddess_coaching
- Standort GitHub (privat): https://github.com/livedialai/goddess_coaching · JSON/Python 4.943 LOC, 237 Dateien, ~744 MB · Push 26.08.2026
- **Daten-Asset (PRIVAT):** Roh-Export zweier Telegram-Gruppenverläufe („goddess-gets-paid" 53 Nachrichten, „goddess-gets-paid-by-myra" 1.486 Nachrichten) mit Medien, extrahierten Audio-WAVs (16 kHz mono, 118 Stück) und Mistral-Voxtral-ASR-Transkripten (108), `users.json` (417 Nutzer), `summary.json`. Enthält außerdem das wiederverwendbare Telethon-Userbot-Tooling (`telethon-bot/`: join, dialogs, export_groups, extract_audio, voxtral_batch) — diente als Ausgangsdaten für die Persona „Goddess Nora".

### personas
- Standort GitHub (privat): https://github.com/livedialai/personas · Markdown 3 LOC, 3 Dateien, ~143 MB · Push 25.08.2026
- **Daten-Asset (PRIVAT):** Snapshot der Astria.ai-Medienbibliothek für das Goddess-Nora-Projekt: `astria_fotos_a.zip` (49 MB) + `astria_fotos_b.zip` (89 MB), zusammen 344 Bilder (LoRA-Trainingsfotos + generierte Bilder für die Tunings Marie/Leonie und 60 Generation-Prompts); wegen des 100-MB-GitHub-Limits in zwei Archive geteilt.

## Quellen & Methode
Geprüft: famdata/F6_fanvue.json, loc_results.json (LOC/Files/Top-Sprachen), Git-Logs aller Repos, README.md + INSTALL.md + app-store-listing.md von fanvue-livekit-signature, Struktur- und Modulvergleich der drei Chatbot-Versionen (src/-File-Listen, Zeilenzahlen der Kerndateien), `fanvue/`-Doku-Überblick, READMEs/Struktur von goddess_coaching (telethon-bot/*) und personas.

---

# FanMall Creator-SaaS

**Kategorie:** Eigenentwicklung (Landing Page + Admin-Bot; Teil einer SaaS-Vermarktung)
**Plattform(en):** Codeberg gofonia
**Kanonisches Repository:** kein Versionsreihe-Fall — zwei eigenständige Komponenten (fanmall = Landing, fanmall-adminbot = Telegram-Admin-Konsole)

## fanmall — Creator-AI-Assistant-Landing Page (fanmall.de)
**Standort:** Codeberg: https://codeberg.org/gofonia/fanmall · **Typ:** Eigenentwicklung
**Sprachen:** HTML · Python (Build-Generator) · **Codeumfang:** 5.657 LOC, 26 Dateien · **Letzter Push:** 28.08.2026

**Funktionsumfang:**
- **Mehrsprachiger Onepager** für https://fanmall.de — optisch an gofonia.de angelehnt (Pink/Orange-Gradient), 5 Sprachen (EN default, DE, ES, FR, PT) als separate Sprachseiten mit Sprachumschalter und `hreflang`-Tags.
- **Dark/Light-Mode:** Dark als Standard, Toggle (🌙/☀️) mit localStorage-Persistenz (`fanmall-theme2`).
- **Produkt-Pakete:** BASIC 29 € (Chat + Signatur), PRO 79 € (Chat + Signatur + Wunschfotos), ULTIMATE 149 € (+ 1.000 Anrufminuten) — jeweils mit 14-Tage-Trial.
- **CTA:** alle Buttons → https://login.fanmall.de (Apache-Proxy auf das Chatbot-Dashboard); **Kontakt:** support@fanmall.de + WhatsApp +49 162 6558333.
- **Build-Pipeline:** `build.py` generiert `public/` komplett neu (Template + Übersetzungs-Dicts), `legal.py` erzeugt die Rechtstexte (Impressum, AGB, Datenschutz) in allen 5 Sprachen; kein Framework, statische Ausgabe sofort deploybar.
- **Deployment:** `deploy/` enthält 3 Apache-VHosts (fanmall-port80.conf, fanmall-ssl.conf, login-fanmall-ssl.conf) plus certbot-Anleitung (fanmall.de + www + login); Zielverzeichnis `/var/www/fanmall`. Anpassungen: USt-IdNr. in `legal.py` ist noch Platzhalter, Preise/ Texte in `build.py`, Banner `public/assets/banner.jpg` (2320×1000).

**Aufbau/Module (aus Verzeichnisstruktur):**
- `build.py` = Onepager-Generator (Template + Übersetzungs-Dicts EN/DE/ES/FR/PT) → schreibt `public/`
- `legal.py` = Generator für Impressum/AGB/Datenschutz-Seiten (5 Sprachen)
- `public/` = generierte statische Seiten: internationale `index.html`, Sprachunterseiten `de/ es/ fr/ pt/`, Rechtseiten je Sprache, `assets/banner.jpg`
- `deploy/` = Apache-VHosts für fanmall.de + login.fanmall.de

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| — | — | keine Dubletten |

*Begründung kanonisch: entfällt — fanmall und fanmall-adminbot sind zwei verschiedene Komponenten desselben Produkts, keine Versionsreihe.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### fanmall-adminbot
- Standort Codeberg: https://codeberg.org/gofonia/fanmall-adminbot · JavaScript (Node.js) 1.108 LOC, 5 Dateien · Push 29.08.2026
- **Telegram-Multi-User-Admin-Konsole** für den Fanvue-Chatbot (Goddess Nora/FanMall): eigenständige PM2-App (`fanvue-admin-bot`), die die SQLite-DB des Chatbots mitbenutzt und dessen `fanvueApi.js` dynamisch importiert (Pfade im Code: `/root/fanvue-live/fanvue/…`). Funktionen: Login/Passwort (scrypt, 5 Fehlversuche → 10-Minuten-Sperre je Chat), Rollen `owner`/`admin`, einmalige Tenant-Kopplung (Chat ↔ Agentur-Konto), Persona-Umschaltung, Sprachwahl EN/DE/FR/ES/PT/IT (`/language`), Vault-Medienverwaltung (Liste mit AI-Tags, Soft-Delete mit Bestätigung, Upload aus Telegram), System-Prompt-Edit mit **LLM-Rewrite** (DeepSeek → Vorschau → Speichern), Voice-ID-Wechsel (`tts_voice_id`), Preisübersicht inkl. Fanvue-`recommendedPrice`, Status-Check; Klassifizierungs-Batch (Stufen A–E, Tags, Preisbänder, `media_catalog`-Tabelle + `sqlite-vec`) laut TODO als Nächstes geplant. Befehle: `/start /hilfe /help /persona /tenant /klassifizieren /preise /medien /prompt /voice /status /passwort /language /logout /addadmin`.

## Quellen & Methode
Geprüft: famdata/F7_fanmall.json, loc_results.json (LOC/Files/Top-Sprachen), Git-Logs, README.md beider Repos (Features, Befehle, Setup, DB-Schema), `build.py` (Template-Struktur, Preis-/CTA-Daten), Verzeichnisbaum `public/` (5 Sprachen + Rechtseiten) und `deploy/` (VHosts).

---

# GoDinIA Restaurant-Booking-Suite

**Kategorie:** Eigenentwicklung
**Plattform(en):** GitHub livedialai, Codeberg gofonia (beide)
**Kanonisches Repository:** godinia-neu
**Umfang kanonisch:** 8.661 Codezeilen, 21 Dateien (Quelle: loc_results.json, nur sourceCount)

## godinia-neu — Reservierungs-SaaS + Waxum WhatsApp-Bridge
**Standort:** Codeberg: https://codeberg.org/gofonia/godinia-neu · **Typ:** Eigenentwicklung
**Sprachen:** PHP, HTML+PHP, JavaScript · **Codeumfang:** 8.661 LOC, 21 Dateien · **Letzter Push:** 2026-08-28 (Einzel-Commit)

**Funktionsumfang:**
- Umbau der WhatsApp-Benachrichtigung des GoDinIA-Terminbuchungs-Plugins: **OpenWA (api.wapi.life) → Waxum (Rust REST-Gateway)** über einen lokalen Node-Bridge-Dienst
- WP-Plugin nimmt keine Waxum-Credentials entgegen — nur Bridge-URL + Bridge-Key als WP-Options (`dinia_wa_bridge_url`, `dinia_wa_bridge_key`); Bridge hält Waxum-Token ausschließlich in ihrer eigenen `.env`
- Bridge normalisiert Empfängernummern (`+49…` → `4917…@s.whatsapp.net`) und forwardet per Bearer-Token an die Waxum-Session „Reservierung"
- Pro Tenant (Restaurant) konfigurierbar in `wp_dinia_customers.settings` (JSON): `whatsapp_enabled`, `whatsapp_number` (Admin-Empfänger), `whatsapp_notify_admin`, `whatsapp_notify_guest`
- Enthält das vollständige GoBookMe-SaaS-WP-Plugin (v1.3.4, Multi-Tenant-Reservierung mit Mollie, CalDAV, REST-API, JS-Widget) inkl. Bridge-Patch in `class-booking.php` (`send_whatsapp()` → `POST /send` mit `X-Bridge-Key`)
- Auf dem Produktionsserver (212.87.214.9, teilt sich GoDinIA + Pizzafamily + Gomeetme) verifiziert: Testreservierung lief durch — Admin-WA + Gast-WA, Bridge-Status 200, Waxum `status=sent`

**Aufbau/Module (aus Verzeichnisstruktur):**
- `bridge/bridge.js` = Node-HTTP-Bridge ohne Dependencies (Mini-`.env`-Loader): `POST /send`, Auth per `X-Bridge-Key`, JID-Normalisierung, Forward `Bearer → /api/v1/sessions/{session}/messages/text`, `GET /health` · `bridge/ecosystem.config.cjs` = PM2-Konfiguration · `bridge/.env.example` = PORT, BRIDGE_KEY, WAXUM_URL, WAXUM_TOKEN, WAXUM_SESSION
- `gobookme-saas/` = WP-Plugin (Hauptdatei `gobookme-saas.php`, `includes/class-booking.php` mit Bridge-Patch, `includes/class-admin.php` mit Bridge-Einstellungen, readme.txt, templates/) — laut README „enthält den Bridge-Patch"
- `README.md` = Architektur-Diagramm (Dinia-Plugin → Node-Bridge :3452 → Waxum :3451), Setup-Anleitung (PM2, wp-cli, MySQL-JSONSET), Testfluss, Diff-Tabelle „OpenWA → Waxum" (Endpunkt, Auth-Header, Nummern-Format, Rückgabe, Konfiguration), Sicherheitshinweise (nie Tokens ins Repo, nur `.env.example`)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| orders (`gobookme-saas/`) | GitHub livedialai | Zwischenstand v1.3.4: gleiche Plugin-Basis, zusätzlich OrderSprinter-Bondrucker-Integration, **aber ohne** Waxum-Bridge (alte OpenWA-WA-Notification); Diff vs. godinia-neu nur in class-admin.php + class-booking.php |
| gobookme | GitHub livedialai | Basisstand v1.3.4 des SaaS-Plugins (Multi-Tenant + Mollie + Widget), älterer Patch-Stand als orders (class-admin/-booking/-rest-api weichen ab) und ohne Bridge |
| wpplugin-godinianeu | Codeberg gofonia | Eigenständiges Einzel-Restaurant-Plugin „GoFonIA Restaurant Buchungen" (v1.3.0) — funktional abgelöst durch die SaaS-Reihe |
| gomeetme-restaurant | GitHub livedialai | Funktional ähnliches, aber unabhängig entwickeltes Tischreservierungs-Plugin (Schwesterprodukt, eigener Code-Stamm) |
| gobookme-client | GitHub livedialai | Komplementär-Client (Widget-Einbindung), keine Dublette |
| caldavshop | Codeberg gofonia | Verwandter Vertriebskanal (Shop für CalDAV-Buchungs-Plugins), kein Plugin-Duplikat |
| godinia | GitHub livedialai + Codeberg gofonia | Leeres Repo ohne Commits (Platzhalter) |

*Begründung kanonisch: godinia-neu ist der am weitesten entwickelte Stand der Versionsreihe — es baut auf dem orders-Bundle samt OrderSprinter-Integration auf, ergänzt den Waxum-Umbau (OpenWA → Node-Bridge → Waxum, Rust) mit dokumentierter und auf dem Produktionsserver verifizierter Architektur, ist der jüngste Stand (Committeldatum 2026-08-28) und hat mit 8.661 LOC den größten Code-Umfang.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### godinia
- Standort GitHub: https://github.com/livedialai/godinia · Sprachen: – · LOC: 0 (keine Commits, `main` ohne Historie) · Letzter Push (Metadaten): 2026-07-29
- Zweck nicht erkennbar (leeres Repo ohne Commits und Metadaten); vermutlich reservierter Platzhalter-Namen für das GoDinIA-Projekt. Unter demselben Namen existiert auch ein leerer Codeberg-Klon (erstellt 2026-08-17).

### wpplugin-godinianeu
- Standort Codeberg: https://codeberg.org/gofonia/wpplugin-godinianeu · Sprachen: PHP, XML+PHP · LOC 1.792, 12 Dateien
- **„GoFonIA Restaurant Buchungen" v1.3.0** (readme.txt-Stable-Tag 1.3.0): eigenständiges WP-Plugin für einzelne Restaurants — Buchungsformular per Shortcode `[gofonia_booking]`, Tische mit Sitzplätzen, Öffnungszeiten pro Wochentag inkl. Schließtage, Monatskalender mit Tagesliste und Status-Änderung per Klick, Statusverwaltung (Neu/Bestätigt/Storniert/Abgeschlossen/Nicht erschienen), E-Mail-Benachrichtigungen an Restaurant und Gast, Gäste-Limit, Honeypot-Spam-Schutz, DSGVO-Hinweis, Uninstall-Bereinigung. Module: `includes/class-gofonia-booking(-calendar/-caldav/-mailer/-settings/-table/-form/-post-type).php`, `templates/booking-form.php`, assets/css+js.

### gobookme
- Standort GitHub: https://github.com/livedialai/gobookme · Sprachen: PHP, HTML+PHP · LOC 8.285, 18 Dateien · Letzter Push: 2026-07-05 (v1.3.4)
- **„Dinia – GoBookMe SaaS"**: WordPress-Multi-Tenant-SaaS (beliebig viele Restaurants auf einer Instanz) mit Mollie-Billing (Pläne, Subscriptions, Coupons, Rechnungen, Webhooks), JS-Buchungs-Widget (`assets/widget.js`, fullcalendar), API-Key-Auth (SHA256-gehasht), Plan-Limits (Tisch-/Reservierungslimits), Self-Service-Signup mit Cloudflare Turnstile, CalDAV-Sync, Admin-DB-Backup, Bestätigungs-Mails. 15 Module in `includes/` (u. a. tenant, customers, plans, subscriptions, mollie, invoices, signup, backup, rest-api, booking, mailer, caldav).

### gobookme-client
- Standort GitHub: https://github.com/livedialai/gobookme-client · Sprachen: PHP · LOC 174, 1 Datei (gobookme-client.php, v1.1.2)
- Client-Seiten-Plugin: bettet das Dinia-Buchungs-Widget auf Restaurant-Websites ein — mit konfigurierbarer SaaS-URL, API-Token und Tenant-E-Mail (API-Key-Auth), Shortcode/Widget-Einbindung. Komplementär-Pendant zum SaaS-Plugin.

### orders
- Standort GitHub: https://github.com/livedialai/orders · Sprachen: PHP, HTML+PHP · LOC 8.509, 20 Dateien · Letzter Push: 2026-07-29
- **Reservierungs- & Kassensystem-Bundle** (Pizza-Family-/GoBookMe-Infrastruktur): `gobookme-saas/` = SaaS-Plugin v1.3.4 mit **OrderSprinter-Bondrucker-Integration** (Module off/additional/instead, HTTP-POST an `reservationprint.php` mit Shared Secret → Printqueue `%printjobs%` → javaprinter im Restaurant) sowie Brevo-Mails mit `wp_mail()`-Fallback; dazu `ordersprinter-2_9_12-resprint.zip` (18,6 MB) = direkt installierbare OrderSprinter-2.9.12-Kassensystem-Distribution inkl. Reservierungsdruck-Endpunkt. Dieser Stand ist die Vorstufe von godinia-neu (dort nur die zwei Patch-Dateien weiterentwickelt).

### gomeetme-restaurant
- Standort GitHub: https://github.com/livedialai/gomeetme-restaurant · Sprachen: PHP, XML+PHP · LOC 1.328, 8 Dateien · Letzter Push: 2026-07-05 (Initial, Version 1.1.0)
- **GoMeetMe Restaurant**: tischbasiertes Reservierungssystem — Tischverwaltung mit Sitzplätzen (2er/4er/6er/8er), automatische Tischzuweisung nach Personenzahl, Zeitslots aus Öffnungszeiten, Online-Reservierung per Shortcode `[gomeetme_restaurant]`, manuelle Admin-Buchungen (Telefon), Dashboard-Widget mit Tagesreservierungen, Bestätigungs-E-Mails, komplett auf Deutsch, mobile-responsive. Module: `includes/class-admin/-caldav/-shortcode/-booking/-rest-api.php`.

### caldavshop
- Standort Codeberg: https://codeberg.org/gofonia/caldavshop · Sprachen: HTML+PHP, Python · LOC 1.326, 11 Dateien · Erstellt 2026-06-20
- **GoFonIA Shop**: FastAPI-Verkaufs- und Lizenzshop (betrieben unter shop.gofonia.de via PM2) für CalDAV-Buchungs-Plugins — 2×4-Produktmatrix (WP-Plugins und Standalone-Lösungen, 0–79 €: Infomaniak, KMeet, Universal, Paid Events), Mollie-Checkout (`POST /checkout`) mit Webhook `GET /webhook/mollie`, SQLite-Lizenzdatenbank, AES-256-CBC-verschlüsselte Lizenzdaten, Verifikationsendpunkt `/api/verify-license?license_key=X&domain=Y`, Admin-Bereich, Zip-Auslieferung (`zips/wp-infomaniak.zip`, `wp-universal.zip`). Hinweis: Mollie-Key, Password und SECRET sind in `app.py` hartkodiert.

## Quellen & Methode
Geprüft: famdata-F8_godinia_booking.json; je Repo README/readme.txt, Hauptdatei (gobookme-saas.php, gofonia-restaurant-booking.php, gobookme-client.php, app.py), includes-Verzeichnisse, git log; Diff-Vergleiche gobookme vs. orders/gobookme-saas sowie orders/gobookme-saas vs. godinia-neu/gobookme-saas (nur class-admin.php und class-booking.php unterscheiden sich); loc_results.json für LOC/sourceCount.

---

# ToMeetIA / GoMeetMe Meeting-Suite

**Kategorie:** Fork/Anpassung (ToMeetIA-Videokonferenz, GoMeetMePRO) + Eigenentwicklung (GoMeetMe-Buchung, Zusatz-Plugins)
**Plattform(en):** Codeberg gofonia (ToMeetIA-Snapshots, malka/tricoma Meet) und GitHub livedialai (GoMeetMe-Suite)
**Kanonisches Repository:** gomeetiav3 (ToMeetIA-Videokonferenz) bzw. gomeetme (GoMeetMe-WordPress-Suite)
**Umfang kanonisch:** gomeetiav3: 85.488 Codezeilen, 963 Dateien (Quelle: loc_results.json)

## ToMeetIA (gomeetiav3) — GoFonIA-Videokonferenz auf La Suite Meet-Basis

**Standort:** Codeberg: https://codeberg.org/gofonia/gomeetiav3 · **Typ:** Fork/Anpassung (Upstream: La Suite Meet + LiveKit, Lizenzen MIT/Etalab-2.0)
**Sprachen:** Python, JavaScript/Django-Jinja, TSX (React) · **Codeumfang:** 85.488 LOC, 963 Dateien · **Letzter Push:** 10.06.2026

**Funktionsumfang:**
- Öffentlich erreichbare Videokonferenz-Plattform unter `join.gofonia.de` (Fork von La Suite Meet mit LiveKit-Websocket `wss://live.gofonia.de`), ohne Login-Zwang: Besucher können Räume erstellen oder Meeting-Links beitreten
- OIDC/Auth umgangen: `GET /api/v1.0/users/me` liefert für Anonyme `{id: null, name: "Anonymous", is_authenticated: false}` statt 401; Raum-Erstellung über `ALLOW_UNREGISTERED_ROOMS=True` auch ohne Login
- Flexible Raum-Namen (`isRoomValid.ts` auf `[a-zA-Z0-9_-]+` erweitert), ToMeetIA-Branding (Header-Schriftzug, Pink/Rot/Orange-Verlauf, Webmanifest, Landing-Page angepasst)
- SIP-Einwahl (Dokumentation `docs/SIP_DIALIN.md`, Installer `scripts/install-asterisk-sip-gateway.sh`, `sip/`), Magic-Link-Dienst (`magic_link_service.py`), mehrsprachige Frontend-Lokalisierung (Crowdin-Konfig)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `src/backend/core/api/` (viewsets.py = anonyme Nutzer-/Raum-API, permissions.py = Raum-Rechte, serializers.py, views.py), `src/backend/core/services/lobby.py` = Lobby-Service
- `src/frontend/src/` = React/TSX-Frontend (Features: auth, rooms, analytics, home), `src/frontend/src/layout/Header.tsx` = Branding, `locales/` = i18n
- `magic_link_service.py` = Magic-Link-Dienst; `sip/` + `scripts/install-asterisk-sip-gateway.sh` = Asterisk-SIP-Gateway
- `public-guest-deploy/` = produktive Minimal-Deploy-Konfiguration (compose.yml, nginx.conf, livekit.yaml, backend.env.example, postgresql.env.example)
- `docker/`, `docker-compose.full.yml`, `Dockerfile.backend-patch`/`frontend-prod`/`magic-link`, `deploy/`, `bin/`, `docs/` (OpenAPI, Theming, SIP_DIALIN)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| gomeetia | Codeberg | Älterer Snapshot (V1) derselben Codebasis, 85.248 LOC / 958 Dateien, nahezu identische README und Custom-Dateien |
| gomeetiav2 | Codeberg | Zwischensnapshot (V2), 85.365 LOC / 976 Dateien; Unterschiede nur im deploy/-Ordner (asterisk, paas, sip-bridge, backend.env) |
| gomeetiav3 | Codeberg | **Kanonisch:** höchste LOC (85.488), jüngster Stand (10.06.2026), zusätzlich build.sh, docker-compose.full.yml, deploy.Dockerfile, Dockerfile.backend-patch/frontend-prod/magic-link, magic_link_service.py, production.env, sip/, scripts/, docs/SIP_DIALIN.md, src/backend/core/api/views.py |
| gomeetiav31 | Codeberg | Leeres Repo ohne Commits (nur `.git`, 0 LOC) — offenbar fehlgeschlagener Snapshot/leerer Push |
| malka | Codeberg | Branding-Variante „tricoma Meet" derselben Codebasis (README „tricoma Meet", tricoma-Logo in Frontend), älterer Stand (06.06.2026, 83.761 LOC / 932 Dateien), ohne die zusätzlichen Deploy-/SIP-Dateien; vorhandene Backend-Diffs (serializers.py, lobby.py, urls.py, utils.py) gegenüber gomeetiav3 |

*Begründung kanonisch: gomeetiav3 ist die jüngste und am weitesten entwickelte Version der Reihe — höchste LOC, jüngster Commit und deutlich größerer Funktionsumfang an Deploy-/Betriebsarten (Dockerfile-Varianten, Docker-Compose-Varianten, Magic-Link-Service, SIP-Gateway, production.env, build.sh), während gomeetia und gomeetiav2 nur ältere bzw. im deploy/-Bereich abweichende Snapshots sind und gomeetiav31 leer ist; malka ist eine eigenständig gepflegte, aber ältere Branding-Variante (tricoma) derselben Repobasis.*

## GoMeetMe (gomeetme) — WordPress-Terminbuchungs-Suite (Distribution)

**Standort:** GitHub: https://github.com/livedialai/gomeetme · **Typ:** Eigenentwicklung (Distribution)
**Sprachen:** PHP (in eingebetteten ZIPs), Markdown · **Codeumfang:** 280 LOC, 5 Dateien — der eigentliche Quellcode liegt in den beiden Plugin-ZIPs · **Letzter Push:** 24.06.2026

**Funktionsumfang:**
- Zwei WordPress-Plugins zur Online-Terminbuchung mit Infomaniak-CalDAV-Anbindung als ZIP-Release: `gomeetmev1.3.zip` (GoMeetMe v1.3 „Klassisches 3-Schritte-Buchungsformular per Shortcode", Version 1.2.0) und `gomeetmepro.zip` (GoMeetMePRO, 644 Dateien, KI-Chatbot mit Sprachsteuerung; das ZIP entspricht im Wesentlichen dem Repo gomeetme-pro)
- Infomaniak-CalDAV-Integration (Benutzername USER123456, Passwort, Kalender-Slug), automatische KMeet-Video-Meeting-Links, E-Mail-Bestätigungen an Kunde + Admin, intelligente Slot-Berechnung, DSGVO-konform (Daten in der Schweiz), Partner-/Call-Home-System, rein WordPress-HTTP-API ohne Composer
- Administrierbare Verfügbarkeit (Wochentage, Zeitfenster, Zeitzone Europe/Berlin, Buchungspuffer, max. 60 Tage im Voraus, 15–180 Min. Dauer)
- `docs/gomeetme-v1.3-integration.md` und `docs/gomeetmepro-integration.md` = Integrations-Dokumentation

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### gomeetme-pro
- Standort https://github.com/livedialai/gomeetme-pro · Sprachen PHP (32.296 LOC, 398 Dateien) · Letzter Push 05.07.2026
- GoMeetMePRO v3.0.3: Fork/Anpassung des **Kognetiks Chatbot (Chatbot ChatGPT) v2.4.6** um Terminbuchung erweitert. Der README-Titel „Kognetiks Chatbot for WordPress" ist unveränderter Upstream-Text — der tatsächliche Plugin-Header lautet „GoMeetMePRO / KI-Chatbot mit Terminbuchung via Infomaniak CalDAV, KMeet-Video-Meeting und E-Mail-Benachrichtigungen", Autor GoFonIA. Eigene Module: `includes/class-gomeetmepro-{caldav,booking,brevo,partner,admin,tools}.php` (OpenAI-Tool-Calling in tools.php), Kognetiks-Kern `chatbot-chatgpt.php`, KI-Provider: Mistral AI (Standard), OpenAI, Anthropic, Google AI, Azure, NVIDIA, DeepSeek; umfangreiche Dokumentations-Suite unter `documentation/`

### gomeetme-affiliate
- Standort https://github.com/livedialai/gomeetme-affiliate · Sprachen PHP (396 LOC, 3 Dateien) · Letzter Push 05.07.2026
- „GoMeetMe Affiliate System" v1.0.0: Affiliate-Tracking als WP-Plugin — jeder Installer wird automatisch Partner, Domain dient als Affiliate-Code, 20 % Standard-Kommission, 30-Tage-Cookie, Datenbanktabellen für Affiliates/Sales, Admin und Admin2 (CLI-Varianten)

### gomeetme-activation-receiver
- Standort https://github.com/livedialai/gomeetme-activation-receiver · Sprachen PHP (245 LOC, 1 Datei) · Letzter Push 05.07.2026
- „GoMeetMe Activation Receiver" v1.4.0: nimmt Aktivierungs-Benachrichtigungen der GoMeetMe Free/Pro/Restaurant-Plugins über REST-Endpoint `gomeetme/v1/activate` (Homepage, Admin-E-Mail, Version, Plugin-Typ, IP) entgegen, speichert in Tabelle `gomeetme_activations`, mit Paginierung, Export und Löschfunktion im Admin

### meetbot-calendar
- Standort https://github.com/livedialai/meetbot-calendar · Sprachen PHP (766 LOC, 8 Dateien) · Letzter Push 29.06.2026
- „GoFonIA Booking Calendar for Meet.bot" v1.0.1: WP-Plugin, das freie Buchungszeiten von Meet.bot als Wochenkalender einbindet (API-Klasse `includes/class-api.php`, Shortcode `[meetbot_calendar]`, Admin `class-admin.php`, Light/Dark-Theme, DE/EN-Sprachdateien, Google-Meet-Integration, „Powered by GoFonIA"-Branding)

## Quellen & Methode

Verifiziert: famdata-Datei F9_tomeetia_meet.json (Pfade, LOC, README-Auszüge, Top-Level-Einträge), loc_results.json (LOC-/Dateizahlen), README.md aller zehn Repos, Plugin-Header in gomeetme-pro.php / gomeetme-affiliate.php / gomeetme-activation-receiver.php / meetbot-calendar.php, `diff -rq` zwischen gomeetia/gomeetiav2/gomeetiav3/malka zur Einordnung der Versionsreihe, `unzip -l` der Plugin-ZIPs in gomeetme, git-Log (Commits/Stand) aller lokalen Klone, Inhalt der docs/-Ordner.

---

# WordPress-Telefonie-Plugin-Suite

**Kategorie:** Eigenentwicklung
**Plattform(en):** GitHub livedialai und Codeberg gofonia
**Kanonische Repositorys:** `gofonia-voice-wp_en` (GoFonIA-Voice-Agent-Reihe) und `mistral-voice-agent` (Mistral-Voice-Agent-Reihe)
**Umfang kanonisch:** gofonia-voice-wp_en: 1.679 LOC, 18 Dateien · mistral-voice-agent: 2.283 LOC, 10 Dateien (Quelle: loc_results.json)

Die Familie umfasst insgesamt **12.807 LOC** in vier Entwicklungslinien: (1) GoFonIA Voice Agent (v3.0.0, DE/EN), (2) Mistral/Grok-Voice-Agent-Widget (Browser-Speech), (3) Jambonz- und SIP.js-Telefonie-Widgets und (4) LiveKit-Voice-Widget.

## GoFonIA Voice Agent (v3.0.0) — WordPress-Plugin

**Standort:** Codeberg: https://codeberg.org/gofonia/gofonia-voice-wp_en · **Typ:** Eigenentwicklung
**Sprachen:** PHP/XML+PHP/Markdown · **Codeumfang:** 1.679 LOC, 18 Dateien · **Letzter Push:** 06.08.2026 (Commit `f8487bc`: EN localization + TopUp + Buy Number flow)

**Funktionsumfang:**
- KI-Sprachassistent: verbindet die WordPress-Seite mit dem GoFonIA-Backend (`https://tenant.gofonia.de`), weist dem Kunden eine Rufnummer (DID) zu, stellt einen sprachgesteuerten Voice-Agent mit Anruf-Widget bereit
- Registrierung/Login per generiertem Username (Backend-API) mit Pflicht-Consent-Checkbox („Kein Auto-Activate“ aus Datenschutzgründen)
- Tab-Menü innerhalb des Plugins ohne Sidebar-Spam: Verbinden, Dashboard, Einstellungen (Meetergo/CalDAV), Prompt-Editor, Tools, Weiterleitung, Wissen, Reservierungen, Tarif, Abmelden
- Reservierungen: Tische anlegen, Tischliste, Reservierungsverwaltung über Backend-API `/api/booking/...`
- Anruf-Widget: konfigurierbare Position, Sichtbarkeit (global / Startseite / Shortcode `[gofonia_widget]`), Button-Text
- DID-Auflösung wie der Telefonagent: Widget-Token wird serverseitig über AJAX-Proxy (`admin-ajax.php?action=gofonia_widget_token`) bezogen — API-Key bleibt im Server, kein Key-Leak ins HTML
- Billing/Tarif-Tab, Firmenwissen, Custom Tools, Weiterleitungen; EN-Version zusätzlich mit TopUp- und Buy-Number-Flow (US) über Template `numbers.php`

**Aufbau/Module (aus Verzeichnisstruktur):**
- `gofonia-voice.php` = Plugin-Hauptdatei; `includes/class-api-client.php` = Backend-API-Client, `includes/dashboard.php` = Admin-Dashboard-Logik, `includes/gofonia-voice.php` = Register-Callback
- `templates/` (12 Dateien): connect.php, dashboard.php, settings.php, prompt-editor.php, tools.php, forwards.php, knowledge.php, reservations.php, billing.php, numbers.php, admin-header.php, frontend.php
- `assets/widget.js` = Anruf-Widget (Frontend), `debug-log.php` = Debug-Ausgabe

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| gofonia-voice-wp_en | Codeberg | **Kanonisch** — neueste v3.0.0-Version (06.08.), EN-Lokalisierung, zusätzlich TopUp + Buy-Number-Flow (`numbers.php`) |
| gofonia-voice-wp | Codeberg | Deutschsprachige Ausführung derselben v3.0.0 (05.08.); funktionaler Soft-Predecessor der `_en`-Fassung (kein `numbers.php`, kein TopUp) |
| wp-cloud-plugin | Codeberg | Ältere v2.0.0-Variante (24.07.): gleiche Struktur, zusätzlich `dispatch-agent.py` (reicht Tenant-DID per JWT an Cloud-Agent), kein README |
| wp-gofonia | Codeberg | Älteste Version dieser Reihe (v2.0.0/2.0.3, 23.07.), 750 LOC, kein README — Basis der 2.x-Linie |

*Begründung kanonisch: `gofonia-voice-wp_en` hat die höchste LOC-Zahl der Reihe außer der LiveKit-Sonderform (1.679 vs. 1.558/817/750), den jüngsten Push (06.08. vs. 05.08./24.07./23.07.) und die meisten Features (TopUp-Flow, Buy-Number-Flow mit eigenem Template, EN-Lokalisierung). Die DE-Version enthält kein Feature, das der EN-Version fehlt.*

## Mistral Voice Agent — WordPress-Plugin (Browser-Speech)

**Standort:** GitHub: https://github.com/livedialai/mistral-voice-agent · **Typ:** Eigenentwicklung
**Sprachen:** PHP/HTML+PHP/Markdown · **Codeumfang:** 2.283 LOC, 10 Dateien · **Letzter Push:** 26.07.2026

**Funktionsumfang:**
- Bidirektionales Sprach-Widget im Browser (AudioWorklet, PCM 16 kHz), schwebender Anruf-Button, kein Walkie-Talkie
- STT: Speechmatics Realtime WebSocket (partielle Transkripte, EndOfUtterance-Stille-Erkennung) mit Voxtral-Batch-STT als Fallback
- LLM: Mistral Chat mit Function Tools (Terminprüfung, Reservierung, Wissen etc.), HTTP-Webhook- oder GoDinIA-/Meetergo-Integration
- TTS: Voxtral; Voice Cloning
- Gesprächszusammenfassung: nach Anrufende automatische Auswertung + E-Mail-Versand (Brevo)
- REST-API, Call-Home (GoMeetMe-Receiver), Debug-Logging, Shortcode, Admin-Konfiguration (API-Keys, Prompt, Voice, Tools)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `mistral-voice-agent.php` = Plugin-Hauptdatei; `includes/class-frontend.php` = Widget/AJAX, `class-proxy.php` = STT/LLM/TTS-Proxy (API-Keys bleiben serverseitig), `class-tools.php` = Function Tools, `class-admin.php` = Einstellungen, `class-logger.php` = Logging
- `assets/`: voice.js, voice.css, pcm-worklet.js, admin.css, index.php (Schutz); `readme.txt`/`license.txt` für Veröffentlichung

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| mistral-voice-agent | GitHub (livedialai) | **Kanonisch** — neueste Version (26.07.): korrekt umbenannter Entry-Point, Cal.com-Integration zusätzlich zu Meetergo/GoDinIA, Veröffentlichungsartefakte (license.txt, readme.txt, index.php-Härtung) |
| wp-mistral-plugin | Codeberg | Zwischenversion (25.07.): identische Struktur, aber noch `xai-voice-agent.php` als Entry-Point (Kopie des Grok-Plugins), keine Cal.com-Integration, kein license.txt/readme.txt |
| wp-grok-speech | Codeberg | Vorläufer-Reihe (16.07., 687 LOC): xAI/Grok-Variante (Browser Speech API → Grok-4 → xAI Eve TTS), gleiche Klassenstruktur (class-admin/tools/frontend/logger/proxy); noch ohne AudioWorklet |
| grok-speech-standalone | Codeberg | Ableger (16.07., 753 LOC): **ohne WordPress** — Self-hosted VoiceAssistant (Deepgram STT, OpenAI-kompatible Chat-API z. B. Requesty/Groq/xAI, xAI TTS Eve, Login über .env.php, Admin-Bereich mit Tools & Live-Debug-Log) |

*Begründung kanonisch: `mistral-voice-agent` hat den höchsten Funktionsumfang und die jüngste Historie (2283 LOC, 26.07.), zusätzlich Cal.com-Integration, sauberen Plugin-Namen und Releaseartefakte. `wp-mistral-plugin` (2038 LOC) ist die Codeberg-Zwischenstufe mit zwei Wissenslücken (falscher Dateiname, fehlende Cal.com), `wp-grok-speech` ist die ältere xAI-Variante derselben Codebasis.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### wp-jambonz
- Standort Codeberg: https://codeberg.org/gofonia/wp-jambonz · PHP/JS/CSS · 1.023 LOC · letzter Push 20.07.2026
- Multi-Tenant **Jambonz Voice AI**-Widget (Version 1.0.0, v3.0.0): DID-Provisionierung über Backend-API, Admin-Konfiguration (Prompt, Voice ID, Tools, Weiterleitungsziele), Shortcode `[jambonz-phone]`
- JsSIP via CDN (kein Build-Step) → SIP-over-WebSocket (WSS) zum Jambonz SBC; SIP-Identity = Tenant-DID, Anruf auf SIP-Application-URI; Zusatz-Header `X-Tenant-ID`/`X-DID`; Tenant-Auflösung serverseitig über DID, Fallback-Weiterleitung an konfigurierte Nummern

### wp-webphone
- Standort Codeberg: https://codeberg.org/gofonia/wp-webphone · PHP/JS/CSS · 481 LOC · letzter Push 20.07.2026
- **SIP.js-Softphone** (lokal gebündelt `sip-0.11.6.min.js`) als WordPress-Widget; Konfiguration: Asterisk-Server, SIP-User/Passwort, DID (Caller ID/From-Header), Anrufziel; kurzer Shortcode `[webphone title color position]`
- Kein Build-Step, keine Abhängigkeiten, Inline-CSS/JS; teilt sich das Plugin-Scaffold (jambonz-voice.php, class-activator/deactivator/admin/widget/shortcode) mit wp-jambonz, ist aber ein eigenständiges Produkt (normale SIP-Anrufe statt Voice-AI)

### wp-livekit
- Standort Codeberg: https://codeberg.org/gofonia/wp-livekit · Python/JS/PHP · 352 LOC · letzter Push 16.07.2026
- **LiveKit Voice Widget**: WordPress-Plugin (`wp-plugin/lk-voice.php`) + LiveKit-Agent (`agent/agent.py`: Deepgram nova-3 STT, Requesty/Groq/OpenAI-LLM, InWorld-/xAI-Eve-TTS; Tools `verfuegbare_zeiten`, `tisch_reservieren`, `gespraech_beenden`) + JWT-Token-Server (`agent/token-server.py`)
- BYOK-Modell: jeder Kunde trägt eigene API-Keys ein (Cascade-Fallbacks); Browser-Widget (`widget/widget.js` + gebündeltes `livekit-client.umd.js`); Server-Configs (nginx.conf, livekit.yaml, PM2 `ecosystem.config.js`)

### wp-greview
- Standort Codeberg: https://codeberg.org/gofonia/wp-greview · PHP · 386 LOC · letzter Push 15.07.2026
- **Google Reviews Slider**: Shortcode `[greviews]`, Daten über serper.dev-API (Place-ID-Finder), Light/Dark-Mode, Sterne-Filter, Auto-Slide mit Hover-Pause, Touch-Swipe, 24h-Cache via WordPress-Transients, GoMeetMe-Push bei Aktivierung

## WP AI Edit & WP Agency Edit — KI-Editoren für WordPress (öffentlich)

**Standort:** GitHub: https://github.com/livedialai/wp-ai-edit · https://github.com/livedialai/wp-agency-edit · **Typ:** Eigenentwicklung (öffentliche Repos mit ZIP-Releases)
**Sprachen:** PHP, JavaScript

**Funktionsumfang:**
- **WP AI Edit** — KI-Chat im WordPress-Backend (schwebendes Widget in wp-admin, nicht auf der öffentlichen Website): Seiten befüllen (Block-Markup), Einstellungen setzen, Plugins installieren/aktivieren/konfigurieren, fremde Designs als Vorlage einlesen; Sicherheits-Snapshot vor jeder Änderung und Rollback; die Fähigkeiten laufen über die WordPress-Abilities-API.
- **Agentur-Fernzugriff:** Eine Agentur-Instanz bedient die Website über HTTPS fern — Zugang über WordPress-Anwendungspasswörter (Kernbestandteil, kein eigenes Schlüsselsystem; Widerruf wirkt sofort); jede Anmeldung und jeder Aufruf werden protokolliert (Zeit, Zugangsname, Route, Status, gekürzte IP).
- **WP Agency Edit** — Zentrale für betreute Websites: beliebig viele Kunden-Websites mit Adresse und Anwendungspasswort hinterlegen, Verbindung prüfen und per KI-Chat auf der jeweiligen Kundenseite ändern — das Sprachmodell läuft zentral; die Kundenseiten brauchen keinen eigenen API-Zugang (DeepSeek, OpenAI, Mistral, Ollama u. a.).
- **Bezug:** WP Agency Edit ist das Gegenstück zu WP AI Edit — auf der Kundenseite den Fernzugriff einrichten, in der Zentrale hinterlegen; Installation als ZIP über die GitHub-Releases.

## Quellen & Methode
Geprüft wurden: famdata-JSON-Einträge (LOCs, Strukturbäume, README-Auszüge), README.md aller 12 Repos, Plugin-Hauptdateien (Version/Description-Header), Komplett-Diffs (`diff -rq`) zur Dublettenverifikation, `git log` letzte Commits, `includes/`-Klassen- und `assets/`-Strukturen (u. a. `class-proxy.php`, `jambonz-widget.js`, `agent/agent.py`, `token-server.py`), loc_results.json (LOC-Werte bestätigt).

---

# RustPBX & HiPIA-Appliance

**Kategorie:** Fork/Anpassung mit Eigenentwicklungs-Anteilen (Basen: restsend/rustpbx bzw. miuda-ai/active-call, beide MIT, Shen Jindi / fourz.cn)
**Plattform(en):** Codeberg gofonia (alle Repos dieser Familie)
**Kanonisches Repository:** rustpbx_de (PBX-Linie) — Appliance-Linie: HiPIA-Appliance (s. u.); gofonia-rust = Basis-Dev-Repo derselben Linie
**Umfang kanonisch:** 213.702 Codezeilen, 625 Dateien (Quelle: loc_results.json)

## rustpbx_de — Hochperformante, softwaredefinierte TK-Anlage (SIP-Proxy & Web-Konsole) in Rust, deutsch lokalisiert

**Standort:** Codeberg: https://codeberg.org/gofonia/rustpbx_de · **Typ:** Fork/Anpassung (Upstream: restsend/rustpbx)
**Sprachen:** Rust 161.355 · HTML (Django/Jinja-Templates) 24.843 · Python 8.823 · TOML 7.211
**Codeumfang:** 213.702 LOC, 625 Dateien · **Letzter Push:** 20.08.2026 (Version 0.5.0-rc.1)

**Funktionsumfang:**
- Vollständiger SIP-Stack (UDP/TCP/WS/TLS/WebRTC), RTP-Proxy mit NAT-Traversal, TLS/SRTP mit automatischen ACME-Zertifikaten
- **HTTP-Router:** jedes eingehende INVITE ruft einen Webhook; JSON-Routingentscheidungen (`forward`/`reject`/`abort`/`spam`) — Anruflogik komplett extern programmierbar, ohne Neukompilierung
- **RWI (Echtzeit-WebSocket-Schnittstelle):** In-Call-Steuerung (originate, answer, hangup, bridge, transfer, hold), bidirektionales Echtzeit-PCM über `voip_bridge:`-WebSocket, Aufzeichnung, Warteschlange/ACD (sequentielles & paralleles Klingeln), Supervisor (listen/whisper/barge/takeover), Konferenz
- Aufzeichnung + **SipFlow** (vereinheitlichte SIP+RTP-Erfassung), offline Post-Call-Transkript (lokales SenseVoice), CDR-Webhooks
- Web-Konsole, WebRTC-Telefon (Browser-Softphone), RBAC, Prometheus-Metriken + OpenTelemetry
- Deutsche Lokalisierung (`locales/de.toml`, deutsche Doku `*.de.md`) neben EN/ZH; Voice-Agent-Funktionalität wurde bewusst nach „Active Call“ verlagert
- Belegte Leistung (README-Benchmarks): 5.000 gleichzeitige Anrufe mit RTP-Proxy ≈ 3,8 Kerne, 0 % Paketverlust, lineare Skalierung
- Zwei Editionen: Community (MIT) und Commerce (Großhandel, IVR-Visual-Editor, Voicemail Pro, Enterprise Auth LDAP/SAML/MFA, Endpoint Manager, SBC, CC — als Git-Submodule unter `src/addons/`)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `src/proxy` SIP-Proxy/Registrar, `src/call` B2BUA/Rufsteuerung, `src/rwi` WebSocket-Schnittstelle, `src/api` + `src/auth`, `src/console` Web-Konsole, `src/tts`, `src/callrecord`/`src/outbound`, `src/handler`; `src/addons/*` Commerce-Submodule (cc, wholesale, sbc, endpoint_manager, enterprise_auth, ivr_editor, telemetry, voicemail)
- Workspace-Crates `crates/`: rustpbx-sipflow, rustpbx-media, rustpbx-models, rustpbx-storage, rustpbx-http-util, rustpbx-record-common
- `templates/` (24) + `static/` (Web-Konsole, WebRTC-Telefon), `tests/` (103 Rust-Tests inkl. Queue-/RWI-/OpenAPI-Contract-Tests), `e2e/` (Python-E2E-Suite, 60 Dateien), `docs/` (19, u. a. rwi_events_reference, ivr_step_protocol), `examples/` (rwi_cli, Webhook-Proxies)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| rustpbx_debin | Codeberg | **Kein Quellcode-Duplikat** — Deployment-Paket: Binary (109 MB) + Assets, gebaut aus rustpbx_de (Commit 4f1b6a14, DE-Lokalisierung v1); Inhalt deckt sich mit rustpbx_de, ist aber nur eine Verteilungsform |
| HiPIA-Appliance | Codeberg | **Produktisierte Variante von gofonia-rust** (Whitelabel): Quellcode fast identisch + `src/admin/`-Wizard, `scripts/install.sh`, sipfonia-Branding & Admin-WebUI |
| HiPIA-Install | Codeberg | **Reines Installer-Artefakt** der Appliance (4 Dateien: install.sh + `release/active-call` ELF + `static/admin.html`) — kein Quellcode |
| gofonia-rust | Codeberg | **Basis-Dev-Repo** der Appliance-Linie; funktional von HiPIA-Appliance übertroffen (dort zusätzlich Admin-Wizard, Installer, Whitelabel; hier nur `static/index.html`) |

*Begründung kanonisch:* **rustpbx_de** ist mit Abstand die größte Codebasis (213,7k LOC vs. 33k bei debin), der jüngste Push (20.08.2026) und die einzige vollständige Quellcode-Distribution; rustpbx_debin ist nur ein daraus gebautes Binärpaket. **HiPIA-Appliance** ist die funktionale Obermenge von gofonia-rust (49.318 vs. 48.023 LOC, +2.000 Codezeilen/5 Dateien: Admin-Wizard-Modul, Installer, Whitelabel-WebUI) mit jüngerem Push (19.08.2026 vs. 15.08.2026) — einseitige Ableitung, keine Gegen-Richtung. gofonia-rust bleibt als das Basis-Dev-Repo dokumentiert.

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### HiPIA-Appliance (Single-Binary-Voice-AI-Gateway / KI-Telefonassistent-Appliance)
- Standort: https://codeberg.org/gofonia/HiPIA-Appliance · Sprachen: Rust 23.092, Python 9.635, TSX 8.856 · 49.318 LOC, 295 Dateien · Letzter Push: 19.08.2026
- Single-Binary-Voice-AI-Gateway („ein SIP-Trunk rein, fertig“): Fork von miuda-ai/active-call (v0.3.78) + FastAPI-Backend mit nativem Function-Calling und cal.id-Terminbuchung; Webhook-basierter Multi-Tenant-SIP-Handler lädt das Playbook (ASR/TTS/LLM, Stimme, Prompt, Tools) pro Anruf frisch aus dem Backend
- HiPIA-Wizard (`http://<host>:8080/admin`): konfiguriert SIP-Trunk, Weiterleitungsziele (warm/blind, als `<refer>`-Tag im generierten Playbook), System-Prompt, Kalender, Custom Tools (OpenAI-JSON-Schema) und Voice-AI (Deepgram-ASR, Inworld/Deepgram-TTS mit Stimmen-Dropdown, OpenAI-kompatibles LLM) — Werte in SQLite (`config/appliance.db`), Auth via argon2 + Session-Token, Werks-Zugang `admin`/`GoHiPIA` mit erzwungenem Passwort-Wechsel
- Rust-Fork-Änderungen: Webhook beantwortet den Anruf (nicht mehr fire-and-forget), natives Function-Calling im Chat-Pfad, Inworld-TTS-Provider (HTTP-OneShot + WS-Streaming), Warm-/Blind-Transfer über Refer mit durchgängig laufender Audio-Bridge (5 Bugfixes: TTS-Unterdrückung, MOH, SDP-Re-Apply, Idempotenz-Guard, Trunk-Credentials)
- Positionierung laut README: on-premise-fähig, 100 % lokal (Audio/Transkripte verlassen die Maschine nicht), BYOK, MIT-Lizenz; `release/active-call` (66,7 MB) + `scripts/install.sh`, `deploy/` (config.toml, systemd-Unit), `frontend/` React/Vite, `backend/` FastAPI (46 Dateien, u. a. dialplan, acl_config, saas_utils, ami_client)

### gofonia-rust
- Standort: https://codeberg.org/gofonia/gofonia-rust · Sprachen: Rust 22.314, Python 9.635, TSX 8.856 · 48.023 LOC, 290 Dateien · Letzter Push: 15.08.2026
- Basis-Entwicklungsrepo des Voice-AI-Assistenten (identischer Stand wie HiPIA-Appliance, aber ohne Admin-Wizard/Whitelabel); letzter Commit „Refer-Transfer: Audio-Bridge läuft durchgängig (5 Bugfixes)“ — dieselben Fixes trägt auch die Appliance-Linie. Struktur: `src/` (Rust active-call-Fork), `backend/` (FastAPI: Webhook, cal.id, Tenant-Config, Postgres), `frontend/` (React/Vite `asterisk-pbx-gui-frontend`)

### rustpbx_debin
- Standort: https://codeberg.org/gofonia/rustpbx_debin · Sprachen: HTML/Jinja 22.261, TOML 6.077 (v. a. Templates + Locales) · 33.238 LOC, 37 Dateien · Letzter Push: 20.08.2026
- Fertiges Deployment-Paket für RustPBX DE ohne Quellcode/Cargo-Build: `install.sh` (curl | bash, idempotent) installiert Binary nach `/usr/local/bin/rustpbx`, Assets nach `/opt/rustpbx/`, erzeugt Konfiguration mit frischem `session_secret`, richtet systemd-Unit + Firewall (UFW 5080/tcp, 15060/udp) ein; erster Admin über die Web-Konsole (`http://<server-ip>:5080/console/`)

### HiPIA-Install
- Standort: https://codeberg.org/gofonia/HiPIA-Install · Sprachen: HTML 480, Bash 162 · 659 LOC, 4 Dateien · Letzter Push: 19.08.2026
- Installer-Repo der Appliance: nur `scripts/install.sh` (idempotent), `release/active-call` (ELF x86-64, 68,7 MB), `static/admin.html` (Admin-Wizard-WebUI); installiert nach `/opt/hpia`, preseedet `config/appliance.db` mit Werks-Zugang `admin`/`GoHiPIA`, systemd-Dienst `hpia` mit Autostart, WebUI auf Port 8080. Kein Quellcode, Zweck (reine Distributionsschicht) klar aus README erkennbar

## Quellen & Methode
Geprüft: README.md aller 5 Repos (rustpbx_de vollständig deutsch, HiPIA/gofonia-rust inkl. Wizard- und Fork-Änderungs-Abschnitte, rustpbx_debin + HiPIA-Install Installationsdoku), Cargo.toml (Workspace-Crates, Version 0.5.0-rc.1), Verzeichnisstruktur von `src/`, `crates/`, `backend/`, `frontend/`, `docs/`, `tests/`, `e2e/`; git log (Letzt-Push-Daten), `diff -rq` HiPIA-Appliance vs. gofonia-rust (Superset-Nachweis), loc_results.json, famdata F11.

---

# Capellia AI Call Center

**Kategorie:** Eigenentwicklung (selbstgehostete Call-Center-Plattform; RAG-Wissen „Ported from GoFonIA v43“)
**Plattform(en):** Codeberg gofonia
**Kanonisches Repository:** livekitcallcenter
**Umfang kanonisch:** 14.300 Codezeilen, 94 Dateien (Quelle: loc_results.json)

## livekitcallcenter (Capellia) — Selbstgehostetes AI-Call-Center mit Warm Transfer & Coaching auf LiveKit-SIP-Basis, ohne klassische PBX

**Standort:** Codeberg: https://codeberg.org/gofonia/livekitcallcenter · **Typ:** Eigenentwicklung
**Sprachen:** TSX 7.053 · Python 6.158 · TypeScript 340 · Markdown 305
**Codeumfang:** 14.300 LOC, 94 Dateien · **Letzter Push:** 04.06.2026 (README: 25.912 Zeilen in 92 Dateien, MVP-Ready, self-hosted, no vendor lock-in)

**Funktionsumfang:**
- **AI-Agent (Inbound & Outbound):** LLM DeepSeek V4 Flash/Pro (zentral in `.env`), STT Deepgram Nova-3 (Realzeit), TTS Inworld (5 deutsche Stimmen: Nadine, Hans, Laura, Klaus, Petra, wählbar pro Agent), System-Prompt, Temperatur, Max-Dauer und Interruption-Schwelle pro Agent; Sprachen Deutsch/Englisch
- **Predictive / Ratio / Manual / Preview-Dialer** mit Redis-FIFO-Hopper (`campaign:{id}:hopper`); dialer_worker scannt aktive Kampagnen alle 30 s und wählt über LiveKit-SIP; Drop-Protection
- **Warm Transfer (AI → Mensch):** KI fordert Transfer an (`transfer_to_agent()`), Transfer-Queue in MongoDB, WebSocket-Alert an das Agenten-Dashboard, Agent nimmt an → dessen Telefon klingelt, und der Agent erhält **vor der Übernahme ein KI-Briefing mit Gesprächskontext** (Anrufer, Thema, KI-Erkenntnis, Empfehlung)
- **Silent Coaching:** Supervisor kann während des Gesprächs Silent-Monitor (zuhören + Transkript), Whisper (nur für Agent hörbar) und Barge (Mitsprechen); Coaching-Prompt pro Agent, LLM erzeugt Echtzeit-Vorschläge; Live-Transkript-Widget (`CoachingTranscriptWidget`)
- **Call Evaluation (Decision-LLM):** automatische Bewertung jedes Anrufs (outcome POSITIVE/NEGATIVE/UNCLEAR, confidence, reason, score 0–100), Bewertungsskala Excellent → Poor, Batch-Evaluation, Score-Override und Statistiken; Evaluations-Prompt pro Agent
- **Employee Scoring:** Formel `base_score + (positive − negative) × 2`, Tages-/Wochen-/Monatsaggregation, farbcodierte Kalenderansicht, Leaderboard pro Projekt
- **RAG Knowledge Base:** semantische Vektor-Suche per Agent — MongoDB-Float-Arrays statt PostgreSQL/pgvector (Port aus GoFonIA v43)
- Weitere Module: Projekte/passende Kampagnen, Kontakte, DNC-Listen, Dispositions, Pause-Codes, Skripte, Call-Analyse, Callbacks, Agenten- & Tool-Verwaltung (HAR-Tools pro Agent), Voice-Verwaltung, LiveKit-SIP-Integration, JWT-Auth
- Stack: FastAPI (Python) + MongoDB (Beanie/Motor) + Redis, Next.js 14 + React 18 (Tailwind), LiveKit + LiveKit-SIP, PM2 (`ecosystem.config.js`)

**Aufbau/Module (aus Verzeichnisstruktur):**
- `main.py` — FastAPI-App „Capellia“ (v1.0.0), bindet 24 Router unter `/api/v1/...` ein
- `api/routes/` — 24 Route-Module, u. a. `transfer.py` (Warm-Transfer: request/accept/reject/queue/ws mit LiveKit-SIP und Agenten-WebSockets), `coaching.py` (start/suggest/ws/end), `evaluations.py` (Decision-LLM, batch, stats), `employee_scores.py` (Kalender + Leaderboard), `knowledge.py` (RAG-Vektorsuche), `dialer.py`, `campaigns.py`, `dnc.py`, `sip_integration.py`, `voices.py`, `auth.py`
- `agent_worker.py` / `dialer_worker.py` — LiveKit-Agent-Worker und Dialer-Loop; `livekit_transcript_handler.py` — Transkript-Datenfluss; `models.py` (Beanie-Dokumente) / `database.py` (Motor/Mongo)
- `frontend/` — Next.js 14 (`livekit-call-center-frontend`) + Tailwind: Agenten-Dashboard, Call-UI, Live-Transkript, Coaching-Widget, Kalender/Leaderboard
- Arbeitsstände mehrerer Agenten-Varianten im Root (`agent.py`, `ai_agent.py`, `enhanced_agent.py`, `modern_agent.py`, `multimodal_agent.py`, `realtime_agent.py`, `proper_agent.py`, `free_agent.py`, `simple_agent.py`, `working_agent.py`, `debug_agent.py`, `agent_worker.py`) sowie Reste wie `models.py.bak`, `seed_dispositions.py` und der leere Ordner `callCenter copy` (Altlasten des iterativen Entwicklungsprozesses)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| capelliav0.1 | Codeberg | **Quasi-identischer Snapshot** (14.339 vs. 14.300 LOC, 95 vs. 94 Dateien, identisches README und gleiche Feature-Liste); 17 abweichende Dateien: capelliav0.1 hat zusätzlich `eslint.config.mjs`, gepinnte `livekit-plugins-deepgram/inworld` + Dokument-Parser (beautifulsoup4, lxml, PyMuPDF, mammoth) und Auth-Env in `.env.example`; ihm fehlen dagegen die `await`-Korrekturen in coaching/evaluations-Routen und der Next.js-Rewrite-Fix → früherer, versionierter Stand („v0.1“) |

*Begründung kanonisch:* Beide Repos sind Einzelsnapshots ohne gemeinsame Historie (je 1 Commit, beide 04.06.2026, erstellt 03.06. / 04.06.2026) — es gibt keine saubere Ableitungsrichtung, die Unterschiede sind marginal und gegenläufig. `livekitcallcenter` trägt den generischen, endgültigen Projektnamen (statt des Versionierten „capelliav0.1“) und enthält die funktionalen Korrekturen (async-Await-Fixes im Backend, Behebung der 307-Redirects durch den Next.js-API-Rewrite); es wird daher gemäß famdata-Note als kanonisches Repo geführt.

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### capelliav0.1
- Standort: https://codeberg.org/gofonia/capelliav0.1 · Sprachen: TSX 7.055, Python 6.173, TypeScript 341 · 14.339 LOC, 95 Dateien · Letzter Push: 04.06.2026
- Identisches Produkt (Capellia v0.1): FastAPI-Backend + Next.js-Frontend, Warm Transfer, Coaching, Evaluation, RAG. Unterschiede zu livekitcallcenter: Dependency-Versionen mit Sicherheits-/Runtime-Fokus (u. a. Deepgram- und Inworld-LiveKit-Plugins, Dokument-Parser für die Wissensbasis) und ausführlichere Auth-Konfiguration; letzter Commit „Fix backend runtime issues and dependency security“

## Quellen & Methode
Geprüft: README.md (englisch) und `docs/README_de.md` (deutsche Übersetzung) beider Repos, `main.py` (FastAPI-Anbindung), `api/routes/transfer.py` (Warm-Transfer-Ablauf, LiveKit-SIP-Config), `requirements.txt` beider Repos, `models.py`/`database.py`, `.env.example`, `frontend/package.json` (Next.js 14), `diff -rq` capelliav0.1 vs. livekitcallcenter (17 Dateien), git log beider Repos (je 1 Commit), loc_results.json, famdata F12.

---

# Forks & lokale Deployments externer OSS

**Kategorie:** Fork/Anpassung, lokale Deployments & Eigenentwicklungen auf Basis externer Plattformen
**Plattform(en):** GitHub livedialai und Codeberg gofonia (beide)
**Kanonisches Repository:** fonester *(Familienreihe Fonoster: fonester / fonoster / gonoster)*
**Umfang kanonisch:** 68.743 Codezeilen, 1.388 Dateien (Quelle: loc_results.json, sourceCount)

> Die Mehrheit dieser Repos sind Forks, Mirrors oder Deployment-Snapshots externer Open-Source-Projekte
> (Fonoster, cal.com, jambonz, Onyx, TastyIgniter, ViciDial, CalDav Synchronizer, dialplane, active-call, n8n/FreePBX).
> Einige (42, xai, ressprinter, dialplane--6phasen) sind trotz Aufnahme in diese Familie Eigenentwicklungen,
> die lediglich auf externen Plattformen aufsetzen — jeweils korrekt typisiert.

## Fonester — 1-Satz-Kurzcharakteristik
**Standort:** GitHub: https://github.com/livedialai/fonester · **Typ:** Fork/Anpassung (externes OSS)
**Sprachen:** TypeScript 43.348 · TSX 14.987 · Markdown 1.800 · YAML 1.637 · CSS 1.589 · JavaScript 1.293
**Codeumfang:** 68.743 LOC, 1.388 Dateien · **Letzter Push:** 2026-05-17 (GitHub)

**Funktionsumfang:**
- Vollständig selbstgehostete Fonoster-Instanz (Version 0.17.1) **ohne jede Cloud-Abhängigkeit**: kein `api.fonoster.com`, kein GitHub-OAuth; Identity läuft lokal (`APISERVER_IDENTITY_ISSUER=http://fonoster.local`)
- Deutsche README mit Quick-Start (`bash <(curl …)`), manueller Installationsanleitung, Login-Doku, Troubleshooting und expliziter Liste „Änderungen vs. Upstream Fonoster"
- Deutscher interaktiver Debian-12-Installer (`install.sh`): fragt Domain, Admin-Email, Passwort; installiert Docker, Nginx, Certbot; erzeugt Session-Secret, `.env` und Nginx-VHost
- Docker-Compose-Stack: dashboard, apiserver, autopilot, routr, rtpengine, asterisk, postgres, influxdb, nats, envoy, autoheal — Images als lokale Mirror-Builds (`livedial/*`, gepinnte Versionen)
- Nginx-Let's-Encrypt-Reverse-Proxy mit Websocket-Support vor Envoy (HTTP statt HTTPS, Aufruf über `127.0.0.1:8449`)
- Envoy-Config mit gRPC-Web + proto Content-Type-Routing-Fix, Dashboard-SSR via `DASHBOARD_ALLOW_INSECURE=true`

**Aufbau/Module (aus Verzeichnisstruktur):**
- `mods/` = komplette Fonoster-Monorepo-Quellen (1.306 Dateien, 33 MB): `apiserver` (gRPC-API, Prisma `schema.prisma`), `dashboard` (React-UI mit acls, agents, api-keys), `identity` (lokale Auth/OAuth), `sdk`, `sipnet`, `voice`, `streams`, `autopilot`, `authz`, `common`, `ctl`, `logger`, `mcp`, `types`
- `asterisk/` = Kapsel-Image (run.sh, Dockerfile); `config/` = envoy.yaml, envoy-tls.yaml, nginx.conf, assistant.example.json; `etc/` = autopilot.yaml, log4j2.yaml
- `install.sh`, `nginx-fonoster.conf`, `compose.yaml`, `.env(.example)`, `site/` (Landingseite „Fonoster“)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung |
|---|---|---|
| fonester | GitHub | Kanonisch — der realisierte, lokal angepasste Fork (Quellcode + Installer + Doku) |
| fonoster | GitHub + Codeberg | Leerer Klon: nur `.git`, keine Commits/Checkout; GitHub-Beschreibung „Fonoster Self-Hosted Fork – lokal, keine Cloud-API“ — inhaltlich durch fonester ersetzt |
| gonoster | GitHub | Leerer Klon (nur `.git`, keine Commits); kein Beschreibungstext, Zweck nicht erkennbar |

*Begründung kanonisch: fonester ist das einzige der drei Repos mit Inhalt — voller Fonoster-Quellcode in `mods/`, dazu sämtliche lokalen Anpassungen (Installer, Nginx, Compose, .env, deutsche Doku).*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### jambonz („transfer-apps“)
- Standort Codeberg: https://codeberg.org/gofonia/jambonz · Sprachen TypeScript 269, Markdown 103, JSON 57 · 429 LOC, 12 Dateien · angelegt 2026-07-19
- Vier eigenständige Node.js/TypeScript-Beispiel-Apps (Blind Transfer, Warm Transfer, Three-Way Warm Transfer, Transfer-Verb) für jambonz-WebSocket-Call-Flows über `@jambonz/sdk ≥ 0.8.3` — Nutzung der `handoff`-Eigenschaft des `agent`-Verbs bzw. des standalone-`transfer`-Verbs. Inhaltlich unverändert übernommene Beispielsammlung (vermutlich Referenz für eigene Voice-Agent-Entwicklung).

### cal
- Standort Codeberg: https://codeberg.org/gofonia/cal · Sprachen TSX 49.159, JSON 44.869, TypeScript 18.330 · 121.254 LOC, 2.171 Dateien · angelegt 2026-06-16
- Kompletter Fork von cal.com (Open-Source-Calendly-Alternative, AGPLv3). **Lokale Anpassung nachweisbar:** ein Commit von Giacomo Steckhan – „fix: disable SAML auto-signIn, redirect /auth/login to /auth/admin-login“ (entfernt die `signIn("saml")`-Schleife; Login-Loop-Fix für Self-Hosted-Betrieb ohne SAML). Keine weiteren erkennbaren Code-Änderungen; Sicht auf `apps/` (web, storybook, swagger, docs) entspricht Upstream.

### caldavsynchronizer
- Standort Codeberg: https://codeberg.org/gofonia/caldavsynchronizer · Sprachen C# 45.208, XML 283, Markdown 243 · 45.737 LOC, 1.036 Dateien · 1 Commit (README-Update, „Recall.ai blurb“)
- Unveränderter Klon des **Outlook CalDav Synchronizer** (AGPLv3): Outlook-Plugin zur Synchronisation von Terminen, Aufgaben und Kontakten mit CalDAV-/CardDAV-Servern (Google, SOGo, Horde …); Visual-Studio-Projekt mit Integrationstests (GenSync, Thought.vCards, OAuth.Google/Swisscom, CustomInstaller). Keine lokalen Code-Änderungen erkennbar — Mirror als Deployment-/Build-Basis.

### astguiclient
- Standort Codeberg: https://codeberg.org/gofonia/astguiclient · Sprachen PHP 255.029, HTML+PHP 34.183, Perl 16.760, T-SQL 12.638 · 350.526 LOC, 1.828 Dateien · angelegt 2026-08-23
- Klon des **ViciDial Call-Center-Pakets** („astguiclient – thirtieth public release 2.0.5“, seit 2003, SourceForge): Dialer/Agenten-Suite für Asterisk mit `trunk/` (agi, bin, docs, extras, libs, sounds, LANG_www, install.pl, INSTALL). Kein Hinweis auf gofonia-/livedialai-Code → unveränderter Upstream-Klon, vermutlich als Grundlage für den Call-Center-VoIP-Stack (vgl. fonio-project/FreePBX).

### tastyigniter
- Standort Codeberg: https://codeberg.org/gofonia/tastyigniter · Sprachen PHP 1.205, JSON 98, YAML 62, Bash 54 · 1.498 LOC, 73 Dateien (ohne vendor) · 1 Commit: „feat: add reservation extension for table bookings“
- Fork von **TastyIgniter 4** (Open-Source-Restaurant-POS/-Ordering, Laravel-basiert). **Lokale Anpassung:** eigene Extension `extensions/gofonia/tableorder` (Tischreservierungen: `src/Http`, `Listeners`, `Providers`, `database/migrations`, composer.json) — inhaltlich verwandt mit dem Reservierungs-/GoDinIA-Umfeld.

### tastyigniter-multi
- Standort Codeberg: https://codeberg.org/gofonia/tastyigniter-multi · Sprachen PHP 1.880, XML+PHP 105, JSON 86 · 2.121 LOC, 79 Dateien (ohne vendor) · 1 Commit: „feat: multi-tenant payment system for restaurant orders“
- Zweite TastyIgniter-Variante mit **eigener Multi-Tenant-Extension** `extensions/gofonia/multitenant` (Models, Controllers, Middleware, Services, `config/multitenant.php`, Resource-Views) und zusätzlicher Composer-Abhängigkeit `tastyigniter/ti-ext-reservation`; keine Web-Asset-Pipeline (`package.json`/`.github` fehlen). Zwei parallele Ausbaustufen statt Versionsreihe: hier Multi-Tenant-Bezahlung, dort Tischreservierung.

### eunovia
- Standort GitHub: https://github.com/livedialai/eunovia · Sprachen Python 293.761, TSX 98.858, TypeScript 35.317 · 537.329 LOC, 4.182 Dateien · letzter Push 2026-09-01
- **Deutsche Version von Onyx** (Fork von onyx-dot-app/onyx, MIT/Enterprise): Gen-AI-/Enterprise-Search-Plattform mit RAG, Connectors, Vespa-Vektor-DB, Celery-Workern, Next.js-Frontend, Opal-Design-System. **Lokale Anpassung:** eigenes i18n-System `web/src/lib/i18n/translations.tsx` mit Locales `"de" | "en"` und **Default „de“**; letzter Commit „fix: add useTranslations() to all remaining sub-components“ (Autor: Eunovia <admin@gofonia.de>) — deutschsprachige UI als primäres Ziel. README/AGENTS.md entsprechen dem Upstream.

### fonio-project
- Standort GitHub: https://github.com/livedialai/fonio-project · Sprachen XML+Lasso 27.593 (v. a. umfangreiche FreePBX-Konfig-/Doku-XML), JSON 18.332 (fonio-inventory.json), PHP 7.248, HTML+PHP 4.577, JS 2.539, Bash 1.526 · 68.332 LOC, 182 Dateien · letzter Push 2026-05-17
- **Deployment-/Admin-Sammlung um die „Fonio AI Platform“** (deutsche KI-Telefonie-Plattform): `projekt/n8n-docker/` (n8n + PostgreSQL + Redis via Compose, deutsche Doku = Repo-README), `projekt/calcom-docker/` und `projekt/freepbx-docker/` (FreePBX/Asterisk-Komplettstack mit astdb, Sounds ulaw/g722/gsm, Logs, mysql-init, mehreren Compose-Varianten), `docker-compose-openclaw*.yml`, `ollama/`, Übersetzungsprojekte (`doctranslate/`, `sworntranslate-digistore/`), PHP-Admin-Dashboards (`admin.php` = DocTranslate.de-Verwaltung für Kunden/Zahlungen/Affiliate/Aufträge, `config.php`, `db-setup.php`, `invoice-frontend.php`), Fonio-JS-Skripte (Login/Inventory) sowie umfangreiche Rebuild-Doku (`FONIO-COMPLETE-DOCUMENTATION.md`, `fonio-implementation-guide.md`, `fonio-rebuild-doc-part1–6`, `gemma2-performance.md`). Zweck: Server-Deployment-Snapshots + Plattform-Blueprint.

### hermyx
- Standort GitHub: https://github.com/livedialai/hermyx · Sprachen Bash 359, Python 62 · 460 LOC, 4 Dateien · letzter Push 2026-05-13
- **Eigene Integrations-Infrastruktur auf Basis externer OSS:** One-Command-Setup (deutscher `install.sh`) für eine selbstgehostete KI-Plattform — installiert **Hermes Agent** (Nous Research) als LLM-Backend (Provider-/Model-Wahl, Gateway-Install/Start) und **Onyx** (DSGVO-konform, self-hosted) und verbindet beide über `hermes-proxy.py` (Python, OpenAI-kompatibler/litellm-kompatibler Proxy zwischen Hermes und Onyx’ litellm-Gateway). Kein Fork von Fremdcode, sondern Installations- und Integrations-Code um zwei externe Projekte.

### active-call
- Standort GitHub: https://github.com/livedialai/active-call · Sprachen Rust 21.793, Markdown 4.884, HTML 573 · 27.609 LOC, 180 Dateien · letzter Push 2026-08-14
- Fork/Mirror von **miuda-ai/active-call** (MIT, Crates.io): SIP/WebRTC-Voice-Agent in Rust mit Dual-Engine-Dialogue (klassische VAD→ASR→LLM→TTS-Pipeline oder OpenAI/Azure-Realtime-Streaming), Rauschunterdrückung (nnnoiseless), WebRTC-AGC, DTMF-Handling und **Playbook-System** (personas/scenes/flows in Markdown). **Lokale Anpassung:** `config/` mit eigenen Playbooks (hello, simple_crm, multi_scene, advanced_example, webhook_example, env_vars_example), `telnyx.example.toml`, `fillers.txt`, `office.wav`, `process_audio.rs`; 1 Squash-Commit („bump version to 0.3.78, upgrade rustrtc to 0.3.117“) — einsatzbereite Konfiguration für eigene Telefonie (Telnyx).

### 42 (GoFonIA 42)
- Standort Codeberg: https://codeberg.org/gofonia/42 · Sprachen Python 11.504, TSX 8.834, PHP 4.509, CSS 742 · 26.556 LOC, 191 Dateien · angelegt 2026-08-02
- **EIGENENTWICKLUNG (kein externes OSS):** GoFonIA 42 – Multi-Tenant-SaaS für Telefon-Assistenten & Online-Reservierungen (Restaurants/KMU). Architektur: OpenSIPS → LiveKit SIP-Bridge → LiveKit-Agent (`agent/agent_v3.py`, controller_v3, music_bot, system_prompt) und Website-Widget gegen Backend-Booking-API (`backend/`: FastAPI, acl_config, ami_client, auth, booking_seed, dialplan, pjsip-Fix-Skripte, email_config; `.bak`-Arbeitsstände). Dazu React-Dashboard (`frontend/`), WordPress-Plugin (`wp-plugin/`), `references/godinia/`, Docker-Compose (VM/Cloud) + CHANGELOG. Verwandt mit dialplane--6phasen (dessen Transfer-Logik stammt aus dieser Integration).

### xai
- Standort Codeberg: https://codeberg.org/gofonia/xai · Sprachen JavaScript 307, Markdown 86 · 393 LOC, 3 Dateien · angelegt 2026-07-07
- **EIGENENTWICKLUNG:** xAI-Voice-Agent „Eve“ — Node.js-Webhook-Server (`index.js`, Express + ws, Port 3000) für xAI-Realtime-Calls: SIP-Route Anrufer → MagnusBilling/Asterisk → xAI SIP → Webhook (systemd-Dienst `xai-webhook.service`, SSL unter `x.gofonia.de`) → WebSocket `wss://api.x.ai/v1/realtime`; REST-Hangup- und REFER-Transfer-Funktionen. `clean.js` löst Küchen-/Chef-Benachrichtigungen aus (`asterisk -rx "Channel originate … Playback /root/xai-test/benachrichtigung"`, Chef-Nummer im Code). Deutsche Doku (`xai-voice-agent-doku.md`) mit Architektur-Diagramm und Betriebsanleitung. API-Key ist in `index.js` hart verdrahtet.

### ressprinter
- Standort Codeberg: https://codeberg.org/gofonia/ressprinter · Inhalt: ZIP `resprinter_single.zip` (35,8 KB, 28 Dateien) · ~1.660 LOC PHP/JS/CSS + Gettext (pygount) · 1 Commit: „Initial: Reservierungssprinter Single-Tenant v1.0.0“ (2026-07-13)
- **EIGENENTWICKLUNG:** WordPress-Plugin **„Reservierungssprinter“** (Single-Tenant; vermutlich frühe/godinia-nahe Variante eines Restaurant-Reservierungs-Tools) — nur als Quelltext-ZIP im Repo: Plugin-Bootstrap `reservierungssprinter.php`, `includes/` (class-admin, class-installer, class-shortcode, class-availability, class-mailer, class-reservation, class-rest-controller, class-ajax-handler), `templates/` (Admin-Dashboard, -Listen, -Räume, -Einstellungen, Reservierungsformular), `assets/widget.js`, deutsche Sprachdateien (`languages/*de_DE.po`/`.pot`).

### opentalk
- Standort Codeberg: https://codeberg.org/gofonia/opentalk · 0 LOC, 0 Dateien · angelegt 2026-06-01
- **Zweck nicht erkennbar:** leerer Klon — nur `.git` ohne Commits/Checkout, kein README, keine Beschreibung. Vermutlich für ein eigenes „OpenTalk“-Projekt (Self-Hosted-Konferenzplattform) angelegt, aber ohne Inhalt geblieben.

### dialplane--6phasen
- Standort Codeberg: https://codeberg.org/gofonia/dialplane--6phasen · Sprachen Python 431, Markdown 44 · 475 LOC, 2 Dateien · angelegt 2026-08-17
- **EIGENENTWICKLUNG auf Basis externer Komponenten:** Portierung der bewährten 6-Phasen-Warm/Kalt-Transfer-Logik aus der LiveKit-Integration (GoFonIA 42: `agent_v3.py`/`controller_v3.py`) auf den dialplane/Pipecat-Stack: `transfer_agent.py` mit TransferCoordinator/FrameProcessor, zwei SIP-Legs (Anrufer – Chef), Sende-Matrix pro Phase (CALLER_AI → CALLER_WAITING → AI_BRIEFING → CHEF_DECIDING → CONNECTED bzw. FALLBACK), Agent bleibt im Media-Pfad (kein B2BUA/REFER). Ausführliche deutsche README mit Phasen- und Architektur-Diagramm.

### dialplane-pipecat
- Standort Codeberg: https://codeberg.org/gofonia/dialplane-pipecat · Sprachen Python 8.718, Markdown 168, YAML 95, TOML 82 · 9.063 LOC, 82 Dateien · angelegt 2026-08-16
- **Fork von dialplane + eigene Pipecat-Integration:** `pyproject.toml` (name „dialplane“, Apache-2.0, Upstream-Autor M. Abdi) mit `src/dialplane/` (Upstream-Engine: sdp, message, auth, dialog, media, ua, transaction, transport) und eigenem Paket `src/dialplane_pipecat/` (transport.py). Deutscher produktiver Telefon-Voice-Agent: registriert sich als SIP-Endpoint (Digest-Auth) an rustpbx, Pipeline Deepgram-STT → OpenAI (Infomaniak) → Inworld-TTS, <500-ms-Roundtrip, Ring-while-Start statt Stille. Dazu Tests (`tests/`, u. a. integration_call, audio_fidelity), `benchmarks/` (load, micro), `examples/` (call, echo_service, mistral_agent) und deutsche README-Architektur.

## Quellen & Methode
- Geprüft: FAMDATA-JSON (alle 19 Einträge), `loc_results.json` (alle Messungen), Git-Logs/Remotes (Squash-/Leer-Klone, lokale Commit-Meldungen), READMEs (fonester, jambonz, cal, caldavsynchronizer, tastyigniter, fonio-project, hermyx, active-call, 42, dialplane--6phasen, dialplane-pipecat), Schlüsseldateien: `install.sh`, `nginx-fonoster.conf`, `compose.yaml`, `.env` (fonester); `translations.tsx`/i18n (eunovia); `index.js`/`clean.js`+Doku (xai); `pyproject.toml`/src-Layout (dialplane-pipecat); `transfer_agent.py` (dialplane--6phasen); Extensions-Layout `extensions/gofonia/*` (TastyIgniter); `unzip -l` + pygount für ressprinter-ZIP (1.660 LOC, da als `__binary__` in loc_results).
- Bei den drei leeren Klonen (fonoster, gonoster, opentalk) liefert nur die Git-Metadaten Auskunft — fair als „Zweck/Inhalt nicht erkennbar“ dokumentiert.
- **Gesamt-LOC der Familie:** ≈ 1.262.000 Codezeilen (16 Repos mit Inhalt: 1.262.185; fonoster/gonoster/opentalk = 0).

---

# MikroVox MikoPBX-Modul

**Kategorie:** Eigenentwicklung
**Plattform(en):** Codeberg gofonia
**Kanonisches Repository:** mikrovox
**Umfang kanonisch:** 472 Codezeilen, 12 Dateien (Quelle: loc_results.json)

## mikrovox — Voice-AI-All-in-One-Modul für MikoPBX
**Standort:** Codeberg: https://codeberg.org/gofonia/mikrovox · **Typ:** Eigenentwicklung
**Sprachen:** PHP, YAML, Markdown · **Codeumfang:** 472 LOC, 12 Dateien · **Letzter Commit:** 2026-08-12

**Funktionsumfang:**
- MikoPBX-Zusatzmodul (module.json: `ModuleMikrovox` v4.2.0, min. MikoPBX 2023.2.168, Developer MikroVox, Support: info@gofonia.de) — installierbar als ZIP-Upload über das MikoPBX-Admin-Cabinet („Zusatzmodule").
- Integrierte Docker-Infrastruktur: `docker-compose.yml` orchestriert 6 Container — `mikrovox_postgres` (pgvector/pgvector:pg15, RAG-Vektordatenbank), `mikrovox_redis` (Session-State), `livekit_server` (WebRTC-Media-Engine, Port 7880/7881), `livekit_sip_bridge` (SIP-zu-WebRTC-Gateway, Port 5080), `mikrovox_backend` (livedial/mikrovox-backend:v4.2, FastAPI-REST-API, Port 8000), `mikrovox_agent` (livedial/mikrovox-agent:v4.2, Python-Voice-Agent).
- Voice-Engine: Groq-LLM (`openai/gpt-oss-120b`), Deepgram STT (Nova-3), Inworld TTS (de-alina-female) — natürliche Echtzeit-Sprachgespräche.
- RAG-Wissensbasis mit 3 Wegen: Text-/FAQ-Editor, Website-Crawling per URL, Dokument-Uploads (PDF, TXT, DOCX, Markdown) mit Auto-Vektorisierung nach PostgreSQL/pgvector.
- WarmTransfers (AI kündigt Anrufer an, Bestätigung, Verbindung) und Cold/Blind-Transfers an Nebenstelle oder externe E.164-Nummer; Zielverwaltung direkt über die MikoPBX-Web-UI.
- Kalender-/Buchungsintegration: Meetergo / Cal.com.
- Real-Time-Sync: automatische Hintergrund-Synchronisation MikoPBX-SQLite ↔ PostgreSQL-Container.
- Build-schleife: Der Composer-Tag zeigt MikoPBX 2026.x-Readiness; 6-Container-Docker-Stack-Ansatz erscheint als eigenständiger „Docker Base" eingebettet im Modul.

**Aufbau/Module (aus Verzeichnisstruktur):**
- `App/` = Phalcon-MVC-Integration: `Controllers/ModuleMikrovoxController.php` (179 LOC, Admin-UI-Routing /module-mikrovox/index), `Forms/ModuleMikrovoxForm.php` (77 LOC, Einstellungsformular: inbound_extension, LLM-Provider Groq, API-Key), `Models/ModuleMikrovox.php`, `Views/index.volt`.
- `Setup/PbxExtensionSetup.php` (90 LOC) = Installations-/Aktivierungshooks (Datenbanktabellen anlegen, Container ziehen, PJSIP-Trunk auf 127.0.0.1:5080 registrieren).
- `models/ModuleMikrovox.php` = Settings-Model (dupiziert im Modul-Layout, MikoPBX-Konvention).
- `bin/` = statisch gebündelte Docker-Binaries (docker, dockerd, containerd, docker-compose, ctr, runc, safe_dockerd, safeScript.php — ~187 MB) — das Modul startet seinen eigenen Docker-Daemon auf dem PBX-Host.
- `docker-compose.yml`, `livekit.yaml` (Port 7880, devkey/secret), `sip.yaml` (SIP-Trunk-Gateway, RTP 10000–20000, Room-Prefix `call-`), `README.md`.

**Dubletten / Versionsreihe dieser Familie (Tabelle):**
| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| mikrovox | Codeberg | Kanonisch (englische Fassung) |
| mikrovox_ru | Codeberg | Sprachvariante: identischer PHP-Code (diff leer), russisch übersetzte README (469 LOC, 26 MD-Zeilen) |

*Begründung kanonisch: mikrovox und mikrovox_ru sind byte-identisch im Code (diff von Models/ModuleMikrovox.php und aller Dateien ergibt außer Pfadpräfixen keine Unterschiede); die einzige inhaltliche Differenz ist die übersetzte README. mikrovox (de/fr-unabhängige englische Fassung, 472 LOC) wird als Referenz gewählt, mikrovox_ru ist die russische Sprachvariante.*

## Quellen & Methode
Geprüft: README.md (Featureliste + Architektur-Diagramm), module.json, docker-compose.yml (6 Container), livekit.yaml, sip.yaml, App/Controllers/ModuleMikrovoxController.php, Setup/PbxExtensionSetup.php, bin/safeScript.php, `diff -r` mikrovox vs. mikrovox_ru, git log (Codeberg). LOC aus loc_results.json (pygount).

---

# TTS/STT & Speech-Infrastruktur

**Kategorie:** Eigenentwicklung (teils Fork/Anpassung von Open-Source-Basis)
**Plattform(en):** GitHub livedialai, Codeberg gofonia
**Kanonisches Repository:** keins — fünf eigenständige Projekte ohne Dubletten
**Umfang gesamt:** 103.394 Codezeilen, 1.840 Dateien (Quelle: loc_results.json)

## qwentts — Qwen3-TTS OpenAI-kompatibler TTS-Server
**Standort:** GitHub: https://github.com/livedialai/qwentts (privat) · **Typ:** Fork/Anpassung
**Sprachen:** Python, Markdown, HTML+Genshi · **Codeumfang:** 14.870 LOC, 116 Dateien · **Letzter Push:** 2026-06-24

**Funktionsumfang:**
- OpenAI-kompatibler FastAPI-TTS-Server für das Modell Qwen3-TTS (Port 8880), basierend auf groxaxo/Qwen3-TTS-Openai-Fastapi + dffdeeq/Qwen3-TTS-streaming.
- Echtzeit-Audio-Streaming: `stream: true` liefert PCM-Chunks bereits während der Generierung (statt erst am Ende) — funktioniert für eingebaute Stimmen und Voice Cloning.
- Stimmenbibliothek: Voice-Profile speicherbar, Nutzung via `voice: "clone:MyVoice"`, automatischer Wechsel zwischen CustomVoice- und Base-Modell.
- Prompt-Caching: Speaker-Embeddings werden einmal pro Profil berechnet und wiederverwendet (spart ~0,7 s pro Voice-Clone-Request).
- Optimierungen: torch.compile + CUDA-Graphs (konfigurierbar in config.yaml, Backend `optimized`), GPU-Keepalive (periodische Matmul gegen AMD-DPM-Downclocking; hält TTFB bei ~0,3 s statt 0,85 s nach Idle).
- Mehrere Server-Backends (api/backends/): official_qwen3_tts, optimized, pytorch (CPU mit IPEX), openvino, vllm_omni_qwen3_tts.
- Pipecat-Voice-Agent-Pipeline in install/pipecat: WebRTC + Parakeet STT + LLM + Streaming-TTS (End-zu-End-Latenz ~2 s statt ~7 s).
- Finetuning-Helfer (finetuning/sft_12hz.py, prepare_data.py, dataset.py), Gradio Voice Studio, Benchmarking-Skripte (bench_tts.py, benchmark_official.py, *-benchmark-*.txt), Dockerfiles für CUDA/ROCm/vLLM.
- Kompatibilität: AMD ROCm getestet und optimiert (Strix Halo gfx1151), NVIDIA CUDA erwartet; CPU-Backend separat dokumentiert.

**Aufbau/Module (aus Verzeichnisstruktur):**
- `api/main.py` = FastAPI-App (Server-Bootstrap, TTS_BACKEND-Umgebungsvariable, CORS, WARMUP, GPU-Keepalive); `api/routers/openai_compatible.py` = OpenAI-kompatible Endpunkte (z. B. `/v1/audio/speech`); `api/services/` = audio_encoding (z. B. PCM/OGG), text_processing (Texthandling).
- `api/backends/factory.py` = Backend-Auswahl; `qwen_tts/` (19 Dateien) = Modell-Kern (core, inference, cli, __main__.py).
- `config.yaml` = Modelle (0.6B-Base/CustomVoice, 1.7B-Base/CustomVoice), Optimierungsflags, Stimmenliste; `install/` mit INSTALL.md; `patches/` = Patches gegen Upstream; `tests/`, `examples/`.

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)

### parakeet-tdt-fastapi
- Standort https://github.com/livedialai/parakeet-tdt-fastapi (privat) · Sprachen: Python, HTML+Genshi · 2.815 LOC, 26 Dateien · Push 2026-06-24
- STT-Server für NVIDIA Parakeet TDT 0.6B v3 über ONNX Runtime (8-Bit quantisiert) — ultraschnelle Transkription auf CPU; zwei Generationen: Legacy-Flask+Waitress (`app.py`, Port 5092) und refaktorierter FastAPI-Dienst (`parakeet_service/` mit routes.py, chunker.py, batchworker.py, model.py, audio.py, config.py), Start über `server.py` (uvicorn). OpenAI-kompatibel (`/v1/audio/transcriptions`, `/batch`), Silero-VAD-Auto-Chunking über Pausen, parallele InferencePool-Calls, SRT/VTT-Ausgabe, Health-Endpunkte; Benchmark: 300-s-Datei in 10,4 s (27,2×, +73 % gegenüber Legacy), 16 gleichzeitige 10-s-Requests mit 39,3× Durchsatz. Dockerfiles (cpu/gpu), pin_pcores.sh.

### prosodia
- Standort https://github.com/livedialai/prosodia (privat) · Sprachen: JavaScript, HTML+PHP, XML · 6.442 LOC, 43 Dateien · Push 2026-05-17
- „Prosodia Multi-TTS Playground": kostenlose Web-Plattform, die für TTS-Provider (Cartesia, Inworld, ElevenLabs, Gemini TTS) automatisch SSML- und Emotions-/Expression-Tags erzeugt; Plus Voice Cloning, Registrierung mit E-Mail-Verifizierung (Brevo), JWT-Auth, Admin-Panel, Usage-Tracking, JSON-basierter Datenspeicher. Backend Node.js/Express (Port 3341, Admin-Server 3342, zusätzlich server-sqlite.js mit gewachsenem prosodia.db), Frontend in PHP (index.php, pages/home/login/playground/profile/register/verify, admin/), mehrere Proxy-Experiment-Versionen (prosodia-proxy.js/-v8/-eu, prosodiav10.js, mistral.js) und Prompt-Dateien (cartesia_prompt.txt, inworld_prompt.txt, gemini_prompt.txt, elevenlabs_prompt.txt). Tagging läuft über Infomaniak-OpenAI-kompatiblen Endpunkt (Modell mistral3); Ziel-URL app.prosodia.ch.

### voice-ai-agents
- Standort https://github.com/livedialai/voice-ai-agents (privat) · Sprachen: Python (45k), JavaScript (25k+), HTML · **78.978 LOC, 1.652 Dateien** · Push 2026-05-17
- **Keine einzelne App, sondern eine große Sammlung/Archiv von Voice-AI-Skripten** („Initial commit: voice-ai-agents scripts from kunde27") mit 15 Unterprojekten: WhatsApp-Sprachbots (`WA-Tamara`, `whatsapp_complete`, `wa-lina`, `livekit-wa-bot` — billing-Logs, viele Versionsnummern), `aigirlfriend` (357 Dateien, v42-Agenten + Backups), `livekit-agent-py` (180 Dateien: worker4.py, translator_agent4.py, redis-translate-multi-v4.py, voicecube_gladia2.py …), `livekit-agent`, `livekit-translate`, `livekit/` (ingress/egress/livekit/sip-Konfigs), `openai-s2s` (LiveKit-Bot-Gemini-Varianten), `deepgram-bot` (= „Translator Phone v3.0 — Deepgram Rundum-Sorglos-Paket": Express+WS-Proxy zu Deepgram Agent API mit Nova-3 STT → GPT-4o-mini → Aura-2 TTS, deutsch/englisch bidirektional, nur DEEPGRAM_API_KEY nötig; README ist auch das Repo-Root-README), `dolmetscher`, `voicecube-dashboard`, `videosdk`, `voice-samples`. Charakterisierbar als Entwicklungs-Archiv mit zahlreichen Backups/Versionen, nicht als konsolidiertes Produkt.

### vertex-proxy
- Standort https://codeberg.org/gofonia/vertex-proxy · Sprachen: Python · 289 LOC, 3 Dateien · erstellt 2026-08-12
- Schlanker FastAPI-Proxy, der Google Vertex-AI-Gemini-Modelle (gemini-2.5-flash-lite, -flash, 2.0-flash, 1.5-flash, openai/gpt-oss-120b) als OpenAI-kompatiblen `/v1/chat/completions`-Endpunkt bereitstellt (inkl. `/v1/models`, Root-Health). Spezifische Hürdenlösungen: volle ADC-Kompatibilität (`google.auth.default()` statt Service-Account-JSON), automatische Schema-Bereinigung für Tool-Calling (Arrays wie `type: ["string","null"]` → Vertex-konforme Strings), Empty-Contents-Fallback bei reinem System-Prompt, SSE-Streaming. Genutzt z. B. als LLM-Zugang für LiveKit-Agents.

## Quellen & Methode
Geprüft: README.md aller 5 Repos, api/main.py, api/routers/openai_compatible.py, api/backends/ (qwentts), server.py + parakeet_service/main.py, routes.py, app.py (parakeet), server.js/package.json/Frontend-Struktur (prosodia), Top-Level-Verzeichnisse + Du/Datei-Zählungen (voice-ai-agents), vertex_proxy.py, git log. LOC aus loc_results.json (pygount); Dateizahlen aus famdata/loc_results.

---

# Bots, Scraper & Automatisierung

**Kategorie:** Eigenentwicklung (überwiegend private Arbeitsstände; kleinanzeigenbot & avr-infra = Fork/Anpassung)
**Plattform(en):** GitHub livedialai (10 Repos, davon 8 privat), Codeberg gofonia (openwabot, öffentlich)
**Kanonisches Repository:** — *(keine echte Versionsreihe/Dublette; ch-grabber & ra-crawler sowie immo-bots, telegram-bots & matrix-bots sind jedoch funktional eng verwandt, siehe Notiz unten)*
**Umfang gesamt:** 845.120 Codezeilen, 6.190 Dateien (Quelle: loc_results.json). **Achtung:** Dieser Wert ist stark verzerrt — ca. 75 % entfallen auf immo-bots (579 k) und telegram-bots (113 k), deren LOC-Zahlen durch unzählige Backup-/Iterationsdateien (`*.js-bak`, `*-neu.js`, nummerierte Kopien) und JSON/HTML-Artefakte aufgebläht sind.

## kleinanzeigenbot — Automatisierter Gebrauchtmarkt-Bot (gepatchter Fork)
**Standort:** GitHub: https://github.com/livedialai/kleinanzeigenbot · **Typ:** Fork/Anpassung (Upstream: Second-Hand-Friends/kleinanzeigen-bot)
**Sprachen:** Python (8.656), YAML, JSON · **Codeumfang:** 11.739 LOC, 82 Dateien · **Letzter Push:** 2026-08-17

**Funktionsumfang:**
- Vollständiger Bot zur Automatisierung von Kleinanzeigen (eBay Kleinanzeigen): Inserate anlegen, auflisten und löschen (CLI-Skripte `list_ads.py`, `delete_ads.py`), Inseratdaten extrahieren (`src/kleinanzeigen_bot/extract.py`), Konfiguration über `config.yaml` (gilt als Credentials, in `.gitignore`).
- **Patch 1 – Auth0-SSO-Login:** Kleinanzeigen ersetzte das klassische Login-Formular durch einen Auth0/OIDC-Flow auf `login.kleinanzeigen.de` (2-Schritt: `/u/login/identifier` → `/u/login/password`); `fill_login_data_and_send()` entsprechend neu implementiert, Double-Login-Retry entfernt.
- **Patch 2 – Headless/Root-Betrieb auf Servern:** Chromium läuft ohne `--headless=new` (wird von Kleinanzeigen erkannt und mit Fake-„IP gesperrt“-Seite blockiert) stattdessen unter Xvfb, `cfg.sandbox=False` für Root-Betrieb; asynchroner Versand mit Wartezeit, abgesichert gegen Anti-Bot-Erkennung.
- **Proxy-Zwang:** Kleinanzeigen blockiert Rechenzentrums-IPs; betrieben wird der Bot über einen SSH-SOCKS-Tunnel über eine Residential-IP (Dokumentation im README).
- Zusätzlich: Tests (21 Dateien), JSON-Schemas (`ad.schema.json`, `config.schema.json`), Docker-Build-Skript, `pyinstaller.spec` für Desktop-Builds, `update_checker`, Troubleshooting-Doku (`BROWSER_TROUBLESHOOTING.md`, `TESTING.md`).

**Aufbau/Module (aus Verzeichnisstruktur):**
- `src/kleinanzeigen_bot/__init__.py` = Auth0-SSO-Login-Patch, `extract.py` = Inserat-Extraktion, `model/` = Datenmodelle (Ad, Config), `utils/web_scraping_mixin.py` = Browser-Setup/Patch, `resources/` = Assets
- `scripts/` = `post_autopep8.py`, `generate_schemas.py` · `docker/` = Image-Build · `schemas/` = JSON-Schemas · `tests/` = Testsuite

## Sonstige Einzelrepos dieser Familie

### ch-grabber (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js, "Genshi-Text" = JS-Dateien) + wenig HTML · 3.592 LOC, 24 Dateien (Ordner `ch-grabber/`) · Letzter Push: 2026-05-17
- **Zweck, aus Inhalt abgeleitet:** Scraper für das Anwaltsverzeichnis der Bundesrechtsanwaltskammer — Ziel ist `bravsearch.bea-brak.de` (BravSearch). CLI mit `-p/--plz` (Suchkriterium) und `-o/--output`; Ausgabe als CSV (`anwaelte.csv`) mit Name, Vorname, Berufsbezeichnung, Kammer, Kanzlei, Adresse, Telefon/E-Mail/Web und `beA_ID`. Elf iterative Varianten nebeneinander (`grabber-RA.js`, `grabber-RA2.js`, `grabber-ultimativ.js`, `grabber-final-fixed.js`, `grabber-fixed.js`, `grabber-puppeteer.js`, `kammer-berlin.js`, `bundesanwaltskammer.js.js`, `serper_5.js`), teils mit axios+cheerio+Cookie-Jar, teils mit Puppeteer; `cities.txt` = deutsche Städte (Schleswig-Holstein/NF-Bereich), `debug_search_result.html` = Debug-Anhang. Kein README; Zweck aus Skriptkopfzeilen und Datenstruktur abgeleitet (klar erkennbar: Sammlung von Rechtsanwalt-Datenbanken/-Listen für Vertriebszwecke).

### ra-crawler (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js) · 981 LOC, 13 Dateien · Letzter Push: 2026-05-17
- **Zweck:** funktionaler Zwilling von ch-grabber, aber Puppeteer-basiert: drei Versionsstände in Ordnern `0/`, `1/`, `4/` mit `RA-ultimate.js`, `RA-ultimate-hard.js`, `ultimate.js`, `ultimate-final.js` (Kommentar: „grabber-final-hardened.js“); gleiche CLI-Parameter (PLZ, Output), gleiches CSV-Schema inkl. `beA_ID`. `4/anwaelte.csvbak` (232 KB) = Datensammlung bereits gecrawlter Anwälte (z. B. Düsseldorf, Timestamp 2026-02-12). Crawlt dasselbe BRAK/BravSearch-Verzeichnis; die Ordner 0/1/4 sind Iterationen. Kein README; Zweck eindeutig aus Inkraft: Anwaltsdatensammlung.

### serper-scraper (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js) · 1.201 LOC, 17 Dateien (Ordner `pizza/`) · Letzter Push: 2026-05-17
- **Zweck:** Lead-Recherche über die Serper-API (Google-Suche/-Maps-Ergebnisse als API): für eine Städteliste (`cities.txt`, 386 deutsche Städte) werden Gewerbetreibende je Kategorie gesammelt — Versicherungen, EDEKA, REWE, Pizzalieferdienste (`serper_pizzalieferservice.js` + `*_maps.js`-Varianten, `call-center.js`). CSV-Ausgabe mit Name, Adresse, Telefon, Webseite, Bewertung, Typ, Seitenposition; konfigurierbare Batch-/Seitenparameter (`.env`), Ergebnisse in `results/`. Kein README; Zweck aus Code (Header „Fehler beim Lesen…“, CSV-Schema) erkennbar — Verkaufs-Lead-Datensammlung für Vertriebsprojekte.

### immo-bots (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js, 213 k), JSON (186 k), HTML (175 k) · 579.462 LOC, 3.973 Dateien · Letzter Push: 2026-05-17
- **Zweck, aus Inhalt abgeleitet:** Sammel-Repo mit ~15 Arbeitsverzeichnissen des „Flatvision AI Makler“-Bot-Ökosystems für Immobilienmarketing: **KI-Makler-Bot** (Matrix-Chatbot `matrix-bot-sdk`/Redis, „Flatvision AI Makler Bot with MySQL User Management“, Freemium-Credits, Admin-WebInterface und Kundennummern; „Virtual Staging“/Immo-Bildverarbeitung mit Gemini/Wavespeed/OpenAI), **Kanäle:** WhatsApp (`WA_immo` = „whatsapp-flatvision-bot“ mit Freemium-Credit-System und Admin-Interface), Microsoft Teams (`teams_immo`), Spanisch-Version (`spain-neu`/`spain-immo` = „Bot de Matrix para virtual staging inmobiliario … Wavespeed AI y Revolut“), **Zahlungen:** Stripe, Revolut, SumUp (`DACH-NEU`), **Voice:** Jambonz, AWS Bedrock, Astria, Gemini S2S (v. a. `Nadine-Immo`), **Scraper:** Idealista (`idealista/`), Gelbe Seiten (`Dach-gem-kie/simple_mehr_anzeigen_scraper.js`), Puppeteer-Bulk-Scraper (`parallel_bulk_scraper.js`), `mysql-immo-bot.js`; **KIE = Kleinanzeigen** (`kie_bot_limacity_ftp.js`, `flatvision_kie_makler_bot.js`), **Portrait-Generator** (`wa-portrait` = „WhatsApp Portrait Generator Bot mit Gemini AI“), Support-/Staging-Bots.
- **Bewertung der LOC:** 579 k LOC sind keine Singularzahl echter Funktion — `Nadine-Immo/` (3.010 Dateien, v. a. `output-*`-Daten, `bible_processed/`) sowie hunderte Backup-Kopien (`*.js-bak`, `*_fixed.js`, `*-neu.js`, `*.js???`) dominieren; mehrere Ordner sind laufende Arbeitsstände desselben Bot-Projekts. Kein README; Zweck durch package.json-Beschreibungen und Skriptköpfe eindeutig erkennbar.

### telegram-bots (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js, 108 k) + HTML/JSON · 112.801 LOC, 681 Dateien · Letzter Push: 2026-05-17
- **Zweck, aus Inhalt abgeleitet:** Sammlung von Bot-Arbeitsständen (Ordner = Server-/Build-Verzeichnisse): `basis_bot/` („Matrix Bot for AI Image Generation“ — iteriert: `final_matrix_basis.js`, `matrix_bot_gpt4o.js`, `matrix_bot_sharp.js`, Flatvision-Makler-KIE-Bots, E-Commerce-Staging, Migrationen `migration*.js`, `charakters/`, `staging/`), `ur-basis/` („matrix-portrait-bot — Portrait-Generierung mit Google Gemini“ + `pm2_configs.json`, `.env-*`), `gemini-test-bot/` („Simple voice bot with Google Gemini“), `tele-translate/` (Telegram-Gruppen-Übersetzer: gram.js, Fireworks-LLM, Whisper STT, Cartesia TTS), `telefonsexbot/` (Ordner; Inhalt laut package.json = „Jambonz Voice Bot with SumUp Payment Integration and Redis Time Management“ — Telefon-Voice-Sales-Bot mit Brevo-SMS), `telegram1/` (562 Dateien: heterogener Arbeitsstrang mit SumUp-Zeitpakete-Bots, TTS-Bots, Krypto-Test, `anwaltsverzeichnis-grabber.js`/`-analyzer.js`, `analyze-html.js`, `anwaelte-*.json` → auch Reverse-Engineering eines Anwaltsverzeichnis-Scrapers).
- **Bewertung:** Wenig README; Zweck aus package.json-Beschreibungen und Dateinamen abgeleitet — es handelt sich um einen Registrierungs-/Arbeitsort mehrerer gleichartiger Bots inkl. Versionierung und Migrationen; LOC-Zahl durch Iterationen aufgebläht. `pm2_configs.json` belegt, dass die Ordner (z. B. `/root/basis_bot`, `/root/matrix-friseur`) tatsächlich betriebene Dienste auf einem Server waren.

### matrix-bots (privat)
- Standort: GitHub livedialai · Sprachen: JavaScript (Node.js, 5.276) + JSON/TS/YAML · 5.630 LOC, 38 Dateien · Letzter Push: 2026-05-17
- **Zweck, aus Inhalt abgeleitet:** Zwei Matrix-Bot-Projekte: `matrix-firma/` = „matrix-translator“ — von einem Telegram-Übersetzer (`telegram_translator.js`) auf Matrix migrierter Übersetzerbot (`matrix-js-sdk`, Homeserver `chat.jurgo.es`, `speakers.json`, ffmpeg, Docker-Compose/Element-Config) und `matrix-friseur/` = Matrix-Portrait-Bot mit OpenAI `gpt-image-1` („Variationen“-Module, Redis) für den Friseur-Anwendungsfall. Kein README; Zweck aus Skriptkopf und package.json erkennbar.

### openwabot (Codeberg, öffentlich)
- Standort: Codeberg: https://codeberg.org/gofonia/openwabot · Sprachen: JavaScript (Node.js, 96) + Markdown · 153 LOC, 5 Dateien · Erstellt: 2026-08-21
- **Zweck:** WhatsApp-Chatbot für das OpenWA-Gateway (openwa.dev), LLM-agnostisch (OpenAI-kompatibel: DeepInfra, OpenAI, DeepSeek …): Webhook `message.received` → LLM `chat/completions` mit Verlauf (letzte 20 Nachrichten, `history/`) → Antwort via OpenWA-REST `POST /api/sessions/{id}/messages/send-text`; `/reset`-Befehl; `system-prompt.txt` zur Laufzeit editierbar; `GET /health`; Start via `node server.js` oder pm2. Vollständig dokumentiertes README (Architektur-Diagramm, Setup, File-Tabelle). Typische Eigenentwicklung, sauber aufgebaut.

### limacity (privat)
- Standort: GitHub livedialai · Sprachen: PHP (72.243) + HTML/PHP, XML · 127.251 LOC, 1.264 Dateien · Letzter Push: 2026-05-17
- **Zweck:** Webroot-/Server-Backup („lima-db.de“-Hosting lt. `doctranslate/server.py`-DB-Konfig; `.bash_history`, `.wget-hsts` = Server-Artefakte) mit ca. 30 Verzeichnissen: Kernprojekt **„SwornTranslate/DocTranslate“** (`doctranslate/` + `doctranslate/sworn/`): PHP-Plattform für beeidigte KI-Dokumentenübersetzung (Nutzer-/Partner-/Admin-Frontends `user.php`, `partner.php`, `admin.php`, Credit-Topup `topup.php`, `cron.php`, `payment.php`, `config.php` mit 19 Cent/Normseite); **Digistore24-Integration** (`sworn/digistore/` mit README.md, `db-digistore-migration.php`, `digistore-webhook.php`, Abo-Crons, `InvoiceFrontend.php`, Preismodelle 39 ct/Normseite bzw. 35 €/150 € pro Monat, `CONFIG_OPTIONS.md`, `DIGISTORE24_RECHNUNGEN.md`).
- Weitere Inhalte: `dolmetscher/` = zweiter Übersetzungs-/Dolmetscher-Service (`dolmetscher.life`: LiveKit, YOURLS, Revolut, Brevo — `setup.txt` dokumentiert Setup), `dr-lingu/` (composer-PHP-App mit JWT-Tests), `driuris/` (Mini-PHP), `jambonz/` = Voice-AI-Agent v4.1 zur Meetergo-Terminvereinbarung (Produktions-Fixliste im Kopf), `gofinia.de/` = Website (Impressum/Datenschutz, API-Doku), Fanvue-Domainfamilie (`fainvue`, `famevue`, `fanevue(.de)`, `fanfue(.de)`, `fannvue`, `fanvou`, `fanvuue`, `fanvuy`, `fenfue`, `fenvue(.de)`, `fanvue.es` = Catchall-/Landingpages; `fanfue.com` = YOURLS-Shortlink-Installation), Mini-Landingpages `acapella.cc`, `carecube`, `eunovia`, `hippocube`, `inference`, `insucube`, `ch`, `default-website`, `empty-install` (YOURLS-Frischinstallation), `joomla-2024-12-06-*` und `wordpress_de-2025-12-11-*` (CMS-Backups).
- **Abgrenzung:** Neben dem Hauptprojekt (SwornTranslate/Digistore24) enthält das Repo viele kleine Neben-Sites — es ist ein kompakter Webroot-Snapshot, kein einzelnes Produkt.

### doctranslate (privat)
- Standort: GitHub livedialai · Sprachen: Python (744) + HTML/PHP, Bash · 987 LOC, 21 Dateien (Ordner `doctranslate/`) · Letzter Push: 2026-05-17
- **Zweck, aus Inhalt abgeleitet:** Python-Backend der DocTranslate-/SwornTranslate-Plattform (Gegenstück zum PHP-Frontend in `limacity/doctranslate`): FastAPI-Server `server.py` für **KI-Dokumentenübersetzung mit Strukturerhalt** — Pipeline DOCX → HTML (mammoth) → Segmentierung → LLM-Übersetzung (OpenAI-kompatibel, Standard `qwen3` via `LLM_BASE_URL`/`LLM_MODEL`) → HTML → DOCX/PDF (WeasyPrint/pandoc); PDF digital via pdf2docx, PDF-Scans via Tesseract-OCR; API-Key-Auth über MySQL (`touch2get.lima-db.de`) für On-Premise-Kunden, `server_check.php` = Health-Check, `requirements.txt`, `setup.sh`, `start.sh` (uvicorn Port 8900). 20 Versionen von `server.py` (`-prod`, `-produktion`, `-bak2…-6`, `-107`, `-108`, `-gig-batch` …) = Arbeitsstand mit vielen Backups. Kein README; Zweck eindeutig aus Docstring und Code erkennbar.

### avr-infra (privat)
- Standort: GitHub livedialai · Sprachen: YAML (1.021) / Markdown / JS · 1.323 LOC, 72 Dateien · Letzter Push: 2026-05-17 · Git-Log: „Initial commit: avr-infra from dialer.advocube.de“
- **Zweck:** Fork/Kopie des Upstream-Projekts agentvoiceresponse/avr-infra (Badges im README verweisen auf https://github.com/agentvoiceresponse/avr-infra) mit Eigenanpassungen: **Voice-AI-Agent-Infrastruktur** — Docker-Compose-Orchestrierung für AVR-Core (Audio-Stream zwischen VoIP-PBX und KI), austauschbare ASR/LLM/TTS-Provider (Google Cloud STT, Deepgram, OpenAI Realtime, Ultravox S2S, Gemini, Anthropic, ElevenLabs, lokale Varianten Vosk/n8n; `docker-compose-*.yml` je Variante) plus Docker-Asterisk mit PJSIP und SIP-Testclient. Unterverzeichnis `avr/` = eigene Ergänzung „avr-vicidial-bot“ (Node.js, WebSocket/Redis, Azure OpenAI, binäres Audio-Packet-Protokoll — Anbindung an Vicidial); deutsche Anpassungen: `verkaufsprompt.txt` („Verkaufsassistent für Deutschland“), `entscheiderprompt.txt`, `keys/`, `functions/`. Eng mit den übrigen Voice-Bots (jambonz, SumUp) verwandt.

## Notizen zu Verwandtschaften
- **ch-grabber ↔ ra-crawler:** gleiches Ziel (BRAK/BravSearch-Anwaltsverzeichnis), gleiches CSV-Schema (inkl. beA_ID), unterschiedliche Technik (axios/cheerio vs. Puppeteer) und getrennte Repos — vermutlich Parallelentwicklungen desselben Zwecks, keine einfache Dublette.
- **immo-bots ↔ telegram-bots ↔ matrix-bots:** entlang derselben Bot-Linie (Flatvision/AI-Bild + Makler-Bots, Matrix/WhatsApp/Telegram, Stripe/SumUp/Revolut-Zahlungen) — die drei Repos überschneiden sich inhaltlich (z. B. existieren `matrix-friseur`-Bezüge in telegram-bots/pm2-Config und als Ordner in matrix-bots); es sind Arbeitskopien desselben Systems über verschiedene Server/Zeitpunkte hinweg.
- **limacity ↔ doctranslate:** zwei Repos desselben Produkts „SwornTranslate/DocTranslate“ (PHP-Frontend + Python-Backend), keine Dublette.

## Quellen & Methode
Geprüft: `data/famdata/F16_bots_automation.json` (Struktur, LOC, README-Auszüge), `loc_results.json` (LOC bestätigt); im Dateisystem je Repo: README.md (vorhanden bei kleinanzeigenbot, openwabot, avr-infra, limacity→`sworn/digistore/README.md`), `package.json`/`pyproject.toml`/`requirements.txt` (Name/Description/Abhängigkeiten), Skriptköpfe (`grabber-*.js`, `RA-ultimate-hard.js`, `serper_*.js`, `mysql-immo-bot.js`, `bot_matrix.js`, `matrix_translator.js`, `matrix_openai_bot.js`, `server.py`), Konfig-/Datenbelege (`.bash_history`, `pm2_configs.json`, `anwaelte.csvbak`, `cities.txt`, `setup.txt`, `topup.php`), Verzeichnisstruktur und Dateizählungen (`find -type f`). Bei Repos ohne README (ch-grabber, ra-crawler, serper-scraper, immo-bots, telegram-bots, matrix-bots, doctranslate) wurde die Funktion ausschließlich aus Code/package.json-Angaben abgeleitet; „nicht eindeutig“ trifft auf keines der Repos zu — alle Zwecke sind aus Inhalt erkennbar, wenngleich der Reifegrad (Over-Backups, unzählige `*-bak`-Versionen) zeigt, dass es sich um private Arbeitsstände handelt und nicht um wartbare Projekte. Die exorbitanten LOC-Werte von immo-bots und telegram-bots sind durch Iterations-Kopien und Artefakt-Dateien aufgebläht und spiegeln keine reale Funktionsmenge wider.

---

# LiveKit-Agenten & Voice-Werkzeuge

**Kategorie:** Eigenentwicklung (voice-bots teilweise Anpassung des Open-Source-Projekts „Agent Voice Response" / avr-sts-openai)
**Plattform(en):** GitHub livedialai und Codeberg gofonia
**Kanonisches Repository:** cloud-agent  *(Versionsreihe livekitcloud → cloud-agent; daneben Self-Hosted-Serie agent3.5/agent4.5en)*
**Umfang kanonisch:** 3.243 Codezeilen, 9 Dateien (Quelle: loc_results.json, nur sourceCount)

## cloud-agent — GoFonIA Cloud Agent (Kanonisches Repository)
**Standort:** Codeberg: https://codeberg.org/gofonia/cloud-agent · **Typ:** Eigenentwicklung
**Sprachen:** Python, Markdown, TOML · **Codeumfang:** 3.243 LOC, 9 Dateien · **Letzter Push:** 2026-07-24 (letzter Commit „fix: log dialed number before SIP create …")

**Funktionsumfang:**
- Cloud-deployter LiveKit-Voice-Agent für die GoFonIA-Multi-Tenant-Plattform; läuft als LiveKit-Cloud-Agent (livekit.toml, Dockerfile), kein eigener Server.
- Nimmt Anrufe auf zwei Wegen an: SIP-Anruf über LiveKit-Cloud-SIP-Bridge und Browser-Call über WordPress-Plugin-Token-Endpoint (LiveKit-JWT mit `metadata.called_did`).
- Tenant-Auflösung: liest `called_did` aus Room-Metadata und fragt das GoFonIA-Backend nach dem Tenant; lädt dann tenant-spezifische Konfiguration (STT, LLM, TTS, Tools, System-Prompt) und führt das Gespräch.
- Eingebetteter Warm-Transfer-Controller v4.1: Single-Room-Phasenmodell mit Publisher-/Subscriber-seitiger Track-Steuerung (Subscription-Matrix), Outbound-Call via `CreateSIPParticipant` (gespielt über voip2gsm-SIP), Warm (mit Briefing) und Cold; kein HTTP-Server, Methoden werden direkt im Agent-Prozess aufgerufen.
- Musik-Bot als asyncio-Task im selben Prozess (WAV über HTTP-URL geladen und gecacht) statt Subprozess; sauberer Shutdown über stop_event.
- Python-Paket `gofonia-cloud-agent` v4.1.0 (pyproject.toml), LiveKit-Plugins: Deepgram, OpenAI, InWorld, Silero, MistralAI (Cartesia optional).

**Aufbau/Module (aus Verzeichnisstruktur):**
- `agent.py` = Main-Entrypoint: Room-Join, Tenant-Auflösung, Agent-Pipeline, Tools (laut README; `agent_v4.py` wird als Backup/Referenzversion einer früheren Iteration bezeichnet)
- `transfer_controller.py` = Warm-Transfer-Controller v4.1 (eingebettet, Phasen, Subscriptions)
- `music_bot.py` = Hold-Music-Stream (Cloud-kompatibel, HTTP-WAV)
- `system_prompt.txt` = System-Prompt; `livekit.toml` = LiveKit-Cloud-Deployment (subdomain test-fzautyaq, Agent-ID CA_bDA46rJ4dLrH); `Dockerfile`, `.env.example`; `README.md` = ausführliche Architektur-/Deploy-Doku (10,8 KB)

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| livekitcloud | Codeberg | Ältere Generation (v3): „Universal Voice Agent" mit konfig-getriebener Pipeline (agent.py lädt Config/Keys per URL), WP-Plugin + Widget; Vorgänger des Cloud-Agent |
| agent3.5 | Codeberg | Deutsche Self-Hosted-Variante der Warm-Transfer-Linie (Controller v4.0 als HTTP-Dienst, CDR-Schreiber) |
| agent4.5en | Codeberg | Englische Variante von agent3.5 mit Stabilitäts-Fixes (OUTBOUND_E164-Flag, „AI hört nur den Chef" bei Transferentscheidung); entwickelter als agent3.5 |
| agent_v3.py / gofonia-agent.py (in livekitcloud) | Codeberg | Identische Dateien (Dublette im selben Repo, 93 KB) |
| agent_v4.py (in cloud-agent) | Codeberg | Backup-/Referenzversion einer früheren Agent-Iteration (README: agent.py = Main) |
| README.md.bak (in livekitcloud) | Codeberg | ältere README-Version („Agent Architecture", WordPress-Fokus) |

*Begründung kanonisch: cloud-agent ist die jüngste, funktional umfassendste Generation der Cloud-Linie (pyproject-Version 4.1.0 mit „Warm Transfer", eingebetteter Controller v4.1 statt v4.0, Tenant-Auflösung, vollständige README-Architekturdoku, Docker/livekit.toml-Deployment). Die Gegenstücke livekitcloud (v3, 2.357 LOC) und agent3.5/agent4.5en (2.222/2.240 LOC) sind Vorstufen bzw. die parallele Self-Hosted-Serie mit eigenem HTTP-Controller + CDR-Buchung.*

## Sonstige Einzelrepos dieser Familie (kurz, je 2–4 Zeilen)
### livekit-agents (GitHub, privat, 60.657 LOC, 216 Dateien)
- Standort https://github.com/livedialai/livekit-agents · Sprachen: Python, YAML, Bash · Letzter Push 2026-05-17 (Initial „LiveKit agents from lingia.life")
- Monorepo-Werkbank/Archiv eigener Voice-Agenten, keine README. `livekit-agent-py/` (170 Dateien): dutzende Iterationen — agent.py bis agent4.py (Deepgram/OpenAI/InWorld/Silero), DeepSeek-IVR-Varianten (deepseek-ivr-production*.py), Übersetzungs-Agenten (deepl3–deepl15, redis-translate-multi-v4.py mit Redis-Speaker-Tracking, translator_agent4.py), Gladia-/Lingo-/Prosodia-/Voxtral-Skripte, VoiceCube-Stimmen-Skripte (voicecube_clone*, voice-cube-newest.py), worker.py/worker4.py, fetch_voices.sh, voices.txt, env.local (Credentials). `livekit-config/`: Self-Hosted-LiveKit-Setup (livekit.yaml, sip.yaml mit SIP-Bridge-Outbound-Trunk zu FreePBX 89.167.7.149, ingress/egress.yaml, docker-compose livekit+redis). `pipecat/` (44 Dateien): Pipecat-basierte ViciDial-Outbound-Bots (bot-vicidial-*, Cartesia/Inworld/Deepgram) samt Verkaufs-Prompts (verkaufsprompt/entscheiderprompt für Supermarkt/Pizza, -us) und zahlreichen .bak-Varianten; Bot „Beate Müller" sondiert per Kaltelefonie die telefonische Bestellbarkeit von Supermärkten.
- Charakter: unaufgeräumte Entwicklungs-Historie mit vielen Backups (auch .bak, .de, -schrott).

### livekitcloud (Codeberg, 2.357 LOC, 16 Dateien)
- Standort https://codeberg.org/gofonia/livekitcloud · Sprachen: Python, PHP, HTML · Letzter Commit 2026-07-17
- „LiveKit Cloud — Universal Voice Agent" (README): läuft komplett auf LiveKit Cloud (Null eigener Server), STT Deepgram, LLM OpenAI-kompatibel (Requesty/Groq), TTS Mistral/InWorld; dynamische Tools über Admin-Panel (jeder HTTP-Endpoint), keine Hardcodierung, kein Redeploy. `agent.py` (4,7 KB) lädt Config/Keys per URL aus Room-Metadata bzw. WP-Endpoint; `agent_v3.py`/`gofonia-agent.py` (identisch, 93 KB) = große v3-Variante mit Music-on-Hold; `admin/index.php` = PHP-Admin-Panel (Keys, Prompt, Tools); `wp-plugin/` = WordPress-Plugin (livekit-voice-agent.php, includes), `widget.js` = Call-Widget (hartcodierte LiveKit-Cloud-URL + Keys), `music_hold.wav`, Dockerfile, README.md.bak = alte WordPress-Architektur-Doku.

### agent3.5 (Codeberg, 2.222 LOC, 4 Dateien) / agent4.5en (Codeberg, 2.240 LOC, 4 Dateien)
- Standorte https://codeberg.org/gofonia/agent3.5 und https://codeberg.org/gofonia/agent4.5en · je 4 Python-Dateien · Letzte Commits 2026-08-06
- Self-Hosted-Linie (VPS, livekit_sip_bridge, pm2): `agent_v3.py` = Sprach-Agent (LiveKit Agents, Deepgram/OpenAI/InWorld/Cartesia/Mistral, Meetergo-Terminbuchung + Firmenwissen-Tools), `controller_v3.py` = „Warm Transfer Controller v4.0 — Single-Room, Phase-Based Subscription Matrix" als HTTP-Dienst (POST /v1/transfer, /v1/briefing-complete, /v1/cancel, GET /v1/room/{room}/state, LiveKit-Webhook), `music_bot.py` = Hold-Music-Track, `cdr_writer.py` = liest livekit_sip_bridge-Logs, schreibt CDRs + Minuten-Zähler nach Postgres (Inbound-Routen, Dedupe, Backfill). `agent4.5en` = dieselben Dateien, aber englische Tool-Texte plus Fixes (OUTBOUND_E164=1 für E.164-Provider, „AI muss nur den Chef hören" bei der Durchstell-Entscheidung) — die entwickeltere Variante; cdr_writer.py und music_bot.py sind identisch.

### simultan (Codeberg, 904 LOC, 8 Dateien)
- Standort https://codeberg.org/gofonia/simultan · Sprachen: Python, Markdown · Letzter Commit 2026-06-06
- „GoFonIA Simultaneous Translation Agent": LiveKit-Worker für Echtzeit-Simultandolmetschen in Videokonferenzen ohne GUI-Änderung. Deepgram Nova-3 (STT) → DeepSeek (Übersetzung) → InWorld TTS (sprachspezifische Stimmen); publiziert pro Sprache zwei Tracks (nur Übersetzung / Übersetzung + Original gemischt). Lauscher-Steuerung über sichtbare Teilnehmernamen: `_xx` = nur Übersetzung, `-xx` = Übersetzung + leises Original (20 %); Room-Filter (akzeptiert nur Nicht-`call-`-Räume), Cost-Guard über lokale LiveKit-API. `simultan_core.py` (ListenerMode, SpeechSegmenter, mix_pcm16) mit Unit-Tests (tests/test_core.py), pm2-Datei ecosystem.config.js, docs/DE.md, pyproject.toml.

### voice-bots (GitHub, privat, 19.505 LOC, 96 Dateien)
- Standort https://github.com/livedialai/voice-bots · Sprachen: JavaScript (18,7 kLOC), Python · Letzter Push 2026-05-17
- Sammlung experimenteller Node.js-Voice-Bots (README vom Upstream-Projekt „Agent Voice Response — OpenAI Speech-to-Speech Integration" / avr-sts-openai). `voice-bot/` (51 Dateien): AudioSocket-Bot für Asterisk mit Azure TTS/STT (audiosocket-bot, Package audiosocket-bot 1.0.0), openai-websocket.js, openai3/9.js (OpenAI-Realtime), mistral-optimiert.js, voxtral-small.js. `bot/` (34 Dateien): jambonz-voice-bot 1.0.0 (Jambonz + SumUp-Zahlung + Redis-Zeitsteuerung, livekit-server-sdk), livekit_voice_bot6.js, fireworks_voice_bot-debug.js, brevo_sms_service.js, user_service.js, verkaufsprompt.txt-de. `voxtral5.js` = AudioSocket-Bot (Mistral + Azure Speech/OpenAI, Port 9099). Offensichtlich Versuchs-/Projekt-Sammlung mit vielen Varianten.

### voice-design (GitHub, privat, 0 LOC, 0 Dateien)
- Standort https://github.com/livedialai/voice-design · Letzter Push 2026-05-17 (Initial „Initial commit: voice-design from lingia.life")
- Leeres Repo (nur `.gitignore`), kein Code, kein README. Zweck nicht erkennbar; gemessen am Commit-Kommentar offenbar ein nie befülltes Platzhalter-/Import-Repo aus der lingia.life-Plattform (gleiche Herkunft wie livekit-agents).

### warmbot (GitHub, öffentlich, 1.674 LOC, 8 Dateien)
- Standort https://github.com/livedialai/warmbot · Sprachen: Python, Markdown, Bash · Letzter Push 2026-05-14 („v2.2.2: Letzte 5 Fixes vor PoC-Test")
- „WarmBot — Warm/Cold Call Transfers mit LiveKit + Pipecat": Track-Permission-basierte Durchstellung ohne moveParticipant, ohne Asterisk, ohne SIP-REFER. `controller.py` = Regie (HTTP :9100, update_subscriptions, CreateSIPParticipant, Participant-Monitoring, Phasen; API /v1/transfer, /v1/briefing-complete, /v1/cancel, /v1/room/{room}/state, Webhook), `transfer_agent.py` = Pipecat-Voice-Bot v2.1 (STT/LLM/TTS + Transfer-Tools, auto_subscribe=False), `music_bot.py` = WAV-Loop-Publisher, `run.sh` = Start aller drei Komponenten, `music_hold.wav` (6,5 MB), `.env.example`, `bot.py` = älterer Bot-Ansatz.

### sipfonia (GitHub, privat, 662 LOC, 4 Dateien)
- Standort https://github.com/livedialai/sipfonia · Sprachen: HTML (admin.html, 480 Zeilen), Bash · Letzter Push 2026-08-19 (jüngster Push der Familie)
- „Voice-AI-Telefonassistent als Single-Binary-Appliance": nimmt eingehende Anrufe automatisch an (SIP-Trunk, G.711 alaw), ASR Deepgram (Deutsch), TTS InWorld/Deepgram, LLM über OpenAI-kompatible Endpoints, Custom HTTP-Tools (z. B. Terminbuchung), Weiterleitung warm (mit Briefing) oder blind; WebUI-Wizard (`static/admin.html`, 572 Zeilen: SIP-Trunk, DID, Prompt, Stimmen, Tools, Kalender). `release/active-call` = gebautes Rust-Binary (ELF x86-64, 68,6 MB) — laut README carrier-grade für bis zu 10.000 parallele Gespräche; `scripts/install.sh` = idempotenter curl|bash-Installer (/opt/hpia, systemd-Dienst hpia, WebUI unter :8080/admin, Werkzugang admin/GoHiPIA). Repo enthält Binary statt Quelltext.

## Quellen & Methode
Geprüft: famdata-JSON (Structure, README-Auszüge, LOC), loc_results.json; im Dateisystem verifiziert per `ls`, `git log`, `diff`, `head`/`grep` auf Schlüsseldateien von allen 9 Repos — u. a. cloud-agent (README.md, agent.py, transfer_controller.py, music_bot.py, pyproject.toml, livekit.toml), livekitcloud (README.md/README.md.bak, agent.py, agent_v3.py vs. gofonia-agent.py via diff, admin/index.php, widget.js, wp-plugin/), agent3.5 vs. agent4.5en (diff aller 4 Dateien), livekit-agents (livekit-agent-py/, livekit-config/, pipecat/ — Struktur + Heads von translator_agent4.py, redis-translate-multi-v4.py, deepseek-ivr-production.py, worker.py, bot-vicidial-cartesia-nothink-fixed.py, verkaufsprompt.txt, env.local), simultan (agent.py, simultan_core.py, tests/, ecosystem.config.js, docs/DE.md), voice-bots (package.json beider Unterordner, voxtral5.js), warmbot (README.md, controller.py, transfer_agent.py, bot.py), sipfonia (README.md, install.sh, admin.html, `file` auf release/active-call), voice-design (git log, git ls-tree: nur .gitignore).

---

# CallID — AI Call Center Plattform

**Kategorie:** Fork/Anpassung (umfassende Eigenweiterentwicklung auf cal.com-Basis)
**Plattform(en):** GitHub livedialai, Codeberg gofonia
**Kanonisches Repository:** callid
**Umfang kanonisch:** 633.723 Codezeilen, 8.001 Dateien (Quelle: loc_results.json)

## callid — 1-Satz-Kurzcharakteristik
**Standort:** GitHub: https://github.com/livedialai/callid / Codeberg: https://codeberg.org/gofonia/callid · **Typ:** Fork/Anpassung (cal.com-Fork „Cal-ID" von OneHash, weitgehend eigenentwickelt)
**Sprachen:** TypeScript, JSON, TSX (ferner CSS/Lasso, XML, Transact-SQL) · **Codeumfang:** 633.723 LOC, 8.001 Dateien · **Letzter Push:** 17.06.2026

**Funktionsumfang:**
- **Termin-/Scheduling-Plattform auf cal.com-Basis:** Event-Typen, Verfügbarkeiten, Teams/Organisationen, Routing-Forms, Instant-Meetings, Videokonferenz-Integrationen, Booking-Seiten mit Embed-Bibliothek (eigene `calid-embeds` neben `@calcom/embeds`).
- **AI Voice Agents („Cal.ai Enterprise Voice AI Agents", via Retell AI, siehe `.env.example` → `RETELL_AI_KEY`):** AI-Agenten nehmen Anrufe an und führen ausgehende Anrufe, führen die Gespräche per LLM-Prompt (Prompt-Templates), buchen Termine direkt per Telefon („book a meeting" als Preset-Funktion), leiten Anrufe weiter (call transfer, voicemail detection), unterstützen mehrsprachige Gespräche; Konfiguration je Event-Typ über den AI-Tab im Event-Typen-Setup (`EventAITab.tsx` / `AIEventController.tsx`) und dynamische Variablen für eingehende Anrufe (`pages/api/get-inbound-dynamic-variables.ts`); eigenes Datenmodell `AIPhoneCallConfiguration` im Prisma-Schema.
- **ElevenLabs-Integration** im App-Store (Voice/Sprachsynthese neben den Retell-Phone-Funktionen).
- **SMS-/WhatsApp-Benachrichtigungen:** Twilio SMS + WhatsApp Business API, geplante Erinnerungs-/Workflow-Meldungen, Opt-out-Verwaltung (`SMSAbuseLock`), Email→SMS-Bridge über SendGrid-Inbound-Webhooks mit signierten Reply-Aliasen (`sms.cal.id`).
- **Ingesamt an Kommunikations- und Call-Operationen orientiertes Feature-Set:** Kontakte-Verwaltung, API-Keys, Impersonation, Insights/Analytics, Workflows, Unified Calendar (Kalender-Sync mit Cache), No-Show-Handling, PBAC-Rechte, Webhooks (inkl. `booking_host_reassigned`).
- **Öffentliche APIs und Integrationen:** REST-API v1 + v2 (Nest.js, Swagger, API-Proxy), Integrationen im App-Store (2.600+ Dateien, u.a. retell-ai, elevenlabs, Google-/Outlook-Kalender); Fastify-Connector mit Swagger-Doku und **MCP-Server** (Tools-Katalog) für externe Consumer und Mobile-Clients.
- **Job-Architektur:** BullMQ-Worker (Redis) mit Data-Sync-Jobs (Calendly-Import, Buchungs-/Kontakt-/Nutzer-Exporte, Kalender-Sync) und Scheduled Jobs; Inngest als Fallback; E-Mail-Zustellung via SendGrid; Sicherheits-Features (Disposable-Email-Block, Rate-Limiting beim Signup).
- **Technik:** Turborepo-Monorepo (Yarn Workspaces), Next.js 15.4 (+ React 18, tRPC, Tailwind), Prisma (PostgreSQL, T-SQL-Skripte für Datenpflege), Playwright-E2E, Vitest, Sentry/PostHog/Axiom; Docker- und Heroku-Deployment (`Procfile`, `Dockerfile`, `entrypoint.sh`); AI-Coding-Grundlagen (AGENTS.md, CLAUDE.md, opencode.json, greptile.json).

**Aufbau/Module (aus Verzeichnisstruktur):**
- `apps/web` = Next.js-15-Frontend + Backend-for-Frontend (tRPC, Auth, Booking-Flows, Settings)
- `apps/api` = Plattform-REST-API v1 (Pages-Routes) und v2 (Nest.js/`@calcom/api-proxy`, Swagger)
- `apps/connector` = Fastify-REST-API für Drittanbieter + MCP-Server (`src/mcp/tools-catalog.ts`)
- `apps/worker` = BullMQ-Consumer (Worker `dataSync`/`default`/`scheduled`)
- `packages/calid` = OneHash-eigene UI-Module (admin, api-keys, contacts, insights, teams, unifiedCalendar, workflows mit Twilio-Messaging-Providern, SMS-Webhook, Cron `queueSmsReminder`)
- `packages/features/ee/cal-ai-phone` = Retell-AI-Service (`retellAIService.ts`, `promptTemplates.ts`, `template-fields-map.ts`, zod-Schemata)
- `packages/features` = Feature-Domains (auth, bookings, calendars, eventtypes inkl. AI-Tab, insights, workflows, routing-forms, pbac)
- `packages/job-engine` / `job-dispatcher` / `queue` / `redis` = Job-Pipeline (Data-Sync, Calendly-Import, Exporte)
- `packages/prisma` = Schema (u.a. `AIPhoneCallConfiguration`, `WhatsAppBusinessPhone`, `SMSAbuseLock`, `WebhookScheduledTriggers`)
- `packages/app-store` = Integrations-Apps (u.a. retell-ai „Supercharge your Call Operations with AI", elevenlabs)
- `docs/` = Mintlify-Dokumentation (noch cal.com-Boilerplate) · `scripts/` = Datenpflege-SQL, Dev-Skripte

**Dubletten / Versionsreihe dieser Familie (Tabelle):**

| Repo | Plattform | Einschätzung (ältere Version / Backup / Sprachvariante / identisch) |
|---|---|---|
| callid | GitHub livedialai | **Kanonisch** — vollständiger Code-Snapshot (Initial-Commit „Cal-ID from onehashai/Cal-ID", 17.06.2026) |
| gofonia/callid | Codeberg | Zweitkopie/Backup — laut Codeberg-API ohne sichtbaren Inhalt (empty=true), Objektgröße ~1,2 GB, kein lokaler Checkout vorhanden |

*Begründung kanonisch: Die GitHub-Kopie ist die einzige lokal vollständig verifizierbare Version (633k LOC, 8.001 Dateien, kompletter Source-Baum); die Codeberg-Kopie liefert laut API keinen extrahierbaren Quellcode. Als Kanon gilt deshalb `livedialai/callid`. Ursprungsquellen der Serie: cal.com (upstream, öffentlich) sowie das private `onehashai/Cal-ID` (Quelle aller CHANGELOG-Einträge 2.0.0–2.3.0).*

## Sonstige Einzelrepos dieser Familie
Keine weiteren Repos in dieser Familie (nur die zwei Mirror-Kopien desselben Snapshot-Stands).

## Quellen & Methode
Geprüft: `git log`/`git remote` (1 Initial-Commit, Remote livedialai/callid), `package.json` (Monorepo-Skripte, u.a. `build:ai`, `dev:ai`), AGENTS.md (Root, apps/web, apps/connector, packages), CHANGELOG.md (Versionen 2.0.0–2.3.0 des Ursprungs-Repos `onehashai/Cal-ID`, 2026-04/05), README.md (unveränderte cal.com-Boilerplate), LICENSE (cal.com-Lizenzdatei, „Copyright (c) 2020-present Cal.com, Inc."), `.env.example` (RETELL_AI_KEY „Cal.ai Enterprise Voice AI Agents", OpenAI, Twilio), `packages/prisma/schema.prisma` (AIPhoneCallConfiguration u.a.), `packages/features/ee/cal-ai-phone/*` (Retell-AI-Service), `packages/app-store/retell-ai/DESCRIPTION.md`, `packages/features/eventtypes/components/tabs/ai/*`, `apps/worker/src/processors+workers`, `packages/job-engine/src/data-sync`, `apps/api` (v1/v2), `apps/connector/src/mcp`, GitHub-API (public, kein Fork-Flag, 245 MB) und Codeberg-API (gofonia/callid: empty=true).

---

# FastCab Taxi-Vermittlung

**Kategorie:** Eigenentwicklung
**Plattform(en):** Codeberg gofonia
**Kanonisches Repository:** fastcap

## fastcap — Vermittlungsplattform für Mietwagenfahrten
**Standort:** Codeberg: https://codeberg.org/gofonia/fastcap · **Typ:** Eigenentwicklung
**Sprachen:** JavaScript (1684), PHP (696), HTML (452), Markdown (183) · **Codeumfang:** 3258 LOC, 37 Dateien · **Letzter Push:** 2026-09-02 (angelegt 2026-08-31)

**Funktionsumfang:**
- **Fahrgast-Portal (PWA, fastcab.eu):** Statisches Frontend (`frontend/passenger/`) mit Google-Places-Autocomplete für Abhol- und Zieladresse (inkl. optionalem „Name/Location"-Feld), Preisquote (5 Min gültig), Buchung wahlweise **bar** (nur registrierte Kunden) oder **Karte** (Mollie-/SumUp-Zahllink), Live-Fahrtstatus per Server-Sent-Events, Bewertungsabschluss; PWA-Features (Manifest, Service Worker, Icons) plus Rechtstexte (AGB, Impressum, Datenschutz) als eigene Seiten.
- **Fahrer-Portal (PWA, app.fastcab.eu):** Cockpit mit **Standort-Gate** (App bleibt gesperrt, bis die Position erfolgreich ans Backend übertragen wurde), Online-Schalter (Zustände OFF → AVAILABLE → IN_TRIP), Angebots-Karten (Von/Nach/Preis, annehmen/ablehnen), Fahrtstatus-Updates, Einnahmen-Ansicht und Standort-Frische-Anzeige (Kontroll-Übertragung bei zu altem Standort).
- **Dispatch/Matching:** Kandidatensuche über Haversine-Filter im konfigurierbaren Radius (Standard 25 km), **Frische-Regel** (nur verfügbare, freigeschaltete Fahrer mit Position < 5 Min alt — Guard gegen tote Handys), Top-5-Shortlist, ETA-Sortierung über Google-Distance-Matrix, Angebotsfenster mit Fallback-Runden (kein Fahrer → NO_DRIVER), **Stammfahrer-Priorität** (exklusives 20-s-Fenster für den bevorzugten Fahrer mit Fahrer-Code); beim Dispatch geht zusätzlich eine WhatsApp-Nachricht an alle Kandidaten (`notifyDriverOffer`) — Fahrer verpassen keine Fahrt ohne geöffnete App.
- **Trip-Engine:** Statusmaschine `REQUESTED → ACCEPTED → MONEY_COLLECTED → DRIVER_EN_ROUTE → ARRIVED → IN_PROGRESS → COMPLETED → RATING` mit Abbruch-Fällen (CANCELLED, PAYMENT_EXPIRED, REFUNDED); **Uber-Modell: Dispatch vor Zahlung** — Bezahlpflicht entsteht erst nach Fahrerannahme (15-Min-Fenster), dadurch kein Refund-Zyklus bei fehlendem Fahrer; Live-Zustand im RAM (Single-Prozess) mit Trip-Event-Audit und Reconciler beim PM2-Restart.
- **Preiskalkulation:** km-Staffel (kmTiers, live z. B. 3,10/2,70/2,11 €/km) + Grundpreis, × **0,93 Uber-Faktor** (≈7 % günstiger als UberDE, Fahrer bekommt 90 %); 5-Min-Quote, Fahrpreis-Snapshot eingefroren auf Quote/Fahrt.
- **Zahlung (Provider-Abstraktion):** Mollie oder SumUp per `.env` umschaltbar (`PAYMENT_PROVIDER`, identische normalisierte Rückgabe `paid/open/…` + `paymentUrl`); Webhook-Verifikation (Signatur, Idempotenz, Mollie sendet Form-Data → `express.urlencoded()`), Status-Poll als Fallback, Refund bei Abbruch; Dev-Endpunkt `/api/dev/pay-trip/:id` zum Testen.
- **Karten-Service:** Google-Maps-Client **nur server-seitig** im Worker (Geocoding, Places Autocomplete, Distance Matrix, Routes) mit LRU-Cache (Ziele 24 h, ETA-Quotes 60 s) — API-Key nie in den Frontends.
- **Auth:** Registrierung/Login per Telefon + Passwort (JWT, Access 24 h/Refresh 30 d), Fahrgast-Registrierung per E-Mail mit Magic-Link, Fahrer-Login per Username, Tenant- (Partner-)Login; Rollen passenger/driver/admin/tenant, Rate-Limiting pro IP+User.
- **Admin/Backoffice (admin.fastcab.eu, WordPress):** WP-Custom-Plugin mit Mini-Admin-Panel (Partner-Konzessionen prüfen/freigeben, Fahrer-Liste, Fahrtenübersicht, Tarif-Einstellungen), REST-Endpoints für Worker und Admin; **Automationen:** DSGVO-Cleanup (täglich 03:00, Löschung der Klingel-Einträge nach 30 Tagen), Wochenreport per Brevo-Mail, monatlicher Auszahlungs-Export als CSV (X-Admin-Token).
- **SaaS-Modell (Partnerbetriebe):** Tenant-Konten (Signup/Login/Freigabe), Fahrerverwaltung pro Partner, Konzessions-Paywall (kein Matching ohne gültige Konzession + bezahltes Abo), Einnahmemodell 10 % Vermittlungsgebühr + 29-€/Fahrer/Monat-Floor, Fahrer-Code zur Kunden-Attribution.
- **Benachrichtigungen:** Brevo-Transaktions-E-Mails zu allen Trip-Events (bei ACCEPTED mit 💳-Bezahl-Link), Waxum-WhatsApp an Fahrer (Dispatch-Angebote + WA-Fahrer-Cockpit: Ziffern-Menü 0–9, Klingel bei Dispatch, signierte `/wa/d/`-Links, Standort-Update per signiertem Link).
- **Telefonbestellung (VoiceStack-Integration):** `voice-tools/` mit Agenten-Prompt + `tools.json` (Tools `fastcab_preis`, `fastcab_bestellung` für Bar-Trips per Anruf) und Voice-Endpunkten `/api/voice/quote|order|trip/:id` (X-Voice-Token).
- **Betrieb:** PM2-Prozess (`ecosystem.config.js`), ein Server (Apache + PHP-FPM + MariaDB, Let's Encrypt, vhosts für fastcab.eu/app/api/admin), statisches Frontend-Deployment per rsync, `backup.sh` (MariaDB-Dump + `.env`, 14-Tage-Rotation, 600-Rechte); `voice-tools` — Anrufbestellung. Multi-Channel-Ausbau (Telegram/WhatsApp-Bots) laut README für „Woche 2+" geplant.

**Aufbau/Module (aus Verzeichnisstruktur):**
- `worker/src/server.js` (743 Z.) — Express-API-Gateway mit allen Routen (Auth, Passenger, Trips, Driver, Payment-Webhooks, WA-Inbound, Voice, SSE, Admin-Exporte)
- `worker/src/auth.js` — JWT, Passwort-Hash, Magic-Link, Registrierungen (passenger/driver/tenant)
- `worker/src/matching.js` — Haversine-Kandidatensuche, Frische-Regel, Shortlist
- `worker/src/trip.js` — Statusmaschine, Preisformel, Stammfahrer-Matching, Live-Zustand, Event-Audit
- `worker/src/maps.js` — Google-Maps-Client (`worker/src/mollie.js` — Zahlungs-Provider-Abstraktion Mollie/SumUp)
- `worker/src/wp.js` — REST-Client zum WordPress-Backend; `worker/src/notify.js` + `email.js` — Waxum-WhatsApp bzw. Brevo-E-Mail
- `worker/src/events.js` — SSE; `worker/src/admin.js` — Automations (DSGVO-Cleanup, Wochenreport, Payouts-CSV); `worker/src/config.js` — Env-Konfiguration
- `frontend/passenger/` (app.js 280 Z., index.html, AGB/Impressum/Datenschutz), `frontend/driver/` (app.js 243 Z., index.html), `frontend/shared/` (Manifest, Service Worker, Icons, style.css)
- `wp-plugin/fastcab/` — `fastcab.php` (Plugin-Kopf, Schema-Installation, Default-Tarife), `includes/schema.php` (7 Custom-Tables: fc_users, fc_tenants, fc_drivers, fc_trips, fc_payments, fc_quotes, fc_trip_events), `includes/rest.php` (695 Z. REST-Endpoints), `includes/admin.php` (Mini-Admin-Panel: Partner/Fahrer/Fahrten/Tarife)
- `voice-tools/` — VoiceStack-Agenten-Prompt + Tool-Beschreibungen; `backup.sh` — Backup- und Rotationsskript

**Dubletten / Versionsreihe dieser Familie:**
| Repo | Plattform | Einschätzung |
|---|---|---|
| — | — | Keine Dubletten: nur ein Repository in dieser Familie |

## Quellen & Methode
Geprüft: README.md (Architektur, Statusmaschine, Preismodell, SaaS-Entscheidungen), `worker/src/*.js` (alle 12 Module: server.js, auth.js, matching.js, trip.js, maps.js, mollie.js, notify.js, email.js, events.js, wp.js, admin.js, config.js), `worker/package.json`, `worker/ecosystem.config.js`, `worker/.env.example`, `frontend/passenger/app.js`, `frontend/driver/app.js`, `frontend/shared/manifest.json`, `wp-plugin/fastcab/` (fastcab.php, includes/schema.php, includes/rest.php, includes/admin.php), `voice-tools/PROMPT.md` + `tools.json`, `backup.sh`, Git-Log (letzter Commit 2026-09-02). LOC-Zahlen aus loc_results.json (3258 LOC, 37 Dateien).

---


## Anhang A: Weitere Forks externer OSS-Projekte (nicht im Detail analysiert)
Diese Repositories sind rein upstream-Gabelungen (Forks) externer Open-Source-Projekte ohne nennenswerte Eigenentwicklung:
- **Qwen3-TTS-Openai-Fastapi** — Qwen3-TTS API-Server (Fork)
- **TastyIgniter** — Restaurant-POS/Ordering (Fork; eigene Variante unter `tastyigniter`, siehe Sektion oben)
- **dialplane** — async SIP/RTP Engine (Fork; eigene Varianten: dialplane--6phasen, dialplane-pipecat)
- **livekit** — LiveKit untere Ebene (Fork als Dependency)
- **pipecat-asterisk** — Pipecat/Asterisk-Bridge (Fork)
- **rustpbx** — "A PBX written by rust" (Upstream-Fork; Eigenentwicklung: rustpbx_de, siehe Sektion)
- **rvoip** — 1,1-GB-Fork, reines Upstream/Asset-Archiv


## Anhang B: Plattform-Duplikate (identische Repos auf beiden Plattformen)
Repos, die auf GitHub UND Codeberg existieren (ggf. unterschiedlicher Stand):
-
 
*
*
x
a
i
*
*
 
—
 
G
i
t
H
u
b
 
(
0
 
M
B
,
 
p
u
s
h
e
d
 
)
 
·
 
C
o
d
e
b
e
r
g
 
(
0
 
M
B
)

## Anhang C: Websites außerhalb der Repo-Liste
- **pizzafamily.de** — WordPress/Polylang, 10 Sprachvarianten (de, en, fr, it, pl, ro, ru, tr, uk, ar), ~950 Seiten (per Sitemap verifiziert), komplett im letzten Monat aufgebaut; inkl. greviews (Google-Reviews-Slider, produktiv).
- **GoFonIA.de / gofonia-website** — Landing Page + Meetergo-Sovereignty-Scan-Proxy (siehe Sektion GoFonIA Web).
