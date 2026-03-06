# Kapitel 2: Der Spielleiter

← [Zurück: Einführung](01_einfuehrung.md) | [Weiter: Spieler →](03_spieler.md)

---

## 🎯 Lernziele dieses Kapitels

- Du kannst Variablen anlegen und Werte speichern
- Du baust ein Programm mit einem echten **Spielzustand** (läuft gerade eine Runde?)
- Du sendest strukturierte Daten per Funk
- Du empfängst Funknachrichten und reagierst darauf
- Du verwendest Zufallszahlen, um das Spiel abwechslungsreich zu machen

---

## 2.1 Planung vor dem Programmieren

Gute Programme entstehen nicht sofort – zuerst wird **geplant**. Wir nutzen dazu ein einfaches **Zustandsdiagramm**:

```
         [Start]
            │
            ▼
    ┌───────────────┐
    │ Warten auf    │◄──────────────────────┐
    │ Taste A       │                       │
    └───────┬───────┘                       │
            │ Taste A gedrückt              │
            ▼                              │
    ┌───────────────┐                       │
    │ Zufällige     │                       │
    │ Aufgabe       │                       │
    │ auswählen     │                       │
    └───────┬───────┘                       │
            │                               │
            ▼                               │
    ┌───────────────┐                       │
    │ Signal        │                       │
    │ anzeigen &    │                       │
    │ per Funk      │                       │
    │ senden        │                       │
    └───────┬───────┘                       │
            │                               │
            ▼                               │
    ┌───────────────┐     Runde gewonnen    │
    │ Auf Antworten │──────────────────────►│
    │ der Spieler   │                       │
    │ warten (5 s)  │                       │
    └───────┬───────┘                       │
            │ Zeit abgelaufen               │
            ▼                               │
    ┌───────────────┐                       │
    │ Kein Gewinner │───────────────────────┘
    │ anzeigen      │
    └───────────────┘
```

### Benötigte Variablen

Überlege, welche Informationen der Spielleiter sich merken muss:

| Variable | Typ | Bedeutung |
|----------|-----|-----------|
| `punkteS1` | Zahl | Gesamtpunkte Spieler 1 |
| `punkteS2` | Zahl | Gesamtpunkte Spieler 2 |
| `punkteS3` | Zahl | Gesamtpunkte Spieler 3 |
| `punkteS4` | Zahl | Gesamtpunkte Spieler 4 |
| `aktuelleAufgabe` | Zahl (1–3) | Welches Signal gerade aktiv ist |
| `rundeAktiv` | Ja/Nein | Läuft gerade eine Runde? |
| `rundeGewonnen` | Ja/Nein | Hat jemand bereits gewonnen? |

> **💡 Warum brauchen wir `rundeAktiv` und `rundeGewonnen`?**  
> Ohne diese Flags würden eingehende Funknachrichten auch außerhalb einer Runde verarbeitet werden – das wäre ein **Bug**. Mit Flags können wir gezielt steuern, wann Nachrichten akzeptiert werden.

---

## 2.2 Variablen anlegen

### Schritt 1: Neue Variablen erstellen

1. Klicke auf die Kategorie **„Variablen"**
2. Klicke auf **„Erstelle eine Variable..."**
3. Erstelle folgende Variablen:
   - `punkteS1`
   - `punkteS2`
   - `punkteS3`
   - `punkteS4`
   - `aktuelleAufgabe`
   - `rundeAktiv`
   - `rundeGewonnen`

### Schritt 2: Startwerte setzen

Im `beim Start`-Block setzen wir alle Variablen auf ihre Anfangswerte:

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze punkteS1 auf [ 0 ]                               │
│  Setze punkteS2 auf [ 0 ]                               │
│  Setze punkteS3 auf [ 0 ]                               │
│  Setze punkteS4 auf [ 0 ]                               │
│                                                           │
│  Setze aktuelleAufgabe auf [ 0 ]                        │
│  Setze rundeAktiv auf [ falsch ]                        │
│  Setze rundeGewonnen auf [ falsch ]                     │
│                                                           │
│  Grundlagen: Zeige Symbol [ ✓ ]   (Startsignal)         │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du die Blöcke?**
- `Funk: Setze Gruppe auf` → Kategorie **Funk**
- `Setze ... auf` → Kategorie **Variablen**
- `falsch` (boolescher Wert) → Kategorie **Logik**
- `Zeige Symbol` → Kategorie **Grundlagen**

---

## 2.3 Eine Runde starten

Das Spiel soll starten, wenn **Taste A** gedrückt wird – aber nur, wenn gerade keine Runde läuft.

### Schritt 3: Taste-A-Block

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ rundeAktiv ] = [ falsch ]                │
│  dann:                                                    │
│      Runde starten (→ eigene Funktion, siehe unten)     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du die Blöcke?**
- `Wenn Knopf A gedrückt` → Kategorie **Eingabe**
- `Wenn ... dann` → Kategorie **Logik**
- Vergleich `= falsch` → Kategorie **Logik** → `... = ...` Block

> **💡 Über Funktionen:** Für komplexe Programmteile nutzen wir in MakeCode eigene **Funktionen**. Das ist wie ein eigener Block, den wir selbst benennen. Gehe zu **„Fortgeschritten"** → **„Funktionen"** → **„Erstelle eine Funktion"**.

### Schritt 4: Die Funktion `rundeStarten`

Erstelle eine neue Funktion mit dem Namen `rundeStarten`:

```
┌─ Funktion rundeStarten ──────────────────────────────────┐
│                                                           │
│  Setze rundeAktiv auf [ wahr ]                          │
│  Setze rundeGewonnen auf [ falsch ]                     │
│                                                           │
│  // Zufällige Aufgabe wählen (1, 2 oder 3)              │
│  Setze aktuelleAufgabe auf:                             │
│      [ wähle zufällig 1 bis 3 ]                         │
│                                                           │
│  // signal anzeigen (→ eigene Funktion)                  │
│  rufauf: signalAnzeigen                                 │
│                                                           │
│  // Signal per Funk senden                               │
│  Funk: Sende Zahl [ aktuelleAufgabe ]                   │
│                                                           │
│  // 5 Sekunden warten (Spieler können antworten)        │
│  Grundlagen: Pausiere [ 5000 ] ms                       │
│                                                           │
│  // Nach 5 Sekunden: Wurde gewonnen?                    │
│  Logik: Wenn [ rundeGewonnen ] = [ falsch ] dann:       │
│      Grundlagen: Zeige Zeichenkette [ "---" ]           │
│      LED: Setze RGB auf [ Farbe: schwarz/aus ]          │
│                                                           │
│  Setze rundeAktiv auf [ falsch ]                        │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `wähle zufällig ... bis ...` → Kategorie **Mathematik**
- `Pausiere ... ms` → Kategorie **Grundlagen**
- `rufauf: ...` → Kategorie **Fortgeschritten** → **Funktionen**

---

## 2.4 Signale anzeigen

### Schritt 5: Die Funktion `signalAnzeigen`

Erstelle eine weitere Funktion `signalAnzeigen`. Diese zeigt je nach `aktuelleAufgabe` das richtige Signal:

```
┌─ Funktion signalAnzeigen ────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ aktuelleAufgabe ] = [ 1 ] dann:          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  LED: Setze RGB auf [ Farbe: Grün ]               │  │
│  │  Grundlagen: Zeige LEDs (großes A-Muster):        │  │
│  │    ■ □ □ □ ■                                      │  │
│  │    ■ □ □ □ ■                                      │  │
│  │    ■ ■ ■ ■ ■                                      │  │
│  │    ■ □ □ □ ■                                      │  │
│  │    ■ □ □ □ ■                                      │  │
│  │  Musik: Spiele Ton [ A5 ] für [ 1 ] Schlag        │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
│  Sonst wenn [ aktuelleAufgabe ] = [ 2 ] dann:           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  LED: Setze RGB auf [ Farbe: Rot ]                │  │
│  │  Grundlagen: Zeige LEDs (großes B-Muster):        │  │
│  │    ■ ■ ■ □ □                                      │  │
│  │    ■ □ □ ■ □                                      │  │
│  │    ■ ■ ■ □ □                                      │  │
│  │    ■ □ □ ■ □                                      │  │
│  │    ■ ■ ■ □ □                                      │  │
│  │  Musik: Spiele Ton [ A3 ] für [ 1 ] Schlag        │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
│  Sonst (aktuelleAufgabe = 3):                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  LED: Setze RGB auf [ Farbe: Blau ]               │  │
│  │  Grundlagen: Zeige Symbol [ ♥ (Herz) ]            │  │
│  │  Musik: Spiele Ton [ A4 ] für [ 1 ] Schlag        │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Hinweis zu den Blocks:**
- `Setze RGB auf Farbe` → Kategorie **LED** (oder **Calliope**)
  - Grün: direkt als Farbauswahl oder RGB(0, 255, 0)
  - Rot: RGB(255, 0, 0)  
  - Blau: RGB(0, 0, 255)
- `Zeige LEDs` → Kategorie **Grundlagen** → eigenes Muster zeichnen (Klick auf Felder)
- `Spiele Ton` → Kategorie **Musik**
  - A5 = hoher Ton, A3 = tiefer Ton, A4 = mittlerer Ton

> **💡 Hinweis zu `Sonst wenn`:** In MakeCode klickst du auf das kleine **„+"** am `Wenn...dann`-Block, um `Sonst wenn`-Zweige hinzuzufügen.

---

## 2.5 Funk-Antworten empfangen

### Schritt 6: Den Funk-Empfänger einrichten

Wenn ein Spieler antwortet, sendet er eine Zahl (z. B. `21` = Spieler 2 hat A gedrückt). Wir müssen diese Zahl empfangen und auswerten.

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  // Nur verarbeiten wenn eine Runde läuft UND            │
│  // noch niemand gewonnen hat                            │
│  Wenn [ rundeAktiv ] = [ wahr ]                         │
│  UND  [ rundeGewonnen ] = [ falsch ]                    │
│  dann:                                                    │
│                                                           │
│      rufauf: antwortVerarbeiten(empfangeneZahl)          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Funk: Wenn Zahl empfangen wird` → Kategorie **Funk**
- `... UND ...` → Kategorie **Logik**
- Die Variable `empfangeneZahl` erscheint automatisch als lila Block

### Schritt 7: Die Funktion `antwortVerarbeiten`

Hiermit decodieren wir die empfangene Zahl:

```
┌─ Funktion antwortVerarbeiten mit Parameter: nachricht ───┐
│                                                           │
│  // Spieler-ID und Aktion aus der Zahl herausrechnen    │
│  Setze spielerID auf: [ ganzzahliger Anteil von ]       │
│                         [ nachricht / 10 ]               │
│                                                           │
│  Setze aktion auf: [ nachricht Teilen mit Rest 10 ]      │
│                    (= nachricht Modulo 10)               │
│                                                           │
│  // Gültigkeitsprüfung: Spieler-ID muss 1-4 sein        │
│  Wenn [ spielerID ] ≥ [ 1 ]                             │
│  UND  [ spielerID ] ≤ [ 4 ]                             │
│  dann:                                                    │
│                                                           │
│      // War die Aktion richtig?                         │
│      Wenn [ aktion ] = [ aktuelleAufgabe ] dann:        │
│      ┌─────────────────────────────────────────────┐    │
│      │  rufauf: richtigGeantwortet(spielerID)      │    │
│      └─────────────────────────────────────────────┘    │
│      Sonst:                                             │
│      ┌─────────────────────────────────────────────┐    │
│      │  rufauf: falschGeantwortet(spielerID)       │    │
│      └─────────────────────────────────────────────┘    │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Modulo und Division:** 
> - `ganzzahliger Anteil von A / B` → unter **Mathematik** → `A ÷ B` 
>   (stelle sicher, dass es die Ganzzahl-Division ist – in MakeCode gibt es diesen Block unter Mathematik)
> - `Rest von A / B` (= Modulo) → unter **Mathematik** → `Rest von A geteilt durch B`

**Beispiel zur Verdeutlichung:**
```
Empfangene Zahl: 21
  spielerID = ganzzahliger Anteil von 21 / 10 = 2   ✓ Spieler 2
  aktion    = Rest von 21 / 10             = 1   ✓ Taste A
```

---

## 2.6 Richtige und falsche Antworten behandeln

### Schritt 8: Funktion `richtigGeantwortet`

```
┌─ Funktion richtigGeantwortet mit Parameter: siegerID ────┐
│                                                           │
│  Setze rundeGewonnen auf [ wahr ]                       │
│                                                           │
│  // Punkte für den richtigen Spieler erhöhen            │
│  Wenn [ siegerID ] = [ 1 ] dann:                        │
│      Ändere punkteS1 um [ 1 ]                           │
│  Sonst wenn [ siegerID ] = [ 2 ] dann:                  │
│      Ändere punkteS2 um [ 1 ]                           │
│  Sonst wenn [ siegerID ] = [ 3 ] dann:                  │
│      Ändere punkteS3 um [ 1 ]                           │
│  Sonst:                                                  │
│      Ändere punkteS4 um [ 1 ]                           │
│                                                           │
│  // Gewinner anzeigen                                   │
│  LED: Setze RGB auf [ Farbe: Grün ]                     │
│  Grundlagen: Zeige Zeichenkette [ "S" ]                 │
│  Grundlagen: Zeige Zahl [ siegerID ]                    │
│  Grundlagen: Pausiere [ 1500 ] ms                       │
│                                                           │
│  // Gewinner per Funk melden (für Spieler-Calliopes)    │
│  Funk: Sende Wert [ "gewinner" ] = [ siegerID ]         │
│                                                           │
│  Grundlagen: Pausiere [ 1000 ] ms                       │
│  LED: Setze RGB auf [ Farbe: schwarz/aus ]              │
│  Grundlagen: Bildschirm löschen                         │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Ändere ... um ...` → Kategorie **Variablen**
- `Funk: Sende Wert` → Kategorie **Funk** → sendet Name+Zahl-Paar
- `Bildschirm löschen` → Kategorie **Grundlagen**

### Schritt 9: Funktion `falschGeantwortet`

```
┌─ Funktion falschGeantwortet mit Parameter: spielerID ────┐
│                                                           │
│  // Punkt abziehen                                      │
│  Wenn [ spielerID ] = [ 1 ] dann:                       │
│      Ändere punkteS1 um [ -1 ]                          │
│  Sonst wenn [ spielerID ] = [ 2 ] dann:                 │
│      Ändere punkteS2 um [ -1 ]                          │
│  Sonst wenn [ spielerID ] = [ 3 ] dann:                 │
│      Ändere punkteS3 um [ -1 ]                          │
│  Sonst:                                                  │
│      Ändere punkteS4 um [ -1 ]                          │
│                                                           │
│  // Spieler über Fehler informieren                     │
│  Funk: Sende Wert [ "falsch" ] = [ spielerID ]          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

## 2.7 Punktestand anzeigen

### Schritt 10: Taste B zeigt Punkte

Wenn Taste B gedrückt wird (und keine Runde läuft), zeigt der Spielleiter alle Punkte:

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ rundeAktiv ] = [ falsch ] dann:                 │
│                                                           │
│      Grundlagen: Zeige Zeichenkette [ "S1:" ]           │
│      Grundlagen: Zeige Zahl [ punkteS1 ]                │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│                                                           │
│      Grundlagen: Zeige Zeichenkette [ "S2:" ]           │
│      Grundlagen: Zeige Zahl [ punkteS2 ]                │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│                                                           │
│      Grundlagen: Zeige Zeichenkette [ "S3:" ]           │
│      Grundlagen: Zeige Zahl [ punkteS3 ]                │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│                                                           │
│      Grundlagen: Zeige Zeichenkette [ "S4:" ]           │
│      Grundlagen: Zeige Zahl [ punkteS4 ]                │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│                                                           │
│      Grundlagen: Bildschirm löschen                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

## 2.8 Das vollständige Spielleiter-Programm

Hier eine Übersicht aller Programmteile:

```
SPIELLEITER – Übersicht aller Blöcke

► beim Start
   → Gruppe 1 setzen, alle Variablen initialisieren, Haken-Symbol zeigen

► Wenn Knopf A gedrückt
   → Wenn keine Runde läuft: rundeStarten aufrufen

► Wenn Knopf B gedrückt
   → Wenn keine Runde läuft: alle Punkte anzeigen

► Funk: Wenn Zahl empfangen (empfangeneZahl)
   → Wenn Runde aktiv UND noch nicht gewonnen: antwortVerarbeiten aufrufen

► Funktion: rundeStarten
   → rundeAktiv = wahr, zufällige Aufgabe wählen, Signal anzeigen,
      per Funk senden, 5 Sek. warten, Runde beenden

► Funktion: signalAnzeigen
   → je nach aktuelleAufgabe: RGB + LEDs + Ton

► Funktion: antwortVerarbeiten(nachricht)
   → spielerID und aktion berechnen, richtig/falsch prüfen

► Funktion: richtigGeantwortet(siegerID)
   → rundeGewonnen = wahr, Punkte +1, Gewinner zeigen + per Funk melden

► Funktion: falschGeantwortet(spielerID)
   → Punkte -1, Fehler per Funk melden
```

---

## 2.9 Aufgaben

### Aufgabe 2.1: Grundprogramm aufbauen ⭐

Baue das Spielleiter-Programm in MakeCode Schritt für Schritt auf, indem du die Anleitung befolgst. Teste jeden Schritt im Simulator, bevor du weitermachst.

**Zwischentests:**
- [ ] Zeigt der Calliope beim Start ein Symbol?
- [ ] Wird beim Drücken von Taste A eine zufällige Aufgabe ausgewählt?
- [ ] Wird das Signal (Farbe + Ton + LEDs) korrekt angezeigt?
- [ ] Sendet der Calliope die Aufgabennummer per Funk? (Prüfe im Simulator: zweiter Calliope)

### Aufgabe 2.2: Decodierung nachvollziehen ⭐

Gegeben sei folgende empfangene Zahl: `34`

1. Berechne von Hand: Welche Spieler-ID steckt darin?
2. Berechne von Hand: Welche Aktion wurde ausgeführt?
3. War die Aktion richtig, wenn die aktuelle Aufgabe `3` war?

### Aufgabe 2.3: Fehleranalyse ⭐⭐

Dein Mitschüler hat folgenden Fehler im Programm: Er hat **vergessen**, `rundeGewonnen` auf `wahr` zu setzen, wenn jemand richtig geantwortet hat.

1. Was würde in diesem Fall passieren?
2. Könnte derselbe Spieler mehrere Punkte für eine Runde bekommen? Erkläre!
3. Wie würde das Spielverhalten sich verändern?

### Aufgabe 2.4: Punkteanzeige verbessern ⭐⭐

Die aktuelle Punkteanzeige zeigt Punkte immer nacheinander. Ändere das Programm so, dass **nur der Spielleiter-Calliope** die Punkte mit einem visuellen Muster darstellt:

- Überlege: Wie könnte man 4 Punktestände gleichzeitig auf einer 5×5-Matrix darstellen?
- Implementiere: Zeige für jeden Spieler eine **Spalte der LED-Matrix**, bei der die Anzahl der leuchtenden LEDs von unten dem Punkte-Stand entspricht (max. 5 LEDs pro Spalte).

*Tipp: Schau dir die Blöcke unter **„LED"** → `Setze LED (x, y) auf wahr/falsch` an.*

### Aufgabe 2.5: Zeitmessung ⭐⭐⭐

Erweitere das Programm um eine **Zeitmessung**: Der Spielleiter soll messen, wie viele Millisekunden zwischen dem Senden des Signals und dem Empfang der richtigen Antwort vergangen sind. Diese Zeit soll nach der Anzeige des Gewinners ebenfalls angezeigt werden.

*Hinweis: Schau dir den Block `Eingabe: Laufzeit (ms)` an. Er gibt die Zeit seit Start des Calliopes in Millisekunden zurück.*

---

## ✅ Zusammenfassung

Du hast den Spielleiter programmiert. Er kann:
- ✅ Eine Runde starten und stoppen
- ✅ Zufällige Signale auswählen und anzeigen
- ✅ Den Signal-Code per Funk senden
- ✅ Funkantworten von Spielern empfangen und decodieren
- ✅ Richtige und falsche Antworten erkennen
- ✅ Punkte vergeben und den Gewinner anzeigen
- ✅ Den Punktestand auf Knopfdruck ausgeben

---

← [Zurück: Einführung](01_einfuehrung.md) | [Weiter: Spieler →](03_spieler.md)
