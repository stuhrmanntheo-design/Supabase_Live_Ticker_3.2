# Spendenlauf Live-Tracker

Echtzeit-Rundenerfassung und Live-Leaderboard für Spendenläufe.  
Stempelstationen tragen Runden ein – der Ticker zeigt die Rangliste sofort aktualisiert an.

## Dateien

**eingabe.html** – Stempelstation 
Mit QR Code Rundennummer Scannen.
Läufer-Startnummer eingeben, Runde wird sofort in die Datenbank geschrieben.  
Optimiert für schnelle Eingabe: Das Feld leert sich sofort, die nächste Nummer kann bereits eingetippt werden während die vorherige noch gespeichert wird.

**ticker.html** – Live-Leaderboard  
Zeigt die Top 10 Läufer nach Rundenanzahl. Aktualisiert sich automatisch in Echtzeit sobald eine neue Runde eingetragen wird – kein manuelles Neuladen nötig.

## Technik

- Reines HTML/CSS/JavaScript – kein Build-Tool, kein Framework
- [Supabase](https://supabase.com) als Datenbank-Backend mit Realtime-Subscriptions
- Hosting über GitHub Pages

## Setup

### 1. Supabase-Projekt anlegen

Tabelle `runden` mit folgendem SQL erstellen:

```sql
CREATE TABLE runden (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  startnummer integer NOT NULL,
  eingetragen_am timestamptz DEFAULT now()
);
```

Sicherheitsregeln setzen:

```sql
ALTER TABLE runden ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Jeder darf eintragen"
ON runden FOR INSERT TO anon
WITH CHECK (true);

CREATE POLICY "Jeder darf lesen"
ON runden FOR SELECT TO anon
USING (true);
```

Unter **Database → Replication** die Tabelle `runden` für Realtime aktivieren.

### 2. Zugangsdaten eintragen

In beiden HTML-Dateien die Variablen oben im Script-Block anpassen:

```js
const SUPABASE_URL = "https://dein-projekt.supabase.co";
const SUPABASE_KEY = "dein-anon-key";
```

### 3. Auf GitHub Pages deployen

Repository auf Public stellen, Dateien hochladen, unter **Settings → Pages** den Branch `main` als Quelle auswählen.

## Nutzung am Veranstaltungstag

- `eingabe.html` auf jedem Stempel-Gerät öffnen (Handy, Tablet)
- `ticker.html` auf dem Beamer oder Bildschirm öffnen
- Empfehlung: Supabase-Projekt ein paar Tage vorher einmal aufrufen, damit es nicht pausiert ist
