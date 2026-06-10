# Lektürewoche: PsychoPy-Code lesen und verstehen
## Die Flanker-Aufgabe

> **Ziel dieser Woche:** Fremden Code lesen, verstehen und erklären können.

---

## Was ist die Flanker-Aufgabe?

In der Eriksen-Flanker-Aufgabe sieht die Versuchsperson eine Reihe von Pfeilen,  
z. B. `<<><<`. Die Aufgabe: Reagieren Sie auf den **mittleren Pfeil** und ignorieren Sie die äußeren.

```
<<<<<   → kongruent   → Antwort: links   (alle zeigen links)
>>>>>   → kongruent   → Antwort: rechts  (alle zeigen rechts)
<<><<   → inkongruent → Antwort: rechts  (Mitte zeigt rechts, Flanker links)
>><>>   → inkongruent → Antwort: links   (Mitte zeigt links, Flanker rechts)
```

Der **Flanker-Effekt**: Inkongruente Trials sind langsamer und fehleranfälliger,  
weil die Flanker-Pfeile automatisch Aufmerksamkeit auf sich ziehen. 

Weitere Untersuchungsmöglichkeiten sind Post-Error-Slowing, wie verhält sich die Versuchsperson nach einem Fehler. Und auch im EEG lässt sich die ERN (Error-Related Negativity) untersuchen, ein Event-Related Potential welche etwa 50-150 ms nach einem Fehler an frontalen, centralen Positionen gemessen werden kann.


## Das vollständige Flanker-Skript

Lesen Sie das Skript **einmal komplett durch**, bevor Sie mit den Übungen beginnen.  
Notieren Sie Stellen, die Sie nicht sofort verstehen. 

```python

# flanker_experiment.py

# ─── 1. Imports ───────────────────────────────────────────────────────────────
from psychopy import visual, core, event
import csv
from pathlib import Path

# ─── 2. Konfiguration ─────────────────────────────────────────────────────────
VP_ID      = "VP01"
VOLLBILD   = False
FENSTER_HG = "black"
MAX_WART   = 2.0
FIXDAUER   = 0.5

# ─── 3. Trialliste ────────────────────────────────────────────────────────────
TRIALS = [
    ("<<<<<", "left",  "kongruent"),
    (">>>>>", "right", "kongruent"),
    ("<<><<", "right", "inkongruent"),
    (">><>>", "left",  "inkongruent"),
] * 5

# ─── 4. Logfile anlegen ───────────────────────────────────────────────────────
Path("logfiles").mkdir(exist_ok=True)
logfile = Path("logfiles") / f"{VP_ID}_flanker.csv"

with open(logfile, mode="w", newline="", encoding="utf-8") as f:
    csv.writer(f).writerow(["trial", "stimulus", "korrekte_taste",
                             "bedingung", "antwort", "korrekt", "rt_ms"])

# ─── 5. Fenster und Stimuli ───────────────────────────────────────────────────
fenster = visual.Window(
    size=(1024, 768),
    fullscr=VOLLBILD,
    color=FENSTER_HG,
    units="pix"
)
uhr = core.Clock()

fixation  = visual.TextStim(win=fenster, text="+",
                             color="white", height=50)
stimulus  = visual.TextStim(win=fenster, text="",
                             color="white", height=80)
feedback  = visual.TextStim(win=fenster, text="",
                             color="white", height=40)
instruktion = visual.TextStim(
    win=fenster,
    text="Reagieren Sie auf den MITTLEREN Pfeil.\n"
         "Linker Pfeil → Taste F   |   Rechter Pfeil → Taste J\n\n"
         "Drücken Sie LEERTASTE zum Starten.",
    color="white", height=28
)

# ─── 6. Instruktion ───────────────────────────────────────────────────────────
instruktion.draw()
fenster.flip()
event.waitKeys(keyList=["space"])

# ─── 7. Trial-Schleife ────────────────────────────────────────────────────────
for trial_nr, (stim_text, korrekte_taste, bedingung) in enumerate(TRIALS, start=1):

    fixation.draw()
    fenster.flip()
    core.wait(FIXDAUER)

    stimulus.setText(stim_text)
    stimulus.draw()
    uhr.reset()
    fenster.flip()

    tasten = event.waitKeys(maxWait=MAX_WART, keyList=["f", "j", "escape"])
    rt_sek = uhr.getTime()

    if tasten is None:
        antwort = None
        korrekt = False
        rt_ms   = None
    elif tasten[0] == "escape":
        fenster.close()
        core.quit()
    else:
        antwort = tasten[0]
        korrekt = (antwort == korrekte_taste)
        rt_ms   = round(rt_sek * 1000)

    feedback.setText("✓" if korrekt else "✗")
    feedback.setColor("green" if korrekt else "red")
    feedback.draw()
    fenster.flip()
    core.wait(0.4)

    with open(logfile, mode="a", newline="", encoding="utf-8") as f:
        csv.writer(f).writerow(
            [trial_nr, stim_text, korrekte_taste, bedingung,
             antwort, korrekt, rt_ms]
        )

# ─── 8. Abschluss ─────────────────────────────────────────────────────────────
fenster.close()
core.quit()
```

---

## 🟢 Übung 1 – Zeile für Zeile lesen

> **Aufgabe:** Lesen Sie den folgenden Ausschnitt laut vor. Schreiben Sie dann hinter  
> jede Zeile (oder jeden Block) einen Kommentar, der erklärt, was dort passiert.  
> Nutzen Sie eigene Worte, keine Fachbegriffe, die Sie nicht verstehen.

```python
fenster = visual.Window(        # ___________________________________________
    size=(1024, 768),           # ___________________________________________
    fullscr=VOLLBILD,           # ___________________________________________
    color=FENSTER_HG,           # ___________________________________________
    units="pix"                 # ___________________________________________
)
uhr = core.Clock()              # ___________________________________________

fixation = visual.TextStim(     # ___________________________________________
    win=fenster,                # ___________________________________________
    text="+",                   # ___________________________________________
    color="white",              # ___________________________________________
    height=50                   # ___________________________________________
)
stimulus = visual.TextStim(     # ___________________________________________
    win=fenster,
    text="",                    # ___________________________________________
    color="white",
    height=80
)
```

**Reflexionsfragen:**

1. Warum hat `stimulus` den Text `""` (leer), `fixation` aber den Text `"+"`?
2. Was bedeutet `units="pix"`? Was könnte alternativ stehen?
3. Wozu braucht man `uhr = core.Clock()` – und wann wird die Uhr gestartet?

<details>
<summary>Musterlösung</summary>

<pre><code class="language-python">fenster = visual.Window(        # Ein Grafikfenster wird erstellt
    size=(1024, 768),           # Größe: 1024 Pixel breit, 768 hoch
    fullscr=VOLLBILD,           # Vollbildmodus an/aus (hier False)
    color=FENSTER_HG,           # Hintergrundfarbe (hier "black")
    units="pix"                 # Koordinaten in Pixeln angeben
)
uhr = core.Clock()              # Zeituhr für Reaktionszeitmessung erstellen
fixation = visual.TextStim(     # Fixationskreuz-Objekt erstellen
    win=fenster,                # in dieses Fenster zeichnen
    text="+",                   # Text: Pluszeichen als Fixationspunkt
    color="white",              # Textfarbe weiß
    height=50                   # Schriftgröße 50 Pixel
)
stimulus = visual.TextStim(     # Pfeil-Stimulus-Objekt erstellen
    win=fenster,
    text="",                    # noch kein Text – wird in der Schleife gesetzt
    color="white",
    height=80
)
</code></pre>

<p><strong>Antworten:</strong></p>
<ol>
<li><code>stimulus.text=""</code> weil der konkrete Pfeiltext erst in der Trial-Schleife gesetzt wird (<code>stimulus.setText(...)</code>). Das Objekt wird einmal erstellt und immer wieder befüllt – das ist effizienter als jedes Mal ein neues Objekt zu erzeugen.</li>
<li><code>units="pix"</code> bedeutet, Positionen und Größen werden in Bildschirmpixeln angegeben. Alternativ: <code>"norm"</code> (−1 bis +1), <code>"deg"</code> (Sehwinkelgrad), <code>"cm"</code>.</li>
<li><code>core.Clock()</code> erzeugt die Uhr. Gestartet (auf 0 zurückgesetzt) wird sie erst mit <code>uhr.reset()</code> – direkt bevor <code>fenster.flip()</code> den Stimulus auf den Bildschirm bringt.</li>
</ol>

</details>

---

## 🟡 Übung 2 – Einen Abschnitt beschreiben

> **Aufgabe A:** Lesen Sie den folgenden Codeblock laut vor – Zeile für Zeile, ohne zu überspringen.  
> **Aufgabe B:** Schreiben Sie danach in wenigen Sätzen, was in diesem Block passiert.  
> **Aufgabe C:** Beantworten Sie die Fragen darunter.

```python
for trial_nr, (stim_text, korrekte_taste, bedingung) in enumerate(TRIALS, start=1):

    fixation.draw()
    fenster.flip()
    core.wait(FIXDAUER)

    stimulus.setText(stim_text)
    stimulus.draw()
    uhr.reset()
    fenster.flip()

    tasten = event.waitKeys(maxWait=MAX_WART, keyList=["f", "j", "escape"])
    rt_sek = uhr.getTime()

    if tasten is None:
        antwort = None
        korrekt = False
        rt_ms   = None
    elif tasten[0] == "escape":
        fenster.close()
        core.quit()
    else:
        antwort = tasten[0]
        korrekt = (antwort == korrekte_taste)
        rt_ms   = round(rt_sek * 1000)
```

**Aufgabe B – Beschreibung (in eigenen Worten):**

```
# Schreiben Sie in wenigen Sätzen, was in diesem Block passiert.
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
```

**Aufgabe C – Gezielte Fragen:**

1. `uhr.reset()` steht **vor** `fenster.flip()`. Was würde sich ändern, wenn es **danach** stünde?

   ```
   _______________________________________________________________
   ```

2. `fenster.flip()` wird **zweimal** aufgerufen. Was erscheint nach dem ersten, was nach dem zweiten?

   ```
   _______________________________________________________________
   ```

3. `event.waitKeys(maxWait=MAX_WART, keyList=["f", "j", "escape"])` – was gibt die Funktion zurück, wenn die Versuchsperson **nichts** drückt?

   ```
   _______________________________________________________________
   ```

4. `korrekt = (antwort == korrekte_taste)` – was ist der Datentyp von `korrekt`, und warum braucht man kein `if/else` dafür?

   ```
   _______________________________________________________________
   ```

5. Warum ist `rt_ms = round(rt_sek * 1000)` und nicht einfach `rt_ms = rt_sek`?

   ```
   _______________________________________________________________
   ```

<details>
<summary>Musterlösung</summary>

<p><strong>Aufgabe B:</strong><br>
Die Schleife iteriert über alle Trials. Bei jedem Trial erscheint zuerst ein Fixationskreuz für 0,5 Sekunden. Dann wird der Pfeil-Stimulus gesetzt und angezeigt; gleichzeitig wird die Reaktionsuhr auf null zurückgesetzt. Das Experiment wartet maximal 2 Sekunden auf eine Taste (F, J oder Escape). Nach dem Tastendruck wird geprüft, ob die Antwort korrekt war, und die Reaktionszeit in Millisekunden umgerechnet.</p>

<p><strong>Aufgabe C:</strong></p>
<ol>
<li>Die Uhr würde die Zeit <strong>ab dem flip()</strong> messen statt ab dem Moment kurz davor. Da <code>flip()</code> bis zum nächsten Bildschirm-Refresh wartet (1 Frame ≈ 16 ms bei 60 Hz), würde die gemessene RT systematisch zu kurz sein.</li>
<li>Erstes <code>flip()</code>: Das Fixationskreuz erscheint. Zweites <code>flip()</code>: Der Pfeil-Stimulus erscheint (und die Uhr startet gleichzeitig).</li>
<li><code>None</code> – wenn kein Tastendruck innerhalb von <code>maxWait</code> erfolgt.</li>
<li><code>bool</code> (<code>True</code> oder <code>False</code>). <code>(antwort == korrekte_taste)</code> ist ein Vergleichsausdruck, der direkt einen bool-Wert zurückgibt – ein <code>if/else</code> wäre redundant.</li>
<li>PsychoPy misst Zeit in Sekunden als Dezimalzahl (z. B. 0.4123). Für Psychologie-Daten ist Millisekunden üblich und <code>round()</code> entfernt unnötige Nachkommastellen.</li>
</ol>

</details>

---

## 🔴 Übung 3 – Das Gesamtskript einordnen

> Das folgende Skript ist identisch mit dem vollständigen Flanker-Skript oben –  
> aber alle Abschnitts-Kommentare (`# ─── ...`) sind entfernt.  
> **Aufgabe A:** Teilen Sie das Skript in sinnvolle Abschnitte ein und benennen Sie jeden.  
> **Aufgabe B:** Beantworten Sie die Fragen darunter.  
> **Aufgabe C:** Im Skript steckt ein absichtlicher Fehler. Finden Sie ihn.

```python

from psychopy import visual, core, event
import csv
from pathlib import Path

VP_ID      = "VP01"
VOLLBILD   = False
FENSTER_HG = "black"
MAX_WART   = 2.0
FIXDAUER   = 0.5

TASTEN = {"left": "f", "right": "j"}   # ← neu

TRIALS = [
    ("<<<<<", "left",  "kongruent"),
    (">>>>>", "right", "kongruent"),
    ("<<><<", "right", "inkongruent"),
    (">><>>", "left",  "inkongruent"),
] * 5

Path("logfiles").mkdir(exist_ok=True)
logfile = Path("logfiles") / f"{VP_ID}_flanker.csv"
with open(logfile, mode="w", newline="", encoding="utf-8") as f:
    csv.writer(f).writerow(["trial", "stimulus", "korrekte_taste",
                             "bedingung", "antwort", "korrekt", "rt_ms"])

fenster = visual.Window(size=(1024, 768), fullscr=VOLLBILD,
                        color=FENSTER_HG, units="pix")
uhr = core.Clock()
fixation    = visual.TextStim(win=fenster, text="+", color="white", height=50)
stimulus    = visual.TextStim(win=fenster, text="",  color="white", height=80)
feedback    = visual.TextStim(win=fenster, text="",  color="white", height=40)
instruktion = visual.TextStim(
    win=fenster,
    text="Reagieren Sie auf den MITTLEREN Pfeil.\n"
         "Linker Pfeil → Taste F   |   Rechter Pfeil → Taste J\n\n"
         "Drücken Sie LEERTASTE zum Starten.",
    color="white", height=28
)

instruktion.draw()
fenster.flip()
event.waitKeys(keyList=["space"])

for trial_nr, (stim_text, korrekte_taste, bedingung) in enumerate(TRIALS, start=1):
    fixation.draw()
    fenster.flip()
    core.wait(FIXDAUER)

    stimulus.setText(stim_text)
    stimulus.draw()
    uhr.reset()             # ← vor flip, damit RT ab Stimulus-Erscheinen gemessen wird
    fenster.flip()

    tasten = event.waitKeys(maxWait=MAX_WART, keyList=["f", "j", "escape"])
    rt_sek = uhr.getTime()

    if tasten is None:
        antwort = None
        korrekt = False
        rt_ms   = None
    elif tasten[0] == "escape":
        fenster.close()
        core.quit()
    else:
        antwort = tasten[0]
        korrekt = (antwort == TASTEN[korrekte_taste])   # ← korrigiert
        rt_ms   = round(rt_sek * 1000)

    feedback.setText("✓" if korrekt else "✗")
    feedback.setColor("green" if korrekt else "red")
    feedback.draw()
    fenster.flip()
    core.wait(0.4)

    with open(logfile, mode="a", newline="", encoding="utf-8") as f:
        csv.writer(f).writerow(
            [trial_nr, stim_text, korrekte_taste, bedingung,
             antwort, korrekt, rt_ms]
        )

fenster.close()
core.quit()

```

**Aufgabe A – Abschnitte einteilen:**

Markieren Sie im Skript (oder schreiben Sie Zeilenbereiche auf), wo folgende Abschnitte beginnen und enden:  
Imports / Konfiguration / Trialliste / Logfile-Setup / Fenster & Stimuli / Instruktion / Trial-Schleife / Abschluss

**Aufgabe B – Fragen zum Gesamtdesign:**

1. `TRIALS = [...] * 5` – wie viele Trials hat das Experiment insgesamt, und wie viele davon sind kongruent?

   ```
   _______________________________________________________________
   ```

2. Warum steht `Path("logfiles").mkdir(exist_ok=True)` **vor** dem Öffnen des Fensters?

   ```
   _______________________________________________________________
   ```

3. Was müsste man ändern, um aus diesem Flanker-Skript ein Stroop-Skript zu machen?  
   Listen Sie mindestens drei konkrete Änderungen auf.

   ```
   1. ____________________________________________________________
   2. ____________________________________________________________
   3. ____________________________________________________________
   ```

4. `VOLLBILD = False` steht in der Konfiguration. Was würde sich im Experiment ändern, wenn man `True` setzt? Warum ist das für echte Experimente wichtig?

   ```
   _______________________________________________________________
   ```

**Aufgabe C – Fehler finden:**

Es gibt im Skript einen absichtlichen Fehler, der die Reaktionszeitmessung verfälscht.  
Beschreiben Sie, wo der Fehler ist, was er bewirkt, und wie er zu korrigieren ist.

```
Fehler gefunden bei Zeile / Block: _________________________________
Was passiert dadurch: ______________________________________________
Korrektur: _________________________________________________________
```

<details>
<summary>Musterlösung</summary>

<p><strong>Aufgabe A – Zeilenbereiche (ungefähr):</strong></p>
<table>
<thead><tr><th>Abschnitt</th><th>Inhalt</th></tr></thead>
<tbody>
<tr><td>Imports</td><td><code>from psychopy import ...</code> bis <code>from pathlib import Path</code></td></tr>
<tr><td>Konfiguration</td><td><code>VP_ID = ...</code> bis <code>FIXDAUER = ...</code></td></tr>
<tr><td>Trialliste</td><td><code>TRIALS = [...]</code></td></tr>
<tr><td>Logfile-Setup</td><td><code>Path("logfiles").mkdir(...)</code> bis <code>csv.writer(f).writerow(...)</code></td></tr>
<tr><td>Fenster &amp; Stimuli</td><td><code>fenster = visual.Window(...)</code> bis <code>instruktion = visual.TextStim(...)</code></td></tr>
<tr><td>Instruktion</td><td><code>instruktion.draw()</code> bis <code>event.waitKeys(keyList=["space"])</code></td></tr>
<tr><td>Trial-Schleife</td><td><code>for trial_nr, ...</code> bis letztes <code>csv.writer(f).writerow(...)</code></td></tr>
<tr><td>Abschluss</td><td><code>fenster.close()</code> + <code>core.quit()</code></td></tr>
</tbody>
</table>

<p><strong>Aufgabe B:</strong></p>
<ol>
<li><code>4 Trials × 5 = 20 Trials</code> gesamt. Davon 2 kongruente × 5 = <strong>10 kongruente</strong>, 10 inkongruente.</li>
<li>Das Logfile wird angelegt, bevor das Fenster geöffnet wird – so ist sichergestellt, dass die Datei existiert, falls das Experiment abstürzt oder abgebrochen wird. Dateifehler (z. B. voller Speicher) werden früh erkannt, bevor die Versuchsperson schon Trials absolviert hat.</li>
<li>Mögliche Änderungen für Stroop:
<ul>
<li>Stimulus-Text: Farbwörter statt Pfeil-Strings (<code>"ROT"</code>, <code>"BLAU"</code>, <code>"GRÜN"</code>)</li>
<li><code>stimulus.setColor(farbe)</code> hinzufügen (die Schriftfarbe muss variieren)</li>
<li>Antwort-Tasten: <code>keyList=["r", "b", "g"]</code> statt <code>["f", "j"]</code></li>
<li>Instruktionstext anpassen: „Nennen Sie die Schriftfarbe"</li>
<li><code>korrekte_taste</code> entspricht der Farbe statt der Richtung</li>
</ul>
</li>
<li>Im Vollbildmodus hat PsychoPy exklusiven Zugriff auf den Bildschirm – das Betriebssystem kann keine anderen Fenster einblenden, die das Timing stören. Vollbild vermindert auch Monitor-Refresh-Jitter. Für echte Experimente ist präzises Timing entscheidend.</li>
</ol>

<p><strong>Aufgabe C – Fehler:</strong><br>
Der Fehler liegt in der Reihenfolge von <code>fenster.flip()</code> und <code>uhr.reset()</code>:</p>

<pre><code class="language-python"># Fehlerhaft (im obigen Skript):
stimulus.draw()
fenster.flip()   # Stimulus erscheint → aber Uhr noch nicht gestartet!
uhr.reset()      # Uhr startet erst NACH dem flip → RT zu kurz gemessen

# Korrekt:
stimulus.draw()
uhr.reset()      # Uhr zurücksetzen
fenster.flip()   # Stimulus erscheint → RT-Messung beginnt ab hier
</code></pre>

<p><strong>Effekt:</strong> Die gemessene RT ist um die Zeit verkürzt, die zwischen <code>flip()</code> und <code>uhr.reset()</code> vergeht – das ist kurz, aber systematisch und damit ein stiller Fehler.</p>

</details>
---

## Zusammenfassung: Wie liest man fremden Code?

Vier Schritte, die beim Lesen von Experiment-Skripten immer helfen:

**1. Struktur erfassen** – Wo sind Imports, Konfiguration, Setup, Schleife, Abschluss?  
**2. Konstanten notieren** – Was steuern `VP_ID`, `MAX_WART`, `FIXDAUER`? Was wäre eine sinnlose Änderung?  
**3. Den Datenfluss verfolgen** – Wo wird `uhr` erstellt, zurückgesetzt, ausgelesen?  
**4. Einen Trial mental durchspielen** – Was passiert Schritt für Schritt bei einem einzigen Trial?
