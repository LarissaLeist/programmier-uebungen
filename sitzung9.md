# Sitzung 9: Antwort-Baukasten, verschiedene Aufgaben, Dateien kombinieren, Auswertung


| Block | Inhalt | Umgebung |
|-------|--------|----------|
| **A** | Antwort-Baukasten: Taste · Maus · Skala · Freitext | PsychoPy |
| **B** | Simon-Aufgabe | PsychoPy |
| **C** | Serial Recall | PsychoPy |
| **D** | CSV-Dateien zusammenführen | Spyder |
| **E** | Erste Auswertung: Pandas & Seaborn | Spyder |

Arbeitsblatt für heute: `my_task_sitzung9.py` (in Spyder öffnen und bearbeiten und über import my_task_sitzung9 in `launch_task.py` aufrufen).

---

# Block A — Antwort-Baukasten

Bisher haben wir Antworten nur über Tastendruck gemessen. 

Heute lernen wir vier verschiedene Wege kennen. Alle folgen demselben Muster:

**Stimulus zeigen → Eingabe einholen → Ergebnis zurückgeben**

Nur der mittlere Schritt unterscheidet sich.

---

## A1: Tastendruck (`event.waitKeys`)


```python
psychopy.event.waitKeys(maxWait=inf, keyList=None, modifiers=False, timeStamped=False, clearEvents=True)[source]
```
Kennt ihr schon, ideal für Reaktionszeitmessung. Für Aufgaben wie Stroop task, Flanker Task, Priming, Go-NoGo uvm.


```python
from psychopy import event

stim.draw()
win.flip()
clock.reset()
event.clearEvents()
keys = event.waitKeys(keyList=["d", "k"], timeStamped=clock)
taste, rt = keys[0]
```



## A2: Mausklick (`event.Mouse`)

Für räumliche Antworten: VP klickt auf eine von mehreren Flächen.

```python
psychopy.event.Mouse(visible=None, newPos=None, win=None)[source]¶
```

In der Anwendung:

```python
from psychopy import visual, core, event

win = visual.Window([800, 800], monitor="TestMonitor", units="deg",
                    color="white", fullscr=False)
                    
def frage_mausklick(win):
    maus = event.Mouse(win=win)

    links  = visual.Rect(win, width=6, height=4, pos=(-7, 0), fillColor="lightgrey")
    rechts = visual.Rect(win, width=6, height=4, pos=( 7, 0), fillColor="lightgrey")
    txt_l  = visual.TextStim(win, text="JA",   pos=(-7, 0), color="black", height=1.0)
    txt_r  = visual.TextStim(win, text="NEIN", pos=( 7, 0), color="black", height=1.0)

    for s in [links, rechts, txt_l, txt_r]:
        s.draw()
    win.flip()

    clock = core.Clock()
    maus.clickReset()
    antwort = None

    while antwort is None:
        if maus.isPressedIn(links):
            antwort = "ja"
        elif maus.isPressedIn(rechts):
            antwort = "nein"

    rt = clock.getTime()
    return antwort, rt
    
a, b = frage_mausklick(win)

print(a,b)
```

Worauf es ankommt:
- `event.Mouse(win=win)`: Maus-Objekt wird einmal angelegt und dann aufgerufen
- `maus.isPressedIn(links)` ist `True`, wenn geklickt **und** Cursor in der Fläche.
- Die `while`-Schleife läuft, bis eine Fläche angeklickt wurde.
- Jede Antwort-Option = ein `Rect` + ein `TextStim` + ein `elif`-Zweig.

### 🟦 Übung A2a — dritte Option

Füge einen mittleren Button „WEISS NICHT" bei `pos=(0, -6)` hinzu.

<details>
<summary>Lösung A2a</summary>

```python
mitte  = visual.Rect(win, width=6, height=4, pos=(0, -6), fillColor="lightgrey")
txt_m  = visual.TextStim(win, text="WEISS NICHT", pos=(0, -6), color="black", height=0.7)

# zusätzlich zeichnen:
for s in [links, rechts, mitte, txt_l, txt_r, txt_m]:
    s.draw()

# in der while-Schleife:
elif maus.isPressedIn(mitte):
    antwort = "weiss_nicht"
```

Ein Button = ein `Rect` + ein `TextStim` + ein `elif`. Beliebig erweiterbar.
</details>

### 🟨 Übung A2b — vier Buttons

Baue vier Buttons für eine Schulnoten-Abfrage: 1, 2, 3, 4 — nebeneinander.

<details>
<summary>Lösung A2b</summary>

```python

from psychopy import visual, core, event

win = visual.Window([800, 800], monitor="TestMonitor", units="deg",
                    color="white", fullscr=False)
                    
def frage_mausklick(win):
    maus = event.Mouse(win=win)
    positionen = [(-9, 0), (-3, 0), (3, 0), (9, 0)]
    noten      = ["1", "2", "3", "4"]
    buttons    = []
    labels     = []

    for i in range(len(noten)):
        b = visual.Rect(win, width=4, height=3, pos=positionen[i], fillColor="lightgrey")
        t = visual.TextStim(win, text=noten[i], pos=positionen[i], color="black", height=1.0)
        buttons.append(b)
        labels.append(t)

    for b in buttons:
        b.draw()
    for t in labels:
        t.draw()
    win.flip()

    antwort = None
    while antwort is None:
        for i in range(len(buttons)):
            if maus.isPressedIn(buttons[i]):
                antwort = noten[i]
                
    return str(antwort)
    
a = frage_mausklick(win)

print(a,b)
```

</details>

---

## A3: Skala/Slider (`visual.Slider`)

Für graduelle Urteile (Likert, Sicherheit, Intensität).

```python
from psychopy import visual, core, event

win = visual.Window([800, 800], monitor="TestMonitor", units="deg",
                    color="white", fullscr=False)

def frage_skala(win, frage_text):
    frage = visual.TextStim(win, text=frage_text, pos=(0, 4),
                            color="black", height=0.8)
    skala = visual.Slider(win, ticks=(-100, 0, 100),
                          labels=["sehr schlecht", "", "sehr gut"],
                          pos=(0, -2), size=(18, 1),
                          granularity=1, style="rating",
                           lineColor='Grey', borderColor='black',
                            color='black', markerColor='black',
                            fillColor='black',
                            font="Arial")
    skala.reset()

    while skala.getRating() is None:
        frage.draw()
        skala.draw()
        win.flip()

    rating = skala.getRating()
    rt     = skala.getRT()
    return rating, rt
    
a, b = frage_skala(win, "Wie fühlst du dich?")

print(a,b)
```

Worauf es ankommt:
- `ticks` = die Werte, `labels` = Endpunkt-Beschriftungen (mittlere leer lassen).
- `granularity=1` → nur ganze Zahlen; `0` wäre stufenlos.
- `skala.draw()` **muss in der Schleife** stehen, sonst reagiert der Slider nicht sichtbar.
- `getRating()` ist `None` solange nichts gewählt → praktische Abbruchbedingung.

### 🟦 Übung A3a — andere Skala

Baue eine 5-stufige Skala mit den Endpunkten „stimme nicht zu" / „stimme voll zu".

<details>
<summary>Lösung A3a</summary>

```python
skala = visual.Slider(win, ticks=(1, 2, 3, 4, 5),
                      labels=["stimme nicht zu", "", "", "", "stimme voll zu"],
                      pos=(0, -2), size=(18, 1),
                      granularity=1, style="rating", color="black")
```
</details>

### 🟨 Übung A3b — Bestätigung mit Leertaste

Aktuell endet die Skala beim ersten Klick. Ändere es so, dass die VP die Wahl mit der Leertaste **bestätigen** muss (vorher noch änderbar).

<details>
<summary>Lösung A3b</summary>

```python
bestaetigt = False
while not bestaetigt:
    frage.draw()
    skala.draw()
    win.flip()
    if skala.getRating() is not None:
        if event.getKeys(keyList=["space"]):
            bestaetigt = True
rating = skala.getRating()
```

VP kann den Marker verschieben und erst mit Leertaste festlegen, das ist realistischer für Fragebögen.
</details>


## A4: Freie Texteingabe

Für offene Antworten: Wörter, Zahlen, kurze Sätze. Wir bauen es selbst aus einer Tastenschleife.

```python
from psychopy import visual, core, event

win = visual.Window([800, 800], monitor="TestMonitor", units="deg",
                    color="white", fullscr=False)

def frage_freitext(win, frage_text):
    frage        = visual.TextStim(win, text=frage_text, pos=(0, 3),
                                   color="black", height=0.8)
    eingabe_stim = visual.TextStim(win, text="", pos=(0, -1),
                                   color="black", height=1.0)

    eingabe = ""
    fertig  = False
    event.clearEvents()

    while not fertig:
        for k in event.getKeys():
            if k == "return":
                fertig = True
            elif k == "backspace":
                eingabe = eingabe[:-1]      # letztes Zeichen weg
            elif k == "space":
                eingabe = eingabe + " "
            elif len(k) == 1:               # echte Zeichen (a, b, 3, ...)
                eingabe = eingabe + k
            # Sondertasten (lshift, ctrl, ...) haben len > 1 → werden ignoriert

        eingabe_stim.text = eingabe + "_"   # "_" als Cursor
        frage.draw()
        eingabe_stim.draw()
        win.flip()

    return eingabe
    
a = frage_freitext(win, "Wie fühlst du dich?")

print(a)
```

Erklärung:
- `event.getKeys()` liefert alle seit dem letzten Aufruf gedrückten Tasten.
- `eingabe[:-1]` = der String ohne das letzte Zeichen → Backspace-Effekt.
- `len(k) == 1` filtert echte Zeichen von Sondertasten: `"a"` hat Länge 1, `"lshift"` nicht.
- Text wird **jeden Frame** neu gezeichnet — so sieht man beim Tippen mit.


### 🟦 Übung A4a — nur Zahlen zulassen

Erlaube nur Ziffern (z.B. für eine Altersabfrage). Tipp: `k.isdigit()`.

<details>
<summary>Lösung A4a</summary>

```python
elif k.isdigit():        # ersetzt den len(k)==1-Zweig
    eingabe = eingabe + k
```

`"7".isdigit()` ist `True`, `"a".isdigit()` ist `False`.
</details>

### 🟨 Übung A4b — leere Eingabe verhindern

Enter soll erst akzeptiert werden, wenn mindestens ein Zeichen eingegeben wurde.

<details>
<summary>Lösung A4b</summary>

```python
if k == "return" and len(eingabe) > 0:
    fertig = True
```
</details>

---

## A5 Überblick

| Weg | Funktion/Objekt | Antworttyp | RT sinnvoll? | Typischer Einsatz |
|-----|-----------------|------------|--------------|-------------------|
| **Taste** | `event.waitKeys` | wenige diskrete Optionen | ja, präzise | Stroop, Simon, Go-Nogo |
| **Maus** | `event.Mouse` + `isPressedIn` | räumliche Wahl | bedingt | Bilder anklicken, Buttons |
| **Skala** | `visual.Slider` | graduell (Likert) | über `getRT()` | Sicherheit, Zustimmung |
| **Freitext** | `event.getKeys`-Schleife | offen | nein | Alter, freie Assoziation |


### 🟨 Übung A5 — kombinieren

Baue einen Mini-Ablauf: erst ein Simon-Trial (Taste + RT), direkt danach „Wie sicher warst du?" (Skala 1–7). Beide Ergebnisse in **eine** Logfile-Zeile.

<details>
<summary>Lösung A5</summary>

```python
# Simon-Trial
taste, rt, korrekt = run_simon_trial(win, reizseite, clock)

# Skala direkt danach
sicherheit, tmp = frage_skala(win, "Wie sicher warst du?")

# Gemeinsam loggen
logfile.write(
    f"{sbj_num},{trial_num},{reizseite},{taste},{korrekt},{rt:.3f},{sicherheit}\n"
)
```
</details>

---

# Block B — Serial Recall

**Idee:** Der Versuchsperson wird eine Liste von Wörtern nacheinander gezeigt. Danach soll sie die Wörter der Reihe nach abrufen (tippen oder Mausklick). Klassischer Gedächtnistest, testet Kapazität, primacy und recency Effekt, ISE. 

Zwei Phasen:
1. **Lernphase**: jedes Wort 1 Sekunde zeigen, dann kurze Pause.
2. **Abrufphase**: für jede Position ein Freitext-Eingabefenster.

```python
from psychopy import visual, core, event
import os
from frage_freitext import freitexteingabe

win = visual.Window([800, 600], color="white", units="deg", monitor="testMonitor")


def run_serial_recall(win, sbj_num):

    woerter = ["Hund", "Tisch", "Regen", "Lampe", "Brief"]

    # ── Phase 1: Wörter zeigen ──────────────────────────────────
    hinweis = visual.TextStim(win, text="Merke dir die Wörter!\nWeiter mit der Leertaste!", 
                               pos=(0, 4), color="black", height=0.8)
    stim    = visual.TextStim(win, text="", color="black", height=2.0)

    for wort in woerter:
        stim.text = wort
        hinweis.draw()
        win.flip()
        event.waitKeys(keyList=["space"])
        stim.draw()
        win.flip()
        core.wait(1.0)

        win.flip()          # kurze Lücke zwischen den Wörtern
        core.wait(0.3)

    # Kurze Pause vor dem Abruf
    pause = visual.TextStim(win, text="Gleich wirst du gefragt...\n[Weiter mit Leertaste]",
                             color="black", height=0.8)
    pause.draw()
    win.flip()
    event.waitKeys(keyList=["space"])

    # ── Phase 2: Abruf ──────────────────────────────────────────
    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")

    filename = os.path.join("Logfiles", f"VP_{sbj_num}_recall.csv")
    header   = "vp_num,position,ziel_wort,antwort,korrekt\n"

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        for i in range(len(woerter)):
            frage_text = f"Wort an Position {i + 1}?"
            antwort    = freitexteingabe(win, frage_text)   # Funktion aus Block A

            korrekt = int(antwort.strip().lower() == woerter[i].lower())

            logfile.write(
                f"{sbj_num},{i + 1},{woerter[i]},{antwort},{korrekt}\n"
            )
            

run_serial_recall(win, "999")
win.close()
core.quit()
```

Worauf es ankommt:
- **Lernphase:** `for wort in woerter` — jedes Wort einmal. `core.wait(0.3)` nach `win.flip()` erzeugt die Lücke.
- **Abrufphase:** `range(len(woerter))` gibt uns die Positionen 0, 1, 2, … — mit `i + 1` für den Text.
- `frage_freitext` kommt direkt aus Block A.
- `.strip().lower()` beim Vergleich toleriert Groß-/Kleinschreibung und Leerzeichen am Rand.

### 🟦 Übung B1: Liste anpassen

Ändere die Wortliste auf 7 Wörter. Was musst du im Code anpassen?

<details>
<summary>Lösung B1</summary>

```python
woerter = ["Baum", "Fenster", "Sonne", "Musik", "Brief", "Hund", "Wasser"]
```

Nichts weiter! `range(len(woerter))` passt sich automatisch an. Das ist der Vorteil: Nur die Liste ändern, die Schleife bleibt gleich.
</details>

### 🟦 Übung B2: zufällige Reihenfolge

Mische die Wörter vor der Lernphase zufällig. Was verändert das am Test?

<details>
<summary>Lösung B2</summary>

```python
random.shuffle(woerter)
# ... dann Lernphase wie gehabt
```

Die Reihenfolge der Präsentation variiert zwischen VPs. Position 1 ist jetzt nicht mehr immer dasselbe Wort → wichtig für Counterbalancing.
</details>

### 🟨 Übung B3 — freie Reihenfolge (Free Recall)

Beim **Free Recall** muss die VP die Wörter in beliebiger Reihenfolge aufschreiben, in einem einzigen Eingabefeld (alle Wörter, durch Komma getrennt). Wie könntest du das umsetzen?

<details>
<summary>Lösung B3</summary>

```python
antwort = frage_freitext(win, "Schreibe alle Wörter, getrennt durch Komma:")
erinnert = antwort.split(",")

# Zielwörter in Kleinbuchstaben als Liste vorbereiten
woerter_klein = []
for w in woerter:
    woerter_klein.append(w.lower())

# Zählen wie viele erinnerten Wörter in der Zielliste stehen
korrekt_gesamt = 0
for a in erinnert:
    if a.strip().lower() in woerter_klein:
        korrekt_gesamt += 1

logfile.write(f"{sbj_num},{antwort},{korrekt_gesamt}\n")
```

`split(",")` zerlegt die Eingabe am Komma in eine Liste. Dann prüft man, wie viele der eingegebenen Wörter in der Original-Liste stehen.
</details>

---


# Block C: Simon-Aufgabe

**Idee:** Ein gelbes Rechteck erscheint links oder rechts. Die VP drückt immer `k` (egal wo der Reiz steht). Trotzdem ist man **schneller**, wenn Reizseite und Antworttaste übereinstimmen — der **Simon-Effekt**.

```python
from psychopy import visual, core, event
import os, random

win = visual.Window([800, 800], monitor="TestMonitor", units="deg",
                    color="white", fullscr=False)
                    
def run_simon(win, sbj_num):

    positionen   = {"links": (-6, 0), "rechts": (6, 0)}
    seiten       = ["links", "rechts"]
    tasten_seite = {"d": "links", "k": "rechts"}

    ziel_taste = "k"                          # richtige Taste: immer k
    ziel_seite = tasten_seite[ziel_taste]     # "rechts"

    fix   = visual.TextStim(win, text="+", color="black", height=1.0)
    clock = core.Clock()

    # Trialliste ohne List Comprehension: 20 zufällige Seiten
    trials = []
    for i in range(20):
        trials.append(random.choice(seiten))

    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")

    filename = os.path.join("Logfiles", f"VP_{sbj_num}_simon.csv")
    header   = "vp_num,trial,reizseite,kongruent,taste,korrekt,rt\n"

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        for trial_num, reizseite in enumerate(trials):

            fix.draw()
            win.flip()
            core.wait(0.5)

            rechteck = visual.Rect(win, width=4, height=4,
                                   fillColor="yellow", pos=positionen[reizseite])
            rechteck.draw()
            win.flip()
            clock.reset()
            event.clearEvents()

            keys = event.waitKeys(keyList=["d", "k"], timeStamped=clock)
            taste, rt = keys[0]

            korrekt   = int(taste == ziel_taste)
            kongruent = int(reizseite == ziel_seite)

            logfile.write(
                f"{sbj_num},{trial_num},{reizseite},{kongruent},"
                f"{taste},{korrekt},{rt:.3f}\n"
            )

            win.flip()
            core.wait(0.5)


run_simon(win, "999")
win.close()
core.quit()
```

Erklärung:
- Nur `pos` wechselt — das ist der gesamte experimentelle Trick.
- `kongruent`: liegt der Reiz auf der Seite der richtigen Antworttaste? Reiz rechts + `k` (rechts) → kongruent. Reiz links + `k` → inkongruent.
- Erwartet: kongruente Trials sind **schneller** — sichtbar erst in der Auswertung (Block E).

### 🟦 Übung C1 Seiten balancieren

Die aktuelle Liste ist rein zufällig. Baue stattdessen **exakt 10 links / 10 rechts** und mische.

<details>
<summary>Lösung C1</summary>

```python
trials = ["links"] * 10 + ["rechts"] * 10
random.shuffle(trials)
```

`shuffle` ändert nur die Reihenfolge, nie die Zusammensetzung.
</details>

### 🟨 Übung C2 vertikaler Simon

Baue die Aufgabe auf **oben/unten** um (Tasten `p` = oben, `l` = unten). Welche Teile musst du anfassen — und welche bleiben unberührt?

<details>
<summary>Lösung C2</summary>

```python
positionen   = {"oben": (0, 5), "unten": (0, -5)}
seiten       = ["oben", "unten"]
tasten_seite = {"p": "oben", "l": "unten"}
ziel_taste   = "l"
# waitKeys(keyList=["p", "l"], ...)
```

Nur Positionen, Seitenliste und Tastenzuordnung ändern sich. Die Schleifenstruktur (draw–flip, Uhr, Logfile) bleibt komplett gleich.
</details>

---

# Block D — CSV-Dateien zusammenführen

**Ziel:** Mehrere Logfiles (eine Datei pro VP) zu einer großen Datei zusammenführen, die dann ausgewertet werden kann.

**Umgebung:** Spyder — kein PsychoPy nötig.

```python
import pandas as pd
import glob
import os

# 1) Alle CSV-Dateien im Logfiles-Ordner finden
suchmuster  = os.path.join("Logfiles", "*.csv")
alle_dateien = glob.glob(suchmuster)

print("Gefundene Dateien:", len(alle_dateien))
for d in alle_dateien:
    print(" ", d)

# 2) Jede Datei einlesen und in eine Liste stecken
tabellen = []
for datei in alle_dateien:
    df = pd.read_csv(datei)
    tabellen.append(df)

# 3) Alle Tabellen zu einer zusammenfügen
gesamt = pd.concat(tabellen)

# 4) Als neue Datei speichern
gesamt.to_csv(os.path.join("Logfiles", "alle_vps.csv"), index=False)
print("Fertig! Zeilen gesamt:", len(gesamt))
```

Worauf es ankommt:
- `pd.read_csv(datei)` liest eine CSV-Datei als **DataFrame** (eine Tabelle mit Zeilen und Spalten).
- `tabellen` ist eine Liste von DataFrames — pro Datei eine.
- `pd.concat(tabellen)` klebt sie untereinander — wie copy-paste in Excel, nur automatisch.
- `index=False` verhindert, dass pandas eine extra Nummerierungsspalte anfügt.

> **Voraussetzung:** Alle Dateien müssen dieselben Spaltenköpfe haben. Das ist erfüllt, wenn alle mit demselben `header` geschrieben wurden.

### 🟦 Übung D1 — Kontrolle

Füge nach dem Zusammenfügen eine Zeile ein, die ausgibt, wie viele **eindeutige VPs** in der Gesamttabelle stehen.

<details>
<summary>Lösung D1</summary>

```python
print("VPs:", gesamt["vp_num"].nunique())
```

`nunique()` zählt die eindeutigen Werte in einer Spalte. Praktisch um zu prüfen, ob wirklich alle Dateien eingelesen wurden.
</details>

### 🟦 Übung D2 — nur Simon-Daten

Im Ordner liegen `VP_1_simon.csv`, `VP_1_recall.csv`, usw. Lies **nur** die Simon-Dateien ein.

<details>
<summary>Lösung D2</summary>

```python
suchmuster = os.path.join("Logfiles", "*_simon.csv")
alle_dateien = glob.glob(suchmuster)
```

`*_simon.csv` als Muster — alles was auf `_simon.csv` endet.
</details>

### 🟨 Übung D3 — Dateiname als Spalte

Füge beim Einlesen den Dateinamen als neue Spalte `"quelldatei"` ein. Warum kann das nützlich sein?

<details>
<summary>Lösung D3</summary>

```python
tabellen = []
for datei in alle_dateien:
    df = pd.read_csv(datei)
    df["quelldatei"] = os.path.basename(datei)   # Dateiname ohne Ordnerpfad
    tabellen.append(df)
```

Nützlich fürs Debugging: Wenn eine Zeile seltsame Werte hat, sieht man sofort, aus welcher Datei sie kommt.
</details>