# 🏓 Calliope Ping-Pong – Einstiegsprojekt

> **Thema:** Funkkommunikation & Spielzustand mit dem Calliope mini v1  
> **Werkzeug:** [MakeCode](https://makecode.calliope.cc) (Grafische Blockprogrammierung)  
> **Dauer:** ca. 2–3 Unterrichtsstunden  
> **Klassenstufe:** 10 (Informatik) – als Einstieg vor dem Reaktionsspiel

---

## 🎯 Über dieses Projekt

Zwei Calliopes spielen Ping-Pong miteinander – ganz ohne Schläger! Statt einem Ball reist eine **Funknachricht** zwischen den Geräten hin und her. Wer den „Ball" hat, sieht das auf seinem Display – und muss möglichst schnell Taste A drücken, um zurückzuschlagen. Gemeinsam versuchen die beiden Spieler, möglichst viele Ballwechsel zu erreichen.

> **Besonderheit:** Beide Calliopes laufen mit **demselben Programm**. Das ist der entscheidende Unterschied zum Reaktionsspiel und ein wichtiges Informatik-Prinzip: ein Programm, das sich je nach Zustand anders verhält.

---

## 🎮 Das Spielprinzip

```
Spieler 1                        Spieler 2
   │                                │
   │  A+B drücken = Aufschlag       │
   │──────────── Zahl 1 ──────────►│  ← Ball empfangen
   │                          ♥ anzeigen
   │                          A drücken
   │◄─────────── Zahl 1 ────────── │  Ball zurückschicken
   │  ♥ anzeigen                   │
   │  A drücken                    │
   │──────────── Zahl 1 ──────────►│
   │                          ...
   │         10 Ballwechsel?       │
   │         → SUPER! anzeigen    │
```

**Ablauf:**
1. Einer der Spieler drückt **A+B** → Aufschlag (Ball geht zum anderen)
2. Wer den Ball hat, sieht ♥ und eine grüne RGB-LED
3. **Taste A drücken** → Ball zurückschicken
4. Jeder Ballwechsel zählt: +1 Rallye
5. Bei **10 Rallyes** endet das Spiel: beide gewinnen!

---

## 📚 Anleitungen

| Schritt | Inhalt | Ziel |
|---------|--------|------|
| [Schritt 1](anleitung/01_grundgeruest.md) | Grundgerüst + erster Ballwechsel-Test | Funk und Zustandsvariable |
| [Schritt 2](anleitung/02_vollstaendiges_spiel.md) | Rallye-Zähler, Spielende, Feedback | Vollständiges Spiel spielbar |

---

## 🧰 Voraussetzungen

- **Hardware:** 2× Calliope mini v1
- **USB-Kabel** zum Übertragen
- **Browser:** Chrome oder Edge (für MakeCode)
- **Vorkenntnisse:** Grundlagen aus Kapitel 01 des Reaktionsspiels

---

## 📋 Lernziele

Nach diesem Projekt können Schülerinnen und Schüler:
- [ ] eine einfache Funkkommunikation zwischen zwei Calliopes aufbauen
- [ ] eine Zustandsvariable (`ballBeiMir`) einsetzen
- [ ] erklären, warum beide Geräte dasselbe Programm verwenden können
- [ ] Spielzustände mit Variablen steuern (warten / Ball empfangen / schlagen)

---

## ⏩ Danach

Nach diesem Einstiegsprojekt geht es weiter mit dem vollständigen **Reaktionsspiel**:  
→ [Zurück zur Übersicht](../README.md)
