# Sitzung 10: Prüfungsvorbereitungs-Quiz — Dictionaries, Freitext & Auswertung

| Block | Inhalt | Umgebung |
|-------|--------|----------|
| **A** | Dictionaries verstehen und bauen | Spyder |
| **B** | Quiz-Struktur: Liste von Dictionaries | Spyder |
| **C** | Quiz in PsychoPy: Freitexteingabe + Feedback | PsychoPy |

Arbeitsblatt für heute: `obstsalat.py` (in PsychoPy öffnen).

PsychoPy lässt sich natürlich auch optimal zur Prüfungsvorbereitung nutzen. Z.B. durch ein selbst erstelltes Quiz.

---

# Block A — Dictionaries

Wiederholung  Listen:

```python
fruechte = ["Banane", "Erdbeere", "Kiwi"]
```

Listen haben einen **Nachteil**: Man greift auf Elemente nur über ihre Position zu (`fruechte[0]`). Was, wenn man stattdessen sagen will: *„Wie viele Kalorien hat eine Banane?"*

Das lässt sich prima über ein **Dictionaries** lösen, das eine Sammlungen von **Schlüssel-Wert-Paaren** ist:

```python
kalorien = {
    "Banane":    89,
    "Erdbeere":  32,
    "Kiwi":      61
}
```

Zugriff über den Schlüssel statt über eine Zahl:

```python
print(kalorien["Banane"])   # → 89
print(kalorien["Kiwi"])     # → 61
```

Neuen Eintrag hinzufügen:

```python
kalorien["Mango"] = 60
```

Alle Schlüssel oder Werte ausgeben:

```python
print(kalorien.keys())    # → dict_keys(['Banane', 'Erdbeere', 'Kiwi', 'Mango'])
print(kalorien.values())  # → dict_values([89, 32, 61, 60])
```

Über ein Dictionary schleifen:

```python
for frucht, kal in kalorien.items():
    print(frucht, "hat", kal, "Kalorien")
```

### 🟦 Übung A1 — eigenes Dictionary

Lege ein Dictionary `preise` an mit mindestens 4 Früchten und ihrem Preis pro 100g (erfundene Werte reichen). Gib den Preis von zwei Früchten aus.

#### Lösung A1

```python
preise = {
    "Banane":    0.19,
    "Erdbeere":  0.89,
    "Kiwi":      0.35,
    "Mango":     0.75
}

print(preise["Erdbeere"])
print(preise["Mango"])
```

### 🟦 Übung A2 — Schleife über Dictionary

Gib für jede Frucht aus: `"Banane kostet 0.19 €"` usw.

#### Lösung A2

```python
for frucht, preis in preise.items():
    print(frucht, "kostet", preis, "€")
```

---

# Block B — Liste von Dictionaries

Ein Dictionary kann einen einzelnen Trial beschreiben. Eine **Liste von Dictionaries** beschreibt ein ganzes Experiment.

```python
fragen = [
    {
        "frage":    "Welche Frucht ist gelb und gebogen?",
        "antworten": ["banane", "banana"]
    },
    {
        "frage":    "Welche rote Frucht hat viele kleine Kerne außen?",
        "antworten": ["erdbeere", "himbeere"]
    },
    {
        "frage":    "Aus welcher Frucht wird Orangensaft gemacht?",
        "antworten": ["orange"]
    },
    {
        "frage":    "Welche grüne Frucht hat eine braune, pelzige Schale?",
        "antworten": ["kiwi"]
    },
    {
        "frage":    "Welche tropische gelbe Frucht schmeckt süß-sauer und wächst auf einer Palme?",
        "antworten": ["ananas", "pineapple"]
    }
]
```

Auf einen einzelnen Trial zugreifen:

```python
trial = fragen[0]
print(trial["frage"])       # → "Welche Frucht ist gelb und gebogen?"
print(trial["antworten"])   # → ["banane", "banana"]
```

In einer Schleife über alle Trials:

```python
for trial in fragen:
    print(trial["frage"])
    print("Richtige Antworten:", trial["antworten"])
```

### Automatische Auswertung

Der Vergleich funktioniert mit `in` — genauso wie `keyList=["d", "k"]` in PsychoPy, nur auf eine Liste von Strings angewendet:

```python
eingabe = "Banane"

korrekt = int(eingabe.strip().lower() in trial["antworten"])
```

- `.strip()` entfernt Leerzeichen am Rand
- `.lower()` macht alles kleingeschrieben
- `in` prüft: ist der String **exakt** eines der Elemente in der Liste?

> **Hinweis:** `"bana" in ["banane"]` ist `False` — `in` auf Listen prüft die ganze Liste auf exakte Übereinstimmung, kein Teilstring-Vergleich. Anders als `"bana" in "banane"` (das wäre `True`).

### 🟦 Übung B1 — manueller Test

Teste die Auswertungslogik in Spyder, **ohne** PsychoPy. Schreibe eine Schleife, die für jeden Trial `input()` aufruft und `korrekt` ausgibt.

#### Lösung B1

```python
punkte = 0

for trial in fragen:
    print(trial["frage"])
    eingabe = input("Deine Antwort: ")
    korrekt = int(eingabe.strip().lower() in trial["antworten"])
    punkte += korrekt
    print("Korrekt!" if korrekt else f"Falsch. Richtig wäre: {trial['antworten'][0]}")

print(f"\nErgebnis: {punkte} von {len(fragen)} Punkten")
```

### 🟨 Übung B2 — eigene Fragen

Füge zwei eigene Fragen zum `fragen`-Dictionary hinzu — zu einem Thema, auf das du dich gerade vorbereitest. Trage jeweils mindestens zwei gültige Antworten ein.

---

# Block C — Quiz in PsychoPy

Jetzt bauen wir dasselbe als richtiges PsychoPy-Experiment mit `frage_freitext` aus Sitzung 9.

```python
from psychopy import visual, core, event
import os

win = visual.Window([800, 600], color="white", units="deg",
                    monitor="testMonitor", fullscr=False)


# Freitext-Funktion

def frage_freitext(win, frage_text):
    frage        = visual.TextStim(win, text=frage_text, pos=(0, 3),
                                   color="black", height=0.7, wrapWidth=20)
    eingabe_stim = visual.TextStim(win, text="", pos=(0, -1),
                                   color="black", height=1.0)
    eingabe = ""
    fertig  = False
    event.clearEvents()

    while not fertig:
        for k in event.getKeys():
            if k == "return" and len(eingabe) > 0:
                fertig = True
            elif k == "backspace":
                eingabe = eingabe[:-1]
            elif k == "space":
                eingabe = eingabe + " "
            elif len(k) == 1:
                eingabe = eingabe + k

        eingabe_stim.text = eingabe + "_"
        frage.draw()
        eingabe_stim.draw()
        win.flip()

    return eingabe


#  Frageliste 

fragen = [
    {
        "frage":    "Welche Frucht ist gelb und gebogen?",
        "antworten": ["banane", "banana"]
    },
    {
        "frage":    "Welche rote Frucht hat viele kleine Kerne außen?",
        "antworten": ["erdbeere", "himbeere"]
    },
    {
        "frage":    "Aus welcher Frucht wird Orangensaft gemacht?",
        "antworten": ["orange"]
    },
    {
        "frage":    "Welche grüne Frucht hat eine braune, pelzige Schale?",
        "antworten": ["kiwi"]
    },
    {
        "frage":    "Welche tropische gelbe Frucht schmeckt süß-sauer und wächst auf einer Palme?",
        "antworten": ["ananas", "pineapple"]
    }
]


#  Hauptfunktion 

def run_quiz(win, sbj_num):

    # Begrüßung
    intro = visual.TextStim(win,
                            text="Obstsalat-Quiz!\n\nBeantworte jede Frage mit der Tastatur.\nWeiter mit Leertaste.",
                            color="black", height=0.8)
    intro.draw()
    win.flip()
    event.waitKeys(keyList=["space"])

    # Logfile vorbereiten
    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")

    filename = os.path.join("Logfiles", f"VP_{sbj_num}_quiz.csv")
    header   = "vp_num,frage_nr,frage,eingabe,korrekt\n"

    punkte = 0

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        for i, trial in enumerate(fragen):

            eingabe = frage_freitext(win, trial["frage"])
            korrekt = int(eingabe.strip().lower() in trial["antworten"])
            punkte += korrekt

            # Feedback
            farbe    = "green" if korrekt else "red"
            feedback = "Richtig! ✓" if korrekt else f"Falsch. Richtig: {trial['antworten'][0]}"
            fb_stim  = visual.TextStim(win, text=feedback, color=farbe, height=0.9)
            fb_stim.draw()
            win.flip()
            core.wait(1.5)

            logfile.write(
                f"{sbj_num},{i + 1},{trial['frage']},{eingabe},{korrekt}\n"
            )

    # Abschluss
    ergebnis = visual.TextStim(win,
                               text=f"Fertig!\nDu hast {punkte} von {len(fragen)} Punkten.",
                               color="black", height=0.9)
    ergebnis.draw()
    win.flip()
    event.waitKeys(keyList=["space"])


# Starten 

run_quiz(win, "999")
win.close()
core.quit()
```

### 🟦 Übung C1 — Feedback verbessern

Zeige im Feedback-Screen auch, was die Person eingegeben hat:  
z.B. *„Du hast 'Birne' geschrieben. Richtig wäre: banane"*

#### Lösung C1

```python
if korrekt:
    feedback = "Richtig! ✓"
else:
    feedback = f"Du hast '{eingabe}' geschrieben.\nRichtig wäre: {trial['antworten'][0]}"
```

### 🟦 Übung C2 — Fragen mischen

Mische die Reihenfolge der Fragen vor dem Quiz zufällig.

#### Lösung C2

```python
import random
random.shuffle(fragen)
```

Eine Zeile — direkt vor der Schleife. Danke an `fragen` als Liste: `shuffle` funktioniert auf jeder Liste, egal was drin steckt.

### 🟨 Übung C3 — Zeitdruck

Füge ein Zeitlimit von 10 Sekunden pro Frage ein. Läuft die Zeit ab, wird die Antwort als falsch gewertet.

> Hinweis: Du brauchst `core.Clock()` und eine zusätzliche Abbruchbedingung in der `while`-Schleife.

#### Lösung C3

```python
def frage_freitext_timed(win, frage_text, zeitlimit=4):
    frage        = visual.TextStim(win, text=frage_text, pos=(0, 3),
                                   color="black", height=0.7, wrapWidth=20)
    eingabe_stim = visual.TextStim(win, text="", pos=(0, -1),
                                   color="black", height=1.0)
    timer_stim   = visual.TextStim(win, text="", pos=(0, -4),
                                   color="grey", height=0.6)

    eingabe = ""
    fertig  = False
    clock   = core.Clock()
    event.clearEvents()

    while not fertig:
        verbleibend = zeitlimit - clock.getTime()
        if verbleibend <= 0:
            break                           # Zeit abgelaufen → leere Antwort

        for k in event.getKeys():
            if k == "return" and len(eingabe) > 0:
                fertig = True
            elif k == "backspace":
                eingabe = eingabe[:-1]
            elif k == "space":
                eingabe = eingabe + " "
            elif len(k) == 1:
                eingabe = eingabe + k

        eingabe_stim.text  = eingabe + "_"
        timer_stim.text    = f"{verbleibend:.0f} s"
        frage.draw()
        eingabe_stim.draw()
        timer_stim.draw()
        win.flip()

    return eingabe   # leer, wenn Zeit abgelaufen
```

---

Vielen Dank für das schöne Seminar und viel Erfolg bei euren Prüfungen!
