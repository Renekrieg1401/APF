# APF·Monitor — Silo-Governance

**Nachtrag-Registrierung 2026-11-18** (unregistriertes Repo gefunden/importiert,
analog PflegeBox/Arcus/Arbeits-Zeitnachweis-Präzedenzfall).

## Was ist das
Rein clientseitige, statische HTML-Anwendung ("APF Monitor — Ambulante Pflege
Fachjournal") — eine redaktionell kuratierte Fachübersicht für die ambulante
Pflege. **Kein IRIS-Digital-/AERIS-Produkt**, bewusst außerhalb der
Produkthierarchie — Renés privates Projekt.

## Architektur (Stand 2026-11-18, nach Umbau)
- Ein einziges `index.html`, kein Backend, keine externe API-Anbindung.
- Zeigt eine feste, redaktionell kuratierte Ausgabe (`buildStaticEdition()`),
  ehrlich gekennzeichnet als "Statische ... Ausgabe · Stand: Juni 2026 · keine
  automatisierte Live-Recherche, keine KI-Generierung".
- Live-Deployment: `http://212.132.117.130/apf-monitor/` bzw. `https://` (self-
  signed, IP-basiert — kein eigener Domainname). Nginx `alias /var/www/apf-
  monitor/`, `auth_basic off` (keine Personendaten, öffentliches Infoangebot).
  Deploy via `scp index.html root@212.132.117.130:/var/www/apf-monitor/`.
- GitHub-Repo `Renekrieg1401/APF` bleibt Quellcode-Verwaltung; GitHub Pages
  war früher die Live-Adresse, ist es laut René-Entscheidung 2026-11-18 nicht
  mehr — der vServer ist jetzt die kanonische Live-Instanz.

## Ursprüngliche Architektur (VERWORFEN, 2026-11-18 René-Direktive "ohne Anthropic")
Ursprünglich sollte die App monatlich automatisch per Anthropic-Claude-API
(mit Web-Search-Tool) ein "Evidenz-Protokoll" recherchieren und generieren,
in `localStorage` archivieren. **Kritischer Fund:** `callAPI()` sendete nie
einen `x-api-key`-Header — jeder Generierungsversuch schlug seit jeher mit
401 fehl, die App fiel bei JEDEM Aufruf auf hartcodierte Demo-Daten zurück,
ohne das sichtbar zu kennzeichnen. René hat sich explizit gegen eine
Anthropic-Anbindung entschieden (auch ein korrekt funktionierender Key hätte
serverseitig gehalten werden müssen — ein API-Key darf nie clientseitig in
einer öffentlichen Seite stehen). Die komplette API-/Websuche-/Scheduling-/
Archiv-Logik wurde entfernt, s. `handoff.md`.

## Wichtig bei jeder künftigen Änderung
- Kein Anthropic/keine externe KI-API — explizite, dauerhafte René-Direktive.
- Jede neue "Ausgabe" ist eine manuelle, redaktionelle Content-Änderung in
  `buildStaticEdition()` (kein automatisierter Regenerierungs-Mechanismus) —
  das `stand`-Feld muss bei jeder inhaltlichen Aktualisierung mitgezogen werden.
- Vor Live-Push: lokal per Playwright verifizieren (0 Konsolenfehler), dann
  auf den vServer deployen — kein GitHub-Pages-Auto-Deploy mehr relevant.
