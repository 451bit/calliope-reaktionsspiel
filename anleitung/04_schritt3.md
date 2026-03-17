# Schritt 3: Punkte & Spielende

← [Zurück: Schritt 2](03_schritt2.md) | [Weiter: Erweiterungen →](05_erweiterungen.md)

---

## 🎯 Ziel dieses Schritts

Am Ende dieses Schritts ist das Spiel vollständig: Punkte werden vergeben und abgezogen, der aktuelle Punktestand kann auf der LED-Matrix angezeigt werden, und nach einer einstellbaren Anzahl Runden endet das Spiel mit einem Gesamtsieger.

> **👥 Arbeitsaufteilung:** Spielleiter-Gruppe und Spieler-Gruppe arbeiten wieder parallel. Diesmal fügt der Spielleiter die Punkteverwaltung hinzu; der Spieler bekommt eine abschließende Spielende-Anzeige.

---

## Teil A · Spielleiter – Punkte & Spielende

### Lernziele (Spielleiter)
- Punkte für jeden Spieler verwalten
- Punktestand als Balkendiagramm auf der LED-Matrix anzeigen
- Spielende nach einer konfigurierbaren Anzahl Runden auslösen
- Gesamtsieger ermitteln und anzeigen

---

### SL-Schritt 1: Neue Variablen für Punkte und Runden

Füge folgende Variablen zum Spielleiter hinzu:

| Variable | Bedeutung |
|----------|-----------|
| `punkteS1` | Gesamtpunkte Spieler 1 |
| `punkteS2` | Gesamtpunkte Spieler 2 |
| `punkteS3` | Gesamtpunkte Spieler 3 |
| `punkteS4` | Gesamtpunkte Spieler 4 |
| `rundenAnzahl` | Maximale Anzahl Runden (z. B. 5) |
| `aktuelleRunde` | Wie viele Runden wurden bereits gespielt? |

Ergänze den `beim Start`-Block:

```
┌─ beim Start (Ergänzung) ────────────────────────────────┐
│                                                          │
│  Setze punkteS1 auf [ 0 ]                              │
│  Setze punkteS2 auf [ 0 ]                              │
│  Setze punkteS3 auf [ 0 ]                              │
│  Setze punkteS4 auf [ 0 ]                              │
│                                                          │
│  Setze rundenAnzahl auf [ 5 ]                          │
│  Setze aktuelleRunde auf [ 0 ]                         │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

### SL-Schritt 2: Punkte in `richtigGeantwortet` vergeben

Erweitere die Funktion `richtigGeantwortet` um die Punkt-Logik:

```
┌─ Funktion richtigGeantwortet mit Parameter: siegerID ────┐
│                                                           │
│  Setze rundeGewonnen auf [ wahr ]                       │
│                                                           │
│  // +1 Punkt für den Gewinner                           │
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
│  // Gewinner per Funk melden                            │
│  Funk: Sende Wert [ "gewinner" ] = [ siegerID ]         │
│                                                           │
│  Grundlagen: Pausiere [ 1000 ] ms                       │
│  LED: Setze RGB auf [ Farbe: aus ]                      │
│  Grundlagen: Bildschirm löschen                         │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Ändere ... um ...` → Kategorie **Variablen**

---

### SL-Schritt 3: Punkt abziehen in `falschGeantwortet`

```
┌─ Funktion falschGeantwortet mit Parameter: spielerID ────┐
│                                                           │
│  // –1 Punkt für den Spieler                            │
│  Wenn [ spielerID ] = [ 1 ] dann:                       │
│      Ändere punkteS1 um [ -1 ]                          │
│  Sonst wenn [ spielerID ] = [ 2 ] dann:                 │
│      Ändere punkteS2 um [ -1 ]                          │
│  Sonst wenn [ spielerID ] = [ 3 ] dann:                 │
│      Ändere punkteS3 um [ -1 ]                          │
│  Sonst:                                                  │
│      Ändere punkteS4 um [ -1 ]                          │
│                                                           │
│  // Fehlermeldung per Funk senden                       │
│  Funk: Sende Wert [ "falsch" ] = [ spielerID ]          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

### SL-Schritt 4: Rundenende in `rundeStarten` einbauen

Ergänze die Funktion `rundeStarten` am Ende um die Rundenzählung und Spielende-Prüfung:

```
┌─ Funktion rundeStarten (Ergänzung am Ende) ─────────────┐
│                                                          │
│  ... (restlicher Code wie in Schritt 2) ...            │
│                                                          │
│  // Runde beenden                                       │
│  Setze rundeAktiv auf [ falsch ]                       │
│                                                          │
│  // Runde hochzählen                                    │
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

---

### SL-Schritt 5: Taste B zeigt Punktestand

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ rundeAktiv ] = [ falsch ] dann:                 │
│      rufauf: punkteVisualisieren                        │
│      Grundlagen: Pausiere [ 5000 ] ms                   │
│      Grundlagen: Bildschirm löschen                     │
│      Grundlagen: Zeige Symbol [ ✓ ]                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

### SL-Schritt 6: Funktion `punkteVisualisieren`

Die LED-Matrix (5×5 = 25 LEDs) zeigt alle vier Spieler als Balkendiagramm. Jede Spalte entspricht einem Spieler, die Balkenhöhe entspricht seinen Punkten + 2 (damit auch negative Punkte darstellbar sind).

```
Konzept: Balkendiagramm auf der LED-Matrix
  
Spieler:   S1    S2    S3    S4
Spalte:     0     1     2     3
  
+3 Punkte: ●     ●                ← Zeile 0 (oben)
+2 Punkte: ●     ●     ●          ← Zeile 1
+1 Punkt:  ●     ●     ●     ●    ← Zeile 2
 0 Punkte: ●           ●     ●    ← Zeile 3
-1 Punkt:                   ●    ← Zeile 4 (unten)
           
→ S1: +3, S2: +2, S3: +1, S4: -1
```

```
┌─ Funktion punkteVisualisieren ──────────────────────────┐
│                                                          │
│  LED: Bildschirm löschen                               │
│                                                          │
│  // Jede Spielerspalte zeichnen                        │
│  rufauf: zeichneSpalte(0, punkteS1)                    │
│  rufauf: zeichneSpalte(1, punkteS2)                    │
│  rufauf: zeichneSpalte(2, punkteS3)                    │
│  rufauf: zeichneSpalte(3, punkteS4)                    │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

### SL-Schritt 7: Hilfsfunktion `zeichneSpalte`

```
┌─ Funktion zeichneSpalte (Parameter: spalte, punkte) ────┐
│                                                          │
│  // Normierung: 0 Punkte → Balkenhöhe 2                │
│  Setze balkenhöhe auf [ punkte + 2 ]                   │
│                                                          │
│  // Werte auf 0–5 begrenzen                            │
│  Wenn [ balkenhöhe ] < [ 0 ] dann: Setze auf [ 0 ]    │
│  Wenn [ balkenhöhe ] > [ 5 ] dann: Setze auf [ 5 ]    │
│                                                          │
│  // LEDs von unten nach oben einschalten               │
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

> **💡 Koordinatensystem der Matrix:**
> ```
> x: 0 1 2 3 4  (Spalten, links → rechts)
> y: 0  ← oben
>    1
>    2
>    3
>    4  ← unten
> ```

---

### SL-Schritt 8: Funktion `spielEnde`

```
┌─ Funktion spielEnde ────────────────────────────────────┐
│                                                          │
│  // Gesamtsieger ermitteln: wer hat die meisten Punkte?│
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
│  // Endergebnis per Funk an Spieler senden             │
│  Funk: Sende Wert [ "ende" ] = [ gesamtsiegerID ]      │
│                                                          │
│  // Sieger anzeigen                                    │
│  LED: Setze RGB auf [ Gelb: RGB(255,200,0) ]           │
│  Grundlagen: Zeige Zeichenkette [ "ENDE!" ]            │
│  Grundlagen: Pausiere [ 1000 ] ms                      │
│  Grundlagen: Zeige Zeichenkette [ ">>S" ]              │
│  Grundlagen: Zeige Zahl [ gesamtsiegerID ]             │
│                                                          │
│  // Balkendiagramm zeigen                              │
│  Grundlagen: Pausiere [ 2000 ] ms                      │
│  rufauf: punkteVisualisieren                           │
│  Grundlagen: Pausiere [ 5000 ] ms                      │
│                                                          │
│  // Spiel zurücksetzen                                 │
│  Setze aktuelleRunde auf [ 0 ]                         │
│  Setze punkteS1 auf [ 0 ]                              │
│  Setze punkteS2 auf [ 0 ]                              │
│  Setze punkteS3 auf [ 0 ]                              │
│  Setze punkteS4 auf [ 0 ]                              │
│  LED: Setze RGB auf [ aus ]                            │
│  Grundlagen: Bildschirm löschen                        │
│  Grundlagen: Zeige Symbol [ ✓ ]   // Bereit            │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## Teil B · Spieler – Spielende-Anzeige

### Lernziele (Spieler)
- Das `"ende"`-Signal vom Spielleiter empfangen
- Gesamtsieger-Feedback anzeigen

---

### SP-Schritt 1: Spielende-Signal empfangen

Erweitere den `Wenn Wert empfangen wird`-Block um den `"ende"`-Fall:

```
┌─ Funk: Wenn Wert empfangen wird (name, wert) ─(ergänzt)─┐
│                                                          │
│  // ... (bisherige "gewinner" und "falsch" Behandlung) │
│                                                          │
│  // Neu: Spielende                                     │
│  Wenn [ name ] = [ "ende" ] dann:                      │
│  ┌────────────────────────────────────────────────┐    │
│  │  Grundlagen: Zeige Zeichenkette [ "ENDE!" ]   │    │
│  │  Grundlagen: Pausiere [ 1000 ] ms             │    │
│  │                                               │    │
│  │  Wenn [ wert ] = [ meineID ] dann:            │    │
│  │  ┌────────────────────────────────────────┐   │    │
│  │  │  // Ich habe das Gesamtspiel gewonnen!│   │    │
│  │  │  LED: Setze RGB auf [ Gelb ]          │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "WIN" │   │    │
│  │  │  Musik: Melodie [ Siegessinfonie ]    │   │    │
│  │  └────────────────────────────────────────┘   │    │
│  │  Sonst:                                       │    │
│  │  ┌────────────────────────────────────────┐   │    │
│  │  │  // Jemand anderes hat gewonnen       │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "S"   │   │    │
│  │  │  Grundlagen: Zeige Zahl [ wert ]      │   │    │
│  │  │  Grundlagen: Zeige Zeichenkette "WIN" │   │    │
│  │  └────────────────────────────────────────┘   │    │
│  │                                               │    │
│  │  // 3 Sekunden warten, dann zurücksetzen     │    │
│  │  Grundlagen: Pausiere [ 3000 ] ms             │    │
│  │  LED: Setze RGB auf [ aus ]                   │    │
│  │  Grundlagen: Bildschirm löschen               │    │
│  │  Setze aufgabe auf [ 0 ]                      │    │
│  │  Setze habeGesendet auf [ falsch ]            │    │
│  │  Grundlagen: Zeige Zeichenkette [ "ID?" ]     │    │
│  └────────────────────────────────────────────────┘    │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 🔬 Test 3: Vollständiges Spiel

**Was ihr braucht:** Spielleiter-Calliope + mindestens ein Spieler-Calliope.

### Testablauf

| Schritt | Aktion | Erwartetes Ergebnis |
|---------|--------|----------------------|
| 1 | Spieler wählen ihre IDs | Jeder sieht seine ID kurz auf dem Display |
| 2 | Spielleiter startet Runde (Taste A) | Signal erscheint auf beiden Calliopes |
| 3 | Mehrere Runden spielen | Punkte werden vergeben/abgezogen |
| 4 | Spielleiter drückt B | Balkendiagramm auf der Matrix (5 Sek.) |
| 5 | Nach 5 Runden | „ENDE!" → Gesamtsieger → Balkendiagramm |
| 6 | Spieler sieht Gesamtsieger | Gewinner: „WIN" (gelb), andere: „Sx WIN" |

### Zusätzliche Testfragen

- Was passiert, wenn in einer Runde niemand antwortet?
- Was passiert, wenn jemand immer falsch antwortet (negative Punkte)?
- Stimmt das Balkendiagramm mit dem echten Punktestand überein?

---

## 🗺️ Zustandsübersicht – das vollständige System

```
SPIELLEITER                              SPIELER (je 1–4×)

beim Start                               beim Start
  → Variablen init                         → Variablen init
  → Gruppe 1                               → Gruppe 1
  → ✓ anzeigen                             → "ID?" anzeigen

Taste A → rundeStarten                   Taste A/B/A+B → ID wählen
  → Zufällige Aufgabe                     (solange noch keine ID gesetzt)
  → signalAnzeigen (RGB+LED+Ton)
  → Funk: Sende Zahl (aktuelleAufgabe)  ←──── Spielleiter sendet Signal
  → 5 Sek. warten
  → Runde beenden + Runde +1            ────► Spieler empfängt Signal
  → Spielende prüfen                          → signalAnzeigen
                                              → Spieler drückt Taste
Funk: empfange Zahl                    ◄──── Spieler sendet ID×10+Aktion
  → antwortVerarbeiten
    (spielerID + aktion decodieren)
  → richtig? → Punkte +1, Funk "gewinner"
  → falsch?  → Punkte -1, Funk "falsch"  ──► Spieler empfängt Feedback
                                               → ✓ (grün) oder ✕ (rot)
Taste B → punkteVisualisieren
  (Balkendiagramm auf Matrix)

Nach N Runden → spielEnde
  → Gesamtsieger ermitteln
  → Funk: Sende Wert "ende"            ──► Spieler empfängt "ende"
  → ENDE + >>Sx + Balken + Reset             → WIN oder Sx WIN anzeigen
```

---

## 📋 Zusammenfassung Schritt 3

| Gerät | Was ist jetzt fertig |
|-------|---------------------|
| **Spielleiter** | Punkte vergeben/abziehen, Balkendiagramm (Taste B), Spielende nach N Runden, Gesamtsieger |
| **Spieler** | Spielende-Anzeige (WIN / Sx WIN) |

Das Spiel ist jetzt vollständig funktionsfähig! 🎮

**⏩ Mehr Möglichkeiten:** [Erweiterungen](05_erweiterungen.md) – Neigungssensor, Zeitbonus, Mehrere Modi und mehr
