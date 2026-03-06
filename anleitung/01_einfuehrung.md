# Kapitel 1: Einführung – Calliope mini & MakeCode

← [Zurück zur Übersicht](../README.md) | [Weiter: Spielleiter →](02_spielleiter.md)

---

## 🎯 Lernziele dieses Kapitels

- Du kennst den Aufbau des Calliope mini v1
- Du kannst MakeCode öffnen und ein erstes Programm übertragen
- Du verstehst, wie Funk zwischen Calliopes funktioniert
- Du kannst einfache Blöcke für Ausgabe und Eingabe einsetzen

---

## 1.1 Der Calliope mini v1 – ein Überblick

Der **Calliope mini** ist ein kleiner Einplatinencomputer, der speziell für den Schulunterricht entwickelt wurde. Die Version 1 hat folgende wichtige Komponenten:

```
         [ USB-Anschluss ]
              ↑
    ┌─────────────────────┐
    │  ● ● ● ● ●          │  ← 5×5 LED-Matrix
    │  ● ● ● ● ●          │
    │  ● ● ● ● ●          │
    │  ● ● ● ● ●          │
    │  ● ● ● ● ●          │
    │                     │
    │  [A]         [B]    │  ← Tasten A und B
    │       [RGB]         │  ← RGB-LED (bunt!)
    │   Lautsprecher ♪    │
    └─────────────────────┘
```

### Wichtige Bauteile:

| Bauteil | Beschreibung | Im Spiel genutzt für... |
|---------|-------------|------------------------|
| **5×5 LED-Matrix** | 25 rote LEDs, frei programmierbar | Symbole, Buchstaben, Punktestand anzeigen |
| **RGB-LED** | 1 LED in beliebiger Farbe | Farbsignal für das Spiel |
| **Taste A** | linker Druckknopf | Spieleraktion: „A drücken" |
| **Taste B** | rechter Druckknopf | Spieleraktion: „B drücken" |
| **Lautsprecher** | integrierter Piezo-Speaker | Ton-Signale im Spiel |
| **Funk (Radio)** | 2,4-GHz-Funkmodul | Kommunikation zwischen Calliopes |
| **Beschleunigungssensor** | misst Neigung und Bewegung | optionale Spieleraktionen |

---

## 1.2 MakeCode öffnen und einrichten

**MakeCode** ist eine grafische Programmierumgebung im Browser. Dort setzt du Blöcke zusammen, ähnlich wie bei Scratch – aber direkt für den Calliope.

### Schritt-für-Schritt:

**1. Browser öffnen**  
Öffne **Chrome** oder **Edge** (Firefox hat manchmal Probleme mit der USB-Verbindung).

**2. Adresse eingeben**  
→ [https://makecode.calliope.cc](https://makecode.calliope.cc)

**3. Neues Projekt erstellen**  
Klicke auf **„+ Neues Projekt"** und gib ihm einen Namen, z. B. `Spielleiter` oder `Spieler`.

**4. Oberfläche kennenlernen**

```
┌──────────────┬────────────────────────────┬─────────────────┐
│   Simulator  │      Block-Kategorien      │   Arbeitsbereich│
│  (Vorschau)  │  Grundlagen  Eingabe  ...  │   (hier baust   │
│              │  Musik       Funk          │    du dein      │
│   [Calliope] │  Variablen   Logik    ...  │    Programm)    │
└──────────────┴────────────────────────────┴─────────────────┘
```

**5. Programm auf den Calliope übertragen**
1. Calliope per USB-Kabel mit dem Computer verbinden
2. Auf **„Herunterladen"** klicken (unten links, blaue Schaltfläche)
3. Die heruntergeladene `.hex`-Datei in den Calliope-Ordner ziehen (erscheint wie ein USB-Stick)
4. Die gelbe LED auf dem Calliope blinkt – wenn sie aufhört, ist das Programm gespeichert

> **💡 Tipp:** In neueren Browsern (Chrome) kann man den Calliope direkt verbinden und muss die Datei nicht manuell verschieben (WebUSB). Klicke nach dem Download auf „Mit Gerät verbinden".

---

## 1.3 Erstes Programm: Hallo, Calliope!

Bevor das Spiel startet, üben wir kurz die Grundlagen.

### Aufgabe 1.1: Begrüßung anzeigen

**Ziel:** Beim Start soll der Calliope deinen Namen anzeigen.

**So geht's:**
1. Öffne MakeCode mit einem neuen Projekt
2. Du siehst bereits zwei Blöcke: `beim Start` und `dauerhaft`
3. Klicke auf die Kategorie **„Grundlagen"**
4. Ziehe den Block **„Zeige Zeichenkette"** in den `beim Start`-Block
5. Klick auf den Text im Block und schreibe deinen Namen
6. Lade das Programm auf den Calliope

**Ergebnis:** Der Calliope zeigt deinen Namen als Lauftext an.

---

### Aufgabe 1.2: Auf Tastendruck reagieren

**Ziel:** Wenn Taste A gedrückt wird, soll ein Herz-Symbol erscheinen. Wenn Taste B gedrückt wird, soll ein Kreuz erscheinen.

**So geht's:**
1. Klicke auf **„Eingabe"**
2. Ziehe **„Wenn Knopf A gedrückt"** auf den Arbeitsbereich
3. Füge darin **„Zeige Symbol"** (aus „Grundlagen") ein → wähle das Herz
4. Mache dasselbe für Taste B mit dem Kreuz-Symbol

**Dein Programm sieht so aus:**
```
[ Wenn Knopf A gedrückt ]       [ Wenn Knopf B gedrückt ]
         ↓                                ↓
  [ Zeige Symbol: ♥ ]            [ Zeige Symbol: ✕ ]
```

> **🔍 Frage:** Was passiert, wenn du A und B gleichzeitig drückst? Probiere es aus!

---

## 1.4 Funk-Grundlagen – Wie kommunizieren die Calliopes?

Im Spiel müssen mehrere Calliopes miteinander kommunizieren. Dazu nutzen sie das eingebaute **Funkmodul** (vergleichbar mit WLAN, aber viel einfacher).

### Wichtige Konzepte:

**Funkgruppe:**  
Alle Calliopes müssen in derselben **Gruppe** funken. Nur dann können sie sich gegenseitig hören. Bei uns nutzen alle Gruppe **`1`**.

**Zahl senden:**  
Ein Calliope kann eine Zahl an alle anderen in der Gruppe schicken.

**Zahl empfangen:**  
Wenn ein Calliope eine Zahl empfängt, wird automatisch ein bestimmter Block ausgeführt.

### Aufgabe 1.3: Einfacher Funk-Test (zu zweit!)

Ihr braucht **zwei Calliopes** für diese Aufgabe.

**Calliope 1: Sender**
```
[ beim Start ]
      ↓
[ Funk: Setze Gruppe auf 1 ]

[ Wenn Knopf A gedrückt ]
      ↓
[ Funk: Sende Zahl 42 ]
```

**Calliope 2: Empfänger**
```
[ beim Start ]
      ↓
[ Funk: Setze Gruppe auf 1 ]

[ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ]
      ↓
[ Grundlagen: Zeige Zahl (empfangeneZahl) ]
```

**Vorgehensweise:**
1. Programmiere den Sender und übertrage ihn auf Calliope 1
2. Programmiere den Empfänger und übertrage ihn auf Calliope 2
3. Drücke auf Calliope 1 die Taste A
4. Was zeigt Calliope 2 an?

> **💡 Achtung:** Bei der Funk-Übertragung: Der `empfangeneZahl`-Block erscheint automatisch, wenn du den `Wenn Zahl empfangen wird`-Block verwendest. Du findest ihn danach auch in der Kategorie **„Variablen"**.

---

## 1.5 Das Spielkonzept verstehen

Bevor wir programmieren, planen wir das System. In der Informatik nennt man das **Systemdesign**.

### Die zwei Programme

Unser Spiel besteht aus **zwei verschiedenen Programmen**:

```
┌─────────────────────────────────────────────────────────────┐
│                        SPIELLEITER                          │
│  - gibt Signale aus (LED + Ton)                             │
│  - sendet Signal-Code per Funk                              │
│  - empfängt Antworten der Spieler                           │
│  - prüft: richtig oder falsch?                              │
│  - vergibt Punkte und zeigt Ergebnis                        │
└─────────────────────────────────────────────────────────────┘
                            ↕ Funk (Gruppe 1)
┌─────────────────────────────────────────────────────────────┐
│                    SPIELER (bis zu 4×)                      │
│  - hat eine feste Spieler-ID (1, 2, 3 oder 4)              │
│  - empfängt das Signal-Zeichen                              │
│  - führt eine Aktion aus (Taste drücken / neigen)           │
│  - sendet: eigene ID + Aktion per Funk                      │
└─────────────────────────────────────────────────────────────┘
```

### Die Signal-Codes

Der Spielleiter sendet eine Zahl, die für ein bestimmtes Signal steht:

| Code | Anzeige beim Spielleiter | Richtige Aktion der Spieler |
|------|--------------------------|----------------------------|
| `1`  | RGB-LED grün + hoher Ton | Taste **A** drücken |
| `2`  | RGB-LED rot + tiefer Ton | Taste **B** drücken |
| `3`  | Herz auf Matrix + mittlerer Ton | Tasten **A+B** gleichzeitig |

### Die Antwort-Codes der Spieler

Jeder Spieler sendet eine **einzige Zahl** zurück. Diese Zahl enthält sowohl die **Spieler-ID** als auch die **ausgeführte Aktion**:

```
Gesendete Zahl = (Spieler-ID × 10) + Aktions-Code
```

**Beispiele:**

| Spieler-ID | Aktion | Aktions-Code | Gesendete Zahl |
|-----------|--------|-------------|---------------|
| 1 | Taste A | 1 | **11** |
| 2 | Taste A | 1 | **21** |
| 1 | Taste B | 2 | **12** |
| 3 | A+B | 3 | **33** |

> **🔍 Denksport:** Warum multiplizieren wir die Spieler-ID mit 10? Was wäre das Problem, wenn wir einfach `Spieler-ID + Aktion` rechnen würden?

*Tipp: Überlege, was passiert wenn Spieler 2 die Aktion 1 ausführt, oder Spieler 1 die Aktion 2-–sind diese unterscheidbar?*

### Aufgabe 1.4: Systementwurf

Bearbeite folgende Fragen **schriftlich** (Heft oder Computer):

1. **Protokoll:** Warum müssen alle Calliopes dieselbe Funkgruppe nutzen?
2. **Codierung:** Berechne: Was sendet Spieler 4, wenn er Taste B drückt?
3. **Decodierung:** Was bedeutet die empfangene Zahl `32`? Wer hat gesendet und was wurde gedrückt?
4. **Systemgrenzen:** Wie viele Spieler könnte das System theoretisch verwalten, wenn auch Aktions-Codes bis `9` möglich wären? Begründe.
5. **Erweiterung:** Was müsste sich ändern, wenn man mehr als 9 mögliche Aktionen hätte?

---

## ✅ Zusammenfassung

Du kannst jetzt:
- ✅ MakeCode öffnen und Programme auf den Calliope übertragen
- ✅ Einfache Ausgaben (Text, Symbole) programmieren
- ✅ Auf Tastendruck reagieren
- ✅ Funk senden und empfangen (einfaches Beispiel)
- ✅ Das Codierungsschema `ID × 10 + Aktion` verstehen und anwenden

---

← [Zurück zur Übersicht](../README.md) | [Weiter: Spielleiter →](02_spielleiter.md)
