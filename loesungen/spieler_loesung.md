# Lösung: Spieler – vollständiger JavaScript-Code

> **Hinweis für Lehrkräfte:** Diese Datei enthält den vollständigen Referenz-Code. Schülerinnen und Schüler sollten die Lösung erst konsultieren, nachdem sie das Problem selbst versucht haben zu lösen.

---

## So verwendest du den Code in MakeCode

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Erstelle ein **neues Projekt** (separate Datei vom Spielleiter!)
3. Klicke auf **„JavaScript"**
4. Lösche den bestehenden Code und füge den Code unten ein
5. Übertrage das Programm auf die Spieler-Calliopes

> **Wichtig:** Jeder Spieler bekommt **dasselbe Programm** – die ID wird erst beim Start per Tastendruck festgelegt!

---

## Vollständiger Code: Spieler

```javascript
// ============================================
// CALLIOPE REAKTIONSSPIEL – SPIELER
// Klasse 10 Informatik
// Dieses Programm ist identisch für alle Spieler.
// Die Spieler-ID wird per Tastendruck beim Start gewählt.
// ============================================

// --- Variablen ---
let meineID = 0
let idGesetzt = false
let aufgabe = 0
let habeGesendet = false

// --- Initialisierung ---
radio.setGroup(1)
meineID = 0
idGesetzt = false
aufgabe = 0
habeGesendet = false
// Spieler auffordern, ID zu wählen
basic.showString("ID?")

// ============================================
// ID-AUSWAHL (vor dem Spielstart)
// A = Spieler 1
// B = Spieler 2
// A+B = Spieler 3
// Dann nochmal A wenn ID=3 → Spieler 4
// ============================================

input.onButtonPressed(Button.A, function () {
    if (!idGesetzt) {
        // ID 1 setzen
        meineID = 1
        idGesetzt = true
        basic.showNumber(1)
        basic.pause(1000)
        basic.clearScreen()
    } else if (meineID == 3) {
        // Upgrade: ID 3 → ID 4
        meineID = 4
        basic.showNumber(4)
        basic.pause(1000)
        basic.clearScreen()
    } else if (aufgabe > 0 && !habeGesendet) {
        // Im Spiel: Aktion 1 (Taste A gedrückt) senden
        habeGesendet = true
        radio.sendNumber(meineID * 10 + 1)
        basic.showArrow(ArrowNames.North)
        basic.pause(500)
        basic.clearScreen()
    }
})

input.onButtonPressed(Button.B, function () {
    if (!idGesetzt) {
        // ID 2 setzen
        meineID = 2
        idGesetzt = true
        basic.showNumber(2)
        basic.pause(1000)
        basic.clearScreen()
    } else if (aufgabe > 0 && !habeGesendet) {
        // Im Spiel: Aktion 2 (Taste B gedrückt) senden
        habeGesendet = true
        radio.sendNumber(meineID * 10 + 2)
        basic.showArrow(ArrowNames.North)
        basic.pause(500)
        basic.clearScreen()
    }
})

input.onButtonPressed(Button.AB, function () {
    if (!idGesetzt) {
        // ID 3 setzen
        meineID = 3
        idGesetzt = true
        basic.showNumber(3)
        basic.pause(1000)
        basic.clearScreen()
    } else if (aufgabe > 0 && !habeGesendet) {
        // Im Spiel: Aktion 3 (A+B gleichzeitig) senden
        habeGesendet = true
        radio.sendNumber(meineID * 10 + 3)
        basic.showArrow(ArrowNames.North)
        basic.pause(500)
        basic.clearScreen()
    }
})

// ============================================
// NEIGUNGSAKTIONEN (Erweiterung aus Kapitel 5)
// Kommentiere diese Blöcke aus, wenn du die
// Basis-Version (nur Tasten) möchtest
// ============================================

basic.forever(function () {
    if (idGesetzt && aufgabe > 0 && !habeGesendet) {
        let x = input.acceleration(Dimension.X)
        if (x < -400) {
            // Links geneigt → Aktion 4
            habeGesendet = true
            radio.sendNumber(meineID * 10 + 4)
            basic.showArrow(ArrowNames.West)
            basic.pause(500)
            basic.clearScreen()
        } else if (x > 400) {
            // Rechts geneigt → Aktion 5
            habeGesendet = true
            radio.sendNumber(meineID * 10 + 5)
            basic.showArrow(ArrowNames.East)
            basic.pause(500)
            basic.clearScreen()
        }
    }
})

// ============================================
// FUNK-EMPFANG: Signal vom Spielleiter (Zahl 1-5)
// ============================================

radio.onReceivedNumber(function (receivedNumber) {
    if (!idGesetzt) return
    // Ist es ein gültiger Signal-Code (1-5)?
    if (receivedNumber >= 1 && receivedNumber <= 5) {
        aufgabe = receivedNumber
        habeGesendet = false
        signalAnzeigen()
    }
})

// ============================================
// FUNK-EMPFANG: Feedback vom Spielleiter (Name+Wert)
// ============================================

radio.onReceivedValue(function (name, value) {
    if (!idGesetzt) return

    if (name == "gewinner") {
        if (value == meineID) {
            // Ich habe gewonnen!
            calliope.setRGBLed(0, 255, 0)
            basic.showIcon(IconNames.Yes)
            music.playTone(Note.A5, music.beat(BeatFraction.Double))
            basic.pause(2000)
        } else {
            // Jemand anderes hat gewonnen
            calliope.setRGBLed(0, 0, 0)
            basic.showString("S" + value)
            basic.pause(2000)
        }
        aufgabe = 0
        calliope.setRGBLed(0, 0, 0)
        basic.clearScreen()

    } else if (name == "falsch") {
        if (value == meineID) {
            // Ich war falsch!
            calliope.setRGBLed(255, 0, 0)
            basic.showIcon(IconNames.No)
            music.playTone(Note.A2, music.beat(BeatFraction.Whole))
            basic.pause(1500)
            calliope.setRGBLed(0, 0, 0)
            basic.clearScreen()
        }

    } else if (name == "ende") {
        // Spielende
        basic.showString("ENDE!")
        basic.pause(1000)
        if (value == meineID) {
            // Ich habe das Gesamtspiel gewonnen!
            calliope.setRGBLed(255, 200, 0)
            basic.showString("WIN!")
            music.beginMelody(music.builtInMelody(Melodies.PowerUp), MelodyOptions.Once)
        } else {
            calliope.setRGBLed(0, 0, 0)
            basic.showString("S" + value + " WIN!")
        }
        basic.pause(3000)
        // Zurücksetzen
        calliope.setRGBLed(0, 0, 0)
        basic.clearScreen()
        aufgabe = 0
        habeGesendet = false
        // ID muss neu gewählt werden
        // (optional: ID beibehalten → idGesetzt = true lassen)
        basic.showString("ID?")
    }
})

// ============================================
// SIGNALANZEIGE (übereinstimmend mit Spielleiter!)
// ============================================

function signalAnzeigen() {
    if (aufgabe == 1) {
        // Grünes Licht + hoher Ton → Taste A
        calliope.setRGBLed(0, 255, 0)
        basic.showLeds(`
            # . . . #
            # . . . #
            # # # # #
            # . . . #
            # . . . #
            `)
        music.playTone(Note.A5, music.beat(BeatFraction.Half))
    } else if (aufgabe == 2) {
        // Rotes Licht + tiefer Ton → Taste B
        calliope.setRGBLed(255, 0, 0)
        basic.showLeds(`
            # # # . .
            # . . # .
            # # # . .
            # . . # .
            # # # . .
            `)
        music.playTone(Note.A3, music.beat(BeatFraction.Half))
    } else if (aufgabe == 3) {
        // Blaues Licht + mittlerer Ton → A+B drücken
        calliope.setRGBLed(0, 0, 255)
        basic.showIcon(IconNames.Heart)
        music.playTone(Note.A4, music.beat(BeatFraction.Half))
    } else if (aufgabe == 4) {
        // Gelbes Licht + tiefer Ton → links neigen
        calliope.setRGBLed(255, 200, 0)
        basic.showArrow(ArrowNames.West)
        music.playTone(Note.A2, music.beat(BeatFraction.Half))
    } else if (aufgabe == 5) {
        // Orange Licht + hoher Ton → rechts neigen
        calliope.setRGBLed(255, 100, 0)
        basic.showArrow(ArrowNames.East)
        music.playTone(Note.A6, music.beat(BeatFraction.Half))
    }
}
```

---

## Häufige Fehler und Debugging-Tipps

### Problem: Spieler sendet mehrfach

**Ursache:** Das `habeGesendet`-Flag wird nicht korrekt gesetzt oder zurückgesetzt.  
**Lösung:** Prüfe, ob `habeGesendet = true` am Anfang jedes Aktionsblocks steht, **bevor** der Funk-Sendebefehl kommt.

### Problem: ID 4 kann nicht gewählt werden

**Ursache:** Der Button-A-Handler muss die aktuelle ID prüfen.  
**Lösung:** Stelle sicher, dass der Code `else if (meineID == 3)` **vor** dem Spielaktions-Code steht.

### Problem: Spieler reagiert auf Signale ohne ID

**Ursache:** Fehlende `idGesetzt`-Prüfung im Funkempfänger.  
**Lösung:** Prüfe `if (!idGesetzt) return` als erstes in allen Funk-Handlern.

### Problem: Neigung wird zu früh ausgelöst (im `dauerhaft`-Block)

**Ursache:** Der Schwellenwert ±400 mg ist zu niedrig.  
**Lösung:** Erhöhe den Schwellenwert auf ±600 oder ±700 mg und teste mit eurem Calliope.

---

## Didaktische Hinweise

- **Differenzierung**: Schülerinnen und Schüler können den Code als Ausgangspunkt nehmen und dann einzelne Funktionen in Blöcken implementieren
- **Erweiterung**: Zeige den Schülerinnen und Schülern, wie man mit `input.acceleration(Dimension.X)` im Simulator experimentieren kann
- **Teamarbeit**: Spielleiter und Spieler-Code können von verschiedenen Teams entwickelt werden – solange das Protokoll vorab geklärt ist
