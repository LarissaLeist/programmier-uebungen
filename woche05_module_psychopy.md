# Woche 5: Module und der Übergang zu PsychoPy
## Von reinem Python zur Experimentalsoftware


## Lernziele

Nach dieser Sitzung können Sie:

- erklären, was ein Modul ist und warum Module existieren
- Module mit `import`, `from ... import` und `as` einbinden
- `random` für kontrollierte Stimulus-Randomisierung einsetzen
- `pathlib` für Dateipfade und Ordneroperationen nutzen
- `csv` für das Schreiben von Logfiles verwenden
- den Unterschied zwischen reinem Python und PsychoPy erklären
- die Grundstruktur eines PsychoPy-Skripts lesen und einordnen

---

## 0. Orientierung: Warum Module – und warum jetzt?

In Woche 4 haben Sie gelernt, Code in **Funktionen** zu kapseln: einmal schreiben, beliebig oft aufrufen.  
Module sind der nächste Schritt: **einmal schreiben, überall einbinden** – auch in anderen Projekten und von anderen Personen.

Warum das für Experimente entscheidend ist: Alles, was Sie bisher geschrieben haben – Stimuluslisten, Schleifen, Auswertungsfunktionen – setzt voraus, dass die Stimuli in einer **zufälligen Reihenfolge** erscheinen, dass Reaktionszeiten **präzise gemessen** werden und dass Daten **sicher gespeichert** werden. Dafür braucht man Module.

> 💡 **Beispiele:** Modul mit Funktion zum EEG/EMG-Trigger senden   

## Theorieteil

## 1. Was ist ein Modul?

Ein **Modul** ist eine Python-Datei (`.py`), die Funktionen, Variablen und Klassen enthält, die man importieren kann.

Python bringt eine große **Standardbibliothek** mit, die ohne Installation verfügbar ist. Beispiele:

| Modul | Inhalt | Experimenteller Einsatz |
|---|---|---|
| `random` | Zufallszahlen, Mischen | Stimuli randomisieren, Bedingungen zuweisen |
| `pathlib` | Dateipfade, Ordner | Logfiles anlegen, Dateistruktur verwalten |
| `csv` | CSV-Dateien lesen/schreiben | Ergebnisse speichern |

Darüber hinaus gibt es **externe Pakete**, die man mit `pip` installiert:

```
pip install psychopy    # die Experimentalsoftware
pip install pandas      # Datenanalyse
pip install numpy       # numerische Berechnungen
```


## 2. Module importieren – drei Wege

### 2.1 Das ganze Modul importieren

```python
import random
```

Um das Modul zu verwenden, wird es als **Präfix** vorangestellt: `random.randint()`.  

```python
zahl = random.randint(1, 6)   # Würfelwurf
print(zahl)
```

Das macht klar, woher die Funktion kommt und verhindert Namenskonflikte.


### 2.2 Einzelne Funktionen importieren

```python
from random import randint, choice, shuffle

zahl = randint(1, 6)    # kein Präfix nötig
```

Vorteil: kürzer. Nachteil: unklar, woher die Funktion kommt, wenn man den Import-Block übersieht.

> 💡 **Empfehlung:** In Experimenten lieber `import random` und dann `random.shuffle()` –  
> so bleibt die Herkunft immer sichtbar.

### 2.3 Aliase vergeben mit `as`

Manche Pakete haben lange Namen:

```python
import random as rnd
import pathlib as pl
import numpy as np       # Konvention in der Wissenschaft

zahl = rnd.randint(1, 6)
pfad = pl.Path("daten") / "vp01.csv"
```


## 3. `random` – Zufall kontrollieren

Es lohnt sich immer, einen Blick in die Dokumentation zu werfen.

https://docs.python.org/3/library/random.html

### 3.1 Wichtige Funktionen

```python
import random

# Zufällige ganze Zahl (beide Grenzen inklusive)
print(random.randint(1, 6))          # z. B. 4

# Zufällige Auswahl aus einer Liste
stimuli = ["Haus", "Baum", "Auto", "Hund"]
print(random.choice(stimuli))        # z. B. "Auto"

# Liste durchmischen (verändert die Liste direkt)
trial_reihenfolge = [1, 2, 3, 4, 5, 6]
random.shuffle(trial_reihenfolge)
print(trial_reihenfolge)             # z. B. [3, 1, 5, 2, 6, 4]

# Zufällige Stichprobe ohne Zurücklegen
auswahl = random.sample(stimuli, k=3)
print(auswahl)                       # z. B. ["Hund", "Haus", "Baum"]
```

### 3.2 Reproduzierbarkeit mit `seed()`

Zufallszahlen in Python sind **pseudozufällig**: Eine Startzahl (Seed) bestimmt die gesamte Folge.  
Das ist in der Wissenschaft wichtig, da Sie damit dasselbe Ergebnis reproduzieren können.

> 💡 **Beispiel:** Trainingstrials sollen Probandenübergreifend gleich sein, Experimentaltrials unterschiedlich


```python
import random

random.seed(42)    # immer dieselbe "zufällige" Reihenfolge
stimuli = ["ROT", "BLAU", "GRÜN", "GELB"]
random.shuffle(stimuli)
print(stimuli)    # → immer gleich, solange seed=42
```

### 3.3 Trialliste randomisieren

Beispielsweise, um Trainingstrials immer gleich zu halten.

```python
import random

SEED = 42

woerter = ["ROT", "BLAU", "GRÜN"] * 4    # je 4 Wiederholungen
farben  = ["rot", "blau", "grün"] * 4

# Trials als Paare erzeugen
trials = list(zip(woerter, farben))
print(f"Trials vor Randomisierung: {trials[:3]} ...")

random.seed(SEED)
random.shuffle(trials)
print(f"Trials nach Randomisierung: {trials[:3]} ...")
```

> 💡 `zip()` verbindet zwei Listen elementweise: `zip(["a","b"], [1,2])` → `[("a",1), ("b",2)]`

---

## 4. `pathlib` – Dateipfade und Ordner

Dateipfade unterscheiden sich zwischen Betriebssystemen (`\` vs. `/`).  
`pathlib.Path` löst das elegant:

```python
from pathlib import Path

# Pfade zusammensetzen – OS-unabhängig
daten_ordner = Path("logfiles")
logfile = daten_ordner / "vp01_stroop.csv"

print(logfile)                    # logfiles/vp01_stroop.csv (Linux/Mac)
                                  # logfiles\vp01_stroop.csv (Windows)
print(logfile.name)               # → "vp01_stroop.csv"
print(logfile.suffix)             # → ".csv"
print(logfile.stem)               # → "vp01_stroop"
print(logfile.parent)             # → logfiles
```

### 4.1 Ordner anlegen

```python
from pathlib import Path

daten_ordner = Path("logfiles")
daten_ordner.mkdir(exist_ok=True)   # Ordner anlegen, Fehler ignorieren wenn er schon existiert
```

> 💡 `exist_ok=True` ist wichtig: Ohne es würde Python einen Fehler werfen, wenn der Ordner schon da ist.

### 4.2 Dateipfade mit Variablen

```python
from pathlib import Path

vp_id    = "VP03"
aufgabe  = "stroop"
daten_ordner = Path("logfiles")
daten_ordner.mkdir(exist_ok=True)

logfile = daten_ordner / f"{vp_id}_{aufgabe}.csv"
print(logfile)   # → logfiles/VP03_stroop.csv
```

---

## 5. `csv` – Logfiles schreiben

In der Psychologie werden Daten fast immer als CSV (Comma-Separated Values) gespeichert. Das Format ist einfach, universell lesbar und kompatibel mit SPSS, R und Excel.

### 5.1 Logfile anlegen und schreiben

```python
import csv
from pathlib import Path

# Datei öffnen und Kopfzeile schreiben
logfile = Path("logfiles") / "vp01_stroop.csv"

with open(logfile, mode="w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerow(["trial", "wort", "farbe", "antwort", "korrekt", "rt"])

print(f"Logfile angelegt: {logfile}")
```

### 5.2 Zeilen anfügen

```python
import csv
from pathlib import Path

logfile = Path("daten") / "vp01_stroop.csv"

# Daten anfügen (mode="a" für append)
with open(logfile, mode="a", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerow([1, "ROT", "rot", "rot", True, 412])
    writer.writerow([2, "BLAU", "rot", "blau", False, 587])

print("Trials gespeichert.")
```

### 5.3 `with`-Statement – warum?

`with open(...) as f:` öffnet die Datei und schließt sie **automatisch** am Ende des Blocks –  
auch wenn ein Fehler auftritt. Das schützt vor beschädigten Logfiles.

```python
# ❌ Riskant: Datei wird bei Fehler nicht geschlossen
f = open("log.csv", "w")
f.write("Daten")
# → wenn hier ein Fehler passiert, bleibt die Datei offen

# ✓ Sicher: with schließt immer
with open("log.csv", "w") as f:
    f.write("Daten")
    # → Datei wird hier automatisch geschlossen
```


## 6. Eigene Module – ein Ausblick

Alles, was Sie bisher in einer Zelle/Datei geschrieben haben, kann als Modul ausgelagert werden.

Angenommen, Sie haben eine Datei `auswertung.py`:

```python
# auswertung.py

def berechne_congruency(wort, farbe):
    """Berechnet die Kongruenz."""
    if wort == farbe:
        return "kongruent"
    else:
        return "inkongruent"

```

In einem anderen Skript importieren Sie dann:

```python
import auswertung

congruency = auswertung.berechne_congruency(wort, farbe)
```

> 💡 Genau so funktioniert PsychoPy: Es ist ein sehr großes, gut dokumentiertes Modul.  
> `from psychopy import visual, core, event` gibt Ihnen fertige Bausteine für Experimente.


## 7. Übergang zu PsychoPy

### 7.1 Warum PsychoPy und nicht reines Python?

Reines Python (`time.sleep()`, `input()`) hat für echte Experimente entscheidende Schwächen:

| Problem | Reines Python | PsychoPy |
|---|---|---|
| Zeitgenauigkeit | `time.sleep()` ist ungenau (±10–50 ms) | Sub-Millisekunden-Präzision über Hardware-Clock |
| Darstellung | Kein Grafikfenster | Vollbild-Fenster mit präzisem Timing |
| Tastatureingabe | `input()` wartet blockierend | Nicht-blockierendes Event-Polling |
| Stimuli | Nur Text in der Konsole | Text, Bilder, Töne, Gitter, alles in Farbe |
| Logging | Manuell | Automatische Logfiles mit Zeitstempeln |

> 💡 PsychoPy wurde von Jon Peirce (Nottingham) speziell für die Psychologie entwickelt und ist heute Standard in der experimentellen Forschung.

### 7.2 Installation (lokal, nicht in Colab)

PsychoPy läuft **lokal auf Ihrem Computer**, nicht in Google Colab – es braucht ein Grafikfenster.

```bash
# In der Kommandozeile / Terminal:
pip install psychopy
```

Alternativ: **PsychoPy Standalone** unter https://www.psychopy.org/download.html – diese Version bringt alles mit, ohne pip.

### 7.3 Die Grundbausteine eines PsychoPy-Skripts

Jedes PsychoPy-Experiment besteht aus denselben Bausteinen:

```
1. Imports        → Welche PsychoPy-Module brauche ich?
2. Konfiguration  → Konstanten, Pfade, VP-ID
3. Setup          → Fenster öffnen, Uhr erstellen, Logfile anlegen
4. Stimulus-Setup → Texte, Bilder, Töne vorbereiten
5. Experiment     → Trial-Schleife: zeigen → warten → auswerten → speichern
6. Abschluss      → Fenster schließen, Logfile sichern
```

### 7.4 Ihr erstes PsychoPy-Skript – Schritt für Schritt

```python
# ─── 1. Imports ───────────────────────────────────────────────────────────────
from psychopy import visual, core, event

# ─── 2. Konfiguration ─────────────────────────────────────────────────────────
VP_ID      = "VP01"
VOLLBILD   = False     # True für echte Experimente
FENSTER_HG = "gray"   # Hintergrundfarbe

# ─── 3. Setup ─────────────────────────────────────────────────────────────────
fenster = visual.Window(
    size=(1024, 768),
    fullscr=VOLLBILD,
    color=FENSTER_HG,
    units="pix"
)
uhr = core.Clock()   # Reaktionszeit-Uhr

# ─── 4. Stimulus-Setup ────────────────────────────────────────────────────────
text_stimulus = visual.TextStim(
    win=fenster,
    text="",           # wird später gesetzt
    color="white",
    height=80
)

text_instruktion = visual.TextStim(
    win=fenster,
    text="Nennen Sie die FARBE des Wortes.\nDrücken Sie LEERTASTE zum Starten.",
    color="white",
    height=30
)

# ─── 5. Instruktion zeigen ────────────────────────────────────────────────────
text_instruktion.draw()
fenster.flip()              # Puffer auf Bildschirm übertragen
event.waitKeys(keyList=["space"])   # warten auf Leertaste

# ─── 6. Ein Trial ─────────────────────────────────────────────────────────────
wort  = "ROT"
farbe = "blue"       # PsychoPy-Farbnamen auf Englisch

text_stimulus.setText(wort)
text_stimulus.setColor(farbe)
text_stimulus.draw()

uhr.reset()          # Uhr auf 0 zurücksetzen
fenster.flip()       # Stimulus erscheint jetzt auf dem Bildschirm

# Auf Tastendruck warten (maximal 2 Sekunden)
tasten = event.waitKeys(maxWait=2.0, keyList=["r", "b", "g", "escape"])
rt = uhr.getTime()   # Reaktionszeit in Sekunden

if tasten is None:
    print("Keine Antwort")
elif tasten[0] == "escape":
    fenster.close()
    core.quit()
else:
    print(f"Taste: {tasten[0]}  |  RT: {rt*1000:.0f} ms")

# ─── 7. Abschluss ─────────────────────────────────────────────────────────────
fenster.close()
core.quit()
```

### 7.5 Was Sie wiedererkennen

Jeder Baustein aus den letzten Wochen taucht hier wieder auf:

| PsychoPy-Code | Woher bekannt |
|---|---|
| `VP_ID = "VP01"` | Woche 1: Variablen |
| `trials = [("ROT","blue"), ...]` | Woche 2: Listen und Tupel |
| `for wort, farbe in trials:` | Woche 3: Schleifen |
| `def zeige_trial(wort, farbe):` | Woche 4: Funktionen |
| `from psychopy import visual` | **Woche 5: Module** |

PsychoPy fügt keine neue Programmierlogik hinzu – es gibt Ihnen Werkzeuge, die Ihr bereits gelerntes Python in ein echtes Experiment verwandeln.

---

## Übungsteil

> - 🟢 **Basis** – einzelne Konzepte direkt anwenden
> - 🟡 **Aufbau** – Konzepte kombinieren
> - 🔴 **Vertiefung** – eigenständig strukturieren

---

### 🟢 Aufgabe 1 – Modul-Syntax

Schreiben Sie für jede der folgenden Anforderungen den korrekten Import und einen Beispielaufruf:

1. Das Modul `random` vollständig importieren und `randint(1, 10)` aufrufen.
2. Nur `shuffle` aus `random` importieren und eine Liste mischen.
3. `pathlib` unter dem Alias `pl` importieren und den Pfad `"daten/vp01.csv"` erzeugen.

```python
# Ihr Code hier
```

---

### 🟢 Aufgabe 2 – Randomisierung mit Seed

Gegeben ist eine Stimulusliste. Führen Sie die Zelle **zweimal** aus – einmal mit `random.seed(7)` und einmal ohne.  
Beschreiben Sie den Unterschied.

```python
import random

stimuli = ["ROT", "BLAU", "GRÜN", "GELB", "VIOLETT", "ORANGE"]

# random.seed(7)   # ← auskommentiert oder aktiv?
random.shuffle(stimuli)
print(stimuli)
```

Warum ist `seed()` in einem Experiment wichtig?

```python
# Ihre Beobachtung und Antwort hier
```

---

### 🟢 Aufgabe 3 – `choice` und `sample`

Gegeben ist eine Wortliste:

```python
import random

woerter = ["Tisch", "Stuhl", "Lampe", "Buch", "Fenster", "Tür"]
```

1. Wählen Sie mit `random.choice()` ein einzelnes Wort aus.
2. Wählen Sie mit `random.sample()` drei Wörter ohne Zurücklegen aus.
3. Was passiert, wenn Sie `random.sample(woerter, k=10)` aufrufen? Führen Sie es aus und lesen Sie die Fehlermeldung.

```python
# Ihr Code hier
```

---

### 🟡 Aufgabe 4 – Trialliste randomisieren

Erstellen Sie eine ausgeglichene Trialliste für ein Stroop-Experiment:

- 3 Farben: `"rot"`, `"blau"`, `"grün"`
- Je 4 kongruente und 4 inkongruente Trials pro Farbe
- Randomisierte Reihenfolge mit `seed=99`

Geben Sie die ersten 5 Trials aus und die Gesamtzahl.

> 💡 Tipp: Erstellen Sie zuerst alle kongruenten und alle inkongruenten Trials als separate Listen, fügen Sie sie zusammen und mischen Sie dann.

```python
import random

SEED = 99

# Ihr Code hier
```

---

### 🟡 Aufgabe 5 – Logfile anlegen

Schreiben Sie eine Funktion `erstelle_logfile(vp_id, aufgabe)`, die:

1. Einen Ordner `"daten"` anlegt (falls nicht vorhanden)
2. Eine CSV-Datei mit dem Namen `"{vp_id}_{aufgabe}.csv"` erstellt
3. Die Kopfzeile `trial, wort, farbe, antwort, korrekt, rt_ms` schreibt
4. Den Pfad zur Datei zurückgibt

```python
import csv
from pathlib import Path

def erstelle_logfile(vp_id, aufgabe):
    # Ihr Code hier
    ...

# Testen:
pfad = erstelle_logfile("VP05", "stroop")
print(pfad)
```

---

### 🟡 Aufgabe 6 – Trial in Logfile speichern

Schreiben Sie eine Funktion `speichere_trial(logfile_pfad, trial_daten)`, die eine Zeile an das Logfile anhängt.

`trial_daten` soll ein Dictionary sein:
```python
{"trial": 1, "wort": "ROT", "farbe": "rot", "antwort": "rot", "korrekt": True, "rt_ms": 412}
```

Testen Sie mit drei simulierten Trials.

```python
import csv
from pathlib import Path

def speichere_trial(logfile_pfad, trial_daten):
    # Ihr Code hier
    ...

# Testen:
pfad = erstelle_logfile("VP05", "stroop")   # aus Aufgabe 5

speichere_trial(pfad, {"trial": 1, "wort": "ROT",  "farbe": "rot",  "antwort": "rot",  "korrekt": True,  "rt_ms": 412})
speichere_trial(pfad, {"trial": 2, "wort": "BLAU", "farbe": "rot",  "antwort": "blau", "korrekt": False, "rt_ms": 587})
speichere_trial(pfad, {"trial": 3, "wort": "GRÜN", "farbe": "grün", "antwort": "grün", "korrekt": True,  "rt_ms": 345})

print("Fertig. Öffnen Sie die CSV-Datei und überprüfen Sie den Inhalt.")
```

---

### 🔴 Aufgabe 7 – Volles Daten-Pipeline

Kombinieren Sie alles: Randomisierung, Logfile, Auswertung.

1. Erstellen Sie eine randomisierte Trialliste (wie in Aufgabe 4).
2. Simulieren Sie Reaktionen: `random.choice(["richtig_taste", "falsche_taste"])` und eine zufällige RT zwischen 300 und 900 ms.
3. Speichern Sie jeden Trial ins Logfile.
4. Berechnen Sie am Ende Accuracy und mittlere RT und geben Sie sie aus.

```python
import random
import csv
from pathlib import Path

SEED     = 42
VP_ID    = "VP01"
AUFGABE  = "stroop"

# Ihr Code hier
```

---

### 🔴 Aufgabe 8 – PsychoPy-Code lesen

Lesen Sie das folgende PsychoPy-Snippet und beantworten Sie die Fragen **ohne es auszuführen** (PsychoPy läuft nicht in Colab):

```python
from psychopy import visual, core, event

fenster = visual.Window(size=(1024, 768), color="gray", units="pix")
text    = visual.TextStim(win=fenster, text="Fixation", color="white", height=40)
uhr     = core.Clock()

text.draw()
fenster.flip()
uhr.reset()
core.wait(0.5)

text.setText("Drücken Sie eine Taste")
text.draw()
fenster.flip()
uhr.reset()

tasten = event.waitKeys(maxWait=3.0, keyList=["space", "escape"])
rt     = uhr.getTime()

if tasten is None:
    print("Timeout")
elif tasten[0] == "escape":
    fenster.close()
    core.quit()
else:
    print(f"RT: {rt * 1000:.0f} ms")

fenster.close()
```

1. Was erscheint zuerst auf dem Bildschirm, und wie lange?
2. Welche Tasten werden akzeptiert?
3. Was passiert, wenn die Versuchsperson 3 Sekunden lang keine Taste drückt?
4. Warum wird `uhr.reset()` zweimal aufgerufen?
5. Welche Variable entspricht dem `korrekt`-Feld aus Ihren bisherigen Übungen – und wie würden Sie sie hier berechnen?

```python
# Ihre Antworten hier (als Kommentare)
```

---

## Typische Fehler – Übersicht

| Fehler | Code | Problem |
|---|---|---|
| Modul nicht importiert | `random.shuffle(liste)` ohne `import random` | `NameError` |
| `shuffle` gibt `None` zurück | `liste = random.shuffle(liste)` | `shuffle` verändert die Liste direkt, gibt `None` zurück |
| Pfad-Trennzeichen | `"daten\vp01.csv"` (Windows) | `\v` ist ein Sonderzeichen; `pathlib` vermeidet das Problem |
| Datei nicht geschlossen | `f = open(...); f.write(...)` ohne `close()` | Datei ggf. beschädigt; `with` vermeidet das |
| Seed vergessen | Shuffle ohne `seed()` | Nicht reproduzierbar |
| `sample` zu groß | `random.sample(liste, k=10)` bei len=6 | `ValueError` |
| CSV ohne `newline=""` | `open("log.csv", "w")` auf Windows | Doppelte Leerzeilen in der CSV |

---

## Lösungen

<details>
  
<summary>Aufgabe 1 – Modul-Syntax</summary>

```python

# (1) ganzes Modul
import random
print(random.randint(1, 10))

# (2) einzelne Funktion
from random import shuffle
liste = [1, 2, 3, 4, 5]
shuffle(liste)
print(liste)

# (3) Alias
import pathlib as pl
pfad = pl.Path("daten") / "vp01.csv"
print(pfad)
```

</details>

<details>
  
<summary>Aufgabe 2 – Randomisierung mit Seed</summary>

Mit `random.seed(7)` ist die Reihenfolge bei jedem Aufruf gleich.  
Ohne `seed()` ist sie bei jedem Aufruf verschieden.

In einem Experiment braucht man `seed()`, um:
- Ergebnisse reproduzieren zu können
- Nachträglich überprüfen zu können, welche Trialreihenfolge VP X hatte
- Pre-registered Designs umzusetzen
- 
</details>

<details>
  
<summary>Aufgabe 3 – choice und sample</summary>

```python
import random
woerter = ["Tisch", "Stuhl", "Lampe", "Buch", "Fenster", "Tür"]

print(random.choice(woerter))             # ein zufälliges Wort
print(random.sample(woerter, k=3))        # drei ohne Zurücklegen
random.sample(woerter, k=10)              # → ValueError: Sample larger than population
```

</details>

<details>
  
<summary>Aufgabe 4 – Trialliste</summary>

```python
import random

SEED   = 99
farben = ["rot", "blau", "grün"]

kongruent = []
for f in farben:  # ich gehe nacheinander durch alle Farben der Liste "farben"    
    for i in range(4):  # das wiederhole ich viermal        
        # ich füge der Liste "kongruent" ein Tupel hinzu:
        # (großgeschriebenes Wort, passende Farbe)
        kongruent.append((f.upper(), f))

inkongruent = []
for w in farben:  # w steht für das Wort    
    for f in farben:  # f steht für die Schriftfarbe        
        if w != f:  # nur wenn Wort und Farbe verschieden sind            
            for i in range(2):  # jede Kombination wird zweimal eingefügt
                
                # ich füge der Liste "inkongruent" ein Tupel hinzu:
                # (großgeschriebenes Wort, nicht passende Farbe)
                inkongruent.append((w.upper(), f))

trials = kongruent + inkongruent
random.seed(SEED)
random.shuffle(trials)

print(f"Trials gesamt: {len(trials)}")
for t in trials[:5]:
    print(t)
```

</details>

<details>
  
<summary>Aufgabe 5 – Logfile anlegen</summary>

```python
import csv
from pathlib import Path

def erstelle_logfile(vp_id, aufgabe):
    """Legt Logfile an und schreibt Kopfzeile. Gibt Pfad zurück."""
    ordner = Path("daten")
    ordner.mkdir(exist_ok=True)
    pfad = ordner / f"{vp_id}_{aufgabe}.csv"
    with open(pfad, mode="w", newline="", encoding="utf-8") as f:
        writer = csv.writer(f)
        writer.writerow(["trial", "wort", "farbe", "antwort", "korrekt", "rt_ms"])
    return pfad
```

</details>

<details>
  
<summary>Aufgabe 6 – Trial speichern</summary>


```python
import csv

def speichere_trial(logfile_pfad, trial_daten):
    """Hängt einen Trial an das Logfile an."""
    with open(logfile_pfad, mode="a", newline="", encoding="utf-8") as f:
        writer = csv.writer(f)
        writer.writerow([
            trial_daten["trial"],
            trial_daten["wort"],
            trial_daten["farbe"],
            trial_daten["antwort"],
            trial_daten["korrekt"],
            trial_daten["rt_ms"],
        ])
```

</details>


<details>
  
<summary>Aufgabe 7 – Volle Pipeline</summary>


```python
import random
import csv
from pathlib import Path

SEED    = 42
VP_ID   = "VP01"
AUFGABE = "stroop"

# Trialliste
farben    = ["rot", "blau", "grün"]
kongruent = [(f.upper(), f) for f in farben for _ in range(4)]
inkongr   = [(w.upper(), f) for w in farben for f in farben if w != f for _ in range(2)]
trials    = kongruent + inkongr

random.seed(SEED)
random.shuffle(trials)

# Logfile
ordner = Path("daten"); ordner.mkdir(exist_ok=True)
pfad = ordner / f"{VP_ID}_{AUFGABE}.csv"
with open(pfad, "w", newline="", encoding="utf-8") as f:
    csv.writer(f).writerow(["trial","wort","farbe","antwort","korrekt","rt_ms"])

farb_taste = {"rot": "r", "blau": "b", "grün": "g"}
korrekt_n  = 0
rt_summe   = 0

for i, (wort, farbe) in enumerate(trials, start=1):
    antwort = random.choice(list(farb_taste.values()))
    rt_ms   = random.randint(300, 900)
    korrekt = (antwort == farb_taste[farbe])
    if korrekt: korrekt_n += 1
    rt_summe += rt_ms
    with open(pfad, "a", newline="", encoding="utf-8") as f:
        csv.writer(f).writerow([i, wort, farbe, antwort, korrekt, rt_ms])

print(f"Accuracy:    {korrekt_n/len(trials):.0%}")
print(f"Mittlere RT: {rt_summe/len(trials):.0f} ms")
print(f"Logfile:     {pfad}")
```

</details>

<details>
  
<summary>Aufgabe 8 – PsychoPy lesen</summary>

```
1. Zuerst erscheint "Fixation" für 0,5 Sekunden (core.wait(0.5)).
   Dann erscheint "Drücken Sie eine Taste".

2. Akzeptierte Tasten: "space" und "escape".

3. Nach 3 Sekunden ohne Tastendruck: tasten ist None → Ausgabe "Timeout".

4. uhr.reset() wird zweimal aufgerufen:
   - Einmal vor der Fixation (um zu messen wie lange sie dauert).
   - Einmal direkt vor fenster.flip() mit dem Ziel-Stimulus,
     damit rt die Zeit AB dem Erscheinen des Ziel-Stimulus misst.

5. korrekt wäre z.B.:
   korrekt = (tasten[0] == erwartete_taste)
   Die erwartete Taste hängt von der korrekten Farbe ab.
```

</details>

---

## Zusammenfassung

| Konzept | Syntax | Experimenteller Einsatz |
|---|---|---|
| Import | `import random` | Modul einbinden |
| Selektiver Import | `from pathlib import Path` | Einzelne Klassen/Funktionen |
| Alias | `import numpy as np` | Kürzere Schreibweise |
| Randomisierung | `random.shuffle(trials)` | Trialreihenfolge mischen |
| Reproduzierbarkeit | `random.seed(42)` | Wissenschaftliche Nachvollziehbarkeit |
| Dateipfade | `Path("daten") / "log.csv"` | OS-unabhängige Pfade |
| Logfile anlegen | `csv.writer` + `mode="w"` | Kopfzeile schreiben |
| Logfile anfügen | `csv.writer` + `mode="a"` | Trials speichern |
| Sicheres Öffnen | `with open(...) as f:` | Datei wird immer geschlossen |

**Drei Regeln für Module in Experimenten:**
1. `random.seed()` **einmal** am Anfang – danach nie wieder ändern
2. Dateipfade immer mit `pathlib.Path` – nie als rohen String zusammenbauen
3. CSV-Dateien immer mit `with open(...) as f:` öffnen – nie mit manuellem `f.close()`

---

## Ausblick: Woche 6

Nächste Woche beginnen Sie, ein vollständiges PsychoPy-Experiment zu schreiben:

```python
from psychopy import visual, core, event
import random
import csv
from pathlib import Path

# Alles, was Sie bisher gelernt haben – in einem echten Experiment:
# Variablen, Listen, Schleifen, Funktionen, Module → PsychoPy
```

Das Stroop-Experiment, das Sie in den letzten Wochen Schritt für Schritt aufgebaut haben,  
wird ab nächster Woche auf dem Bildschirm erscheinen, mit echtem Timing, echten Farben  
und einem Logfile, das direkt in SPSS oder R geöffnet werden kann.
