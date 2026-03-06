# Kapitel 3: Die Spieler

← [Zurück: Spielleiter](02_spielleiter.md) | [Weiter: Punkte & Anzeige →](04_punkte_und_anzeige.md)

---

## 🎯 Lernziele dieses Kapitels

- Du verstehst, warum alle Spieler dasselbe Programm bekommen können
- Du kannst eine **Spieler-ID** zur Laufzeit konfigurieren
- Du empfängst Funksignale und reagierst auf sie
- Du sendest codierte Antworten an den Spielleiter
- Du gibst dem Spieler visuelles Feedback (richtig / falsch / Gewinner)

---

## 3.1 Konzept: Ein Programm – vier Spieler

Ein wichtiger Grundsatz der Informatik ist die **Wiederverwendbarkeit von Code**. Anstatt vier verschiedene Programme für die vier Spieler zu schreiben, schreiben wir **ein einziges Spieler-Programm**, das sich durch die **Spieler-ID** unterscheidet.

### Wie legen wir die ID fest?

Die ID wird beim Start durch Tastendruck gesetzt:

|  Tasten  | Spieler-ID |
|----------|-----------|
| Taste A  | ID = 1 |
| Taste B  | ID = 2 |
| A + B gleichzeitig | ID = 3 |
| Taste A nach dem Start (2. Mal) | ID = 4 |

> **💡 Elegantere Lösung:** In Kapitel 5 (Erweiterungen) lernst du, wie man die ID eleganter setzen kann, z. B. durch mehrfaches Drücken von A.

---

## 3.2 Variablen und Startkonfiguration

### Benötigte Variablen

| Variable | Typ | Bedeutung |
|----------|-----|-----------|
| `meineID` | Zahl (1–4) | Diese Instanz ist Spieler X |
| `idGesetzt` | Ja/Nein | Wurde die ID schon gewählt? |
| `aufgabe` | Zahl (0–5) | Aktuell aktive Aufgabe (0 = keine) |
| `habeGesendet` | Ja/Nein | Habe ich in dieser Runde schon geantwortet? |

### Schritt 1: Variablen anlegen und initialisieren

```
┌─ beim Start ─────────────────────────────────────────────┐
│                                                           │
│  Funk: Setze Gruppe auf [ 1 ]                            │
│                                                           │
│  Setze meineID auf [ 0 ]                                │
│  Setze idGesetzt auf [ falsch ]                         │
│  Setze aufgabe auf [ 0 ]                                │
│  Setze habeGesendet auf [ falsch ]                      │
│                                                           │
│  // Bitte den Spieler, seine ID zu wählen               │
│  Grundlagen: Zeige Zeichenkette [ "ID?" ]               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

## 3.3 ID-Auswahl beim Start

### Schritt 2: ID über Taste A setzen

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
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

### Schritt 3: ID über Taste B setzen

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
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

### Schritt 4: IDs 3 und 4 über A+B und gesonderten Mechanismus

```
┌─ Wenn Knopf A+B gedrückt ───────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
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

> **Spieler 4 – Sonderfall:** Da es keine vierte Taste gibt, verwenden wir Taste A wenn die ID bereits auf 3 gesetzt ist, um auf 4 zu wechseln – oder wir nutzen einen Schrägstrich an Stelle von Taste A beim zweiten Drücken. Alternativ: Im Kapitel 5 lernst du die elegante Variante mit dem Beschleunigungssensor.

**Einfache Variante für Spieler 4:**
```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ falsch ] dann:                  │
│      → ID = 1 (wie oben)                               │
│  Sonst wenn [ meineID ] = [ 3 ] dann:                  │
│  ┌──────────────────────────────────────────────────┐   │
│  │  // Upgrade: von ID 3 auf ID 4 wechseln         │   │
│  │  Setze meineID auf [ 4 ]                        │   │
│  │  Grundlagen: Zeige Zahl [ 4 ]                   │   │
│  │  Grundlagen: Pausiere [ 1000 ] ms               │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **⚠️ Wichtig:** Alle Spieler-Calliopes müssen **vor dem ersten Spielstart** ihre ID gewählt haben!

---

## 3.4 Funksignal vom Spielleiter empfangen

### Schritt 5: Auf Signal reagieren

Der Spielleiter sendet eine Zahl (1, 2 oder 3) als Signal. Der Spieler muss:
1. Das Signal empfangen und speichern
2. Das Signal auf dem eigenen Display anzeigen
3. Den Spieler bereit machen (Eingaben aktivieren)

```
┌─ Funk: Wenn Zahl empfangen wird (empfangeneZahl) ────────┐
│                                                           │
│  // Nur reagieren wenn ID bereits gesetzt                │
│  Wenn [ idGesetzt ] = [ wahr ] dann:                    │
│                                                           │
│      // Ist es ein Signal (1-3) oder eine andere Nachricht?  │
│      Wenn [ empfangeneZahl ] ≥ [ 1 ]                    │
│      UND  [ empfangeneZahl ] ≤ [ 3 ] dann:             │
│      ┌──────────────────────────────────────────────┐   │
│      │  Setze aufgabe auf [ empfangeneZahl ]        │   │
│      │  Setze habeGesendet auf [ falsch ]           │   │
│      │  rufauf: signalAnzeigen                      │   │
│      └──────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

### Schritt 6: Funktion `signalAnzeigen` (Spieler-Version)

Die Spieler-Version der Signalanzeige zeigt dem Spieler, was zu tun ist:

```
┌─ Funktion signalAnzeigen ────────────────────────────────┐
│                                                           │
│  Wenn [ aufgabe ] = [ 1 ] dann:                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Grün ]                    │   │
│  │  Grundlagen: Zeige LEDs                         │   │
│  │    Zeige "A" als Muster (großes A auf Matrix)   │   │
│  │  Musik: Spiele Ton [ A5 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst wenn [ aufgabe ] = [ 2 ] dann:                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Rot ]                     │   │
│  │  Grundlagen: Zeige LEDs                         │   │
│  │    Zeige "B" als Muster                         │   │
│  │  Musik: Spiele Ton [ A3 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  Sonst (aufgabe = 3):                                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  LED: Setze RGB auf [ Blau ]                    │   │
│  │  Grundlagen: Zeige Symbol [ ♥ ]                 │   │
│  │  Musik: Spiele Ton [ A4 ] für [ ½ ] Schlag      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **📌 Wichtig:** Spielleiter und Spieler müssen **identische Signalcodes** verwenden!  
> Aufgabencode `1` = „grün, Ton hoch, drücke A" – auf beiden Geräten gleich.

---

## 3.5 Spieleraktionen senden

### Schritt 7: Auf Taste A reagieren (Spieleraktion)

Wenn der Spieler Taste A drückt, meint er damit „Aktion 1". Wir senden:  
`meineID × 10 + 1`

```
┌─ Wenn Knopf A gedrückt ─────────────────────────────────┐
│                                                           │
│  // Nur senden wenn ID gesetzt, Aufgabe aktiv,          │
│  // und noch nicht gesendet                             │
│  Wenn [ idGesetzt ] = [ wahr ]                          │
│  UND  [ aufgabe ] > [ 0 ]                               │
│  UND  [ habeGesendet ] = [ falsch ] dann:               │
│                                                           │
│      Setze habeGesendet auf [ wahr ]                    │
│                                                           │
│      // Nachricht codieren und senden: ID*10 + 1        │
│      Funk: Sende Zahl [ meineID × 10 + 1 ]              │
│                                                           │
│      // Visuelles Feedback: "Ich habe gesendet"         │
│      Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]          │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│      Grundlagen: Bildschirm löschen                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Berechnung im Block:**
- In MakeCode: `meineID` (Variable) `×` `10` (Mathematik-Block: mal) `+` `1`
- Das ergibt z. B. für Spieler 2: `2 × 10 + 1 = 21`

### Schritt 8: Auf Taste B reagieren (Aktion 2)

```
┌─ Wenn Knopf B gedrückt ─────────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ wahr ]                          │
│  UND  [ aufgabe ] > [ 0 ]                               │
│  UND  [ habeGesendet ] = [ falsch ] dann:               │
│                                                           │
│      Setze habeGesendet auf [ wahr ]                    │
│      Funk: Sende Zahl [ meineID × 10 + 2 ]              │
│                                                           │
│      Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]          │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│      Grundlagen: Bildschirm löschen                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

### Schritt 9: Auf A+B reagieren (Aktion 3)

```
┌─ Wenn Knopf A+B gedrückt ───────────────────────────────┐
│                                                           │
│  Wenn [ idGesetzt ] = [ wahr ]                          │
│  UND  [ aufgabe ] > [ 0 ]                               │
│  UND  [ habeGesendet ] = [ falsch ] dann:               │
│                                                           │
│      Setze habeGesendet auf [ wahr ]                    │
│      Funk: Sende Zahl [ meineID × 10 + 3 ]              │
│                                                           │
│      Grundlagen: Zeige Symbol [ Pfeil hoch ↑ ]          │
│      Grundlagen: Pausiere [ 500 ] ms                    │
│      Grundlagen: Bildschirm löschen                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **💡 Warum `habeGesendet`?**  
> Ohne dieses Flag könnte ein Spieler mehrfach senden – z. B. durch schnelles Drücken. Das würde das Spiel verfälschen. Das Flag stellt sicher, dass pro Runde nur **eine** Antwort gesendet wird.

---

## 3.6 Feedback vom Spielleiter empfangen

Der Spielleiter sendet nach jeder Runde Rückmeldungen per `Funk: Sende Wert`. Das sind **Name-Wert-Paare** (z. B. `"gewinner" = 2`). Der Spieler-Calliope empfängt diese und zeigt dem Spieler, was passiert ist.

### Schritt 10: Auf Wert-Nachrichten reagieren

```
┌─ Funk: Wenn Wert empfangen wird (name, wert) ────────────┐
│                                                           │
│  // War ich der Gewinner?                               │
│  Wenn [ name ] = [ "gewinner" ] dann:                   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Wenn [ wert ] = [ meineID ] dann:              │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Ich habe gewonnen! 🎉               │   │   │
│  │  │  LED: Setze RGB auf [ Grün ]            │   │   │
│  │  │  Grundlagen: Zeige Symbol [ Haken ✓ ]   │   │   │
│  │  │  Musik: Melodie [ Dadadum ] abspielen   │   │   │
│  │  │  Grundlagen: Pausiere [ 2000 ] ms       │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  │  Sonst:                                          │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Jemand anderes hat gewonnen         │   │   │
│  │  │  LED: Setze RGB auf [ aus ]             │   │   │
│  │  │  Grundlagen: Zeige Zeichenkette ["S"]   │   │   │
│  │  │  Grundlagen: Zeige Zahl [ wert ]        │   │   │
│  │  │  Grundlagen: Pausiere [ 2000 ] ms       │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  │                                                  │   │
│  │  // Aufräumen nach der Runde                    │   │
│  │  Setze aufgabe auf [ 0 ]                        │   │
│  │  LED: Setze RGB auf [ aus ]                     │   │
│  │  Grundlagen: Bildschirm löschen                 │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
│  // Wurde ich für falsche Antwort bestraft?             │
│  Wenn [ name ] = [ "falsch" ] dann:                     │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Wenn [ wert ] = [ meineID ] dann:              │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │  // Ich war falsch! -1 Punkt 😬          │   │   │
│  │  │  LED: Setze RGB auf [ Rot ]             │   │   │
│  │  │  Grundlagen: Zeige Symbol [ Kreuz ✕ ]   │   │   │
│  │  │  Musik: Spiele Ton [ A1 ] für [1] Schlag│   │   │
│  │  │  Grundlagen: Pausiere [ 1500 ] ms       │   │   │
│  │  │  LED: Setze RGB auf [ aus ]             │   │   │
│  │  │  Grundlagen: Bildschirm löschen         │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────┘   │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**Wo findest du `Wenn Wert empfangen wird`?**  
→ Kategorie **Funk** → `Wenn Wert empfangen wird (name)(wert)` 

> **⚠️ Achtung:** Der Block `Wenn Zahl empfangen` und `Wenn Wert empfangen` sind **unterschiedliche Blöcke**! Der Spielleiter sendet sowohl Zahlen (Signal-Codes) als auch Werte (Feedback). Du brauchst beide Empfänger-Blöcke im Spieler-Programm.

---

## 3.7 Das vollständige Spieler-Programm – Übersicht

```
SPIELER – Übersicht aller Blöcke

► beim Start
   → Gruppe 1 setzen, Variablen initialisieren, "ID?" anzeigen

► Wenn Knopf A gedrückt
   → Wenn ID noch nicht gesetzt: ID = 1 setzen
   → Wenn ID = 3: ID = 4 (Upgrade)
   → Wenn Spiel läuft: Aktion 1 senden (ID×10 + 1)

► Wenn Knopf B gedrückt
   → Wenn ID noch nicht gesetzt: ID = 2 setzen
   → Wenn Spiel läuft: Aktion 2 senden (ID×10 + 2)

► Wenn Knopf A+B gedrückt
   → Wenn ID noch nicht gesetzt: ID = 3 setzen
   → Wenn Spiel läuft: Aktion 3 senden (ID×10 + 3)

► Funk: Wenn Zahl empfangen (empfangeneZahl)
   → Wenn 1-3: Aufgabe setzen, Signal anzeigen, habeGesendet = falsch

► Funk: Wenn Wert empfangen (name, wert)
   → "gewinner": Gewinner-Feedback anzeigen
   → "falsch": Fehler-Feedback anzeigen

► Funktion: signalAnzeigen
   → Farbe + LEDs + Ton je nach Aufgabe
```

---

## 3.8 Test: Spielleiter und Spieler zusammen

Bevor du weitermachst, führe einen gemeinsamen Test durch.

### Checkliste für den Test

**Voraussetzungen:**
- [ ] Spielleiter-Programm ist auf einem Calliope (Kapitel 2)
- [ ] Spieler-Programm ist auf einem weiteren Calliope
- [ ] Beide sind eingeschaltet und im Bereich von ~10 Metern

**Testablauf:**
1. [ ] Spieler drückt A (ID = 1 wird auf dem Spieler-Display angezeigt)
2. [ ] Spielleiter drückt A (Signal erscheint auf beiden Displays, Ton ertönt)
3. [ ] Spieler reagiert mit der richtigen Taste
4. [ ] Spielleiter zeigt „S1" (Gewinner) an
5. [ ] Spieler zeigt Haken an
6. [ ] Spielleiter drückt B → Punktestand wird angezeigt

---

## 3.9 Aufgaben

### Aufgabe 3.1: Spieler-Programm aufbauen ⭐

Baue das Spieler-Programm in MakeCode Schritt für Schritt auf. Teste gemeinsam mit mindestens einem weiteren Calliope (Spielleiter).

### Aufgabe 3.2: Nachricht verfolgen ⭐⭐

Spieler 3 drückt nach dem Signal Taste B. 

1. Welche Zahl wird an den Spielleiter gesendet?
2. Der Spielleiter hat `aktuelleAufgabe = 2`. War die Antwort richtig?
3. Was passiert auf dem Spieler-Calliope als Feedback?

### Aufgabe 3.3: Fehlerfall analysieren ⭐⭐

Ein Spieler hat vergessen, `habeGesendet` auf `wahr` zu setzen.

1. Was könnte passieren, wenn der Spieler Taste A mehrmals schnell drückt?
2. Könnte dies zu falschen Ergebnissen beim Spielleiter führen? Begründe.
3. Wie würdest du das Problem im Spielleiter-Programm alternativ absichern?

### Aufgabe 3.4: Zwei-Spieler-Test ⭐⭐

Teste das Spiel mit **zwei Spieler-Calliopes** und dem Spielleiter.

- Drückt einer der Spieler immer die falsche Taste. Was passiert mit seinen Punkten?
- Drückt ein Spieler gar nicht. Was passiert nach 5 Sekunden?
- Drücken beide Spieler gleichzeitig die richtige Taste. Wer gewinnt? (Tipp: Funk-Pakete kommen sequenziell an – wer zuerst sendet, gewinnt.)

### Aufgabe 3.5: Eigene Erweiterung ⭐⭐⭐

Füge deinem Spieler-Programm eine **Anzeige der eigenen Runden** hinzu:
- Der Spieler zählt selbst mit, wie viele Runden er gespielt hat
- Er zählt auch, wie viele davon er richtig beantwortet hat
- Nach 5 Runden zeigt er seine persönliche **Trefferquote** in Prozent an

*Tipp: Nutze zusätzliche Variablen und rechne den Prozentwert mit `Mathematik`-Blöcken.*

---

## ✅ Zusammenfassung

Das Spieler-Programm kann jetzt:
- ✅ Eine Spieler-ID beim Start konfigurieren
- ✅ Signal-Codes vom Spielleiter empfangen und anzeigen
- ✅ Aktionen codieren und per Funk senden
- ✅ Sicherstellen, dass pro Runde nur eine Antwort gesendet wird
- ✅ Feedback (Gewinner, Fehler) vom Spielleiter empfangen und anzeigen

---

← [Zurück: Spielleiter](02_spielleiter.md) | [Weiter: Punkte & Anzeige →](04_punkte_und_anzeige.md)
