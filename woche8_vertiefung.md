# PsychoPy Vertiefung: 
# `ImageStim` statt `TextStim` & Randomisierung



**Ziel:** Stimuli nicht mehr **im Code erzeugen**, sondern **aus Dateien laden**. Ihr lernt, wie PsychoPy Bilder anzeigt (`ImageStim`), wie man Pfade sauber baut (`os.path.join`), wie man eine Stimulusliste **aus einem Ordner** erzeugt, wie man diese noch besser kontrolliert und wie man aus dem Dateinamen die Trial-Information gewinnt. Am Ende läuft die Stroop-Task mit **Farbbildern** statt Textwörtern. Und wir können eine **GoNo-Go Aufgabe** umsetzen.

**Arbeitsumgebung:** wie gehabt — `launch_task.py` in **PsychoPy Coder** starten, `my_task.py` in **Spyder** entwickeln. Neu ist nur ein **Bilder-Ordner** neben den Skripten. Alle Dateien sind wieder auf OLAT.


## Warum überhaupt Dateien statt Text?

Bisher haben wir Stimuli **im Code** gebaut: `visual.TextStim(win, text="rot", color="red")`. Das geht für Wörter und Farben. Sobald aber **Bilder, Audios oder Videos** ins Spiel kommen, gibt es kein „schreib es hin"-Äquivalent mehr. Dieses Materialien **liegen dann auf der Festplatte** und müssen geladen werden.

Zweiter Vorteil: **Trennung von Material und Logik**. Die 40 Bilder eurer Studie tauscht ihr im Ordner aus, ohne eine Zeile Code anzufassen. Aber auch ein Nachteil: in dem Ordner müssen eure Dateien **vollständig** vorliegen.

```
my_task.py
launch_task.py
bilder/
    ├── rot_rot.png
    ├── rot_blau.png
    ├── blau_rot.png
    └── blau_blau.png
Logfiles/
```

> **Namenskonvention (heute):** `WORT_FARBE.png`. `rot_blau.png` heißt: das Wort **„rot"**, dargestellt in der Farbe **blau** → ein **inkongruenter** Trial. Diese Konvention ist unser Trick, um gleich aus dem Dateinamen automatisch `wort` und `farbe` zu bekommen.

---

## Block 1 — Ein einzelnes Bild anzeigen: `ImageStim`

Heute beschäftigen wir uns mit der `class psychopy.visual.ImageStim`. Die vollständige Dokumentation findet ihr unter: https://psychopy.org/api/visual/imagestim.html#psychopy.visual.ImageStim

`ImageStim` funktioniert nach demselben **Draw–Flip-Prinzip** wie `TextStim`, nur dass der Inhalt (also das Image) aus einer Datei auf eurer Festplatte kommt.

```python
from psychopy import visual, core, event
import os

def run_task(win, sbj_num, age, sex, vleitung):

    # Pfad sauber zusammensetzen: Ordner + Dateiname
    bildpfad = os.path.join("bilder", "gelb_blau.png")

    bild = visual.ImageStim(win, image=bildpfad)
    bild.draw()
    win.flip()
    event.waitKeys(keyList=["space"])
```

Erklärung:
- `image=...` erwartet einen **Pfad als String**. `os.path.join("bilder", "gelb_blau.png")` baut daraus je nach Betriebssystem `bilder/gelb_blau.png` (Mac/Linux) oder `bilder\gelb_blau.png` (Windows). **Nie** den Schrägstrich selbst hinschreiben — `os.path.join` macht es plattformübergreifend richtig.
- `draw()` → `win.flip()` → macht Stimulus sichtbar. Exakt wie beim Textstimulus.
- Bild fehlt oder Pfad falsch → PsychoPy wirft einen Fehler. Beim Testen der häufigste Stolperstein.

> **Größe:** `visual.ImageStim(win, image=..., size=(8, 8))` skaliert das Bild. Die Zahlen hängen von den **units** eures Fensters ab. Lasst `size` zunächst weg → PsychoPy nimmt die Originalgröße des Bildes.

### 🟦 Übung 1a — *ein anderes Bild*

Zeige stattdessen `blau_blau.png` an (Wort „blau", Farbe blau). Ändere nur **eine** Zeile.

<details>
<summary>Lösung Übung 1a</summary>

<pre><code class="language-python">bildpfad = os.path.join("bilder", "blau_blau.png")
</code></pre>

Nur der Dateiname ändert sich — der Rest (`ImageStim`, `draw`, `flip`) bleibt identisch.
</details>

### 🟦 Übung 1b — *Größe steuern*

Zeige `gelb_gelb.png` doppelt so groß wie in Übung 1a, indem du `size` setzt. Probiere zwei, drei Werte aus, bis es gut aussieht.

<details>
<summary>Lösung Übung 1b</summary>

<pre><code class="language-python">bild = visual.ImageStim(win, image=bildpfad, size=(10, 10))
</code></pre>

Es gibt keinen „richtigen" Wert, er hängt von den window-units und eurer Bildschirmauflösung ab. Deshalb testet man Größen immer am echten Setup.
</details>


---

## Block 2 — Aus dem Dateinamen die Trial-Info gewinnen

Der Clou der Namenskonvention `WORT_FARBE.png`: Wir können `wort` und `farbe` **aus dem Dateinamen herauslesen**, statt sie von Hand in eine Liste zu tippen.

```python
name = "gelb_blau.png"

basis = name.replace(".png", "")     # "gelb_blau" und Endung weg durch Ersetzen
wort, farbe = basis.split("_")       # split am "_" → ["gelb", "blau"] → entpackt
print(wort)     # "gelb"
print(farbe)    # "blau"
```

Worauf es ankommt:
- `.replace(".png", "")` schneidet die Endung ab, indem es die Endung ersetzt.
- `.split("_")` zerlegt den String am Unterstrich in eine **Liste**.
- **Voraussetzung:** genau **ein** Unterstrich im Namen. `gelb_blau.png` — `dunkel_gelb_blau.png` würde drei Teile liefern und beim Entpacken einen Fehler werfen.

### 🟦 Übung 2a — *gemeinsam*

Was liefert `"blau_gelb.png".replace(".png", "").split("_")`? Und was ist danach `wort`, was `farbe`?

<details>
<summary>Lösung Übung 2a</summary>

<pre><code class="language-python">"blau_gelb.png".replace(".png", "").split("_")   # → ["blau", "gelb"]
# nach: wort, farbe = ...
# wort  = "blau"
# farbe = "gelb"
</code></pre>

Wort „blau" in der Farbe gelb → inkongruent.
</details>

### 🟨 Übung 2b — *Congruency direkt bestimmen*

Schreibe zwei Zeilen, die aus `wort` und `farbe` die Variable `kongruent` (1 oder 0) berechnen — genau wie in der letzten Sitzung.

<details>
<summary>Lösung Übung 2b</summary>

<pre><code class="language-python">kongruent = int(wort == farbe)   # 1 wenn gleich, sonst 0
</code></pre>

`gelb_gelb.png` → `kongruent = 1`, `gelb_blau.png` → `kongruent = 0`. Die Logik ist dieselbe wie bei Textstimuli — nur dass `wort`/`farbe` jetzt aus dem Dateinamen kommen.
</details>

---

## Block 3 — Alle Bilder eines Ordners automatisch einlesen

Statt Dateinamen von Hand einzugeben, lassen wir Python **in den Ordner schauen**. Mit `glob.glob` können wir nach einem Muster suchen und nach bestimmten Dateien suchen, z.B. alle `.png`-Dateien im Order `bilder`.

```python
import os
import glob # neues Modul

suchmuster = os.path.join("bilder", "*.png")        # "bilder/*.png"
alle_bilder = glob.glob(suchmuster)  # Liste aller passenden Dateien

for bild in alle_bilder:
    bild = visual.ImageStim(win, image=bild)
    bild.draw()
    win.flip()
    event.waitKeys(keyList=["space"])
```

Erklärung:
- `os.path.join("bilder", "*.png")` liefert den Bildpfad.
- durch das Modul `glob` und dessen Funktion glob lesen wir die Dateien ein, die zu unserem vorgegebenen Muster (`*.png`) passen. Kann aus `*.csv`, `*.wav`, etc. sein.
- `*` ist ein Platzhalter: egal welcher Name, Hauptsache „.png". glob.glob liefert direkt die vollständigen Pfade zurück, z. B. ['bilder/blau_blau.png', 'bilder/blau_gelb.png', ...]. Kein extra Filter nötig, .txt- oder .DS_Store-Dateien tauchen gar nicht erst auf.




### 🟦 Übung 3a — *Filter verstehen*

Im Ordner liegt versehentlich auch eine Datei `notizen.txt`. Landet sie in `bilder`? Warum (nicht)?

<details>
<summary>Lösung Übung 3a</summary>

Nein. `notizen.txt` endet nicht mit `".png"` und wird daher nicht eingelesen. Genau dafür ist der Filter da, er schützt uns vor Fremddateien im Ordner.
</details>

### 🟨 Übung 3b — *Einlesen und Zuordnung aus dem Ordner*

Lese nun alle Bilder aus dem Ordner ein und ordne sie direkt dem Wort und der Farbe zu.  Nutze eine `for`-Schleife und das Parsen aus Block 2.

<details>
<summary>Lösung Übung 3b</summary>

<pre><code class="language-python">def run_task(win, sbj_num, age, sex, vleitung):

    # Pfad sauber zusammensetzen: Ordner + Dateiname
    bildpfad = os.path.join("bilder", "*.png")
    all_files = glob.glob(bildpfad)
    
    
    for bildname in all_files:
        bild = visual.ImageStim(win, image=bildname)
        bild.draw()
        win.flip() 
        event.waitKeys(keyList=["space"])
        
        basisname = Path(bildname).stem
        wort, farbe = basisname.split("_")       # split am "_" → ["gelb", "blau"] → entpackt
        print("Wort:", wort)     # "gelb"
        print("Farbe:", farbe)    # "blau"
</code></pre>

Jetzt steckt in <code>trials</code> alles, was die Schleife braucht: der <strong>Pfadbaustein</strong> (Dateiname) plus die <strong>Bedingungsinfo</strong> (Wort, Farbe) — vollautomatisch aus dem Ordner gezogen.
</details>

---

## Block 4 — Der Bilder-Stroop: die Schleife

Jetzt setzen wir alles zusammen. Die Struktur ist **identisch** zur Text-Stroop-Schleife der letzten Sitzung — nur zwei Dinge ändern sich: (1) der Stimulus ist ein `ImageStim` statt `TextStim`, (2) `wort`/`farbe` kommen aus dem Dateinamen.

```python
from psychopy import visual, core, event
import os, glob, random

def run_task(win, sbj_num, age, sex, vleitung):

    # --- Instruktion (aus voriger Sitzung, hier gekürzt) ---
    # zeige_instruktion(win, 'intro')

    # --- 1) Bilder finden und Trial-Liste bauen ---
    suchmuster  = os.path.join("bilder", "*.png")
    alle_bilder = glob.glob(suchmuster)
    alle_bilder.sort()

    trials = []
    for pfad in alle_bilder:
        dateiname   = os.path.basename(pfad)
        basis       = dateiname.replace(".png", "")
        wort, farbe = basis.split("_")
        trials.append((pfad, wort, farbe))

    random.shuffle(trials)

    # --- 2) Tastenzuordnung ---
    key_map = {"d": "gelb", "k": "blau"}

    # --- 3) Fixationskreuz + Uhr (einmal erzeugen, immer wiederverwenden) ---
    fix   = visual.TextStim(win, text="+", color="black", height=1.0)
    clock = core.Clock()

    # --- 4) Logfile anlegen ---
    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")

    filename = os.path.join("Logfiles", f"VP_{sbj_num}_bildstroop.csv")
    header   = "vp_num,vleitung,trial,datei,wort,farbe,kongruent,antwort,korrekt,rt\n"

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        # --- 5) Die Trial-Schleife ---
        for trial_num, (pfad, wort, farbe) in enumerate(trials):

            # Fixationskreuz
            fix.draw()
            win.flip()
            core.wait(0.5)

            # Bild laden und zeigen
            stim = visual.ImageStim(win, image=pfad)
            stim.draw()
            win.flip()
            clock.reset()
            event.clearEvents()

            # Antwort + RT
            keys = event.waitKeys(keyList=["d", "k"], timeStamped=clock)
            taste, rt = keys[0]

            # Auswertung
            antwort   = key_map[taste]
            korrekt   = int(antwort == farbe)
            kongruent = int(wort == farbe)

            # Dateiname fürs Logfile (ohne Ordnerpfad)
            dateiname = os.path.basename(pfad)

            # Zeile schreiben
            logfile.write(
                f"{sbj_num},{vleitung},{trial_num},{dateiname},{wort},{farbe},"
                f"{kongruent},{antwort},{korrekt},{rt:.3f}\n"
            )

            # Kurze Pause (leerer Bildschirm)
            win.flip()
            core.wait(0.5)
```

Erklärung:
- **Fast alles ist bekannt.** Fixationskreuz, `clock.reset()` nach `flip()`, `waitKeys(..., timeStamped=clock)`, das Logfile — alles aus der letzten Sitzung. Neu sind nur **die drei Zeilen**, die das Bild laden und zeigen.
- **`ImageStim` in der Schleife erzeugen:** Wir bauen den Stimulus **pro Trial neu** (`stim = visual.ImageStim(...)`), weil sich das Bild jeden Trial ändert. Das Fixationskreuz dagegen erzeugen wir **einmal vor** der Schleife (es ändert sich nie). Dies könnten wir auch wieder auslagern in eine separate Funktion.
- **Neue Spalte `datei`** im Header und in der Write-Zeile: So steht im Logfile, welches Bild in welchem Trial lief — praktisch fürs Nachvollziehen.

**Test:** `launch_task.py` starten, Dialog ausfüllen — jetzt erscheinen Farbbilder statt Textwörter, Tasten `d`/`k` werden erkannt, das Logfile füllt sich inklusive Dateiname.


---

## Block 5 — Über eine Liste die Bedingungen steuern

In Block 4 haben wir die Trial-Liste direkt aus den Bilddateien gebaut: jede Datei im Ordner = ein Trial. Das funktioniert für wenige Bilder, aber in echten Experimenten will man mehr Kontrolle: **Wie viele** Trials soll es geben? **Welcher Anteil** soll kongruent vs. inkongruent sein? Soll es seltene Spezial-Trials geben (z. B. nur 30 % Go)?

Die Lösung: Wir bauen **zuerst** eine `conditions`-Liste, die festlegt, was in **jedem einzelnen Durchgang** passieren soll. Die Bilder werden dann **passend zur Bedingung** ausgewählt.

### 5.1 Die Idee: `conditions` bestimmt den Versuchsplan

```python
# Jeder Eintrag steht für einen Trial:
# 0 = kongruent, 1 = inkongruent
conditions = [0, 1, 0, 1, 0, 1, 0, 1]
```

- Die **Länge** der Liste = die **Anzahl der Trials**. 8 Einträge → 8 Durchgänge.
- Der **Wert** sagt, welche Bedingung in diesem Durchgang dran ist.
- Danach mischen wir die Liste (`random.shuffle`) → zufällige Reihenfolge, aber **feste Anzahl** pro Bedingung.

Das ist der entscheidende Unterschied zur bisherigen `trials`-Liste: Wir trennen den **Versuchsplan** (was soll passieren?) von der **Stimulusauswahl** (welches Bild zeigen wir dafür?).


### 5.2 Beliebig viele Trials erzeugen

Mit der Multiplikation von Listen (`*`) erzeugt man beliebig lange Bedingungslisten:

```python
# 20 Trials: je 10 kongruent (0) und 10 inkongruent (1)
conditions = [0] * 10 + [1] * 10
random.shuffle(conditions)
print(len(conditions))   # 20
```

```python
# 60 Trials: je 30 und 30
conditions = [0] * 30 + [1] * 30
random.shuffle(conditions)
print(len(conditions))   # 60
```

Die Anzahl der Trials ändert man an **einer einzigen Stelle** — die Schleife, das Logfile, alles andere bleibt unberührt.


### 🟦 Übung 5a — *Länge und Verteilung*

Wie viele Trials hat die folgende Liste, und wie viele davon sind kongruent (0)?

```python
conditions = [0] * 15 + [1] * 5
```

<details>
<summary>Lösung Übung 5a</summary>

20 Trials insgesamt: <strong>15 kongruent</strong> (0) und <strong>5 inkongruent</strong> (1). Das Verhältnis ist 75 % / 25 %.
</details>


### 5.3 Ungleiche Anteile: 70/30 für Go-Nogo

In einer **Go-Nogo-Aufgabe** soll die VP bei den meisten Trials reagieren (Go), aber bei wenigen Trials die Reaktion **unterdrücken** (Nogo). Typisch ist ein Verhältnis von **70 % Go / 30 % Nogo**. Die seltenen Nogo-Trials bauen eine Reaktionsgewohnheit auf, die dann gehemmt werden muss.

Genau dieses Verhältnis steuern wir über die `conditions`-Liste:

```python
# Go-Nogo: 70 % Go (0), 30 % Nogo (1)
n_trials = 40
n_go   = 28     # 70 % von 40
n_nogo = 12     # 30 % von 40

conditions = [0] * n_go + [1] * n_nogo
random.shuffle(conditions)

print(len(conditions))          # 40
print(conditions.count(0))      # 28 — immer exakt
print(conditions.count(1))      # 12 — immer exakt
```

Worauf es ankommt:
- Die Anzahl ist **exakt**: immer genau 28 Go und 12 Nogo, egal wie oft man `shuffle` aufruft. `shuffle` ändert nur die **Reihenfolge**, nie die **Zusammensetzung**.
- `.count(0)` zählt, wie oft `0` in der Liste vorkommt — praktisch zur Kontrolle.
- Will man das Verhältnis ändern, ändert man nur `n_go` und `n_nogo`. Der Rest des Codes bleibt gleich.


### 🟦 Übung 5b — *andere Verhältnisse*

Baue eine `conditions`-Liste mit **50 Trials**, davon **80 % Go** (0) und **20 % Nogo** (1). Wie viele Einträge hat jede Bedingung?

<details>
<summary>Lösung Übung 5b</summary>

<pre><code class="language-python">n_go   = 40     # 80 % von 50
n_nogo = 10     # 20 % von 50
conditions = [0] * n_go + [1] * n_nogo
random.shuffle(conditions)
</code></pre>

40 Go-Trials und 10 Nogo-Trials. Die Rechnung: <code>50 * 0.80 = 40</code> und <code>50 * 0.20 = 10</code>.
</details>


### 🟦 Übung 5c — *drei Bedingungen*

Erweitere die Liste auf **drei** Bedingungen: `0` = kongruent, `1` = inkongruent, `2` = neutral. 30 Trials, gleichverteilt (je 10).

<details>
<summary>Lösung Übung 5c</summary>

<pre><code class="language-python">conditions = [0] * 10 + [1] * 10 + [2] * 10
random.shuffle(conditions)
print(len(conditions))   # 30
</code></pre>

Einfach eine weitere Bedingung anhängen (<code>+ [2] * 10</code>). Dasselbe Prinzip funktioniert für beliebig viele Stufen.
</details>


### 5.4 `conditions` in die Schleife einbauen

Jetzt verbinden wir die `conditions`-Liste mit der Bildauswahl. Pro Trial schauen wir in `conditions` nach, welche Bedingung dran ist, und wählen dazu **passend ein Bild**. Dafür sortieren wir unsere Bilder vorher in zwei Gruppen.

```python
from psychopy import visual, core, event
import os, glob, random

def run_task(win, sbj_num, age, sex, vleitung):

    # --- 1) Bilder finden und nach Bedingung sortieren ---
    suchmuster  = os.path.join("bilder", "*.png")
    alle_bilder = glob.glob(suchmuster)

    kongruente_bilder   = []
    inkongruente_bilder = []

    for pfad in alle_bilder:
        dateiname = os.path.basename(pfad)
        basis     = dateiname.replace(".png", "")
        wort, farbe = basis.split("_")
        if wort == farbe:
            kongruente_bilder.append(pfad)
        else:
            inkongruente_bilder.append(pfad)

    # --- 2) Versuchsplan: conditions-Liste ---
    n_kong   = 20      # 0 = kongruent
    n_inkong = 20      # 1 = inkongruent
    conditions = [0] * n_kong + [1] * n_inkong
    random.shuffle(conditions)

    # --- 3) Tastenzuordnung, Fixation, Uhr ---
    key_map = {"d": "gelb", "k": "blau"}
    fix     = visual.TextStim(win, text="+", color="black", height=1.0)
    clock   = core.Clock()

    # --- 4) Logfile ---
    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")
    filename = os.path.join("Logfiles", f"VP_{sbj_num}_bildstroop.csv")
    header   = "vp_num,vleitung,trial,datei,wort,farbe,kongruent,antwort,korrekt,rt\n"

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        # --- 5) Die Trial-Schleife: conditions steuert ---
        for trial_num, bedingung in enumerate(conditions):

            # Bild passend zur Bedingung auswählen
            if bedingung == 0:
                pfad = random.choice(kongruente_bilder)
            else:
                pfad = random.choice(inkongruente_bilder)

            # wort und farbe aus dem Dateinamen (wie in Block 2)
            dateiname = os.path.basename(pfad)
            basis     = dateiname.replace(".png", "")
            wort, farbe = basis.split("_")

            # Fixationskreuz
            fix.draw()
            win.flip()
            core.wait(0.5)

            # Bild zeigen
            stim = visual.ImageStim(win, image=pfad)
            stim.draw()
            win.flip()
            clock.reset()
            event.clearEvents()

            # Antwort + RT
            keys = event.waitKeys(keyList=["d", "k"], timeStamped=clock)
            taste, rt = keys[0]

            # Auswertung
            antwort   = key_map[taste]
            korrekt   = int(antwort == farbe)
            kongruent = int(wort == farbe)

            logfile.write(
                f"{sbj_num},{vleitung},{trial_num},{dateiname},{wort},{farbe},"
                f"{kongruent},{antwort},{korrekt},{rt:.3f}\n"
            )

            win.flip()
            core.wait(0.5)
```

Worauf es ankommt:
- Die **`conditions`-Liste steuert**, nicht mehr die Bilderliste. Früher: „jedes Bild = ein Trial". Jetzt: „jeder Eintrag in `conditions` = ein Trial, das Bild wird dazu **ausgewählt**".
- **`random.choice`** zieht jedes Mal **ein** zufälliges Bild aus der passenden Gruppe. Dasselbe Bild kann also mehrfach vorkommen — das ist gewollt, denn wir haben mehr Trials als Bilder.
- **Bedingungsanteile ändern** = nur `n_kong` und `n_inkong` anpassen. Kein anderer Code muss sich ändern.
- Die **Schleife selbst** ist fast identisch zu Block 4 — nur die ersten Zeilen (Bildauswahl über `bedingung`) sind neu.


### 🟦 Übung 5d — *auf Go-Nogo umbauen*

Ändere die `conditions`-Liste und die Bildauswahl so, dass `0` = Go (häufig, 70 %) und `1` = Nogo (selten, 30 %) bedeutet. Go-Bilder sind alle Bilder **ohne** `"gelb"` im Namen, Nogo-Bilder sind die mit `"gelb"`. (40 Trials insgesamt.)

Tipp: Du musst nur **zwei** Dinge anpassen: die Anzahl in der `conditions`-Liste und die Sortierlogik der Bilder.

<details>
<summary>Lösung Übung 5d</summary>

<pre><code class="language-python"># conditions: 70 % Go, 30 % Nogo
n_go   = 28
n_nogo = 12
conditions = [0] * n_go + [1] * n_nogo
random.shuffle(conditions)

# Bilder sortieren: Go = kein "gelb", Nogo = "gelb"
go_bilder   = []
nogo_bilder = []
for pfad in alle_bilder:
    if "gelb" in os.path.basename(pfad):
        nogo_bilder.append(pfad)
    else:
        go_bilder.append(pfad)

# In der Schleife:
if bedingung == 0:
    pfad = random.choice(go_bilder)
else:
    pfad = random.choice(nogo_bilder)
</code></pre>

Das Grundmuster ist exakt dasselbe: <code>conditions</code> legt fest <strong>wie viele</strong> und <strong>welche Art</strong>, die <code>if/else</code>-Verzweigung wählt das passende Bild. Ob die Bedingungen „kongruent/inkongruent" oder „Go/Nogo" heißen, ändert nur die <strong>Bedeutung</strong> — die <strong>Programmierlogik</strong> bleibt gleich.
</details>


### 🟨 Übung 5e — *Kontrolle einbauen*

Füge **vor** der Schleife zwei `print`-Zeilen ein, die in der Konsole ausgeben: (a) wie viele Trials insgesamt, (b) wie viele pro Bedingung. Warum ist das beim Entwickeln nützlich?

<details>
<summary>Lösung Übung 5e</summary>

<pre><code class="language-python">print("Trials insgesamt:", len(conditions))
print("Bedingung 0:", conditions.count(0), "| Bedingung 1:", conditions.count(1))
</code></pre>

Beim Entwickeln sieht man sofort, ob die Verteilung stimmt. Gerade bei ungleichen Anteilen (70/30) fällt ein Tippfehler (<code>[0] * 28 + [1] * 2</code> statt <code>* 12</code>) so auf, <strong>bevor</strong> man 40 Trials durchklickt.
</details>


### 🟨 Übung 5f — *nie zwei Nogo hintereinander*

Bei Go-Nogo will man manchmal verhindern, dass zwei seltene Nogo-Trials direkt aufeinanderfolgen (die VP braucht dazwischen Go-Trials, um die Reaktionsgewohnheit wieder aufzubauen). Schreibe eine Prüfung, die nach dem `shuffle` kontrolliert, ob zwei `1`-er nebeneinander stehen, und falls ja, **nochmal** mischt — so lange, bis es passt.

<details>
<summary>Lösung Übung 5f</summary>

<pre><code class="language-python">def zwei_nogo_in_folge(liste):
    for i in range(len(liste) - 1):
        if liste[i] == 1 and liste[i+1] == 1:
            return True
    return False

random.shuffle(conditions)
while zwei_nogo_in_folge(conditions):
    random.shuffle(conditions)
</code></pre>

Die Funktion geht die Liste paarweise durch. Solange zwei <code>1</code>-er benachbart sind, wird neu gemischt. Bei 12 Nogo in 40 Trials findet <code>shuffle</code> schnell eine gültige Anordnung.
</details>

---


## Block 6 — Zusammenführen & Ausblick

### Roter Faden / Wiederholung
- **`ImageStim` = TextStim mit Datei.** Gleiches Draw–Flip-Prinzip, nur `image=pfad` statt `text=...`.
- **`os.path.join`** baut Pfade plattformübergreifend, nie Schrägstriche selbst tippen.

- **Namenskonvention `WORT_FARBE.png`** + `.replace().split("_")` gibt die Bedingungsinfo direkt zurück, kein Von-Hand-Tippen reduziert die Fehleranfälligkeit.
- Die **Schleife selbst blieb fast gleich**. Wir können vieles Modular zusammmen setzen.
- **Bedingungen steuern:** Über eine zuvor erstelle Variabe (hier `conditions` in Block 5) können wir die Trialanzahl bestimmen, diese gezielt ansteuern (z.B. konkruent oder inkongruent, Go oder NoGO), aufeinander folgende Trials kontrollieren usw.
- **Ausblick**: verschiedene Antwortmöglichkeiten als Modul (Tastendruck, Mausklick, Skala, freies Eingabefenster); Zusammenführen von `.csv`-Dateien zu einer Datei; erste Auswertungsschritte (Pandas) und Grafikerstellung (seaborn) in Python


