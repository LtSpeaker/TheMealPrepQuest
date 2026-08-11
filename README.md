# TheMealPrepQuest

Single-Page-Web-App zur Rezept-Inspiration fürs Meal Prep. Reines HTML/CSS/JS, kein Build-Step, gehostet über GitHub Pages als PWA.

**Live:** https://ltspeaker.github.io/TheMealPrepQuest/

## Was die App macht

1. **Fragebogen** – Kategorie(n) (Fleisch/Fisch/Vegetarisch, Mehrfachauswahl), Zeit pro Kochsession, Anzahl Tage, Anzahl Personen, optionale Filter (Low Carb, High Protein, Kalorienarm, Schnell).
2. **Ergebnisse** – zufällige Auswahl aus der Rezept-Datenbank (60 Rezepte, 20 pro Kategorie), passend zur gewählten Kombination. Zutaten werden automatisch auf die Personenzahl skaliert, Zubereitung ist pro Karte ausklappbar. Über "Andere Vorschläge" lässt sich neu würfeln. Jede Karte hat Like/Dislike-Buttons: Gelikte Rezepte landen in einer eigenen "Favoriten"-Ansicht, disliked Rezepte werden sofort gegen ein anderes ausgetauscht und tauchen künftig nicht mehr auf. Portionen lassen sich pro Rezept zum Wochenplan hinzufügen.
3. **Einkaufsliste** – aggregiert die Zutaten über alle geplanten Rezepte/Portionen. Export als `.ics`-Datei (für Apple Erinnerungen/Kalender-Import) oder als Text zum Kopieren.
4. **Kochliste** – zeigt alle geplanten Gerichte mit vollständigen Zutaten und Zubereitungsschritten als abhakbare Checkliste. Bleibt gespeichert, bis der Wochenplan geleert wird – gedacht für die Zeit zwischen Einkaufen und tatsächlichem Kochen.

Der Wochenplan (geplante Gerichte, Personenzahl, Farbmodus, abgehakte Zubereitungsschritte) wird in `localStorage` gespeichert und übersteht damit einen Neustart der App. Das ist rein geräte-/browserlokal – es gibt kein Backend und keine Synchronisierung zwischen Geräten.

## Dateien

| Datei | Inhalt |
|---|---|
| `index.html` | Struktur, Styles (CSS-Variablen fürs Farbschema), komplette App-Logik |
| `recipes-data.js` | Rezept-Datenbank als ES-Modul (`export const RECIPES = [...]`) |
| `manifest.json` | PWA-Manifest |
| `icon.svg` | Favicon / Browser-Tab-Icon (abgerundet) |
| `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | Homescreen-Icons (randlos-quadratisch, iOS/Android runden selbst) |

Kein Build-Step, keine Abhängigkeiten – `index.html` importiert `recipes-data.js` per natives ES-Modul (`<script type="module">`).

## Rezept-Datenmodell

Jeder Eintrag in `recipes-data.js`:

```js
{
  id: "hf1", category: "fleisch", title: "…",
  time: 30, freezer: true, tags: ["protein", "schnell"],
  nutrition: { kcal: 520, protein: 42, carbs: 55, fat: 14 }, // pro Portion – aktuell nur Datenbasis, nicht in der UI angezeigt
  ingredients: [ { name: "…", amount: 150, unit: "g" } ],    // amount = PRO PERSON PRO PORTION
  steps: [ "…" ],
}
```

`tags` sind vollständig manuell gepflegt (`lowcarb`, `protein`, `kalorienarm`, `schnell`) – es gibt keine automatische Ableitung, z. B. muss `schnell` bei `time <= 30` explizit gesetzt werden.

## Design

- Helles/dunkles Farbschema, manuell umschaltbar über den Button oben rechts (persistiert, Standard folgt der Systemeinstellung beim ersten Besuch)
- Schriften: Fredoka (Headlines), Plus Jakarta Sans (Fließtext), von Google Fonts geladen
- Kategorien und Filter-Tags sind farblich codiert (Definitionen am Anfang des Scripts in `index.html`: `CAT_DEFS`, `FILTER_DEFS`)

## Lokal entwickeln

Kein Build nötig, aber ein lokaler Webserver, da der dynamische Modul-Import nicht über `file://` funktioniert:

```
python3 -m http.server 8000
```

dann `http://localhost:8000` im Browser öffnen.

## Offene Ideen (nicht verbindlich)

- Echte Web-Rezeptsuche/Live-Rezeptquelle – würde ein Backend bzw. einen Serverless-Ansatz erfordern, da GitHub Pages nur statische Inhalte ausliefert
- Weitere Rezepte
- Nährwerte optional in der UI anzeigen (Datenfelder sind bereits vorhanden)
