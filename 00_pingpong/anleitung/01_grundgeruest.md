# Schritt 1: Grundgerüst – erster Ballwechsel

← [Zurück zur Übersicht](../README.md) | [Weiter: Schritt 2 →](02_vollstaendiges_spiel.md)

---

## 🎯 Ziel dieses Schritts

Am Ende dieses Schritts können Spieler 1 und Spieler 2 einen Ball per Funk hin- und herschicken. Wer den Ball hat, sieht das auf seinem Display.

> **💡 Besonderheit:** Beide Calliopes bekommen **dasselbe Programm**! Ihr programmiert also gemeinsam – alle gleichzeitig. Erst am Schluss wird das Programm auf beide Geräte übertragen.

---

## Das Spielprinzip kurz erklärt

Unser „Ball" ist eine **Funknachricht** (die Zahl 1). Wer sie empfängt, hat den Ball. Die Variable `ballBeiMir` sagt dem Calliope, ob er gerade den Ball hält – und steuert damit, was passiert, wenn du Taste A drückst.

```
ballBeiMir = wahr  →  ♥ anzeigen, auf Taste A warten
ballBeiMir = falsch →  warten, bis Ball ankommt
```

---

## Schritt 1: Neues Projekt anlegen

1. Öffne [makecode.calliope.cc](https://makecode.calliope.cc)
2. Klicke auf **„+ Neues Projekt"**
3. Name: **`PingPong`**

---

## Schritt 2: Variablen anlegen

1. Klicke auf **„Variablen"** → **„Erstelle eine Variable..."**
2. Erstelle folgende Variablen:

| Variable | Bedeutung |
|----------|-----------|
| `ballBeiMir` | Habe ich gerade den Ball? (wahr/falsch) |
| `spielLaeuft` | Läuft das Spiel? (wahr/falsch) |

---

## Schritt 3: Startkonfiguration

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze ballBeiMir auf [ falsch ]                        │
│  Setze spielLaeuft auf [ falsch ]                       │
│                                                           │
│  Grundlagen: Zeige Zeichenkette [ "PP" ]                │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du die Blöcke?**
- `Funk: Setze Gruppe auf` → Kategorie **Funk**
- `Setze ... auf` → Kategorie **Variablen**
- `falsch` → Kategorie **Logik**
- `Zeige Zeichenkette` → Kategorie **Grundlagen**

„PP" steht für Ping-Pong und zeigt: Das Programm ist bereit.

---

## Schritt 4: Aufschlag mit A+B

Einer der beiden Spieler startet das Spiel, indem er **A+B gleichzeitig** drückt. Der Ball geht sofort zum anderen Spieler.

```
┌─ Wenn Knopf A+B gedrückt ───────────────────────────────┐
│                                                           │
│  Logik: Wenn [ spielLaeuft ] = [ falsch ] dann:         │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze spielLaeuft auf [ wahr ]                 │   │
│  │  Setze ballBeiMir auf [ falsch ]                │   │
│  │                                                  │   │
│  │  // Ball zum Gegner schicken                    │   │
│  │  Funk: Sende Zahl [ 1 ]                         │   │
│  │                                                  │   │
│  │  // Kurz anzeigen: "Ich hab aufgeschlagen"      │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil rechts → ]    │   │
│  │  Grundlagen: Pausiere [ 800 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **⚠️ Nur einer von beiden drückt A+B!** Wenn beide gleichzeitig drücken, entstehen zwei Bälle. Macht vorher aus, wer aufschlägt.

---

## Schritt 5: Ball empfangen

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  // Ist es der Ball? (Zahl 1)                            │
│  Wenn [ empfangeneZahl ] = [ 1 ] dann:                  │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze ballBeiMir auf [ wahr ]                  │   │
│  │  Setze spielLaeuft auf [ wahr ]                 │   │
│  │                                                  │   │
│  │  // Zeigen: "Ich hab den Ball!"                 │   │
│  │  LED: Setze RGB auf [ Grün: RGB(0,255,0) ]      │   │
│  │  Grundlagen: Zeige Symbol [ ♥ (Herz) ]          │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Funk: Wenn Zahl empfangen wird` → Kategorie **Funk**
- `empfangeneZahl` erscheint automatisch als lila Block
- `LED: Setze RGB auf` → Kategorie **LED**

---

## Schritt 6: Ball zurückschicken – Taste A

Wenn ich den Ball habe (`ballBeiMir = wahr`) und Taste A drücke, schicke ich ihn zum Gegner.

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ ballBeiMir ] = [ wahr ] dann:            │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze ballBeiMir auf [ falsch ]                │   │
│  │                                                  │   │
│  │  // RGB ausschalten                             │   │
│  │  LED: Setze RGB auf [ aus: RGB(0,0,0) ]         │   │
│  │                                                  │   │
│  │  // Ball senden                                 │   │
│  │  Funk: Sende Zahl [ 1 ]                         │   │
│  │                                                  │   │
│  │  // Kurz anzeigen: "Ich hab zurückgeschlagen"   │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil rechts → ]    │   │
│  │  Grundlagen: Pausiere [ 500 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Warum `ballBeiMir = wahr` prüfen?** Ohne diese Bedingung könntest du Taste A drücken, wenn du den Ball gar nicht hast – und würdest trotzdem eine Fake-Nachricht senden. Das würde das Spiel durcheinanderbringen.

---

## ✅ Programm auf beide Calliopes übertragen

1. Calliope 1 verbinden → Herunterladen → `.hex` draufziehen
2. Calliope 2 verbinden → Herunterladen → dieselbe `.hex`-Datei draufziehen

**Beide Calliopes bekommen exakt dieselbe Datei!**

---

## 🔬 Test 1: Erster Ballwechsel

**Was ihr braucht:** 2 Calliopes, beide eingeschaltet.

### Testablauf

| Schritt | Aktion | Erwartetes Ergebnis |
|---------|--------|----------------------|
| 1 | Calliope 1 drückt **A+B** | Calliope 1 zeigt →, Calliope 2 zeigt ♥ (grün) |
| 2 | Calliope 2 drückt **A** | Calliope 2 zeigt →, Calliope 1 zeigt ♥ (grün) |
| 3 | Calliope 1 drückt **A** | Ball wieder bei Calliope 2 |

### Mögliche Probleme

| Problem | Lösung |
|---------|--------|
| Nichts passiert | Beide auf Funk-Gruppe **1** prüfen |
| Ball kommt nicht an | Abstand verringern (< 10 m), USB-Verbindung trennen |
| Beide zeigen gleichzeitig ♥ | Nur einer darf A+B drücken – neu starten |

> **✅ Ball geht hin und her?** Perfekt – weiter zu Schritt 2!

---

## 📋 Zusammenfassung Schritt 1

Was das Programm jetzt kann:
- Beide Calliopes starten mit demselben Programm
- A+B = Aufschlag → Ball geht zum Gegner
- Ball empfangen → ♥ (grün) anzeigen
- Taste A = zurückschlagen → → anzeigen

**⏭ Weiter:** [Schritt 2 – Rallye-Zähler & Spielende](02_vollstaendiges_spiel.md)
