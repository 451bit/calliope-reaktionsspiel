# Schritt 2: Signale & Reaktionen

← [Zurück: Schritt 1](02_schritt1.md) | [Weiter: Schritt 3 →](04_schritt3.md)

---

## 🎯 Ziel dieses Schritts

Am Ende dieses Schritts läuft die erste echte Spielrunde: Der Spielleiter wählt zufällig ein Signal, zeigt es mit Farbe, LEDs und Ton an und sendet es per Funk. Die Spieler sehen das Signal auf ihrem Display und schicken ihre Antwort zurück. Der Spielleiter wertet aus und gibt Feedback.

> **👥 Arbeitsaufteilung:** Spielleiter-Gruppe und Spieler-Gruppe arbeiten wieder parallel. Nehmt euch Zeit, um zu verstehen, wie beide Programme zusammenpassen, bevor ihr testet!

---

## Teil A · Spielleiter – Signale senden & auswerten

### Lernziele (Spielleiter)
- Variablen für den Spielzustand nutzen
- Zufällige Signale erzeugen und anzeigen (RGB, LED, Ton)
- Spielerantworten per Funk empfangen und decodieren
- Richtige und falsche Antworten erkennen und per Funk zurückmelden

---

### SL-Schritt 1: Neue Variablen anlegen

Öffne das **Spielleiter**-Projekt aus Schritt 1 und füge folgende Variablen hinzu:

| Variable | Bedeutung |
|----------|-----------|
| `aktuelleAufgabe` | Welches Signal gerade aktiv ist (1, 2 oder 3) |
| `rundeAktiv` | Läuft gerade eine Runde? (wahr/falsch) |
| `rundeGewonnen` | Hat schon jemand richtig geantwortet? (wahr/falsch) |

Und ergänze den `beim Start`-Block:

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze aktuelleAufgabe auf [ 0 ]                        │
│  Setze rundeAktiv auf [ falsch ]                        │
│  Setze rundeGewonnen auf [ falsch ]                     │
│                                                           │
│  Grundlagen: Zeige Symbol [ ✓ ]                          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Warum `rundeAktiv` und `rundeGewonnen`?**
> Ohne diese Flags würden eingehende Funknachrichten auch außerhalb einer Runde verarbeitet werden. Mit diesen Variablen steuern wir gezielt, wann Antworten akzeptiert werden.

---

### SL-Schritt 2: Taste A startet eine Runde

Ersetze den alten Taste-A-Block durch diesen:

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ rundeAktiv ] = [ falsch ] dann:          │
│      rufauf: rundeStarten                               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du eigene Funktionen?**
→ Kategorie **Fortgeschritten** → **Funktionen** → **„Erstelle eine Funktion"**

---

### SL-Schritt 3: Funktion `rundeStarten`

Erstelle eine neue Funktion mit dem Namen `rundeStarten`:

```
┌─ Funktion rundeStarten ──────────────────────────────────┐
│                                                           │
│  Setze rundeAktiv auf [ wahr ]                          │
│  Setze rundeGewonnen auf [ falsch ]                     │
│                                                           │
│  // Zufällige Aufgabe wählen (1, 2 oder 3)              │
│  Setze aktuelleAufgabe auf [ wähle zufällig 1 bis 3 ]  │
│                                                           │
│  // Signal auf dem Spielleiter anzeigen                  │
│  rufauf: signalAnzeigen                                 │
│                                                           │
│  // Signal per Funk an alle Spieler senden               │
│  Funk: Sende Zahl [ aktuelleAufgabe ]                   │
│                                                           │
│  // 5 Sekunden warten – Spieler können jetzt antworten   │
│  Grundlagen: Pausiere [ 5000 ] ms                       │
│                                                           │
│  // Zeit abgelaufen: Falls noch niemand richtig lag     │
│  Logik: Wenn [ rundeGewonnen ] = [ falsch ] dann:       │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Grundlagen: Zeige Zeichenkette [ "---" ]        │   │
│  │  LED: Setze RGB auf [ Farbe: aus/schwarz ]       │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Setze rundeAktiv auf [ falsch ]                        │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `wähle zufällig ... bis ...` → Kategorie **Mathematik**
- `Pausiere ... ms` → Kategorie **Grundlagen**

---

### SL-Schritt 4: Funktion `signalAnzeigen`

Erstelle die Funktion `signalAnzeigen`. Sie zeigt je nach `aktuelleAufgabe` das richtige Signal aus Farbe, LED-Muster und Ton:

```
┌─ Funktion signalAnzeigen ────────────────────────────────┐
│                                                           │
│  Wenn [ aktuelleAufgabe ] = [ 1 ] dann:                 │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Grün: RGB(0,255,0) ]      │   │
│  │  Grundlagen: Zeige LEDs (großes A-Muster):      │   │
│  │    ■ □ □ □ ■                                    │   │
│  │    ■ □ □ □ ■                                    │   │
│  │    ■ ■ ■ ■ ■                                    │   │
│  │    ■ □ □ □ ■                                    │   │
│  │    ■ □ □ □ ■                                    │   │
│  │  Musik: Spiele Ton [ A5 ] für [ 1 ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst wenn [ aktuelleAufgabe ] = [ 2 ] dann:           │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Rot: RGB(255,0,0) ]       │   │
│  │  Grundlagen: Zeige LEDs (großes B-Muster):      │   │
│  │    ■ ■ ■ □ □                                    │   │
│  │    ■ □ □ ■ □                                    │   │
│  │    ■ ■ ■ □ □                                    │   │
│  │    ■ □ □ ■ □                                    │   │
│  │    ■ ■ ■ □ □                                    │   │
│  │  Musik: Spiele Ton [ A3 ] für [ 1 ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst (aktuelleAufgabe = 3):                           │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Blau: RGB(0,0,255) ]      │   │
│  │  Grundlagen: Zeige Symbol [ ♥ (Herz) ]          │   │
│  │  Musik: Spiele Ton [ A4 ] für [ 1 ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 `Sonst wenn` hinzufügen:** Klicke auf das kleine **„+"** am `Wenn...dann`-Block, um weitere Zweige anzuhängen.

**Signaltabelle:**

| Aufgaben-Code | RGB-Farbe | LED-Anzeige | Ton | Bedeutung |
|:---:|---------|----------|-----|-----------|
| 1 | Grün | A-Muster | hoch (A5) | Drücke Taste A |
| 2 | Rot | B-Muster | tief (A3) | Drücke Taste B |
| 3 | Blau | Herz ♥ | mittel (A4) | Drücke A+B |

---

### SL-Schritt 5: Spielerantworten empfangen und decodieren

Wenn ein Spieler antwortet, sendet er eine codierte Zahl, z. B. `21` = Spieler 2 hat Aktion 1 ausgeführt. Der Spielleiter muss diese Zahl empfangen und auswerten.

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  // Nur auswerten wenn eine Runde läuft                  │
│  // UND noch niemand richtig geantwortet hat             │
│  Wenn [ rundeAktiv ] = [ wahr ]                         │
│  UND  [ rundeGewonnen ] = [ falsch ] dann:              │
│                                                           │
│      rufauf: antwortVerarbeiten(empfangeneZahl)         │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

### SL-Schritt 6: Funktion `antwortVerarbeiten`

Die Spieler codieren ihre Antwort als `spielerID × 10 + aktion`. Diese Funktion decodiert die Zahl wieder:

```
┌─ Funktion antwortVerarbeiten mit Parameter: nachricht ───┐
│                                                           │
│  // Spieler-ID berechnen (z.B. 21 → 2)                  │
│  Setze spielerID auf:                                   │
│      [ ganzzahliger Anteil von ] [ nachricht / 10 ]     │
│                                                           │
│  // Aktion berechnen (z.B. 21 → 1)                      │
│  Setze aktion auf:                                      │
│      [ Rest von ] [ nachricht geteilt durch 10 ]        │
│                                                           │
│  // Gültigkeitsprüfung: ID muss 1–4 sein                │
│  Wenn [ spielerID ] ≥ [ 1 ]                             │
│  UND  [ spielerID ] ≤ [ 4 ] dann:                      │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Wenn [ aktion ] = [ aktuelleAufgabe ] dann:    │   │
│  │      rufauf: richtigGeantwortet(spielerID)      │   │
│  │  Sonst:                                          │   │
│  │      rufauf: falschGeantwortet(spielerID)       │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Beispiel:**
```
Empfangene Zahl: 21
  spielerID = ganzzahliger Anteil von 21 / 10 = 2  → Spieler 2
  aktion    = Rest von 21 / 10             = 1  → Taste A (Aktion 1)
```

> **Wo findest du die Rechenblöcke?**
> - `ganzzahliger Anteil von A / B` → **Mathematik** (Ganzzahl-Division)
> - `Rest von A / B` (Modulo) → **Mathematik** → „Rest von ... geteilt durch ..."

---

### SL-Schritt 7: Funktion `richtigGeantwortet`

```
┌─ Funktion richtigGeantwortet mit Parameter: siegerID ────┐
│                                                           │
│  Setze rundeGewonnen auf [ wahr ]                       │
│                                                           │
│  // Gewinner anzeigen auf dem Spielleiter                │
│  LED: Setze RGB auf [ Farbe: Grün ]                     │
│  Grundlagen: Zeige Zeichenkette [ "S" ]                 │
│  Grundlagen: Zeige Zahl [ siegerID ]                    │
│  Grundlagen: Pausiere [ 1500 ] ms                       │
│                                                           │
│  // Gewinner per Funk an alle Spieler melden             │
│  Funk: Sende Wert [ "gewinner" ] = [ siegerID ]         │
│                                                           │
│  Grundlagen: Pausiere [ 1000 ] ms                       │
│  LED: Setze RGB auf [ Farbe: aus ]                      │
│  Grundlagen: Bildschirm löschen                         │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **Was ist `Funk: Sende Wert`?**
> Im Gegensatz zu `Sende Zahl` sendet `Sende Wert` ein **Name-Wert-Paar**, z. B. `"gewinner" = 2`. So kann der Spieler unterscheiden, welche Art von Nachricht er empfangen hat. Diesen Block findest du in Kategorie **Funk**.

---

### SL-Schritt 8: Funktion `falschGeantwortet`

```
┌─ Funktion falschGeantwortet mit Parameter: spielerID ────┐
│                                                           │
│  // Spieler über Fehler per Funk informieren             │
│  Funk: Sende Wert [ "falsch" ] = [ spielerID ]          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **Punkte** werden erst in Schritt 3 hinzugefügt. Hier informieren wir erst nur per Funk.

---

## Teil B · Spieler – Signale anzeigen & antworten

### Lernziele (Spieler)
- Empfangene Signale korrekt anzeigen (RGB, LED, Ton)
- Taste A/B/A+B als codierte Antwort senden
- Verhindern, dass mehrere Antworten pro Runde gesendet werden
- Feedback vom Spielleiter empfangen und anzeigen

---

### SP-Schritt 1: Zwei neue Variablen anlegen

Öffne das **Spieler**-Projekt aus Schritt 1 und füge hinzu:

| Variable | Bedeutung |
|----------|-----------|
| `aufgabe` | Aktuell aktive Aufgabe vom Spielleiter (0 = keine) |
| `habeGesendet` | Habe ich in dieser Runde schon geantwortet? |

Ergänze den `beim Start`-Block:

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze meineID auf [ 0 ]                                │
│  Setze idGesetzt auf [ falsch ]                         │
│  Setze aufgabe auf [ 0 ]                                │
│  Setze habeGesendet auf [ falsch ]                      │
│                                                           │
│  Grundlagen: Zeige Zeichenkette [ "ID?" ]               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

### SP-Schritt 2: Signal vom Spielleiter empfangen

Ersetze den alten (einfachen) Funk-Empfangs-Block durch diesen:

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  // Nur reagieren wenn meine ID bereits gesetzt ist      │
│  Wenn [ idGesetzt ] = [ wahr ] dann:                    │
│                                                           │
│      // Signal-Code vom Spielleiter (1–3)?               │
│      Wenn [ empfangeneZahl ] ≥ [ 1 ]                    │
│      UND  [ empfangeneZahl ] ≤ [ 3 ] dann:             │
│      ┌──────────────────────────────────────────────┐   │
│      │  Setze aufgabe auf [ empfangeneZahl ]        │   │
│      │  Setze habeGesendet auf [ falsch ]           │   │
│      │  rufauf: signalAnzeigen                      │   │
│      └──────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

### SP-Schritt 3: Funktion `signalAnzeigen` (Spieler-Version)

Die Spieler zeigen dasselbe Signal wie der Spielleiter – damit ist klar, was zu tun ist:

```
┌─ Funktion signalAnzeigen ────────────────────────────────┐
│                                                           │
│  Wenn [ aufgabe ] = [ 1 ] dann:                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Grün ]                    │   │
│  │  Grundlagen: Zeige LEDs (A-Muster wie oben)     │   │
│  │  Musik: Spiele Ton [ A5 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst wenn [ aufgabe ] = [ 2 ] dann:                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Rot ]                     │   │
│  │  Grundlagen: Zeige LEDs (B-Muster wie oben)     │   │
│  │  Musik: Spiele Ton [ A3 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst (aufgabe = 3):                                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Blau ]                    │   │
│  │  Grundlagen: Zeige Symbol [ ♥ ]                 │   │
│  │  Musik: Spiele Ton [ A4 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **📌 Wichtig:** Spielleiter und Spieler müssen **identische Signalcodes** verwenden! Aufgaben-Code `1` bedeutet auf beiden Geräten: grün, A-Muster, hoher Ton.

---

### SP-Schritt 4: Tasten A, B und A+B als Antwort senden

Erweitere die Taste-A/B/A+B-Blöcke, sodass sie im Spielbetrieb eine codierte Antwort senden. Die Antwort ist: `meineID × 10 + Aktion`.

**Taste A (Aktion 1) – erweiterter Block:**

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  // ID-Auswahl (wie in Schritt 1):                      │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
│      Setze meineID auf [1], idGesetzt auf [wahr], ...   │
│                                                           │
│  // Im Spielbetrieb: Antwort senden                     │
│  Sonst wenn [ aufgabe ] > [ 0 ]                         │
│  UND [ habeGesendet ] = [ falsch ] dann:               │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze habeGesendet auf [ wahr ]                │   │
│  │  Funk: Sende Zahl [ meineID × 10 + 1 ]          │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]      │   │
│  │  Grundlagen: Pausiere [ 500 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Taste B (Aktion 2):**

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  // ID-Auswahl (wie in Schritt 1):                      │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
│      Setze meineID auf [2], idGesetzt auf [wahr], ...   │
│                                                           │
│  // Im Spielbetrieb: Antwort senden                     │
│  Sonst wenn [ aufgabe ] > [ 0 ]                         │
│  UND [ habeGesendet ] = [ falsch ] dann:               │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze habeGesendet auf [ wahr ]                │   │
│  │  Funk: Sende Zahl [ meineID × 10 + 2 ]          │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]      │   │
│  │  Grundlagen: Pausiere [ 500 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Tasten A+B (Aktion 3):**

```
┌─ Wenn Knopf A+B gedrückt ───────────────────────────────┐
│                                                           │
│  // ID-Auswahl (wie in Schritt 1):                      │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
│      Setze meineID auf [3], idGesetzt auf [wahr], ...   │
│                                                           │
│  // Im Spielbetrieb: Antwort senden                     │
│  Sonst wenn [ aufgabe ] > [ 0 ]                         │
│  UND [ habeGesendet ] = [ falsch ] dann:               │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze habeGesendet auf [ wahr ]                │   │
│  │  Funk: Sende Zahl [ meineID × 10 + 3 ]          │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]      │   │
│  │  Grundlagen: Pausiere [ 500 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Warum `habeGesendet`?**
> Ohne dieses Flag könnte ein Spieler beim schnellen Drücken mehrere Antworten senden. Das Flag stellt sicher, dass pro Runde **genau eine** Antwort gesendet wird.

> **💡 `meineID × 10` in MakeCode:** Verwende den `×`-Block aus der Kategorie **Mathematik** zwischen `meineID` (Variable) und `10`, dann `+` `1`/`2`/`3`.

---

### SP-Schritt 5: Feedback vom Spielleiter empfangen

Der Spielleiter sendet Wert-Nachrichten (`"gewinner"` oder `"falsch"`). Der Spieler reagiert darauf:

```
┌─ Funk: Wenn Wert empfangen wird (name, wert) ────────────┐
│                                                           │
│  // Gewinner-Meldung                                     │
│  Wenn [ name ] = [ "gewinner" ] dann:                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Wenn [ wert ] = [ meineID ] dann:              │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Ich habe gewonnen!                  │   │   │
│  │  │  LED: Setze RGB auf [ Grün ]            │   │   │
│  │  │  Grundlagen: Zeige Symbol [ Haken ✓ ]   │   │   │
│  │  │  Musik: Melodie [ Dadadum ] abspielen   │   │   │
│  │  │  Grundlagen: Pausiere [ 2000 ] ms       │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  │  Sonst:                                          │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Jemand anderes hat gewonnen         │   │   │
│  │  │  LED: Setze RGB auf [ aus ]             │   │   │
│  │  │  Grundlagen: Zeige Zeichenkette [ "S" ] │   │   │
│  │  │  Grundlagen: Zeige Zahl [ wert ]        │   │   │
│  │  │  Grundlagen: Pausiere [ 2000 ] ms       │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  │  // Aufräumen nach der Runde                    │   │
│  │  Setze aufgabe auf [ 0 ]                        │   │
│  │  LED: Setze RGB auf [ aus ]                     │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  // Falsch-Meldung                                       │
│  Wenn [ name ] = [ "falsch" ] dann:                     │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Wenn [ wert ] = [ meineID ] dann:              │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Ich war falsch!                     │   │   │
│  │  │  LED: Setze RGB auf [ Rot ]             │   │   │
│  │  │  Grundlagen: Zeige Symbol [ Kreuz ✕ ]   │   │   │
│  │  │  Musik: Spiele Ton [ A1 ] für [1] Schlag│   │   │
│  │  │  Grundlagen: Pausiere [ 1500 ] ms       │   │   │
│  │  │  LED: Setze RGB auf [ aus ]             │   │   │
│  │  │  Grundlagen: Bildschirm löschen         │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du `Wenn Wert empfangen wird`?**
→ Kategorie **Funk** → `Wenn Wert empfangen wird (name)(wert)`

> **⚠️ Achtung:** `Wenn Zahl empfangen` und `Wenn Wert empfangen` sind **verschiedene Blöcke**! Du brauchst beide im Spieler-Programm.

---

## 🔬 Test 2: Erste echte Spielrunde

**Was ihr braucht:** Spielleiter-Calliope + mindestens ein Spieler-Calliope, alle eingeschaltet.

### Testablauf

| Schritt | Aktion | Erwartetes Ergebnis |
|---------|--------|----------------------|
| 1 | Spieler drückt **A** (ID=1 setzen) | Spieler zeigt kurz „1" |
| 2 | Spielleiter drückt **A** (Runde starten) | Beide zeigen dasselbe Signal (Farbe, Muster, Ton) |
| 3 | Spieler drückt die **richtige Taste** | Spielleiter zeigt „S1", Spieler zeigt ✓ (grün) |
| 4 | Spielleiter drückt erneut **A** | Nächste Runde mit zufälligem Signal |
| 5 | Spieler drückt die **falsche Taste** | Spieler sieht ✕ (rot) |
| 6 | Nach 5 Sekunden keine Antwort | Spielleiter zeigt „---" (Zeit abgelaufen) |

### Checkliste

- [ ] Signal erscheint auf beiden Displays gleichzeitig (Farbe + LED + Ton)?
- [ ] Spieler kann nur einmal pro Runde antworten?
- [ ] Richtiger Spieler bekommt Bestätigung (✓), falscher bekommt Kreuz (✕)?
- [ ] Nach 5 Sekunden läuft die Runde ab?

---

## 📋 Zusammenfassung Schritt 2

| Gerät | Was ist jetzt fertig |
|-------|---------------------|
| **Spielleiter** | Zufälliges Signal senden, Antworten decodieren, richtig/falsch unterscheiden, Feedback per Funk |
| **Spieler** | Signal anzeigen, codierte Antwort senden (ID×10+Aktion), Feedback empfangen |

**⏭ Weiter:** [Schritt 3 – Punkte & Spielende](04_schritt3.md)
