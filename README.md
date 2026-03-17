# 🎮 Calliope Reaktionsspiel – Lerneinheit Klasse 10 Informatik

> **Thema:** Vernetzte Systeme & Programmierung mit dem Calliope mini v1  
> **Werkzeug:** [MakeCode](https://makecode.calliope.cc) (Grafische Blockprogrammierung)  
> **Dauer:** ca. 6–8 Unterrichtsstunden  
> **Klassenstufe:** 10 (Informatik)

---

## 🎯 Über dieses Projekt

In diesem Projekt programmieren Schülerinnen und Schüler ein **Multiplayer-Reaktionsspiel** mit mehreren Calliope-mini-Geräten. Dabei werden grundlegende Informatik-Konzepte wie **Funkkommunikation, Variablen, Bedingungen, Schleifen** und **Zustandssteuerung** auf spielerische Weise erarbeitet.

### Das Spielprinzip

| Rolle | Anzahl | Aufgabe |
|-------|--------|---------|
| **Spielleiter** | 1 Calliope | gibt Signale aus, wertet Antworten aus, vergibt Punkte |
| **Spieler** | 1–4 Calliopes | reagieren auf Signale, senden Aktionen per Funk |

**Ablauf einer Runde:**
1. Der Spielleiter zeigt ein **Signal** (LED-Muster + Ton)
2. Die Spieler müssen die richtige Aktion ausführen (z. B. Taste A drücken)
3. Wer **zuerst richtig** liegt, bekommt **+1 Punkt**
4. Wer **falsch** liegt, bekommt **–1 Punkt**
5. Der Spielleiter zeigt den Gewinner und den aktuellen Punktestand an

---

## 📚 Anleitungen

Die Lerneinheit ist in folgende Kapitel aufgeteilt:

| Kapitel | Inhalt | Schwerpunkt |
|---------|--------|-------------|
| [01 Einführung](anleitung/01_einfuehrung.md) | Calliope kennenlernen, MakeCode, Funk-Grundlagen | Grundlagen |
| [02 Schritt 1](anleitung/02_schritt1.md) | Grundgerüst beider Programme + erster Funk-Test | Funk, Variablen |
| [03 Schritt 2](anleitung/03_schritt2.md) | Signale & Reaktionen – erste echte Spielrunde | Funk, Zustand, Logik |
| [04 Schritt 3](anleitung/04_schritt3.md) | Punkte, Spielende, Gesamtsieger | Variablen, LED-Matrix |
| [05 Erweiterungen](anleitung/05_erweiterungen.md) | Neigungssensor, Zeitbonus, eigene Ideen | Kreativität |

---

## 🧰 Voraussetzungen

- **Hardware:** Calliope mini v1 (1× Spielleiter + 1–4× Spieler)
- **USB-Kabel** zum Übertragen der Programme
- **Browser:** aktueller Chrome oder Edge (für MakeCode)
- **Vorkenntnisse:** Grundlegende Erfahrung mit Blockprogrammierung (z. B. Scratch)

---

## 🚀 Schnellstart

1. Browser öffnen → [makecode.calliope.cc](https://makecode.calliope.cc)
2. „Neues Projekt" erstellen
3. Mit Kapitel [01 – Einführung](anleitung/01_einfuehrung.md) beginnen

---

## 📋 Lernziele

Nach Abschluss des Projekts können Schülerinnen und Schüler:
- [ ] den Calliope mini über MakeCode programmieren
- [ ] Funksignale senden und empfangen
- [ ] Variablen zur Datenspeicherung einsetzen
- [ ] Bedingungen und Verzweigungen korrekt anwenden
- [ ] ein komplexes System aus mehreren vernetzten Geräten planen und umsetzen

---

## 🗂️ Projektstruktur

```
calliope-spiel/
├── README.md                    ← diese Datei
├── anleitung/
│   ├── 01_einfuehrung.md        ← Calliope, MakeCode, Funk-Grundlagen
│   ├── 02_schritt1.md           ← Schritt 1: Grundgerüst & erster Funk-Test
│   ├── 03_schritt2.md           ← Schritt 2: Signale & Reaktionen
│   ├── 04_schritt3.md           ← Schritt 3: Punkte & Spielende
│   └── 05_erweiterungen.md      ← Eigene Erweiterungen
└── loesungen/
    ├── spielleiter_loesung.md
    └── spieler_loesung.md
```

---

## 📄 Lizenz

Dieses Material steht unter der [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.de) Lizenz und darf frei verwendet, verändert und weitergegeben werden – mit Namensnennung.
