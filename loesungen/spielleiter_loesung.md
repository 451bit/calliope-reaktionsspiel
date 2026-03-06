# Lösung: Spielleiter – vollständiger JavaScript-Code

> **Hinweis für Lehrkräfte:** Diese Datei enthält den vollständigen Referenz-Code. Schülerinnen und Schüler sollten die Lösung erst konsultieren, nachdem sie das Problem selbst versucht haben zu lösen.

---

## So verwendest du den Code in MakeCode

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Erstelle ein neues Projekt
3. Klicke unten links auf **„JavaScript"** (statt „Blöcke")
4. Lösche den bestehenden Code
5. Kopiere den Code unten hinein
6. Klicke auf **„Blöcke"**, um die Blöcke-Ansicht zu sehen
7. Übertrage das Programm auf den Calliope

---

## Vollständiger Code: Spielleiter

```javascript
// ============================================
// CALLIOPE REAKTIONSSPIEL – SPIELLEITER
// Klasse 10 Informatik
// ============================================

// --- Variablen ---
let punkteS1 = 0
let punkteS2 = 0
let punkteS3 = 0
let punkteS4 = 0
let aktuelleAufgabe = 0
let rundeAktiv = false
let rundeGewonnen = false
let aktuelleRunde = 0
let rundenAnzahl = 5
let rundenStartzeit = 0

// --- Initialisierung ---
radio.setGroup(1)
radio.setTransmitPower(7)
punkteS1 = 0
punkteS2 = 0
punkteS3 = 0
punkteS4 = 0
aktuelleAufgabe = 0
rundeAktiv = false
rundeGewonnen = false
aktuelleRunde = 0
basic.showIcon(IconNames.Yes)

// --- Taste A: Runde starten ---
input.onButtonPressed(Button.A, function () {
    if (!rundeAktiv) {
        rundeStarten()
    }
})

// --- Taste B: Punkte anzeigen ---
input.onButtonPressed(Button.B, function () {
    if (!rundeAktiv) {
        basic.showString("S1:")
        basic.showNumber(punkteS1)
        basic.pause(300)
        basic.showString("S2:")
        basic.showNumber(punkteS2)
        basic.pause(300)
        basic.showString("S3:")
        basic.showNumber(punkteS3)
        basic.pause(300)
        basic.showString("S4:")
        basic.showNumber(punkteS4)
        basic.pause(300)
        basic.clearScreen()
    }
})

// --- Funk: Zahl empfangen ---
radio.onReceivedNumber(function (receivedNumber) {
    if (rundeAktiv && !rundeGewonnen) {
        antwortVerarbeiten(receivedNumber)
    }
})

// === FUNKTIONEN ===

function rundeStarten() {
    rundeAktiv = true
    rundeGewonnen = false
    // Zufällige Aufgabe (1 = A, 2 = B, 3 = A+B)
    aktuelleAufgabe = Math.randomRange(1, 3)
    signalAnzeigen()
    // Signal per Funk senden
    radio.sendNumber(aktuelleAufgabe)
    rundenStartzeit = input.runningTime()
    // 5 Sekunden auf Antworten warten
    basic.pause(5000)
    // Niemand hat gewonnen?
    if (!rundeGewonnen) {
        basic.showString("---")
        calliope.setRGBLed(0, 0, 0)
    }
    aktuelleRunde += 1
    rundeAktiv = false
    // Spielende prüfen
    if (aktuelleRunde >= rundenAnzahl) {
        spielEnde()
    }
}

function signalAnzeigen() {
    if (aktuelleAufgabe == 1) {
        // Grün + hoher Ton → Taste A
        calliope.setRGBLed(0, 255, 0)
        basic.showLeds(`
            # . . . #
            # . . . #
            # # # # #
            # . . . #
            # . . . #
            `)
        music.playTone(Note.A5, music.beat(BeatFraction.Whole))
    } else if (aktuelleAufgabe == 2) {
        // Rot + tiefer Ton → Taste B
        calliope.setRGBLed(255, 0, 0)
        basic.showLeds(`
            # # # . .
            # . . # .
            # # # . .
            # . . # .
            # # # . .
            `)
        music.playTone(Note.A3, music.beat(BeatFraction.Whole))
    } else if (aktuelleAufgabe == 3) {
        // Blau + mittlerer Ton → A+B gleichzeitig
        calliope.setRGBLed(0, 0, 255)
        basic.showIcon(IconNames.Heart)
        music.playTone(Note.A4, music.beat(BeatFraction.Whole))
    }
}

function antwortVerarbeiten(nachricht: number) {
    let spielerID = Math.floor(nachricht / 10)
    let aktion = nachricht % 10
    // Gültigkeitsprüfung
    if (spielerID < 1 || spielerID > 4) return
    if (aktion == aktuelleAufgabe) {
        richtigGeantwortet(spielerID)
    } else {
        falschGeantwortet(spielerID)
    }
}

function richtigGeantwortet(siegerID: number) {
    rundeGewonnen = true
    // Antwortzeit berechnen
    let antwortzeit = input.runningTime() - rundenStartzeit
    // Punkte je nach Geschwindigkeit
    let bonus = 1
    if (antwortzeit < 1000) {
        bonus = 3
    } else if (antwortzeit < 3000) {
        bonus = 2
    }
    // Punkte vergeben
    if (siegerID == 1) { punkteS1 += bonus }
    else if (siegerID == 2) { punkteS2 += bonus }
    else if (siegerID == 3) { punkteS3 += bonus }
    else { punkteS4 += bonus }
    // Gewinner anzeigen
    calliope.setRGBLed(0, 255, 0)
    basic.showString("S" + siegerID)
    // Gewinner per Funk melden
    radio.sendValue("gewinner", siegerID)
    basic.pause(2000)
    calliope.setRGBLed(0, 0, 0)
    basic.clearScreen()
}

function falschGeantwortet(spielerID: number) {
    // Punkt abziehen
    if (spielerID == 1) { punkteS1 -= 1 }
    else if (spielerID == 2) { punkteS2 -= 1 }
    else if (spielerID == 3) { punkteS3 -= 1 }
    else { punkteS4 -= 1 }
    // Spieler informieren
    radio.sendValue("falsch", spielerID)
}

function spielEnde() {
    // Gesamtsieger ermitteln
    let maxPunkte = punkteS1
    let gesamtsiegerID = 1
    if (punkteS2 > maxPunkte) { maxPunkte = punkteS2; gesamtsiegerID = 2 }
    if (punkteS3 > maxPunkte) { maxPunkte = punkteS3; gesamtsiegerID = 3 }
    if (punkteS4 > maxPunkte) { maxPunkte = punkteS4; gesamtsiegerID = 4 }
    // Gleichstand prüfen
    let gleichstand = 0
    if (punkteS1 == maxPunkte) gleichstand++
    if (punkteS2 == maxPunkte) gleichstand++
    if (punkteS3 == maxPunkte) gleichstand++
    if (punkteS4 == maxPunkte) gleichstand++
    // Ergebnis per Funk senden
    radio.sendValue("ende", gesamtsiegerID)
    // Ergebnis anzeigen
    calliope.setRGBLed(255, 200, 0)
    basic.showString("ENDE!")
    basic.pause(500)
    if (gleichstand > 1) {
        basic.showString("DRAW!")
    } else {
        basic.showString("S" + gesamtsiegerID + " WIN!")
    }
    basic.pause(1000)
    // Einzelpunktstände anzeigen
    basic.showString("1:" + punkteS1 + " 2:" + punkteS2)
    basic.showString("3:" + punkteS3 + " 4:" + punkteS4)
    // Balkendiagramm auf Matrix
    basic.pause(1000)
    punkteVisualisieren()
    basic.pause(5000)
    // Spiel zurücksetzen
    aktuelleRunde = 0
    punkteS1 = 0
    punkteS2 = 0
    punkteS3 = 0
    punkteS4 = 0
    calliope.setRGBLed(0, 0, 0)
    basic.clearScreen()
    basic.showIcon(IconNames.Yes)
}

function punkteVisualisieren() {
    basic.clearScreen()
    zeichneSpalte(0, punkteS1)
    zeichneSpalte(1, punkteS2)
    zeichneSpalte(2, punkteS3)
    zeichneSpalte(3, punkteS4)
}

function zeichneSpalte(spalte: number, punkte: number) {
    // Normierung: +2, damit -2..+3 darstellbar ist (0..5 LEDs)
    let hoehe = punkte + 2
    hoehe = Math.max(0, Math.min(5, hoehe))
    // Von unten nach oben LEDs setzen
    for (let y = 0; y < hoehe; y++) {
        led.plot(spalte, 4 - y)
    }
}
```

---

## Hinweise für Lehrkräfte

### Bekannte Einschränkungen

1. **`calliope.setRGBLed(r, g, b)`**: Die genaue Schreibweise der RGB-LED-Funktion kann je nach MakeCode-Version variieren. Alternativ ist die Funktion über `neopixel` oder als Block unter der Calliope-Kategorie zugänglich.

2. **Gleichzeitigkeit**: Der Calliope ist kein Multi-Threading-System. Während `basic.pause(5000)` läuft, werden Funk-Ereignisse **gepuffert** und danach verarbeitet. Das bedeutet: Antworten kommen zuverlässig an, aber nicht immer in Echtzeit.

3. **Funk-Reichweite**: Im Klassenraum ist Gruppe 1 ausreichend. Bei vielen parallel laufenden Calliopes im Raum → andere Gruppen wählen (z. B. 11, 22, 33...).

### Didaktische Hinweise

- **Debugging**: Füge `basic.showNumber(receivedNumber)` temporär in den Funk-Empfänger ein, um Nachrichten sichtbar zu machen
- **Differenzierung**: Schnellere Schüler können mit Kapitel 5 starten; langsamere Schüler können den JavaScript-Code als Ausgangspunkt nehmen und ihn in Blöcke übersetzen
