# [Supabase_Live_Ticker_3.2](https://stuhrmanntheo-design.github.io/Supabase_Live_Ticker_3.2/)

**Spendenlauf Live-Tracker**

Echtzeit-Rundenerfassung und Live-Dashboard für Spendenläufe. Stempelstationen tragen Runden ein – das Dashboard zeigt die Rangliste automatisch aktualisiert an.

## Live-Seiten

| Seite | Zweck | Link |
|---|---|---|
| Eingabe | Stempelstation für Helfer | [eingabe.html](https://stuhrmanntheo-design.github.io/Supabase_Live_Ticker_3.2/eingabe.html) |
| Dashboard | Übersicht für Beamer/Infostand | [dashboard.html](https://stuhrmanntheo-design.github.io/Supabase_Live_Ticker_3.2/dashboard.html) |
| Ultra-Dashboard | Erweiterte Live-Ansicht | [ultra-dashboard.html](https://stuhrmanntheo-design.github.io/Supabase_Live_Ticker_3.2/ultra-dashboard.html) |

## Dateien

**eingabe.html – Stempelstation**
Läufer-Startnummer per QR-Code-Scan oder manuell eingeben, Runde wird sofort in die Datenbank geschrieben. Optimiert für schnelle Eingabe: Das Feld leert sich sofort, die nächste Nummer kann bereits eingetippt werden, während die vorherige noch gespeichert wird.

**dashboard.html – Live-Übersicht**
Zeigt die Top-Läufer und Kennzahlen der Veranstaltung. Aktualisiert sich automatisch per Polling, ohne dass die Seite manuell neu geladen werden muss.

**ultra-dashboard.html – Erweiterte Live-Ansicht**
Zusätzliche Statistiken und Rekordrunden. Ebenfalls automatisch aktualisiert per Polling.

Alle Seiten zeigen ausschließlich Startnummern und Klassen an, keine Schülernamen.

## Technik

- Reines HTML/CSS/JavaScript – kein Build-Tool, kein Framework
- [Supabase](https://supabase.com/) als Datenbank-Backend
- Hosting über GitHub Pages

## Setup

### 1. Supabase-Projekt anlegen

Tabelle `runden` für die anonyme Rundenerfassung:

```sql
CREATE TABLE runden (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  startnummer integer NOT NULL,
  eingetragen_am timestamptz DEFAULT now()
);

ALTER TABLE runden ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Jeder darf eintragen"
ON runden FOR INSERT TO anon
WITH CHECK (true);

CREATE POLICY "Jeder darf lesen"
ON runden FOR SELECT TO anon
USING (true);
```

Tabelle `klassenbloecke` zur Zuordnung von Startnummern-Bereichen zu Klassen, ebenfalls ohne Personenbezug:

```sql
CREATE TABLE klassenbloecke (
  klasse text PRIMARY KEY,
  start_nr integer NOT NULL,
  end_nr integer NOT NULL
);

ALTER TABLE klassenbloecke ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Jeder darf lesen"
ON klassenbloecke FOR SELECT TO anon
USING (true);
```

Anschließend die tatsächlichen Nummernbereiche pro Klasse einfügen.

### 2. Zugangsdaten eintragen

In allen HTML-Dateien die Variablen oben im Script-Block anpassen:

```js
const SUPABASE_URL = "https://dein-projekt.supabase.co";
const SUPABASE_KEY = "dein-anon-key";
```

### 3. Auf GitHub Pages deployen

Repository auf Public stellen, Dateien hochladen, unter Settings → Pages den Branch `main` als Quelle auswählen.

## Nutzung am Veranstaltungstag

- `eingabe.html` auf jedem Stempel-Gerät öffnen (Handy, Tablet)
- `dashboard.html` oder `ultra-dashboard.html` auf dem Beamer oder Bildschirm am Infostand öffnen
- Empfehlung: Supabase-Projekt ein paar Tage vorher einmal aufrufen, damit es nicht pausiert ist

## Datenschutz

Aus Datenschutzgründen enthält keine der Supabase-Tabellen Namen oder andere personenbezogene Daten. Die Zuordnung von Startnummern zu Schülernamen erfolgt ausschließlich lokal auf den jeweiligen Geräten und wird nie an Supabase übertragen.
