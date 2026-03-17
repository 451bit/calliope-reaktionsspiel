# Schritt 2: Rallye-Zähler & Spielende

← [Zurück: Schritt 1](01_grundgeruest.md) | [Zurück zur Übersicht](../README.md)

---

## 🎯 Ziel dieses Schritts

Am Ende dieses Schritts zählt das Programm jeden Ballwechsel mit und zeigt ihn auf der LED-Matrix an. Nach 10 Rallyes endet das Spiel automatisch – beide Spieler sehen eine Siegesanzeige.

---

## Schritt 1: Variable für Rallyes anlegen

Füge eine neue Variable hinzu:

| Variable | Bedeutung |
|----------|-----------|
| `rallyes` | Anzahl der erfolgreichen Ballwechsel |

Ergänze den `beim Start`-Block:

```
┌─ beim Start (Ergänzung) ────────────────────────────────┐
│                                                          │
│  ... (bisheriger Code) ...                             │
│                                                          │
│  Setze rallyes auf [ 0 ]                               │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## Schritt 2: Rallyes zählen beim Empfang

Jedes Mal, wenn der Ball ankommt, wird ein Ballwechsel gezählt. Erweitere den `Funk: Wenn Zahl empfangen`-Block:

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  Wenn [ empfangeneZahl ] = [ 1 ] dann:                  │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze ballBeiMir auf [ wahr ]                  │   │
│  │  Setze spielLaeuft auf [ wahr ]                 │   │
│  │                                                  │   │
│  │  // Rallye zählen                               │   │
│  │  Ändere rallyes um [ 1 ]                        │   │
│  │                                                  │   │
│  │  // Spielende prüfen                            │   │
│  │  Wenn [ rallyes ] ≥ [ 10 ] dann:               │   │
│  │      rufauf: spielEnde                          │   │
│  │  Sonst:                                          │   │
│  │      // Ball anzeigen                           │   │
│  │      LED: Setze RGB auf [ Grün: RGB(0,255,0) ]  │   │
│  │      Grundlagen: Zeige Symbol [ ♥ ]             │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du:**
- `Ändere ... um ...` → Kategorie **Variablen**
- `rufauf: ...` → Kategorie **Fortgeschritten** → **Funktionen**

> **💡 Warum Rallyes beim Empfang zählen?** Jeder Spieler empfängt den Ball gleich oft (± 1). Gezählt wird jedes ankommen des Balls – so steigt der Zähler bei beiden gleichmäßig.

---

## Schritt 3: Rallye-Anzeige beim Zurückschlagen

Kurz bevor der Ball zurückgesendet wird, soll der aktuelle Rallye-Zähler auf dem Display aufblitzen. Erweitere den Taste-A-Block:

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Logik: Wenn [ ballBeiMir ] = [ wahr ] dann:            │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Setze ballBeiMir auf [ falsch ]                │   │
│  │  LED: Setze RGB auf [ aus: RGB(0,0,0) ]         │   │
│  │                                                  │   │
│  │  // Aktuellen Zählerstand kurz anzeigen         │   │
│  │  Grundlagen: Zeige Zahl [ rallyes ]              │   │
│  │  Grundlagen: Pausiere [ 600 ] ms                │   │
│  │                                                  │   │
│  │  // Ball senden                                 │   │
│  │  Funk: Sende Zahl [ 1 ]                         │   │
│  │                                                  │   │
│  │  Grundlagen: Zeige Symbol [ Pfeil rechts → ]    │   │
│  │  Grundlagen: Pausiere [ 500 ] ms                │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

## Schritt 4: Funktion `spielEnde` erstellen

Erstelle eine neue Funktion mit dem Namen `spielEnde`:

```
┌─ Funktion spielEnde ────────────────────────────────────┐
│                                                          │
│  // Spiel stoppen                                       │
│  Setze spielLaeuft auf [ falsch ]                      │
│  Setze ballBeiMir auf [ falsch ]                       │
│                                                          │
│  // RGB: Gold/Gelb = Sieg!                             │
│  LED: Setze RGB auf [ Gelb: RGB(255,200,0) ]           │
│                                                          │
│  // Siegesanzeige                                      │
│  Grundlagen: Zeige Zeichenkette [ "SUPER!" ]           │
│  Grundlagen: Pausiere [ 1000 ] ms                      │
│  Grundlagen: Zeige Zahl [ rallyes ]                    │
│  Grundlagen: Pausiere [ 2000 ] ms                      │
│                                                          │
│  // Reset                                              │
│  LED: Setze RGB auf [ aus ]                            │
│  Setze rallyes auf [ 0 ]                               │
│  Grundlagen: Zeige Zeichenkette [ "PP" ]               │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

> **💡 Beide Calliopes führen `spielEnde` aus!** Der Empfänger löst es direkt aus (weil `rallyes ≥ 10`). Der andere Spieler merkt es, weil kein weiterer Ball mehr ankommt – er wartet einfach auf den nächsten Aufschlag.

---

## Vollständige Programmübersicht

Zur Kontrolle: So sieht das fertige Programm aus.

```
PING-PONG – Alle Blöcke im Überblick

► beim Start
   → Gruppe 1, ballBeiMir = falsch, spielLaeuft = falsch,
     rallyes = 0, "PP" anzeigen

► Wenn Knopf A+B gedrückt
   → Wenn spielLaeuft = falsch:
     spielLaeuft = wahr, ballBeiMir = falsch
     Funk: Sende Zahl 1 → zur anzeigen

► Wenn Knopf A gedrückt
   → Wenn ballBeiMir = wahr:
     ballBeiMir = falsch, RGB aus,
     rallyes kurz zeigen,
     Funk: Sende Zahl 1, → anzeigen

► Funk: Wenn Zahl empfangen (empfangeneZahl = 1)
   → ballBeiMir = wahr, spielLaeuft = wahr
   → rallyes + 1
   → Wenn rallyes ≥ 10: spielEnde
   → Sonst: RGB grün, ♥ anzeigen

► Funktion: spielEnde
   → spielLaeuft = falsch, ballBeiMir = falsch
   → RGB gelb, "SUPER!", rallyes zeigen
   → Reset, "PP" anzeigen
```

---

## 🔬 Test 2: Vollständiges Spiel

**Testablauf:**

| Schritt | Aktion | Erwartetes Ergebnis |
|---------|--------|----------------------|
| 1 | Calliope 1 drückt A+B | Ball geht zu Calliope 2 (zeigt ♥) |
| 2 | Mehrfach hin und her schlagen | Rallye-Zähler steigt (Zahl blinkt vor jedem Schlag) |
| 3 | Nach 10 Ballwechseln | Beide zeigen „SUPER!" + Anzahl der Rallyes |
| 4 | Automatisch | Reset auf „PP", nächstes Spiel möglich |

### Tipps für den Test
- Schafft ihr 10 Rallyes ohne Fehler?
- Was passiert, wenn du Taste A drückst, wenn der Ball bei dir ist?
- Was passiert, wenn du A drückst, wenn du den Ball *nicht* hast?

---

## 💡 Aufgaben

### Aufgabe 1: Verhalten verstehen ⭐
Erkläre mit eigenen Worten: Warum können beide Calliopes dasselbe Programm verwenden, obwohl sie unterschiedliche Rollen haben (Ball haben / Ball nicht haben)?

### Aufgabe 2: Zielzahl ändern ⭐
Ändere das Ziel von 10 auf 5 Rallyes. Teste es.

### Aufgabe 3: Fehler analysieren ⭐⭐
Was passiert, wenn Spieler A Taste A drückt, obwohl `ballBeiMir = falsch`?  
1. Erkläre das aktuelle Verhalten des Programms.  
2. Was würde passieren, wenn du die Bedingung `ballBeiMir = wahr` entfernst?

### Aufgabe 4: Zeitlimit einbauen ⭐⭐⭐
Wer den Ball hat, muss ihn innerhalb von **3 Sekunden** zurückschlagen, sonst verliert das Team einen Punkt.

*Hinweis: Der Block `Eingabe: Laufzeit (ms)` gibt die Zeit seit Programmstart in Millisekunden zurück. Speichere den Zeitpunkt, wann du den Ball empfangen hast, und vergleiche ihn regelmäßig im `dauerhaft`-Block.*

---

## 📋 Zusammenfassung

Das Ping-Pong-Spiel ist vollständig und du hast kennengelernt:
- ✅ Wie ein Programm durch Variablen seinen Zustand ändert (`ballBeiMir`)
- ✅ Warum beide Geräte dasselbe Programm haben können
- ✅ Wie du Ereignisse (Tastendruck, Funk-Empfang) mit Bedingungen verknüpfst
- ✅ Wie ein Spiel durch Zähler und Funktionen strukturiert wird

**⏩ Nächstes Projekt:** [Reaktionsspiel → Hauptprojekt](../../README.md)
