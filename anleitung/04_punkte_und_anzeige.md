# Kapitel 4: Punkte, Anzeige & Spielende

← [Zurück: Spieler](03_spieler.md) | [Weiter: Erweiterungen →](05_erweiterungen.md)

---

## 🎯 Lernziele dieses Kapitels

- Du verwaltest einen mehrdimensionalen Punktestand
- Du implementierst eine visuelle Punktedarstellung auf der LED-Matrix
- Du erkennst, wann das Spiel zu Ende ist, und zeigst den Gewinner
- Du verstehst das Konzept der **Spielphase** (Spielzustand-Maschine)

---

## 4.1 Spielphasen – der Zustandsautomat

Ein komplexeres Spiel hat mehrere **Phasen** (Zustände). In der Informatik nennt man das einen **endlichen Automaten** oder **Zustandsautomaten (FSM – Finite State Machine)**.

Unser Spiel hat folgende Phasen beim Spielleiter:

```
                    ┌─────────────────────────────────────────┐
                    │                                         │
                    ▼                                         │
         ┌─────────────────┐                                  │
     ──► │   BEREIT        │ ◄── Taste B: Punktestand zeigen  │
         │  (Warten auf A) │                                  │
         └────────┬────────┘                                  │
                  │ Taste A                                    │
                  ▼                                           │
         ┌─────────────────┐                                  │
         │   SIGNAL        │ Zufällige Aufgabe,               │
         │   ANZEIGEN      │ RGB+Ton+Matrix, Funk senden       │
         └────────┬────────┘                                  │
                  │ sofort                                    │
                  ▼                                           │
         ┌─────────────────┐                                  │
         │   WARTEN AUF    │ ◄── Spieler senden Antworten     │
         │   ANTWORTEN     │                                  │
         └────┬───────┬────┘                                  │
              │       │                                       │
   Richtige   │       │ 5 Sek. vorbei                        │
   Antwort    │       │ (niemand richtig)                     │
   empfangen  ▼       ▼                                       │
         ┌─────────────────┐                                  │
         │   ERGEBNIS      │ Gewinner zeigen / "Niemand"      │
         │   ANZEIGEN      │ Punkte aktualisieren             │
         └────────┬────────┘                                  │
                  │                                           │
                  │ Spielende? Weiter?                        │
                  └─────────────────────────────────────────► │
                    (zurück zu BEREIT)
```

> **📚 Informatik-Hintergrund:** Zustandsautomaten sind ein fundamentales Konzept der theoretischen Informatik. Sie beschreiben, welche Zustände ein System haben kann und unter welchen Bedingungen es von einem Zustand in den nächsten wechselt.

### Aufgabe 4.1: Zustandsautomaten analysieren

Beantworte folgende Fragen zum obigen Zustandsdiagramm:

1. Wie viele Zustände hat der Spielleiter-Automat?
2. Was sind die **Übergangsbedingungen** (Transitionen)?
3. Welcher Zustand ist der **Startzustand**?
4. Gibt es einen **Endzustand**? Was wäre sinnvoll?

---

## 4.2 Spielende erkennen

Bisher läuft das Spiel unendlich viele Runden. Wir fügen nun ein **Spielende** hinzu: Nach einer einstellbaren Anzahl von Runden endet das Spiel.

### Neue Variable

| Variable | Typ | Bedeutung |
|----------|-----|-----------|
| `rundenAnzahl` | Zahl | Maximale Anzahl Runden |
| `aktuelleRunde` | Zahl | Wie viele Runden wurden gespielt? |

### Schritt 1: Variablen initialisieren

```
┌─ beim Start (Ergänzung) ────────────────────────────────┐
│                                                          │
│  Setze rundenAnzahl auf [ 5 ]    // 5 Runden pro Spiel │
│  Setze aktuelleRunde auf [ 0 ]                         │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Schritt 2: Rundenende in `rundeStarten` einbauen

Ergänze die Funktion `rundeStarten` am Ende:

```
┌─ Funktion rundeStarten (Ergänzung am Ende) ─────────────┐
│                                                          │
│  ... (restlicher Code wie bisher) ...                  │
│                                                          │
│  // Rundenende                                          │
│  Setze rundeAktiv auf [ falsch ]                       │
│                                                          │
│  // Runde zählen                                        │
│  Ändere aktuelleRunde um [ 1 ]                         │
│                                                          │
│  // Spielende prüfen                                    │
│  Wenn [ aktuelleRunde ] ≥ [ rundenAnzahl ] dann:       │
│  ┌────────────────────────────────────────────────┐    │
│  │  rufauf: spielEnde                             │    │
│  └────────────────────────────────────────────────┘    │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Schritt 3: Die Funktion `spielEnde`

```
┌─ Funktion spielEnde ────────────────────────────────────┐
│                                                          │
│  // Gewinner des Gesamtspiels ermitteln                │
│  // (wer hat die meisten Punkte?)                      │
│  Setze maxPunkte auf [ punkteS1 ]                      │
│  Setze gesamtsiegerID auf [ 1 ]                        │
│                                                          │
│  Wenn [ punkteS2 ] > [ maxPunkte ] dann:               │
│      Setze maxPunkte auf [ punkteS2 ]                  │
│      Setze gesamtsiegerID auf [ 2 ]                    │
│                                                          │
│  Wenn [ punkteS3 ] > [ maxPunkte ] dann:               │
│      Setze maxPunkte auf [ punkteS3 ]                  │
│      Setze gesamtsiegerID auf [ 3 ]                    │
│                                                          │
│  Wenn [ punkteS4 ] > [ maxPunkte ] dann:               │
│      Setze maxPunkte auf [ punkteS4 ]                  │
│      Setze gesamtsiegerID auf [ 4 ]                    │
│                                                          │
│  // Endergebnis per Funk senden                        │
│  Funk: Sende Wert [ "ende" ] = [ gesamtsiegerID ]      │
│                                                          │
│  // Sieger zeigen                                      │
│  LED: Setze RGB auf [ Gold/Gelb: RGB(255,200,0) ]     │
│  Grundlagen: Zeige Zeichenkette [ "ENDE!" ]            │
│  Grundlagen: Pausiere [ 1000 ] ms                      │
│  Grundlagen: Zeige Zeichenkette [ "1:" ]               │
│  Grundlagen: Zeige Zahl [ punkteS1 ]                   │
│  Grundlagen: Zeige Zeichenkette [ "2:" ]               │
│  Grundlagen: Zeige Zahl [ punkteS2 ]                   │
│  Grundlagen: Zeige Zeichenkette [ "3:" ]               │
│  Grundlagen: Zeige Zahl [ punkteS3 ]                   │
│  Grundlagen: Zeige Zeichenkette [ "4:" ]               │
│  Grundlagen: Zeige Zahl [ punkteS4 ]                   │
│  Grundlagen: Pausiere [ 500 ] ms                       │
│  Grundlagen: Zeige Zeichenkette [ ">>S" ]              │
│  Grundlagen: Zeige Zahl [ gesamtsiegerID ]             │
│                                                          │
│  // Punktestand-LED-Visualisierung anzeigen            │
│  Grundlagen: Pausiere [ 2000 ] ms                      │
│  rufauf: punkteVisualisieren                           │
│                                                          │
│  // Spiel zurücksetzen für neue Runde                  │
│  Setze aktuelleRunde auf [ 0 ]                         │
│  Setze punkteS1 auf [ 0 ]                              │
│  Setze punkteS2 auf [ 0 ]                              │
│  Setze punkteS3 auf [ 0 ]                              │
│  Setze punkteS4 auf [ 0 ]                              │
│  LED: Setze RGB auf [ aus ]                            │
│  Grundlagen: Bildschirm löschen                        │
│  Grundlagen: Zeige Symbol [ ✓ ]  // Bereit             │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 4.3 Visuelle Punktedarstellung auf der Matrix

Die LED-Matrix hat 5 Spalten und 5 Zeilen. Wir können sie nutzen, um alle 4 Spieler gleichzeitig anzuzeigen.

### Konzept: Balkendiagramm auf der Matrix

```
Spieler:  S1    S2    S3    S4
Spalte:    1     2     3     4
(Spalte 5 ist leer / Trennlinie)

3 Punkte:  ●     ●     ●     ●   ← Zeile 1 (oben)
2 Punkte:  ●     ●           ●   ← Zeile 2
1 Punkt:   ●     ●           ●   ← Zeile 3
0 Punkte:                        ← Zeile 4
          -1:              ●     ← Zeile 5 (Minuspunkte)

→ S1 hat 3 Punkte, S2 hat 3 Punkte, S3 hat 0 Punkte, S4 hat -1 Pkt.
```

> Da die Matrix nur positive Werte von 0-5 gut darstellen kann, normalisieren wir:  
> Wir zeigen immer **Punkte + 2** als Balkenhöhe (damit –2 bis +3 darstellbar sind).

### Schritt 4: Funktion `punkteVisualisieren`

Diese Funktion zeichnet das Balkendiagramm:

```
┌─ Funktion punkteVisualisieren ──────────────────────────┐
│                                                          │
│  LED: Bildschirm löschen                               │
│                                                          │
│  // Für jeden Spieler die LEDs in der Spalte setzen    │
│  // Wir nutzen LED: setze LED (x, y) auf true/false   │
│                                                          │
│  // Spieler 1 → Spalte 0                               │
│  rufauf: zeichneSpalte(0, punkteS1)                    │
│                                                          │
│  // Spieler 2 → Spalte 1                               │
│  rufauf: zeichneSpalte(1, punkteS2)                    │
│                                                          │
│  // Spieler 3 → Spalte 2                               │
│  rufauf: zeichneSpalte(2, punkteS3)                    │
│                                                          │
│  // Spieler 4 → Spalte 3                               │
│  rufauf: zeichneSpalte(3, punkteS4)                    │
│                                                          │
│  Grundlagen: Pausiere [ 5000 ] ms  // 5 Sek. anzeigen │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Schritt 5: Hilfsfunktion `zeichneSpalte(spalte, punkte)`

```
┌─ Funktion zeichneSpalte mit Parametern: spalte, punkte ─┐
│                                                          │
│  // Normierung: Punkte + 2 = Balkenhöhe (0 bis 5)      │
│  Setze balkenhöhe auf [ punkte + 2 ]                   │
│                                                          │
│  // Sicherstellen: Balkenhöhe bleibt zwischen 0 und 5  │
│  Wenn [ balkenhöhe ] < [ 0 ] dann: Setze auf [ 0 ]    │
│  Wenn [ balkenhöhe ] > [ 5 ] dann: Setze auf [ 5 ]    │
│                                                          │
│  // LEDs von unten (y=4) nach oben (y=0) setzen        │
│  Wenn [ balkenhöhe ] ≥ [ 1 ] dann:                    │
│      LED: Setze LED (spalte, 4) auf [ wahr ]           │
│  Wenn [ balkenhöhe ] ≥ [ 2 ] dann:                    │
│      LED: Setze LED (spalte, 3) auf [ wahr ]           │
│  Wenn [ balkenhöhe ] ≥ [ 3 ] dann:                    │
│      LED: Setze LED (spalte, 2) auf [ wahr ]           │
│  Wenn [ balkenhöhe ] ≥ [ 4 ] dann:                    │
│      LED: Setze LED (spalte, 1) auf [ wahr ]           │
│  Wenn [ balkenhöhe ] ≥ [ 5 ] dann:                    │
│      LED: Setze LED (spalte, 0) auf [ wahr ]           │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Wo findest du `LED: Setze LED`?**  
→ Kategorie **LED** → `LED zeichnen (x)(y)`

> **💡 Koordinatensystem der LED-Matrix:**
> ```
> x: 0 1 2 3 4  (Spalten, links nach rechts)
> y: 0           (oben)
>    1
>    2
>    3
>    4           (unten)
> ```

---

## 4.4 Spieler beim Spielende informieren

Im Spieler-Programm muss ebenfalls auf das `"ende"`-Signal reagiert werden:

### Schritt 6: Spieler-Programm erweitern

Füge im `Wenn Wert empfangen wird`-Block folgendes hinzu:

```
┌─ Funk: Wenn Wert empfangen wird (ergänzt) ──────────────┐
│                                                          │
│  // (bisherige "gewinner" und "falsch" Behandlung)     │
│  ...                                                    │
│                                                          │
│  // Spielende                                          │
│  Wenn [ name ] = [ "ende" ] dann:                      │
│  ┌────────────────────────────────────────────────┐    │
│  │  Grundlagen: Zeige Zeichenkette [ "ENDE!" ]   │    │
│  │  Grundlagen: Pausiere [ 1000 ] ms             │    │
│  │                                               │    │
│  │  Wenn [ wert ] = [ meineID ] dann:            │    │
│  │  ┌────────────────────────────────────────┐   │    │
│  │  │  // Ich habe das Gesamtspiel gewonnen!│   │    │
│  │  │  LED: Setze RGB auf [ Gelb/Gold ]     │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "WIN" │   │    │
│  │  │  // Melodie abspielen                 │   │    │
│  │  │  Musik: Melodie [ Siegessinfonie ]    │   │    │
│  │  └────────────────────────────────────────┘   │    │
│  │  Sonst:                                       │    │
│  │  ┌────────────────────────────────────────┐   │    │
│  │  │  // Jemand anderes hat gewonnen       │   │    │
│  │  │  LED: Setze RGB auf [ aus ]           │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "S"   │   │    │
│  │  │  Grundlagen: Zeige Zahl [ wert ]      │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "WIN" │   │    │
│  │  └────────────────────────────────────────┘   │    │
│  │                                               │    │
│  │  // 3 Sekunden warten                        │    │
│  │  Grundlagen: Pausiere [ 3000 ] ms             │    │
│  │  LED: Setze RGB auf [ aus ]                   │    │
│  │  Grundlagen: Bildschirm löschen               │    │
│  │                                               │    │
│  │  // Spiel-Variablen zurücksetzen             │    │
│  │  Setze aufgabe auf [ 0 ]                      │    │
│  │  Setze habeGesendet auf [ falsch ]            │    │
│  │  Grundlagen: Zeige Zeichenkette [ "ID?" ]     │    │
│  └────────────────────────────────────────────────┘    │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 4.5 Vollständiger Spielablauf – Ende-zu-Ende-Test

Führe nach der Implementierung einen vollständigen Test durch:

### Testprotokoll

Führe den Test **zu viert** durch und notiere die Ergebnisse:

| Runde | Signal | S1 | S2 | S3 | S4 | Gewinner | Richtig? |
|-------|--------|----|----|----|----|----------|----------|
| 1 | ? | ? | ? | ? | ? | ? | ✓/✗ |
| 2 | ? | ? | ? | ? | ? | ? | ✓/✗ |
| 3 | ? | ? | ? | ? | ? | ? | ✓/✗ |
| 4 | ? | ? | ? | ? | ? | ? | ✓/✗ |
| 5 | ? | ? | ? | ? | ? | ? | ✓/✗ |
| **Gesamt** | | | | | | **Winner:** | |

---

## 4.6 Aufgaben

### Aufgabe 4.1: Zustandsdiagramm erweitern ⭐

Zeichne das Zustandsdiagramm aus Abschnitt 4.1 neu und ergänze den **SPIELENDE**-Zustand mit allen Übergangen (Transitionen).

### Aufgabe 4.2: Gleichstand behandeln ⭐⭐

Was passiert aktuell bei einem **Gleichstand** (zwei Spieler haben gleich viele Punkte)? 

1. Beschreibe das aktuelle Verhalten des Programms.
2. Wie könntest du einen Gleichstand erkennen?
3. Implementiere eine Gleichstandsbehandlung: Wenn zwei oder mehr Spieler denselben Höchstpunktestand haben, soll `"DRAW"` angezeigt werden anstatt ein Einzelsieger.

### Aufgabe 4.3: Dynamische Rundenzahl einstellen ⭐⭐

Derzeit ist `rundenAnzahl = 5` fest einprogrammiert. Ändere das so, dass die Anzahl der Runden **vor dem Spielstart eingestellt** werden kann:

- Vor dem Start: Taste A = mehr Runden (+1), Taste B = weniger Runden (–1, Minimum 3)
- Wenn A+B gedrückt wird: Spielstart mit der gewählten Rundenzahl

*Hinweis: Du brauchst einen neuen Zustand „Konfiguration" vor dem eigentlichen Spiel.*

### Aufgabe 4.4: Scoreboard verbessern ⭐⭐⭐

Verbessere die `punkteVisualisieren`-Funktion so, dass:
- In Spalte 4 eine Legende angezeigt wird (z. B. Trennstrich)
- Negative Punkte durch **blinkende LEDs** signalisiert werden
- Der aktuelle Rundenstand (`aktuelleRunde / rundenAnzahl`) als Zusatzanzeige erscheint

---

## ✅ Zusammenfassung

Das Spielsystem ist jetzt vollständig:
- ✅ Spielende nach einer konfigurierbaren Rundenzahl
- ✅ Gesamtsieger-Ermittlung mit Vergleich aller Punktestände
- ✅ Visuelle Punktedarstellung als Balkendiagramm auf der Matrix
- ✅ Spieler werden über das Spielende informiert
- ✅ Spiel kann automatisch neu gestartet werden

---

← [Zurück: Spieler](03_spieler.md) | [Weiter: Erweiterungen →](05_erweiterungen.md)
