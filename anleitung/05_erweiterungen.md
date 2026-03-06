# Kapitel 5: Erweiterungen & eigene Ideen

← [Zurück: Punkte & Anzeige](04_punkte_und_anzeige.md) | [Zurück zur Übersicht](../README.md)

---

## 🎯 Ziel dieses Kapitels

In diesem Kapitel entwickelt ihr das Spiel **selbstständig weiter**. Die Erweiterungen sind nach Schwierigkeit geordnet (⭐ bis ⭐⭐⭐⭐) und ihr könnt wählen, welche ihr umsetzen möchtet. 

**Wichtig:** Plant zuerst, bevor ihr programmiert! Nutzt Skizzen, Zustandsdiagramme oder Pseudocode.

---

## 5.1 Erweiterung: Neigungssensor als Spieleraktion

**Schwierigkeit:** ⭐⭐  
**Themen:** Eingabe, Sensoren, Bedingungen

### Idee

Bisher können Spieler nur Tasten drücken. Wir fügen zwei neue Aktionen hinzu:
- **Links neigen** → Aktion 4
- **Rechts neigen** → Aktion 5

Der Spielleiter kann dann auch neue Signale senden:
- **Signal 4:** Pfeil-links auf Matrix + tiefer Ton → links neigen
- **Signal 5:** Pfeil-rechts auf Matrix + hoher Ton → rechts neigen

### Relevante Blöcke

| Block | Kategorie | Funktion |
|-------|-----------|---------|
| `Wenn Calliope geschüttelt wird` | Eingabe | erkennt Schütteln |
| `Beschleunigung (mg) in Richtung X` | Eingabe | gibt Neigungswert |
| `Wenn Calliope links geneigt` | Eingabe | erkennt Linksneigung |
| `Wenn Calliope rechts geneigt` | Eingabe | erkennt Rechtsneigung |

### Planungsaufgabe

Bevor du programmierst, beantworte:
1. Welche neuen Signalcodes werden gesendet (4 und 5)?
2. Wie erkennt der Spieler, ob er links oder rechts neigt? (Schau dir den `Neigung`-Block an)
3. Welche Zahl sendet Spieler 3, wenn er den Calliope nach links neigt?
4. Zeichne das erweiterte Signaltabellen-Schema.

### Implementierung

**Spielleiter: signalAnzeigen erweitern**
```
Sonst wenn [ aktuelleAufgabe ] = [ 4 ] dann:
    LED: Setze RGB auf [ Gelb: RGB(255,200,0) ]
    Grundlagen: Zeige LEDs (Pfeil links):
      □ ■ □ □ □
      ■ □ □ □ □  
      ■ ■ ■ ■ □
      ■ □ □ □ □
      □ ■ □ □ □
    Musik: Spiele Ton [ A2 ] für [ 1 ] Schlag

Sonst wenn [ aktuelleAufgabe ] = [ 5 ] dann:
    LED: Setze RGB auf [ Orange: RGB(255,100,0) ]
    Grundlagen: Zeige LEDs (Pfeil rechts):
      □ □ □ ■ □
      □ □ □ □ ■
      □ ■ ■ ■ ■
      □ □ □ □ ■
      □ □ □ ■ □
    Musik: Spiele Ton [ A6 ] für [ 1 ] Schlag
```

**Spielleiter: Zufallsbereich erweitern**
```
// Statt "wähle zufällig 1 bis 3":
Setze aktuelleAufgabe auf: [ wähle zufällig 1 bis 5 ]
```

**Spieler: Neigungsaktionen hinzufügen**
```
┌─ dauerhaft ─────────────────────────────────────────────┐
│                                                          │
│  Wenn [ aufgabe ] > [ 0 ]                              │
│  UND  [ habeGesendet ] = [ falsch ] dann:              │
│                                                          │
│      // Links neigen: Beschleunigung X < -400 mg      │
│      Wenn [ Beschleunigung X ] < [ -400 ] dann:       │
│          Setze habeGesendet auf [ wahr ]               │
│          Funk: Sende Zahl [ meineID × 10 + 4 ]        │
│          Grundlagen: Zeige Symbol [ Pfeil links ]      │
│          Grundlagen: Pausiere [ 500 ] ms              │
│          Grundlagen: Bildschirm löschen               │
│                                                          │
│      // Rechts neigen: Beschleunigung X > 400 mg      │
│      Wenn [ Beschleunigung X ] > [ 400 ] dann:        │
│          Setze habeGesendet auf [ wahr ]               │
│          Funk: Sende Zahl [ meineID × 10 + 5 ]        │
│          Grundlagen: Zeige Symbol [ Pfeil rechts ]     │
│          Grundlagen: Pausiere [ 500 ] ms              │
│          Grundlagen: Bildschirm löschen               │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

> **⚠️ Achtung:** Der `dauerhaft`-Block wird sehr schnell ausgeführt. Ohne das `habeGesendet`-Flag würde der Spieler viele Nachrichten senden während er den Calliope geneigt hält. Prüfe genau, ob das Flag korrekt gesetzt wird!

---

## 5.2 Erweiterung: Zeitbonus

**Schwierigkeit:** ⭐⭐  
**Themen:** Zeitmessung, Variablen, Mathematik

### Idee

Schnelle Spieler bekommen **mehr Punkte**! Wer innerhalb der ersten Sekunde richtig antwortet, bekommt +3 Punkte. Wer innerhalb von 3 Sekunden richtig antwortet, bekommt +2 Punkte. Alle anderen bekommen +1 Punkt.

### Konzept

| Antwortzeit | Punkte |
|-------------|--------|
| < 1000 ms | **+3** |
| 1000–3000 ms | **+2** |
| > 3000 ms | **+1** |
| Falsch | **–1** |

### Planungsaufgabe

1. Wo und wie muss die **Startzeit** gemessen werden?  
   *(Tipp: `Eingabe: Laufzeit (ms)` gibt die Zeit seit Start des Calliopes)*
2. Wo wird die **Antwortzeit** berechnet?  
   *(Antwortzeit = Zeitpunkt der Antwort – Startzeit der Runde)*
3. Welche Variable brauchst du für die Startzeit?
4. Schreibe Pseudocode für die Punkte-Berechnung.

### Pseudocode (zum Vervollständigen)

```
// In rundeStarten:
Setze rundenStartzeit auf [ Laufzeit (ms) ]

// In richtigGeantwortet:
Setze antwortzeit auf [ Laufzeit (ms) - rundenStartzeit ]

Wenn [ antwortzeit ] < [ 1000 ] dann:
    Punkte += 3
Sonst wenn [ antwortzeit ] < [ 3000 ] dann:
    Punkte += ___
Sonst:
    Punkte += ___
```

---

## 5.3 Erweiterung: Spieler-ID per Schütteln wählen

**Schwierigkeit:** ⭐⭐  
**Themen:** Zufallszahlen, Ereignisse, Zustand

### Idee

Anstatt die ID manuell zu setzen, kann jeder Spieler den Calliope **schütteln**. Er bekommt dann eine zufällige freie ID zugewiesen. Der Spielleiter vergibt die IDs zentralisiert.

### Planungsaufgabe

1. Wie koordiniert man, dass keine zwei Spieler dieselbe ID bekommen?
2. Welche zusätzliche Funkkommunikation wäre nötig?  
   *(Hinweis: Der Spieler schüttelt → sendet „Ich brauche eine ID" → Spielleiter antwortet mit der nächsten freien ID)*
3. Zeichne das erweiterte Kommunikationsprotokoll als Sequenzdiagramm.

---

## 5.4 Erweiterung: Mehrere Runden-Modi

**Schwierigkeit:** ⭐⭐⭐  
**Themen:** Konfiguration, Menüsystem, Zustände

### Idee

Vor dem Spiel kann der **Spielleiter** einen von drei Modi auswählen:

| Modus | Beschreibung |
|-------|-------------|
| **Klassisch** | Nur Tastendruck-Aktionen (Codes 1–3) |
| **Standard** | Tastendruck + Neigung (Codes 1–5) |
| **Profi** | Alle Aktionen + kürzere Wartezeit (3 statt 5 Sek.) |

### Planungsaufgabe

1. Skizziere ein **Menü-System** für die Spielleiter-Oberfläche.
2. Welche Variablen brauchst du, um den gewählten Modus zu speichern?
3. Welche Teile des Programms müssen sich je nach Modus **unterschiedlich verhalten**?
4. Implementiere das Menüsystem (Taste A = nächste Option, A+B = bestätigen).

---

## 5.5 Erweiterung: Blitz-Runde

**Schwierigkeit:** ⭐⭐⭐  
**Themen:** Timing, Spielmechanik, Feedback

### Idee

In der „Blitz-Runde" (letzte Runde oder Stechen bei Gleichstand) gelten besondere Regeln:
- Wartezeit nur **2 Sekunden** statt 5
- Falsches Drücken führt zu **sofortigem Ausschluss** aus der Runde
- Der Gewinner der Blitz-Runde bekommt **doppelte Punkte**

### Planungsaufgabe

1. Wann wird die Blitz-Runde ausgelöst? (Gleichstand, letzte Runde, beides?)
2. Was bedeutet „Ausschluss aus der Runde" technisch gesehen?  
   *(Wird die Antwort ignoriert? Wird der Spieler per Funk informiert?)*
3. Implementiere die Blitz-Runde als optionale letzte Runde.

---

## 5.6 Erweiterung: Highscore-Speicher

**Schwierigkeit:** ⭐⭐⭐⭐  
**Themen:** Datenspeicherung, persistente Variablen

### Idee

Der beste Gesamtpunktestand wird **dauerhaft gespeichert** – auch wenn der Calliope ausgeschaltet wird!

### Relevante Blöcke

MakeCode bietet über die Kategorie **„Fortgeschritten"** → **„Gerät"** die Möglichkeit, Daten in internen Speicher zu schreiben:

```
Laufzeit: Schreibe Zahl auf Gerät
Laufzeit: Lies Zahl von Gerät
```

> **Hinweis:** Diese Blöcke sind in MakeCode für Calliope v1 ggf. als Erweiterung verfügbar. Prüft zunächst, ob eure Version dies unterstützt.

### Planungsaufgabe

1. Was genau soll gespeichert werden? (Höchstpunktzahl? Name? Datetime?)
2. Wann wird gespeichert? (Nach jeder Runde? Nur am Ende?)
3. Wie wird der Highscore angezeigt? (Beim Start? Auf Tastendruck?)

---

## 5.7 Eigene Idee umsetzen

**Schwierigkeit:** variabel

Entwickelt **eure eigene Erweiterung**! Das kann sein:
- Ein **komplett neues Signal** (z. B. Geräusch-Erkennung mit dem Mikrofon)
- Eine **neue Spieler-Feedback-Animation** (z. B. rollende Anzeige für Punkte)
- Ein **Spektatoren-Modus** (ein fünfter Calliope zeigt nur an, wer gerade führt)
- **Handicap-System** (Spieler mit mehr Punkten müssen länger warten)

### Projektplanungs-Vorlage

Beantworte für eure Idee folgende Fragen:

**1. Beschreibung der Erweiterung:**  
*(Was genau soll die Erweiterung machen?)*

**2. Betroffene Programmteile:**  
- [ ] Spielleiter beim Start
- [ ] Spielleiter: Rundenablauf
- [ ] Spielleiter: Funk-Empfang
- [ ] Spielleiter: Punkteverwaltung
- [ ] Spieler beim Start
- [ ] Spieler: Funk-Empfang
- [ ] Spieler: Aktionen senden
- [ ] Spieler: Feedback empfangen
- [ ] Neues Protokoll nötig

**3. Neue Variablen:**  

| Variable | Typ | Zweck |
|----------|-----|-------|
| | | |

**4. Neue Funktionen:**  

| Funktion | Aufgabe |
|----------|---------|
| | |

**5. Veränderte Funktionen:**  

| Funktion | Was ändert sich? |
|----------|-----------------|
| | |

**6. Testplan:**  
*(Wie überprüft ihr, ob eure Erweiterung korrekt funktioniert?)*

---

## 5.8 Reflexion und Präsentation

Am Ende der Lerneinheit präsentiert ihr euer Projekt. Bereitet folgendes vor:

### Präsentationsinhalt (ca. 5–10 Minuten)

1. **Vorführung:** Zeigt das Spiel live – mehrere Spieler gegen mehrere Zuschauer!
2. **Technik erklärt:** Erklärt an einem Beispiel, wie die Kommunikation zwischen Spielleiter und Spieler funktioniert (Codierung, Übertragung, Decodierung)
3. **Herausforderungen:** Was war schwierig? Welche Bugs habt ihr gefunden?
4. **Erweiterungen:** Welche Erweiterung habt ihr umgesetzt und warum?
5. **Verbesserungsideen:** Was würdet ihr noch machen, wenn ihr mehr Zeit hättet?

### Bewertungskriterien (mögliche Vorlage)

| Kriterium | max. Punkte |
|-----------|------------|
| Grundprogramm funktioniert (Spielleiter + mind. 2 Spieler) | 20 |
| Korrekte Punk-Vergabe (richtig/falsch) | 10 |
| Spielende und Gesamtsieger-Anzeige | 10 |
| Mindestens eine Erweiterung umgesetzt | 20 |
| Präsentation verständlich und vollständig | 20 |
| Code sauber und kommentiert | 10 |
| Teamarbeit und Eigenständigkeit | 10 |
| **Gesamt** | **100** |

---

← [Zurück: Punkte & Anzeige](04_punkte_und_anzeige.md) | [Zurück zur Übersicht](../README.md)
