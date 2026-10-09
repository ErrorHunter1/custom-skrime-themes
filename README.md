# Skrime Themes

Browser-Extension (Manifest V3) für **skrime.eu**:

- **Themes:** 8 Vorlagen, Live-Editor, eigenes CSS und Import per URL (Theme-API).
- **Dashboard-Gruppen:** eigene Gruppen für deine Produkte, Drag & Drop sowie Listen-, Kompakt- und Kachelansicht, dazu eigene Namen, Tags und Notizen.

## Installation

**ZIP Herunterladen und entpacken

**Chrome / Edge / Brave / Opera:** `chrome://extensions` → Entwicklermodus an → „Entpackte Erweiterung laden“ → diesen Ordner wählen.

**Firefox (≥ 121):** `about:debugging#/runtime/this-firefox` → „Temporäres Add-on laden“ → `manifest.json` wählen.

Danach skrime.eu-Tabs neu laden.

## Bedienung

- **Popup** (Toolbar-Icon): Theme anklicken = sofort aktiv; Schalter oben = Original-Design.
- **Editor** („Theme bearbeiten“): 12 Farbrollen, Schriftart (Bunny Fonts), eigenes CSS. Solange der Editor offen ist, sehen offene skrime-Tabs die Änderungen **live**; beim Schließen gilt wieder das gespeicherte Theme.
- **Import & Theme-API**: Theme per URL, Datei oder JSON laden. Abonnierte URLs werden alle 6 Stunden aktualisiert.

## Dashboard-Gruppen

Auf `skrime.eu/dashboard` ersetzt die Extension die Tabs „Server / TeamSpeak / Web & SSL / Service“ durch eine eigene Produktübersicht. Beim ersten Besuch werden die Produkte automatisch nach Typ gruppiert.

| Funktion | So geht's |
|---|---|
| Gruppe anlegen | **+ Gruppe**: Name, Farbe, Icon und optional „neue Produkte automatisch hier einsortieren“ |
| Produkte verschieben | per **Drag & Drop**, auch innerhalb einer Gruppe; alternativ über ⋮ → „Verschieben nach“ |
| Gruppen sortieren | Gruppe am Griff ⠿ ziehen oder über ⋯ → „Nach oben/unten“ |
| Umbenennen | Doppelklick auf den Gruppennamen |
| Produkt anpassen | ✎: Anzeigename, Tags, Notiz, Farbmarkierung, Gruppe, ausblenden |
| Ansicht | Liste, Kompakt oder Kacheln; Sortierung nach Name, Ablaufdatum, Preis, Status oder Typ |
| Filtern & Suchen | Typ-Chips und Suche über Name, Domain, Tarif, Tags, Notizen und ID (ab 6 Zeichen) |
| Spalten & Optionen | ⚙-Menü: Spalten ein- oder ausblenden, gelöschte und ausgeblendete Produkte anzeigen, Export/Import, Original-Ansicht |

Weitere Details:
- **Status:** Live-Status (online/offline) und Tarifname übernimmt die Extension aus skrimes eigener, ausgeblendeter Ansicht. skrime aktualisiert sie alle 5 Sekunden.
- **Ablaufdatum:** Ab 14 Tagen vor Ablauf wird es orange, ab 7 Tagen rot markiert.
- **Monatskosten:** Sie werden pro Gruppe und insgesamt summiert.
- **Datenquelle:** Die Produkte kommen aus `https://skrime.eu/api/product/all`, mit deiner Login-Session und ohne API-Key. Zugangsdaten, die die API mitliefert (z. B. Root-Passwörter), werden sofort verworfen und nie gespeichert.
- **Speicherort:** Gruppen, Reihenfolge, Namen, Tags und Notizen liegen nur lokal im Browser (`storage.local`). Bei skrime ändert sich nichts.
- **Abschalten:** Im Editor unter *Einstellungen → Dashboard-Gruppen* oder im ⚙-Menü über „Original-Ansicht von skrime“.

## Wie es funktioniert

skrime.eu hat die meisten Farben fest im CSS (z. B. `#2f55d4` über 110-mal). Das Content-Script lädt deshalb jedes skrime-Stylesheet, schreibt jede Farbe auf eine Variable um (`#2f55d4` → `rgb(var(--skt-2f55d4, 47, 85, 212))`) und ersetzt damit das Original. Das Ergebnis wird gecacht. Ein Theme setzt dann nur noch die Variablen. Jede Originalfarbe wird ihrer nächsten Farbrolle zugeordnet und in OKLCH mit verschoben, so werden auch Hover-Töne, Verläufe und Schatten stimmig umgefärbt.

## Theme-Format / Theme-API

Eine „Theme-API“ ist einfach eine URL, die JSON liefert, z. B. eine Datei auf GitHub (raw), ein Gist oder ein eigener Endpoint. Akzeptiert werden ein einzelnes Theme, ein Array oder `{ "name": "...", "themes": [...] }`. Ein Beispiel liegt in [examples/themes.json](examples/themes.json).

```json
{
  "id": "ocean",
  "name": "Ocean",
  "author": "du",
  "colors": { "primary": "#0e7490", "background": "#071318" },
  "font": "Inter",
  "tintImages": true,
  "css": ".navbar { backdrop-filter: blur(8px); }"
}
```

| Rolle | Original | Verwendung |
|---|---|---|
| `primary` | `#2f55d4` | Buttons, Links, Akzente |
| `accent` | `#614092` | zweite Farbe im Hero-Verlauf |
| `background` | `#15171c` | Seitenhintergrund, Footer |
| `surface` | `#1a1c21` | Karten, Flächen |
| `border` | `#2b2f36` | Rahmen, Trennlinien |
| `text` | `#f8f9fa` | Fließtext |
| `muted` | `#9bacc4` | gedämpfter Text |
| `secondary` | `#ced4da` | Sekundär-Elemente |
| `success` / `danger` / `warning` / `info` | `#2eca8b` / `#e43f52` / `#f17425` / `#17a2b8` | Status |

Fehlende Rollen behalten den Originalwert.

## Sicherheit

Eigenes CSS aus fremden Quellen (URL, Datei, JSON) mit `url()`, `@import` oder `image-set()` wird **zurückgehalten**, bis du es im Editor freigibst. Solches CSS könnte sonst Daten aus dem eingeloggten Kundenbereich an fremde Server senden. Ändert eine abonnierte Quelle ihr CSS, ist eine erneute Freigabe nötig.

## Grenzen

- skrime liefert nur ein Dark-Theme. Helle Themes funktionieren nur eingeschränkt, weil Überschriften und Button-Texte fest weiß sind.
- Bilder werden nur per `hue-rotate` angenähert (Option „Illustrationen anpassen“).
- Die Hintergrundbilder der Original-Produktkarten von skrime sind Bilder und werden nicht umgefärbt. Die Gruppenansicht nutzt sie nicht.
- Die Dashboard-Gruppen hängen von der skrime-API und den IDs `#pills-tab` und `#pills-tabContent` ab. Ändert skrime das, erscheint wieder die Original-Ansicht.
