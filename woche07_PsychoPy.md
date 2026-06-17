# Sitzung: Vom Python-Skript zur PsychoPy-Experimentlogik

**Voraussetzung:** Python-Grundlagen geübt, PsychoPy **Coder** schon gesehen

**Ziel:** Die Studierenden verstehen die Trennung von *Launch-Datei* und *Task-Datei*. Sie bauen die Task **Stück für Stück** auf — von der Instruktion über das Logfile bis zu einem kleinen Stroop-Experiment mit Tastenantwort und Reaktionszeit.


**Arbeitsumgebung heute:**
- **Launch-Datei** (`launch_task.py`) aus OLAT herunterladen, diese wird in **PsychoPy** geöffnet und von dort gestartet → öffnet den Dialog und das Fenster.
- **Task-Datei** (`my_task.py`) wird in **Spyder** (oder der IDE Ihrer Wahl) programmiert, so können wir die Syntax besser verwalten während das Fenster sauber aus PsychoPy heraus läuft.

> Experiment starten über grünen Pfeil im **PsychoPy Coder**, Änderungen am Code in **Spyder**.


## Block 0 — Architektur: Launch vs. Task

Bisher lag alles in einer Datei. Jetzt teilen wir ein Experiment auf zwei Dateien auf:

- **Launch-Datei** = *Konfiguration & Start*. Wer ist die VP? Welches Programm? Fenster aufmachen, Task aufrufen, am Ende sauber schließen. Ändert sich kaum.
- **Task-Datei** = *die eigentliche Programmierlogik*. Instruktionen, Trials, Stimuli, Antworten, Logging. Hier wird entwickelt und getestet.


**Schema:**

```
launch_task.py  (in PsychoPy öffnen & starten)
      │
      │  importiert
      ▼
   my_task.py    (in Spyder entwickeln)
      │
      └── run_task(win, sbj_num, ...)   ← die Funktion, die alles macht
```

`import my_task` lädt die Datei `my_task.py` als **Modul**; mit `my_task.run_task(...)` rufen wir eine **Funktion** daraus auf. Dieselbe Logik wie `import random` → `random.choice(...)`, nur ist das Modul diesmal unsere eigene, selbst geschriebene Datei. 

> Die Datei `my_task.py` muss sich im gleichen Ordner befinden wie  `launch_task.py`. 

---

## Block 1 — Launch-Datei lesen: Module & Dialog

Wir gehen die `launch_task.py` von oben nach unten durch.

### 1.1 Die Imports

```python
import my_task
from psychopy import gui, core, visual, event
```

| Import | Was steckt drin? | Wofür heute? |
|--------|------------------|--------------|
| `my_task` | unsere eigene Task-Datei | enthält Funktion `run_task(...)` |
| `gui` | Dialogfenster | Abfrage: VP-Nummer, Alter, Geschlecht abfragen |
| `core` | Kernfunktionen | `core.wait()`, `core.quit()`, Uhren |
| `visual` | alles Sichtbare | `Window`, `TextStim` |
| `event` | Eingaben | Tasten abfragen (`event.waitKeys`, `event.getKeys`) |

Hinweis: `from psychopy import ...` holt mehrere Untermodule auf einmal. Wir schreiben bewusst `core.quit()` statt nur `quit()` → so ist klar, woher die Funktion kommt.

### 1.2 Die Dialogbox 

Häufig sieht man den Dialog so, dass die Werte **per Position** ausgelesen werden (`thisInfo[0]`, `thisInfo[1]`, …). Das funktioniert, ist aber fragil: Fügt man später ein Feld ein oder ändert die Reihenfolge, verrutschen **alle** Indizes, und man bekommt stillschweigend falsche Zuordnungen (Alter landet in `sex` usw.).

```python
myDlg = gui.Dlg(title="Aufgabenname")
myDlg.addField('VP-Nummer:')
myDlg.addField('Alter:')
myDlg.addField('Geschlecht:', choices=["weiblich", "maennlich", "divers"])
myDlg.addField('Programmwahl:', choices=["Erwachsene", "Kinder"])
myDlg.addField('Versuchsleitung:')

sbj_num = int(thisInfo[0])
age = int(thisInfo[1])
sex = thisInfo[2]
program = thisInfo[3]
vleitung = thisInfo[4]
```


Die aktuelle PsychoPy-API (und so ist es auch in `launch_task.py` gerade festgehalten, bietet eine bessere Lösung: Jedem Feld einen **`key`** geben und die Werte später **per Schlüssel** auslesen. `myDlg.data` lässt sich dann wie ein Dictionary über den Schlüssel ansprechen, ganz unabhängig von der Reihenfolge.

```python
myDlg = gui.Dlg(title="Stroop-Aufgabe")
myDlg.addField(key='vp_num',   label='VP-Nummer:')
myDlg.addField(key='age',      label='Alter:')
myDlg.addField(key='sex',      label='Geschlecht:',
               choices=["weiblich", "maennlich", "divers"])
myDlg.addField(key='program',  label='Programmwahl:',
               choices=["Erwachsene", "Kinder"])
myDlg.addField(key='vleitung', label='Versuchsleitung:')

```

Auslesen jetzt **über die Schlüssel** statt über Positionen:

```python
sbj_num  = int(info['vp_num'])
age      = int(info['age'])
sex      = info['sex']
program  = info['program']
vleitung = info['vleitung']
```

Worauf es ankommt:
- `key=...` ist der **stabile Name** des Feldes, `label=...` der angezeigte Text. Beide trennen → der angezeigte Text kann sich ändern, ohne dass der Code bricht.
- `info['age']` statt `info[1]` → **selbsterklärend** und **umsortier-sicher**.
- `myDlg.show()` **blockiert**, bis OK/Cancel geklickt wird.
- Das `if myDlg.OK / else core.quit()` ist die **Abbruchsicherung**. Ohne sie liefe das Skript mit leeren Daten weiter.
- `int(...)` bei VP-Nummer und Alter, weil Dialogeingaben immer **Strings** sind; für Rechnungen (z. B. `sbj_num % 2`) brauchen wir Zahlen.

> **Hinweis zur Versionsabhängigkeit:** Der `key`/`label`-Stil ist der empfohlene Weg in aktuellen PsychoPy-Versionen. Falls auf einem Rechner eine ältere PsychoPy-Version läuft, in der `addField` noch kein `key` akzeptiert, ist der positionsbasierte Zugriff (siehe oben) der Rückfallweg. Dann konsequent auf die Reihenfolge des Aufrufs achten! 

### 🟦 Übung 1 — *eigenständig*

In allen EEG-Studien erheben wir die Händigkeit der Versuchspersonen. Erweitere die Dialogbox um ein zusätzliches Feld `'Händigkeit:'` mit `choices=["rechts", "links"]`. Vergib einen sinnvollen `key` und lies den Wert in eine Variable `handedness` ein.

<details>
<summary>Lösung Übung 1</summary>

```python
myDlg.addField(key='handedness', label='Händigkeit:', choices=["rechts", "links"])
# ... nach show() und der OK-Prüfung:
handedness = info['handedness']
```

Der entscheidende Punkt: Weil wir über den **Schlüssel** `'handedness'` zugreifen, ist es völlig egal, an welcher Stelle das Feld eingefügt wurde.
</details>

---

## Block 2 — `my_task.py` anlegen: Funktion + Instruktion zeigen

Jetzt wechseln wir zu **Spyder** und bearbeiten die Task-Datei. Wir starten mit dem Grundgerüst: **eine Funktion**, die später alles enthält. Zunächst zeigt sie nur eine **Instruktion**.

### 2.1 Das Grundgerüst der Funktion

```python
# my_task.py
from psychopy import visual, core, event

def run_task(win, sbj_num, age, sex, vleitung):
    # Hier wird Schritt für Schritt alles hineinwachsen:
    # 1. Instruktion  
    # 2. Logfile
    # 3. Trial-Schleife
    pass
```

Wichtig zum Verständnis: `win` und die VP-Daten werden als **Argumente** übergeben. 
Die Funktion erzeugt das Fenster **nicht selbst**. 
Das Fenster gehört zur Launch-Datei, die Funktion *benutzt* es nur. Das ist die saubere Arbeitsteilung.

### 2.2 Das Draw–Flip-Prinzip

Bevor wir irgendetwas anzeigen, hier die zentrale Funktionalität von PsychoPy:

1. `draw()` → Funktion, die einen Stimulus auf die **unsichtbare Rückseite** (Backbuffer) malt.
2. `win.flip()` → Vorder- und Rückseite der Bildschirmanzeige **tauschen** → jetzt wird unsichtbarer Backpuffer sichtbar.
3. Was nicht vor dem `flip()` gezeichnet wurde, erscheint nicht.


### 2.3 Instruktion zeigen — bis Tastendruck

```python
def run_task(win, sbj_num, age, sex, vleitung):
    instruktion = visual.TextStim(
        win,
        text="Gleich erscheinen Farbwörter. Manchmal in verschiedenen Farben, als das Wort, das da steht.\n\n"
             "Drücke die Taste für die FARBE, in der das Wort geschrieben ist:\n"
             "d = rot   |   k = grün  \n\n"
             "Drücke die Leertaste, um mit der Übung zu starten.",
        color="black", height=0.8
    )
    
    # Die Instruktion
    instruktion.draw()
    win.flip()
    event.waitKeys(keyList=["space"])   # wartet, bis Leertaste gedrückt wird
```

Worauf es ankommt:
- `\n` erzeugt Zeilenumbrüche im Stimulus-Text.
- `event.waitKeys(keyList=["space"])` **hält an**, bis genau diese Taste kommt → so liest die VP in Ruhe.
- Schon hier wird die **Tastenzuordnung** angekündigt (d/k), die wir später noch brauchen.

**Test:** Mit diesem Stand ist die Task bereits lauffähig — `launch_task.py` in PsychoPy starten, Dialog ausfüllen, Instruktion erscheint, Leertaste schließt sie.

### 🟦 Übung 2 — *Mischung: gemeinsam starten, dann allein*

Zeige **nach** der Instruktion einen kurzen „Los geht's!", jetzt sollen nur die Tasten d und k aktiv sein. Was brauchen Sie dafür alles? Nutze einen neuen `TextStim`.

<details>
<summary>Lösung Übung 2</summary>

```python
los = visual.TextStim(win, text="Los geht's!", color="black", height=1.0)
los.draw()
win.flip()
event.waitKeys(keyList=["k, d"])
#event.waitKeys(maxWait=3, keyList=["k, d"])
#core.wait(1.0)
```

Häufiger Fehler: `win.flip()` vergessen → Bildschirm bleibt leer. Oder `draw()` vergessen → die alte Anzeige bleibt stehen. 
</details>

---

## Block 3 — Logfile mit Header schreiben

*Ein Experiment ohne Daten ist nur eine Diashow.* Bevor der erste Trial läuft, legen wir das Logfile an — mit **Kopfzeile**, die festlegt, welche Spalten wir gleich befüllen.

### 3.1 Datei anlegen — VP-spezifischer Name

```python
import os

def run_task(win, sbj_num, age, sex, vleitung):

    # ... (Instruktion von oben) ...

    # Ordner für Logfiles, falls nicht vorhanden
    if not os.path.exists("Logfiles"):
        os.makedirs("Logfiles")

    # Pfad MIT Ordner zusammensetzen → Datei landet in Logfiles/
    filename = os.path.join("Logfiles", f"VP_{sbj_num}_stroop.csv")

    header = "vp_num,vleitung,trial,wort,farbe,kongruent,antwort,korrekt,rt\n"

    with open(filename, "w", buffering=1) as logfile:
        logfile.write(header)

        # Hier kommt dann die Schleife
```

Worauf es ankommt:
- `"w"` = write, **überschreibt** eine vorhandene Datei → deshalb VP-Nummer im Namen.
- Eine **CSV** ist nur Text mit Kommas: eine Kopfzeile, danach eine Zeile pro Trial.
- `\n` = Zeilenumbruch, am Ende jeder Zeile.
- Die Kopfzeile entspricht dem Header: Jede Datenzeile muss später **dieselbe Spaltenzahl und -reihenfolge** haben. Wir definieren hier schon die Stroop-Spalten (`wort`, `farbe`, `kongruent`, `antwort`, `korrekt`, `rt`), auch wenn wir sie erst später (in Block 4) füllen.



### 🟦 Übung 3 

Angenommen, wir haben in einer Variable `data` alle Informationen, die ins Logfile geschrieben werden sollen. Wie muss der Aufruf sein, damit er ins logfile geschrieben wird?
```python
data = "VPNummer,Versuchsleiter,0,TEST,rot,1,none,0,0.000\n"
```


<details>
<summary>Lösung Übung 3</summary>

```python
# Wir kann es ins Logfile geschrieben werden?
#Entweder
logfile.write(data)
#oder direkt
logfile.write(f"{sbj_num},{vleitung},0,TEST,rot,1,none,0,0.000\n")
```

Zählt man die Kommas, müssen Kopfzeile und Datenzeile **identisch viele Felder** haben.
</details>

---

## Block 4 — Die Trial-Schleife: Stroop mit Antwort & RT

Jetzt das Herzstück. In Übung 3 habt ihr gelernt, **eine** Zeile ins Logfile zu schreiben. Genau das passiert jetzt automatisch — einmal pro Durchgang. Wir bauen die Schleife, die pro Trial ein Farbwort zeigt, die Antwort und die Reaktionszeit erfasst und beides ins Logfile schreibt. Hier laufen alle bisherigen Bausteine zusammen.

### 4.1 Die Trials vorbereiten

Ein Stroop-Trial besteht aus einem **Wort** und einer **Anzeigefarbe**. Ist beides gleich → *kongruent*, sonst *inkongruent*. Wir starten erst einmal mit **zwei Farben** (rot, blau) und **zwei Tasten** (`d`, `k`).

```python
    # Tastenzuordnung: welche Taste steht für welche Farbe?
    key_map = {"d": "rot", "k": "blau"}

    # Deutsches Farbwort → englischer PsychoPy-Farbcode (fürs Rendern)
    COLOR_DE_EN = {"rot": "red", "blau": "blue"}

    # Liste von Trials: (Wort, Anzeigefarbe)
    trials = [
        ("rot",  "rot"),    # kongruent
        ("rot",  "blau"),   # inkongruent
        ("blau", "rot"),    # inkongruent
        ("blau", "blau"),   # kongruent
    ]
```

> `key_map` ist ein **Dictionary** und weißt die Farbe der Taste zu. Linke Hand auf `d`, rechte Hand auf `k`.

> `COLOR_DE_EN` ist ebenfalls ein **Dictionary** und weißt die Farbe der englischen Farbe zu. Das erleichert uns den Vergleich, um Congruency und Correctness festzustellen, und um sicherzugehen, dass die Farbe richtig gerendet wird, da PsychoPy mit englischen Farben arbeitet.

### 🟦 Übung 4a — *gemeinsam*

> Bevor wir die Schleife bauen: Was liefert `key_map["d"]` zurück? Und was passiert bei `key_map["x"]`?

<details>
<summary>Lösung Übung 4a</summary>

```python
key_map["d"]   # → "rot"
key_map["x"]   # → KeyError! "x" ist kein Schlüssel im Dictionary
```

Ein Dictionary findet einen Wert nur über einen **vorhandenen Schlüssel**. Genau deshalb begrenzen wir gleich mit `keyList=["d", "k"]`, *welche* Tasten überhaupt akzeptiert werden, so kann gar kein ungültiger Schlüssel in `key_map` landen.
</details>

### 4.2 Die Schleife

Genau die `for`-Schleifen aus Python strukturieren jetzt den **Trial-Ablauf**. Wir bauen in **zwei Stufen**: zuerst eine vollständig lauffähige Schleife, die Wörter zeigt, Tasten abfragt und loggt. Danach ergänzen wir die Reaktionszeit.

#### Stufe 1 — die lauffähige Schleife (noch ohne RT)

```python
    # Fixationskreuz einmal erzeugen (Inhalt ändert sich nie)
    fix = visual.TextStim(win, text="+", color="black", height=1.0)

    for trial_num, (wort, farbe) in enumerate(trials):

        # --- Fixationskreuz vorab ---
        fix.draw()
        win.flip()
        core.wait(0.5)

        # --- Stroop-Wort zeigen ---
        stim = visual.TextStim(win, text=wort,
                               color=COLOR_DE_EN[farbe], height=1.5)
        stim.draw()
        win.flip()
        event.clearEvents()           # alte Tastendrücke verwerfen

        # --- auf eine der beiden Antworttasten warten ---
        keys = event.waitKeys(keyList=["d", "k"])
        taste = keys[0]               # waitKeys liefert eine Liste von Tasten → erste nehmen

        # --- Auswertung ---
        antwort   = key_map[taste]            # Taste → Farbantwort
        korrekt   = int(antwort == farbe)     # richtig, wenn Antwort = Anzeigefarbe
        kongruent = int(wort == farbe)        # kongruenter Trial?

        # --- Zeile ins Logfile (genau wie in Übung 3, ohne rt) ---
        logfile.write(
            f"{sbj_num},{vleitung},{trial_num},{wort},{farbe},"
            f"{kongruent},{antwort},{korrekt}\n"
        )

        # --- kurze Pause zwischen Trials ---
        win.flip()                    # nichts gezeichnet → Bildschirm leer
        core.wait(0.5)
```

Worauf es ankommt:
- `enumerate(trials)` liefert **Index + Trial** gleichzeitig → `trial_num` ist gratis fürs Logfile dabei.
- Das **Entpacken** `(wort, farbe)` direkt im `for` ist dasselbe Tupel-Entpacken wie in Python geübt.
- `event.clearEvents()` verwirft Tasten, die *vor* dem Stimulus gedrückt wurden, könnte es zu Fehlern kommen im Falle eines zu frühen Drucks einer Taste.
- `event.waitKeys(keyList=["d", "k"])` **hält an**, bis `d` oder `k` kommt, und liefert eine **Liste** zurück → `keys[0]` ist die gedrückte Taste als **String** (z. B. `"d"`).
- Die `write`-Zeile ist die aus Übung 3, jetzt aber mit den Schleifen-Variablen statt vordefinierter Testwerte. Noch **ohne** `rt`, deshalb auch **ohne** die `rt`-Spalte im Header.

> **Wichtig:** In dieser Stufe bleibt die Spalte `rt` leer, da wir sie noch nicht befüllen. 

**Test:** Dieser Stand läuft komplett durch. Wörter erscheinen, Tasten werden erkannt, das Logfile füllt sich Zeile für Zeile. Ein vollständiges kleines Experiment, nur ohne Zeitmessung.

#### Stufe 2 — Reaktionszeit ergänzen

Jetzt messen wir, **wie schnell** geantwortet wurde. Dafür brauchen wir eine Uhr (`core.Clock`) und drei kleine Änderungen. Die Stellen, die neu oder geändert sind, sind markiert:

```python
    clock = core.Clock()              # (1) NEU: Uhr für die Reaktionszeit

    fix = visual.TextStim(win, text="+", color="black", height=1.0)

    for trial_num, (wort, farbe) in enumerate(trials):

        fix.draw()
        win.flip()
        core.wait(0.5)

        stim = visual.TextStim(win, text=wort,
                               color=COLOR_DE_EN[farbe], height=1.5)
        stim.draw()
        win.flip()
        clock.reset()                 # (2) NEU: Uhr auf 0, sobald das Wort erscheint
        event.clearEvents()

        # (3) GEÄNDERT: timeStamped=clock → waitKeys liefert jetzt (Taste, Zeit)
        keys = event.waitKeys(keyList=["d", "k"], timeStamped=clock)
        taste, rt = keys[0]           # jetzt ein Tupel: Taste UND Reaktionszeit

        antwort   = key_map[taste]
        korrekt   = int(antwort == farbe)
        kongruent = int(wort == farbe)

        # (4) GEÄNDERT: rt am Ende ergänzt
        logfile.write(
            f"{sbj_num},{vleitung},{trial_num},{wort},{farbe},"
            f"{kongruent},{antwort},{korrekt},{rt:.3f}\n"
        )

        win.flip()
        core.wait(0.5)
```

Was sich geändert hat:
- **(1) `clock = core.Clock()`** vor der Schleife: eine Uhr, die wir pro Trial zurücksetzen.
- **(2) `clock.reset()`** *unmittelbar nach* `win.flip()` → die RT zählt ab dem Moment, in dem das Wort **sichtbar** wird. Reihenfolge ist hier entscheidend: erst zeigen, dann Uhr auf null.
- **(3) `timeStamped=clock`** verändert die **Rückgabe** von `waitKeys`: Statt nur der Taste kommt jetzt ein Tupel `(Taste, Zeit)` zurück. Deshalb auch `taste, rt = keys[0]` statt `taste = keys[0]`.
- **(4)** `{rt:.3f}` ergänzt die Reaktionszeit am Ende der Logfile-Zeile (auf Millisekunden gerundet). **Jetzt** muss die Kopfzeile auch die `rt`-Spalte enthalten.



### 🟦 Übung 4b — *Zuweisung verstehen*

In der Auswertung steht `korrekt = int(antwort == farbe)`. Warum das `int(...)` drumherum? Was stünde ohne `int(...)` im Logfile — und warum ist die Zahl praktischer?

<details>
<summary>Lösung Übung 4b</summary>

`antwort == farbe` ergibt einen **Wahrheitswert**: `True` oder `False`. `int(...)` macht daraus `1` bzw. `0`:

```python
int(True)    # → 1
int(False)   # → 0
```

Ohne `int(...)` stünde wörtlich `True`/`False` in der CSV. Mit `1`/`0` lässt sich später viel leichter rechnen — z. B. der Mittelwert der `korrekt`-Spalte ist direkt die **Trefferquote** (0.75 = 75 % richtig). Genau dieselbe Logik gilt für `kongruent`.
</details>

### 🟦 Übung 4c — *Feedback*

> Erweitere die Schleife so, dass **nach** der Antwort ein kurzes Feedback erscheint: „Richtig!" (grün) bzw. „Falsch!" (rot) für 0.5 s, abhängig von `korrekt`.

<details>
<summary>Lösung Übung 4c</summary>

```python
        if korrekt == 1:
            feedback = visual.TextStim(win, text="Richtig!", color="green", height=1.0)
        else:
            feedback = visual.TextStim(win, text="Falsch!", color="red", height=1.0)
        feedback.draw()
        win.flip()
        core.wait(0.5)
```

(Einfügen **vor** dem leerenden `win.flip()` am Schleifenende.) Das Feedback nutzt die bereits berechnete Variable `korrekt` — wir müssen nichts neu auswerten. Hier zahlt sich Übung 4b aus: Weil `korrekt` schon `1`/`0` ist, reicht ein simples `if korrekt == 1`. 
</details>

### 🟨 Übung 4d — *Randomisierung*

> Sorge dafür, dass die Reihenfolge der Trials pro VP **zufällig** ist (`import random` → `random.shuffle(trials)` vor der Schleife). Überlege: An welcher Logfile-Spalte erkennt man später, dass die Reihenfolge variiert hat?

<details>
<summary>Lösung</summary>

```python
import random
random.shuffle(trials)   # vor der for-Schleife
```

</details>

---

## Block 5 — Zusammenführen & Ausblick

### Roter Faden / Wiederholung
- **Draw–Flip** ist das wichtigste Prinzip — Stimuli erscheinen erst nach `flip()`.
- **Eine Funktion** (`run_task`) bündelt die Logik; **eine Schleife** strukturiert die Trials.
- Das **Logfile** ist nur Text: Mit der Kopfzeile/Header und eine Zeile pro Trial.

### Ausblick auf die nächsten Sitzungen
- **Config** Variablen / Modul
- **Dateien einlesen**: Für Bildmaterial, Audios, Counterbalancing
- **Feedback**: RT zu schnell/langsam, Antwort richtig/falsch
- **Positionen verändern**
- **Dictionaries** 
- **Mehr Trials & Bedingungen**: Trial-Liste programmatisch erzeugen statt von Hand.
- **Randomisierung & Gegenbalancierung** (vgl. `sbj_num % 2`).
- **Fehlerbehandlung** mit `try/finally`, damit das Logfile auch bei Abstürzen sicher schließt.
- **Timing-Präzision**: Genauigkeit von `waitKeys`-RTs, Frame-basiertes Timing.
- **Sortierregel**: Go-Nogo, Wisconsin Card Sorting Test
