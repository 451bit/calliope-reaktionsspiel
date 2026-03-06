# Lösung: Spielleiter – vollständiger Python-Code (MakeCode)

> **Hinweis für Lehrkräfte:** Diese Datei enthält den vollständigen Referenz-Code. Schülerinnen und Schüler sollten die Lösung erst konsultieren, nachdem sie das Problem selbst versucht haben zu lösen.

---

## So verwendest du den Code in MakeCode

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Erstelle ein neues Projekt: **„+ Neues Projekt"**
3. Klicke unten in der Mitte auf **„Python"** (neben „Blöcke" und „JavaScript")
4. Lösche den bestehenden Code vollständig
5. Kopiere den Code unten hinein
6. Klicke auf **„Herunterladen"** → `.hex`-Datei auf den Calliope ziehen
7. Optional: Klicke auf **„Blöcke"**, um die automatisch erzeugten Blöcke zu sehen

> **💡 Tipp:** MakeCode übersetzt den Python-Code intern in JavaScript und dann in Maschinencode. Du kannst jederzeit zwischen Python- und Blöcke-Ansicht wechseln.

---

## Vollständiger Code: Spielleiter

```python
# ============================================
# CALLIOPE REAKTIONSSPIEL – SPIELLEITER
# Klasse 10 Informatik
# MakeCode Python
# ============================================

# --- Variablen ---
punkte_s1 = 0
punkte_s2 = 0
punkte_s3 = 0
punkte_s4 = 0
aktuelle_aufgabe = 0
runde_aktiv = False
runde_gewonnen = False
aktuelle_runde = 0
runden_anzahl = 5
runden_startzeit = 0

# --- Initialisierung ---
radio.set_group(1)
radio.set_transmit_power(7)
basic.show_icon(IconNames.YES)

# ============================================
# TASTE A → Runde starten
# ============================================

def on_button_a():
    if not runde_aktiv:
        runde_starten()
input.on_button_pressed(Button.A, on_button_a)


# ============================================
# TASTE B → Punktestand anzeigen
# ============================================

def on_button_b():
    if not runde_aktiv:
        basic.show_string("S1:")
        basic.show_number(punkte_s1)
        basic.pause(300)
        basic.show_string("S2:")
        basic.show_number(punkte_s2)
        basic.pause(300)
        basic.show_string("S3:")
        basic.show_number(punkte_s3)
        basic.pause(300)
        basic.show_string("S4:")
        basic.show_number(punkte_s4)
        basic.pause(300)
        basic.clear_screen()
input.on_button_pressed(Button.B, on_button_b)


# ============================================
# FUNK: Zahl empfangen (Spieler-Antworten)
# ============================================

def on_received_number(received_number):
    if runde_aktiv and not runde_gewonnen:
        antwort_verarbeiten(received_number)
radio.on_received_number(on_received_number)


# === FUNKTIONEN ===

def runde_starten():
    global runde_aktiv, runde_gewonnen, aktuelle_aufgabe
    global aktuelle_runde, runden_startzeit

    runde_aktiv = True
    runde_gewonnen = False
    # Zufällige Aufgabe (1 = A, 2 = B, 3 = A+B)
    aktuelle_aufgabe = randint(1, 3)
    signal_anzeigen()
    # Signal per Funk senden
    radio.send_number(aktuelle_aufgabe)
    runden_startzeit = input.running_time()
    # 5 Sekunden auf Antworten warten
    basic.pause(5000)
    # Niemand hat gewonnen?
    if not runde_gewonnen:
        basic.show_string("---")
        basic.set_led_color(basic.rgb(0, 0, 0))
    aktuelle_runde += 1
    runde_aktiv = False
    # Spielende prüfen
    if aktuelle_runde >= runden_anzahl:
        spiel_ende()


def signal_anzeigen():
    if aktuelle_aufgabe == 1:
        # Grün + hoher Ton → Taste A
        basic.set_led_color(basic.rgb(0, 255, 0))
        basic.show_leds("""
            # . . . #
            # . . . #
            # # # # #
            # . . . #
            # . . . #
            """)
        music.play_tone(Note.A5, music.beat(BeatFraction.WHOLE))
    elif aktuelle_aufgabe == 2:
        # Rot + tiefer Ton → Taste B
        basic.set_led_color(basic.rgb(255, 0, 0))
        basic.show_leds("""
            # # # . .
            # . . # .
            # # # . .
            # . . # .
            # # # . .
            """)
        music.play_tone(Note.A3, music.beat(BeatFraction.WHOLE))
    elif aktuelle_aufgabe == 3:
        # Blau + mittlerer Ton → A+B gleichzeitig
        basic.set_led_color(basic.rgb(0, 0, 255))
        basic.show_icon(IconNames.HEART)
        music.play_tone(Note.A4, music.beat(BeatFraction.WHOLE))


def antwort_verarbeiten(nachricht):
    spieler_id = nachricht // 10
    aktion = nachricht % 10
    # Gültigkeitsprüfung
    if spieler_id < 1 or spieler_id > 4:
        return
    if aktion == aktuelle_aufgabe:
        richtig_geantwortet(spieler_id)
    else:
        falsch_geantwortet(spieler_id)

def richtig_geantwortet(sieger_id):
    global runde_gewonnen
    global punkte_s1, punkte_s2, punkte_s3, punkte_s4

    runde_gewonnen = True
    # Antwortzeit berechnen
    antwortzeit = input.running_time() - runden_startzeit
    # Punkte je nach Geschwindigkeit
    if antwortzeit < 1000:
        bonus = 3
    elif antwortzeit < 3000:
        bonus = 2
    else:
        bonus = 1
    # Punkte vergeben
    if sieger_id == 1:
        punkte_s1 += bonus
    elif sieger_id == 2:
        punkte_s2 += bonus
    elif sieger_id == 3:
        punkte_s3 += bonus
    else:
        punkte_s4 += bonus
    # Gewinner anzeigen
    basic.set_led_color(basic.rgb(0, 255, 0))
    basic.show_string("S" + str(sieger_id))
    # Gewinner per Funk melden
    radio.send_value("gewinner", sieger_id)
    basic.pause(2000)
    basic.set_led_color(basic.rgb(0, 0, 0))
    basic.clear_screen()


def falsch_geantwortet(spieler_id):
    global punkte_s1, punkte_s2, punkte_s3, punkte_s4

    # Punkt abziehen
    if spieler_id == 1:
        punkte_s1 -= 1
    elif spieler_id == 2:
        punkte_s2 -= 1
    elif spieler_id == 3:
        punkte_s3 -= 1
    else:
        punkte_s4 -= 1
    # Spieler informieren
    radio.send_value("falsch", spieler_id)


def spiel_ende():
    global aktuelle_runde
    global punkte_s1, punkte_s2, punkte_s3, punkte_s4

    # Gesamtsieger ermitteln
    max_punkte = punkte_s1
    gesamtsieger = 1
    if punkte_s2 > max_punkte:
        max_punkte = punkte_s2
        gesamtsieger = 2
    if punkte_s3 > max_punkte:
        max_punkte = punkte_s3
        gesamtsieger = 3
    if punkte_s4 > max_punkte:
        max_punkte = punkte_s4
        gesamtsieger = 4
    # Gleichstand prüfen
    gleichstand = 0
    for p in [punkte_s1, punkte_s2, punkte_s3, punkte_s4]:
        if p == max_punkte:
            gleichstand += 1
    # Ergebnis per Funk senden
    radio.send_value("ende", gesamtsieger)
    # Ergebnis anzeigen
    basic.set_led_color(basic.rgb(255, 200, 0))
    basic.show_string("ENDE!")
    basic.pause(500)
    if gleichstand > 1:
        basic.show_string("DRAW!")
    else:
        basic.show_string("S" + str(gesamtsieger) + " WIN!")
    basic.pause(1000)
    # Einzelpunktstände anzeigen
    basic.show_string("1:" + str(punkte_s1) + " 2:" + str(punkte_s2))
    basic.show_string("3:" + str(punkte_s3) + " 4:" + str(punkte_s4))
    # Balkendiagramm auf Matrix
    basic.pause(1000)
    punkte_visualisieren()
    basic.pause(5000)
    # Spiel zurücksetzen
    aktuelle_runde = 0
    punkte_s1 = 0
    punkte_s2 = 0
    punkte_s3 = 0
    punkte_s4 = 0
    basic.set_led_color(basic.rgb(0, 0, 0))
    basic.clear_screen()
    basic.show_icon(IconNames.YES)


def punkte_visualisieren():
    basic.clear_screen()
    zeige_spalte(0, punkte_s1)
    zeige_spalte(1, punkte_s2)
    zeige_spalte(2, punkte_s3)
    zeige_spalte(3, punkte_s4)


def zeige_spalte(spalte, punkte):
    # Normierung: +2, damit -2..+3 darstellbar ist (0..5 LEDs)
    hoehe = punkte + 2
    hoehe = max(0, min(5, hoehe))
    # Von unten nach oben LEDs setzen
    for i in range(hoehe):
        led.plot(spalte, 4 - i)
```

---

## Code und Blöcke im Vergleich

Nach dem Einfügen kannst du in MakeCode auf **„Blöcke"** wechseln – der Code wird automatisch in die entsprechenden Blöcke übersetzt.

### Wichtige Python-Besonderheiten in MakeCode

| Python (MakeCode) | Erklärung |
|-------------------|-----------|
| `input.on_button_pressed(Button.A, handler)` | Registriert `handler` als Funktion, die bei Tastendruck A aufgerufen wird |
| `radio.on_received_number(handler)` | Wird aufgerufen, wenn eine Zahl per Funk empfangen wird |
| `global punkte_s1` | In Python muss man globale Variablen mit `global` deklarieren, bevor man sie in einer Funktion ändert |
| `nachricht // 10` | Ganzzahlige Division (kein Rest) |
| `nachricht % 10` | Modulo – gibt den Rest der Division zurück |
| `randint(1, 3)` | Zufallszahl zwischen 1 und 3 (inklusiv) |

---

## Häufige Fehler

### `global` vergessen

**Problem:** Variable wird in der Funktion nicht verändert – es entsteht eine neue lokale Variable.  
**Lösung:** Schreibe `global variablenname` als erste Zeile in die Funktion.

```python
# Falsch:
def richtig_geantwortet(sieger_id):
    runde_gewonnen = True       # ← erzeugt eine NEUE lokale Variable!

# Richtig:
def richtig_geantwortet(sieger_id):
    global runde_gewonnen
    runde_gewonnen = True       # ← ändert die globale Variable
```

### Einrückung (Indentation)

**Problem:** `IndentationError` in MakeCode  
**Ursache:** Python verwendet Einrückungen (4 Leerzeichen) statt `{}`-Klammern. Alle Zeilen innerhalb einer Funktion oder `if`-Bedingung müssen gleich weit eingerückt sein.

---

## Hinweise für Lehrkräfte

- **Debugging**: Füge `basic.show_number(received_number)` temporär in den Funk-Empfänger ein, um Nachrichten sichtbar zu machen
- **`global`-Deklarationen** eignen sich für eine Diskussion über Gültigkeitsbereiche (Scope) von Variablen
- **Gleichstand-Logik** ist mit einer `for`-Schleife umgesetzt – guter Ausgangspunkt für Schleifen-Diskussionen
- **Funk-Reichweite**: Im Klassenraum ist Gruppe 1 ausreichend. Bei vielen parallelen Calliopes → andere Gruppen wählen (z. B. 11, 22, 33...)
- Die RGB-LED-Funktion `basic.set_led_color(basic.rgb(r, g, b))` kann je nach MakeCode-Version anders heißen; ggf. in der Kategorie **„Calliope"** im Blöcke-Editor den genauen Namen prüfen
