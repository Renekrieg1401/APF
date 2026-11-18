# Handoff — APF·Monitor, 2026-11-18

## Nachtrag 2026-11-18 (1) — Genesis-Registrierung + kritischer API-Bug behoben + Umbau auf statisch
- **[ERLEDIGT]:** Repo `Renekrieg1401/APF` war unregistriert — geklont nach
  `Projekte/APF/`, `CLAUDE.md`+`handoff.md` neu angelegt, in Root-`CLAUDE.md`
  registriert (bewusst außerhalb der AERIS/IRIS-Digital-Produkthierarchie).
- **[ERLEDIGT] Kritischer Fund:** `callAPI()` sendete nie einen `x-api-key`-
  Header an `api.anthropic.com` — jeder "echte" Generierungsversuch schlug
  seit jeher mit 401 fehl, die App fiel bei jedem Aufruf auf hartcodierte
  Demo-Daten ("[Juni 2026]"-Inhalte) zurück, OHNE das je sichtbar als Demo
  zu kennzeichnen (`isDemo`-Flag wurde berechnet, aber nie gerendert).
- **[ERLEDIGT] Weitere Funde:** Zeitzonen-Inkonsistenz in `buildPrompt()`
  (nutzte Browser-Lokalzeit statt Berlin-Zeit für das Suchfenster — seit der
  Entfernung der API-Logik gegenstandslos); kaputtes Markup
  (`</body></body></body>` ohne `</html>`); toter Code einer unrelated
  ElevenLabs-Sprachausgabefunktion ("Jonas Berger Stimme") mit Domain-
  Tippfehler (`nekrieg1401` statt `renekrieg1401`) und einer verwaisten
  `.btn-tts`-CSS-Regel ohne zugehöriges Element.
- **[ERLEDIGT] René-Entscheidung "ohne Anthropic":** Nach Rückfrage zur
  Architektur (Server-Proxy für den API-Key wäre nötig gewesen, da ein
  API-Key nie clientseitig in einer öffentlichen Seite stehen darf) hat René
  entschieden: keine Anthropic-Anbindung, stattdessen eine statische,
  ehrlich gekennzeichnete Ausgabe. Komplette API-/Websuche-/Monats-
  Scheduling-/Archiv-Logik entfernt (`callAPI`, `extractJsonFromResponse`,
  `buildPrompt`, `generate`, `manualGenerate`, `checkAutoGenerate`,
  `scheduleNextGeneration`, `msUntilNextDue`, `getMonthlyDueUTC`,
  `getBerlinNow`, `isStaleEntry`, kompletter `localStorage`-Archiv-
  Mechanismus, `notify`/`dismissNotif`, Welcome/Loading/Error-UI).
  `buildDemoData` → `buildStaticEdition`, zeigt jetzt direkt und sichtbar
  "Statische, redaktionell kuratierte Ausgabe · Stand: Juni 2026 · keine
  automatisierte Live-Recherche, keine KI-Generierung".
- **[ERLEDIGT] Legal-Texte korrigiert:** AGB §2/§4 (API-Schlüssel-Klausel
  entfernt), Datenschutz §3/§4 (localStorage-API-Key-Abschnitt und
  kompletter Anthropic-Claude-API-Abschnitt entfernt), Haftungsausschluss
  §1/§3 (KI-generiert-Behauptung entfernt) — alle drei waren nach der
  Umstellung sachlich falsch (DSGVO-relevant, da die Erklärung die
  tatsächliche Datenverarbeitung widerspiegeln muss). Paragraphen-Nummerierung
  durchgehend angepasst.
- **[ERLEDIGT] Verifikation:** Playwright-Lauf lokal — 0 Konsolenfehler,
  Journal rendert korrekt (9 Tiles), Sidebar-Toggle funktioniert, Legal-Modal
  öffnet korrekt. Vorher/Nachher nicht per Screenshot dokumentiert (kein
  Nutzer-sichtbarer UI-Vergleich angefordert, nur Bug-Fix).
- **[ERLEDIGT] Deploy auf Renés vServer:** `/var/www/apf-monitor/` (neu
  angelegt), nginx-Vhost-Backup vor Änderung
  (`aeris.bak-<timestamp>-apfadd`), neue `location /apf-monitor/`-Blöcke in
  beiden Server-Blöcken (Port 80 + 443, analog `aeris-learn`-Muster —
  `auth_basic off`, öffentliches Infoangebot ohne Personendaten). Live
  verifiziert: `http://212.132.117.130/apf-monitor/` UND
  `https://212.132.117.130/apf-monitor/` (self-signed), beide byte-identisch
  zur lokalen Datei.
- **[ERLEDIGT]:** Commit `e9a9f00`, gepusht nach `origin/main`
  (`Renekrieg1401/APF`). GitHub Pages ist laut René-Entscheidung nicht mehr
  die kanonische Live-Adresse — der vServer ist es jetzt.
- **[OFFEN]:** Kein `testing-qa`-Agentenlauf (Score ~5/10, direkt von Claude
  per Playwright verifiziert). Impressum/AGB/Datenschutz enthalten weiterhin
  unausgefüllte `[Platzhalter]` (vorbestehend, nicht Teil dieses Auftrags).
  AGB/Datenschutz-"Stand"-Datumsangabe (Januar 2025) nicht aktualisiert (reine
  Kosmetik, keine inhaltliche Falschaussage mehr nach der Bereinigung).
  Kein eigener Domainname für den vServer-Pfad (nur IP-Adresse, self-signed
  Zertifikat auf 443) — falls René das öffentlich bewerben will, wäre ein
  echtes Zertifikat/eine Domain ein sinnvoller nächster Schritt.
- **[KORREKTUR-POTENZIAL]:** Die kuratierten Inhalte selbst (Rechtsstand
  Juni 2026, DNQP/RKI/AWMF-Zitate) wurden inhaltlich NICHT neu geprüft — nur
  die Kennzeichnung als "statisch, Stand Juni 2026" ergänzt. Vor
  Praxiseinsatz mit echten Lesern: fachliche Aktualität gegenprüfen, da die
  Inhalte nicht mehr automatisch aktualisiert werden.

## Separat besprochen, noch nicht begonnen
René hat im Anschluss gefragt, wie die PWA ohne 30%-Store-Provision
monetarisiert werden kann, und danach direkt beauftragt, die PWA für
Google/Apple Store (natives Wrapping) + 3,75€-Monatsabo vorzubereiten. Das
steht in direktem Spannungsverhältnis zur gerade getroffenen Entscheidung
"statische Inhalte ohne Live-Aktualisierung" (Abo-Rechtfertigung bei
eingefrorenem Inhalt unklar) und zur ursprünglichen 30%-Vermeidungsfrage
(Store+IAP bedeutet in der Regel genau die Provision, die vermieden werden
sollte) — Rückfrage an René lief parallel zum Bugfix, Antwort/Entscheidung
steht zum Zeitpunkt dieses Handoffs noch aus.
