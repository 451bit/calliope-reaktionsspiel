# Schritt 1: Grundgerüst – erster Funk-Test

← [Zurück: Einführung](01_einfuehrung.md) | [Weiter: Schritt 2 →](03_schritt2.md)

---

## 🎯 Ziel dieses Schritts

Am Ende dieses Schritts funktioniert die Funkkommunikation zwischen Spielleiter und Spieler. Der Spielleiter schickt beim Tastendruck eine Nachricht – und der Spieler empfängt sie sofort.

> **👥 Arbeitsaufteilung:** In der Klasse arbeitet ihr gleichzeitig an zwei Projekten – **Spielleiter** und **Spieler**. Tauscht euch mit eurer Partnergruppe ab: Erst wenn beide Programme fertig sind, könnt ihr gemeinsam testen!

---

## Teil A · Spielleiter – Grundgerüst

### Lernziele (Spielleiter)
- Funk-Gruppe einstellen
- Beim Start ein Startsignal anzeigen
- Per Tastendruck eine Zahl senden

---

### SL-Schritt 1: Neues Projekt anlegen

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Klicke auf **„+ Neues Projekt"**
3. Name: **`Spielleiter`**

---

### SL-Schritt 2: Funk-Gruppe und Startanzeige

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Grundlagen: Zeige Symbol [ ✓ ]                          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du die Blöcke?**
- `Funk: Setze Gruppe auf` → Kategorie **Funk**
- `Zeige Symbol` → Kategorie **Grundlagen** (Haken-Symbol auswählen)

Das Häkchen ✓ zeigt: Der Spielleiter ist bereit.

---

### SL-Schritt 3: Per Taste A etwas senden

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Funk: Sende Zahl [ 1 ]                                  │
│                                                           │
│  Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]               │
│  Grundlagen: Pausiere [ 500 ] ms                         │
│  Grundlagen: Bildschirm löschen                          │
│  Grundlagen: Zeige Symbol [ ✓ ]                          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du die Blöcke?**
- `Funk: Sende Zahl` → Kategorie **Funk**
- `Pausiere ... ms` → Kategorie **Grundlagen**
- `Bildschirm löschen` → Kategorie **Grundlagen**

> **💡 Warum Pfeil hoch?** Das gibt visuelles Feedback: „Ich habe gerade etwas gesendet."

---

### ✅ Spielleiter – Programm auf den Calliope übertragen

1. Calliope per USB-Kabel verbinden
2. Auf **„Herunterladen"** klicken
3. `.hex`-Datei in den Calliope-Ordner ziehen (oder WebUSB verwenden)
4. Wenn die gelbe LED aufhört zu blinken → Programm ist bereit!

---

## Teil B · Spieler – Grundgerüst

### Lernziele (Spieler)
- Eigene ID über Tastendruck wählen
- Funk-Nachrichten empfangen und anzeigen

---

### SP-Schritt 1: Neues Projekt anlegen

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc) → **„+ Neues Projekt"**
2. Name: **`Spieler`**

---

### SP-Schritt 2: Variablen anlegen

1. Klicke auf **„Variablen"** → **„Erstelle eine Variable..."**
2. Erstelle folgende Variablen:
   - `meineID`
   - `idGesetzt`

---

### SP-Schritt 3: Startkonfiguration

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze meineID auf [ 0 ]                                │
│  Setze idGesetzt auf [ falsch ]                         │
│                                                           │
│  Grundlagen: Zeige Zeichenkette [ "ID?" ]               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Setze ... auf` → Kategorie **Variablen**
- `falsch` → Kategorie **Logik** (boolescher Wert)
- `Zeige Zeichenkette` → Kategorie **Grundlagen**

---

### SP-Schritt 4: ID über Tasten setzen

**Taste A → Spieler 1:**

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ idGesetzt ] = [ falsch ] dann:           │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze meineID auf [ 1 ]                        │   │
│  │  Setze idGesetzt auf [ wahr ]                   │   │
│  │  Grundlagen: Zeige Zahl [ 1 ]                   │   │
│  │  Grundlagen: Pausiere [ 1000 ] ms               │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Taste B → Spieler 2:**

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ idGesetzt ] = [ falsch ] dann:           │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze meineID auf [ 2 ]                        │   │
│  │  Setze idGesetzt auf [ wahr ]                   │   │
│  │  Grundlagen: Zeige Zahl [ 2 ]                   │   │
│  │  Grundlagen: Pausiere [ 1000 ] ms               │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Tasten A+B gleichzeitig → Spieler 3:**

```
┌─ Wenn Knopf A+B gedrückt ───────────────────────────────┐
│                                                           │
│  Logik: Wenn [ idGesetzt ] = [ falsch ] dann:           │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze meineID auf [ 3 ]                        │   │
│  │  Setze idGesetzt auf [ wahr ]                   │   │
│  │  Grundlagen: Zeige Zahl [ 3 ]                   │   │
│  │  Grundlagen: Pausiere [ 1000 ] ms               │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Spieler 4:** ID 4 kann durch eine Erweiterung des Taste-A-Blocks hinzugefügt werden (→ Schritt 3). Für den Einstieg reichen IDs 1–3.

---

### SP-Schritt 5: Funk-Nachrichten empfangen

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  Grundlagen: Zeige Zahl [ empfangeneZahl ]              │
│  Grundlagen: Pausiere [ 1000 ] ms                       │
│  Grundlagen: Bildschirm löschen                         │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Funk: Wenn Zahl empfangen wird` → Kategorie **Funk**
- `empfangeneZahl` erscheint automatisch als lila Block

---

### ✅ Spieler – Programm auf den Calliope übertragen

Genau wie beim Spielleiter: Herunterladen → `.hex`-Datei auf den zweiten Calliope kopieren.

---

## 🔬 Test 1: Erster Funk-Test

**Was ihr braucht:** Spielleiter-Calliope + Spieler-Calliope, beide eingeschaltet.

### Testablauf

| Schritt | Aktion | Erwartetes Ergebnis |
|---------|--------|----------------------|
| 1 | Spieler drückt **A** | Spieler-Display zeigt kurz „1", dann leer |
| 2 | Spielleiter drückt **A** | Spielleiter zeigt kurz ↑, dann wieder ✓ |
| 3 | – | Spieler-Display zeigt kurz „1" |

> **✅ Alles korrekt?** Wenn der Spieler nach dem Druck des Spielleiters die Zahl sieht, funktioniert der Funk. Weiter zu Schritt 2!

### Mögliche Probleme

| Problem | Lösung |
|---------|--------|
| Spieler sieht nichts | Beide auf Funk-Gruppe **1** prüfen |
| Spieler zeigt falsche Zahl | `empfangeneZahl` korrekt verwenden (nicht eine feste Zahl eintippen) |
| Calliope reagiert nicht | USB-Kabel prüfen, neu herunterladen |

---

## 📋 Zusammenfassung Schritt 1

| Gerät | Was ist jetzt fertig |
|-------|---------------------|
| **Spielleiter** | Funk-Gruppe, Start-Anzeige, Senden per Taste A |
| **Spieler** | Funk-Gruppe, ID-Wahl (A/B/A+B), Anzeige empfangener Zahlen |

**⏭ Weiter:** [Schritt 2 – Signale & Reaktionen](03_schritt2.md)
