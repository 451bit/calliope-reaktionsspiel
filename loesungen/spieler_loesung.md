# Lösung: Spieler – vollständiger Python-Code (MakeCode)

> **Hinweis für Lehrkräfte:** Diese Datei enthält den vollständigen Referenz-Code. Schülerinnen und Schüler sollten die Lösung erst konsultieren, nachdem sie das Problem selbst versucht haben zu lösen.

---

## So verwendest du den Code in MakeCode

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Erstelle ein **neues Projekt** (separate Datei vom Spielleiter!)
3. Klicke unten in der Mitte auf **„Python"** (neben „Blöcke" und „JavaScript")
4. Lösche den bestehenden Code vollständig und füge den Code unten ein
5. Übertrage das Programm auf die Spieler-Calliopes

> **Wichtig:** Jeder Spieler bekommt **dasselbe Programm** – die ID wird erst beim Start per Tastendruck festgelegt!

---

## Vollständiger Code: Spieler

```python
# ============================================
# CALLIOPE REAKTIONSSPIEL – SPIELER
# Klasse 10 Informatik
# MakeCode Python
# Dieses Programm ist identisch für alle Spieler.
# Die Spieler-ID wird per Tastendruck beim Start gewählt.
# ============================================

# --- Variablen ---
meine_id = 0
id_gesetzt = False
aufgabe = 0
habe_gesendet = False

# --- Initialisierung ---
radio.set_group(1)
# Spieler auffordern, ID zu wählen
basic.show_string("ID?")

# ============================================
# ID-AUSWAHL (vor dem Spielstart)
# A = Spieler 1 | B = Spieler 2
# A+B = Spieler 3 | nochmal A wenn ID=3 → Spieler 4
# ============================================

@input.on_button_pressed(Button.A)
def on_button_a():
    global meine_id, id_gesetzt, habe_gesendet
    if not id_gesetzt:
        meine_id = 1
        id_gesetzt = True
        basic.show_number(1)
        basic.pause(1000)
        basic.clear_screen()
    elif meine_id == 3:
        # Upgrade: ID 3 → ID 4
        meine_id = 4
        basic.show_number(4)
        basic.pause(1000)
        basic.clear_screen()
    elif aufgabe > 0 and not habe_gesendet:
        # Im Spiel: Aktion 1 (Taste A gedrückt) senden
        habe_gesendet = True
        radio.send_number(meine_id * 10 + 1)
        basic.show_arrow(ArrowNames.NORTH)
        basic.pause(500)
        basic.clear_screen()


@input.on_button_pressed(Button.B)
def on_button_b():
    global meine_id, id_gesetzt, habe_gesendet
    if not id_gesetzt:
        meine_id = 2
        id_gesetzt = True
        basic.show_number(2)
        basic.pause(1000)
        basic.clear_screen()
    elif aufgabe > 0 and not habe_gesendet:
        # Im Spiel: Aktion 2 (Taste B gedrückt) senden
        habe_gesendet = True
        radio.send_number(meine_id * 10 + 2)
        basic.show_arrow(ArrowNames.NORTH)
        basic.pause(500)
        basic.clear_screen()


@input.on_button_pressed(Button.AB)
def on_button_ab():
    global meine_id, id_gesetzt, habe_gesendet
    if not id_gesetzt:
        meine_id = 3
        id_gesetzt = True
        basic.show_number(3)
        basic.pause(1000)
        basic.clear_screen()
    elif aufgabe > 0 and not habe_gesendet:
        # Im Spiel: Aktion 3 (A+B gleichzeitig) senden
        habe_gesendet = True
        radio.send_number(meine_id * 10 + 3)
        basic.show_arrow(ArrowNames.NORTH)
        basic.pause(500)
        basic.clear_screen()

# ============================================
# NEIGUNGSAKTIONEN (Erweiterung aus Kapitel 5)
# Kommentiere diese Blöcke aus, wenn du die
# Basis-Version (nur Tasten) möchtest
# ============================================

def on_forever():
    global habe_gesendet
    if id_gesetzt and aufgabe > 0 and not habe_gesendet:
        x = input.acceleration(Dimension.X)
        if x < -400:
            # Links geneigt → Aktion 4
            habe_gesendet = True
            radio.send_number(meine_id * 10 + 4)
            basic.show_arrow(ArrowNames.WEST)
            basic.pause(500)
            basic.clear_screen()
        elif x > 400:
            # Rechts geneigt → Aktion 5
            habe_gesendet = True
            radio.send_number(meine_id * 10 + 5)
            basic.show_arrow(ArrowNames.EAST)
            basic.pause(500)
            basic.clear_screen()

basic.forever(on_forever)

# ============================================
# FUNK-EMPFANG: Signal vom Spielleiter (Zahl 1-5)
# ============================================

@radio.on_received_number
def on_received_number(received_number: int):
    global aufgabe, habe_gesendet
    if not id_gesetzt:
        return
    # Ist es ein gültiger Signal-Code (1-5)?
    if received_number >= 1 and received_number <= 5:
        aufgabe = received_number
        habe_gesendet = False
        signal_anzeigen()

# ============================================
# FUNK-EMPFANG: Feedback vom Spielleiter (Name+Wert)
# ============================================

@radio.on_received_value
def on_received_value(name: str, value: int):
    global aufgabe, habe_gesendet, id_gesetzt
    if not id_gesetzt:
        return

    if name == "gewinner":
        if value == meine_id:
            # Ich habe gewonnen!
            calliope.set_rgb_led(0, 255, 0)
            basic.show_icon(IconNames.YES)
            music.play_tone(Note.A5, music.beat(BeatFraction.DOUBLE))
            basic.pause(2000)
        else:
            # Jemand anderes hat gewonnen
            calliope.set_rgb_led(0, 0, 0)
            basic.show_string("S" + str(value))
            basic.pause(2000)
        aufgabe = 0
        calliope.set_rgb_led(0, 0, 0)
        basic.clear_screen()

    elif name == "falsch":
        if value == meine_id:
            # Ich war falsch!
            calliope.set_rgb_led(255, 0, 0)
            basic.show_icon(IconNames.NO)
            music.play_tone(Note.A2, music.beat(BeatFraction.WHOLE))
            basic.pause(1500)
            calliope.set_rgb_led(0, 0, 0)
            basic.clear_screen()

    elif name == "ende":
        # Spielende
        basic.show_string("ENDE!")
        basic.pause(1000)
        if value == meine_id:
            # Ich habe das Gesamtspiel gewonnen!
            calliope.set_rgb_led(255, 200, 0)
            basic.show_string("WIN!")
            music.begin_melody(music.built_in_melody(Melodies.POWER_UP), MelodyOptions.ONCE)
        else:
            calliope.set_rgb_led(0, 0, 0)
            basic.show_string("S" + str(value) + " WIN!")
        basic.pause(3000)
        # Zurücksetzen
        calliope.set_rgb_led(0, 0, 0)
        basic.clear_screen()
        aufgabe = 0
        habe_gesendet = False
        # ID muss neu gewählt werden
        # (optional: ID beibehalten → id_gesetzt = True lassen)
        basic.show_string("ID?")

# ============================================
# SIGNALANZEIGE (übereinstimmend mit Spielleiter!)
# ============================================

def signal_anzeigen():
    if aufgabe == 1:
        # Grünes Licht + hoher Ton → Taste A
        calliope.set_rgb_led(0, 255, 0)
        basic.show_leds("""
            # . . . #
            # . . . #
            # # # # #
            # . . . #
            # . . . #
            """)
        music.play_tone(Note.A5, music.beat(BeatFraction.HALF))
    elif aufgabe == 2:
        # Rotes Licht + tiefer Ton → Taste B
        calliope.set_rgb_led(255, 0, 0)
        basic.show_leds("""
            # # # . .
            # . . # .
            # # # . .
            # . . # .
            # # # . .
            """)
        music.play_tone(Note.A3, music.beat(BeatFraction.HALF))
    elif aufgabe == 3:
        # Blaues Licht + mittlerer Ton → A+B drücken
        calliope.set_rgb_led(0, 0, 255)
        basic.show_icon(IconNames.HEART)
        music.play_tone(Note.A4, music.beat(BeatFraction.HALF))
    elif aufgabe == 4:
        # Gelbes Licht + tiefer Ton → links neigen
        calliope.set_rgb_led(255, 200, 0)
        basic.show_arrow(ArrowNames.WEST)
        music.play_tone(Note.A2, music.beat(BeatFraction.HALF))
    elif aufgabe == 5:
        # Orange Licht + hoher Ton → rechts neigen
        calliope.set_rgb_led(255, 100, 0)
        basic.show_arrow(ArrowNames.EAST)
        music.play_tone(Note.A6, music.beat(BeatFraction.HALF))
```

---

## Häufige Fehler und Debugging-Tipps

### Problem: Spieler sendet mehrfach

**Ursache:** Das `habe_gesendet`-Flag wird nicht korrekt gesetzt oder zurückgesetzt.  
**Lösung:** Prüfe, ob `habe_gesendet = True` am Anfang jedes Aktionsblocks steht, **bevor** der Funk-Sendebefehl kommt.

### Problem: ID 4 kann nicht gewählt werden

**Ursache:** Der Button-A-Handler muss die aktuelle ID prüfen.  
**Lösung:** Stelle sicher, dass der `elif meine_id == 3`-Zweig **vor** dem Spielaktions-Code steht.

### Problem: Spieler reagiert auf Signale ohne ID

**Ursache:** Fehlende `id_gesetzt`-Prüfung im Funkempfänger.  
**Lösung:** Prüfe `if not id_gesetzt: return` als erstes in allen Funk-Handlern.

### Problem: Neigung wird zu früh ausgelöst (im `on_forever`-Block)

**Ursache:** Der Schwellenwert ±400 mg ist zu niedrig.  
**Lösung:** Erhöhe den Schwellenwert auf ±600 oder ±700 mg und teste mit eurem Calliope.

### Problem: `UnboundLocalError` oder Variable wird nicht verändert

**Ursache:** In Python muss jede Variable, die in einer Funktion **verändert** wird, mit `global` deklariert sein.  
**Lösung:** Füge am Funktionsanfang z. B. `global habe_gesendet, aufgabe` ein.

| Python-Besonderheit | Falsch | Richtig |
|---|---|---|
| Globale Variable ändern | `habe_gesendet = True` (ohne `global`) | `global habe_gesendet` zuerst |
| Negation | `!id_gesetzt` | `not id_gesetzt` |
| Logisches Und | `id_gesetzt && aufgabe > 0` | `id_gesetzt and aufgabe > 0` |
| Wahrheitswerte | `true` / `false` | `True` / `False` |
| Methode aufrufen | `basic.showString(...)` | `basic.show_string(...)` |

---

## Didaktische Hinweise

- **Differenzierung**: Schülerinnen und Schüler können den Code als Ausgangspunkt nehmen und dann einzelne Funktionen in Blöcken implementieren
- **Erweiterung**: Zeige den Schülerinnen und Schülern, wie man mit `input.acceleration(Dimension.X)` im Simulator experimentieren kann
- **Teamarbeit**: Spielleiter und Spieler-Code können von verschiedenen Teams entwickelt werden – solange das Protokoll vorab geklärt ist
