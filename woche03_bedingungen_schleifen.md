# Woche 3: Python Grundlagen III
## Bedingungen und Schleifen im Kontext von Experimenten

> **Voraussetzung:** Woche 2 – Listen, Tupel, Strings

---

## Lernziele

Nach dieser Sitzung können Sie:

- Bedingungen formulieren und auswerten (z. B. korrekte Reaktionen prüfen)
- Vergleichsoperatoren und logische Operatoren gezielt einsetzen
- `for`-Schleifen zur automatisierten Trial-Steuerung nutzen
- Stimuluslisten und verschachtelte Trialstrukturen durchlaufen
- Bedingungen und Schleifen kombinieren, um einfache Experimentlogik zu implementieren
- typische Fehler erkennen und beheben

---

## 0. Orientierung: Warum das für Experimente zentral ist

In Woche 2 haben Sie gelernt, Daten zu *speichern* – in Listen, Tupeln und Strings.  
Diese Woche lernen Sie, mit diesen Daten zu *arbeiten*:

**Bedingungen** beantworten die Frage: *War diese Reaktion korrekt? Ist die RT gültig?*  
**Schleifen** beantworten die Frage: *Wie zeige ich 80 Stimuli, ohne 80-mal denselben Code zu schreiben?*

In jedem echten Experiment – egal ob Stroop, N-Back oder CPT – stecken genau diese zwei Konzepte:

```
Trial 1: Stimulus zeigen → Reaktion erfassen → Reaktion bewerten  ← Bedingung
Trial 2: Stimulus zeigen → Reaktion erfassen → Reaktion bewerten  ← Bedingung
Trial 3: ...                                                       ← Schleife
```

---

## Theorieteil

---

## 1. Bedingungen (`if` / `elif` / `else`)

### 1.1 Grundstruktur

```python
rt = 450   # Reaktionszeit in ms

if rt < 300:
    print("sehr schnell")
```

Zwei Regeln, die immer gelten:

1. Nach `if`, `elif` und `else` kommt immer ein **Doppelpunkt**
2. Der eingerückte Block darunter (4 Leerzeichen) gehört zur Bedingung – das ist **kein Stil, das ist Syntax**

### 1.2 if / elif / else

```python
rt = 350

if rt < 300:
    print("zu schnell – Trial ausschließen")
elif rt < 600:
    print("gültig")
else:
    print("zu langsam – Trial ausschließen")
```

Python prüft die Bedingungen **von oben nach unten** und führt den ersten zutreffenden Block aus.  
`else` ist der Auffangfall – er braucht keine eigene Bedingung.

> ❓ **Vorhersage:** Was gibt der Code für `rt = 299` aus? Für `rt = 600`?

### 1.3 Vergleichsoperatoren

| Operator | Bedeutung | Experimenteller Kontext |
|---|---|---|
| `==` | gleich | Taste entspricht Zielreiz |
| `!=` | ungleich | falsche Antwort gedrückt |
| `>` | größer als | RT überschreitet Cutoff |
| `<` | kleiner als | RT unterschreitet Cutoff |
| `>=` | größer oder gleich | Mindest-Accuracy erreicht |
| `<=` | kleiner oder gleich | maximale RT eingehalten |

> ⚠️ **Häufiger Fehler:** `=` ist Zuweisung, `==` ist Vergleich.
> ```python
> rt = 450     # weist den Wert 450 zu
> rt == 450    # fragt: "Ist rt gleich 450?" → True oder False
> ```

### 1.4 Logische Operatoren: Bedingungen verknüpfen

Oft müssen mehrere Bedingungen gleichzeitig gelten:

| Operator | Bedeutung | Beispiel |
|---|---|---|
| `and` | beide müssen wahr sein | RT gültig **und** Antwort korrekt |
| `or` | mindestens eine muss wahr sein | Taste A **oder** Taste B |
| `not` | kehrt den Wahrheitswert um | **nicht** korrekt |

```python
rt = 450
korrekt = True

if rt < 600 and korrekt:
    print("gültiger Trial, korrekte Reaktion")

if rt < 200 or rt > 1000:
    print("Trial ausschließen – RT außerhalb des Fensters")
```

> 💡 `korrekt` ist bereits ein `bool` – Sie müssen nicht `korrekt == True` schreiben.  
> `if korrekt:` reicht völlig.

---

## 2. `for`-Schleifen: Wiederholungen automatisieren

### 2.1 Grundprinzip

Eine `for`-Schleife nimmt jedes Element einer Sequenz und führt den eingerückten Block einmal aus:

```python
stimuli = ["Haus", "Baum", "Auto"]

for stimulus in stimuli:
    print("Aktueller Stimulus:", stimulus)
```

```
Aktueller Stimulus: Haus
Aktueller Stimulus: Baum
Aktueller Stimulus: Auto
```

Die Variable nach `for` (hier `stimulus`) ist frei wählbar – sie nimmt nacheinander jeden Wert an.

### 2.2 `range()` – Schleifen mit Zähler

`range(n)` erzeugt eine Folge von `0` bis `n-1`:

```python
for trial in range(5):
    print("Trial:", trial)
# → Trial: 0, Trial: 1, Trial: 2, Trial: 3, Trial: 4
```

`range(start, stop)` beginnt bei `start` statt bei 0:

```python
for trial in range(1, 6):
    print("Trial:", trial)
# → Trial: 1, Trial: 2, Trial: 3, Trial: 4, Trial: 5
```

> 💡 Genau wie beim Slicing: `stop` ist exklusiv.

### 2.3 Index und Wert gleichzeitig: `enumerate()`

Oft brauchen Sie sowohl die Nummer als auch den Wert des Trials:

```python
stimuli = ["Haus", "Baum", "Auto"]

for i, stimulus in enumerate(stimuli):
    print(f"Trial {i + 1}: {stimulus}")
```

```
Trial 1: Haus
Trial 2: Baum
Trial 3: Auto
```

> 💡 **f-Strings:** `f"..."` erlaubt es, Variablen direkt in einen String einzusetzen.  
> Alles in `{}` wird ausgewertet: `f"RT: {rt} ms"` → `"RT: 450 ms"`

### 2.4 Verschachtelte Listen durchlaufen

Aus Woche 2 kennen Sie bereits diese Trialstruktur:

```python
trials = [
    ["Haus",  512, True],
    ["Baum",  678, False],
    ["Auto",  445, True],
]

for trial in trials:
    stimulus     = trial[0]
    reaktionszeit = trial[1]
    korrekt      = trial[2]
    print(f"{stimulus}: {reaktionszeit} ms, korrekt: {korrekt}")
```

Oder kürzer mit direktem Entpacken:

```python
for stimulus, reaktionszeit, korrekt in trials:
    print(f"{stimulus}: {reaktionszeit} ms, korrekt: {korrekt}")
```

---

## 3. Bedingungen + Schleifen kombinieren

Das ist der Kern jeder Experimentlogik.

### 3.1 Reaktionen automatisch auswerten

```python
reaktionszeiten = [512, 245, 789, 380, 1050, 298]

for rt in reaktionszeiten:
    if rt < 300:
        print(f"{rt} ms → zu schnell, ausschließen")
    elif rt > 1000:
        print(f"{rt} ms → zu langsam, ausschließen")
    else:
        print(f"{rt} ms → gültig")
```

### 3.2 Treffer zählen

Eine Variable außerhalb der Schleife zählt mit:

```python
trials = [
    ["Haus", 512, True],
    ["Baum", 678, False],
    ["Auto", 445, True],
    ["Hund", 390, True],
]

korrekte_trials = 0

for stimulus, rt, korrekt in trials:
    if korrekt:
        korrekte_trials = korrekte_trials + 1

print(f"Korrekte Trials: {korrekte_trials} von {len(trials)}")
```

> ❓ **Vorhersage:** Was gibt der Code aus?

### 3.3 Accuracy berechnen

```python
accuracy = korrekte_trials / len(trials)
print(f"Accuracy: {accuracy:.0%}")   # → "75%"
```

> 💡 `:.0%` in f-Strings formatiert eine Dezimalzahl als Prozentwert ohne Nachkommastellen.

---

## Übungsteil

> - 🟢 **Basis** – einzelne Konzepte direkt anwenden
> - 🟡 **Aufbau** – Konzepte kombinieren
> - 🔴 **Vertiefung** – eigenständig denken und übertragen

---

### 🟢 Aufgabe 1 – Reaktionsbewertung

Schreiben Sie eine Bedingung, die für `rt = 720` eine Rückmeldung ausgibt:

- `rt < 200` → `"zu schnell – ausschließen"`
- `rt < 600` → `"gültig"`
- sonst → `"zu langsam – ausschließen"`

Testen Sie anschließend mit `rt = 150`, `rt = 450` und `rt = 800`.  
Was passiert genau an der Grenze `rt = 200` und `rt = 600`?

```python
rt = 720
# Ihr Code hier
```

---

### 🟢 Aufgabe 2 – Stimulusliste durchlaufen

Erstellen Sie eine Liste mit vier Stimulusnamen und geben Sie jeden mit seiner Trial-Nummer aus.  
Nutzen Sie `enumerate()` so dass die Zählung bei **1** beginnt (nicht 0).

```python
# Ihr Code hier
```

Erwartete Ausgabe (mit Ihren eigenen Stimuli):
```
Trial 1: Haus
Trial 2: Baum
Trial 3: Auto
Trial 4: Hund
```

---

### 🟢 Aufgabe 3 – Vergleich vs. Zuweisung

Erklären Sie den Unterschied zwischen diesen beiden Zeilen.  
Was gibt jeweils `print(x)` danach aus?

```python
x = 10
x = 5     # (a)
x == 5    # (b)
```

Und was ist hier der Fehler?

```python
antwort = "links"
if antwort = "links":   # (c)
    print("korrekt")
```

```python
# Ihr Code und Erklärung hier
```

---

### 🟡 Aufgabe 4 – Trials filtern

Gegeben ist eine Liste von Reaktionszeiten aus einem echten Experiment.  
Iterieren Sie über die Liste und geben Sie für jede RT aus, ob sie gültig ist.  
Zählen Sie am Ende, wie viele Trials gültig waren.

Gültigkeitskriterium: `200 <= rt <= 1000`

```python
reaktionszeiten = [512, 189, 834, 1200, 445, 301, 78, 956, 1050, 523]

# Ihr Code hier
```

> 💡 Tipp: Sie können `200 <= rt <= 1000` direkt so schreiben – Python unterstützt verkettete Vergleiche.

---

### 🟡 Aufgabe 5 – Accuracy berechnen

Gegeben ist eine Trialstruktur mit Stimulus, RT und Korrektheit.

1. Iterieren Sie über alle Trials.
2. Zählen Sie korrekte und inkorrekte Trials getrennt.
3. Berechnen Sie die Accuracy (Anteil korrekter Trials).
4. Geben Sie aus: Anzahl korrekt, Anzahl inkorrekt, Accuracy als Prozent.

```python
trials = [
    ["Haus",  512,  True],
    ["Baum",  678,  False],
    ["Auto",  445,  True],
    ["Hund",  390,  True],
    ["Stuhl", 820,  False],
    ["Tisch", 334,  True],
]

# Ihr Code hier
```

---

### 🟡 Aufgabe 6 – Vorhersagen treffen

Notieren Sie erst schriftlich, was ausgegeben wird – dann ausführen und vergleichen.

```python
# Block A
for i in range(3):
    print(i * 2)
```

```python
# Block B
werte = [True, False, True, True]
zaehler = 0
for w in werte:
    if w:
        zaehler = zaehler + 1
print(zaehler)
```

```python
# Block C
stimuli = ["A", "B", "C", "D"]
for i, s in enumerate(stimuli):
    if i % 2 == 0:
        print(s)
```

> 💡 `%` ist der Modulo-Operator: `i % 2 == 0` ist `True`, wenn `i` gerade ist.

```python
# Ihre Vorhersagen und Überprüfung hier
```

---

### 🔴 Aufgabe 7 – Mini-Experiment: Stroop-Auswertung

Im Stroop-Experiment gibt es kongruente Trials (Wort und Farbe passen zusammen)  
und inkongruente Trials (Wort und Farbe widersprechen sich).

Gegeben ist eine Trialstruktur: `[stimulus, bedingung, rt, korrekt]`

```python
stroop_trials = [
    ["ROT",   "kongruent",   412, True],
    ["BLAU",  "inkongruent", 587, True],
    ["GRÜN",  "kongruent",   389, True],
    ["ROT",   "inkongruent", 634, False],
    ["BLAU",  "kongruent",   401, True],
    ["GRÜN",  "inkongruent", 712, True],
]
```

Berechnen Sie:

1. Die mittlere RT für kongruente Trials (Summe / Anzahl)
2. Die mittlere RT für inkongruente Trials
3. Den Stroop-Effekt (mittlere RT inkongruent − mittlere RT kongruent)
4. Die Gesamtaccuracy

```python
# Ihr Code hier
```

> 💡 Tipp: Verwenden Sie separate Zähl- und Summenvariablen für jede Bedingung,  
> die Sie vor der Schleife auf `0` setzen.

---

### 🔴 Aufgabe 8 – Fehler finden und erklären

Jeder Block enthält einen Fehler. Beschreiben Sie zuerst das Problem, dann korrigieren Sie.

```python
# (a) – SyntaxError
rt = 500
if rt > 400
    print("langsam")
```

```python
# (b) – Logikfehler: Welche RTs werden als "gültig" markiert?
rt = 250
if rt < 200:
    print("gültig")
else:
    print("ungültig")
```

```python
# (c) – Wird nie etwas ausgegeben. Warum?
stimuli = ["Haus", "Baum", "Auto"]
for i in range(len(stimuli)):
    if stimuli[i] == "haus":   # Groß-/Kleinschreibung
        print("Treffer")
```

```python
# (d) – Was ist das Problem mit dieser Zählung?
treffer = 0
werte = [True, False, True]
for w in werte:
    treffer = 1   # soll hochzählen
print(treffer)
```

```python
# Ihre Analyse und Korrekturen hier
```

---

## Typische Fehler – Übersicht

| Fehler | Code | Problem |
|---|---|---|
| Fehlender Doppelpunkt | `if rt > 500` | `SyntaxError` – Doppelpunkt fehlt |
| Zuweisung statt Vergleich | `if x = 5:` | `SyntaxError` – `==` statt `=` |
| Falsche Einrückung | Block nicht eingerückt | `IndentationError` oder falsches Verhalten |
| `range()` missverstehen | `range(3)` → 0, 1, 2 | stop ist exklusiv, Start ist 0 |
| Zähler nicht initialisieren | `zaehler + 1` ohne `zaehler = 0` | `NameError` |
| Logikfehler bei Grenzen | `< 200` statt `<= 200` | falsches Ergebnis, kein Fehler |
| Groß-/Kleinschreibung | `"haus" == "Haus"` | `False` – Python unterscheidet Groß/Klein |

---

## Lösungen

<details>
<summary>Aufgabe 1 – Reaktionsbewertung</summary>


```python
rt = 720

if rt < 200:
    print("zu schnell – ausschließen")
elif rt < 600:
    print("gültig")
else:
    print("zu langsam – ausschließen")
```

An der Grenze `rt = 200`: Die erste Bedingung `rt < 200` ist `False` (200 ist nicht kleiner als 200),  
also greift `elif rt < 600` → Ausgabe: `"gültig"`.  
An der Grenze `rt = 600`: Beide ersten Bedingungen sind `False`, also `else` → `"zu langsam"`.
</details>

<details>
<summary>Aufgabe 2 – Stimulusliste</summary>


```python
stimuli = ["Haus", "Baum", "Auto", "Hund"]

for i, stimulus in enumerate(stimuli):
    print(f"Trial {i + 1}: {stimulus}")
```

</details>

<details>
<summary>Aufgabe 3 – Vergleich vs. Zuweisung</summary>


```python
x = 10
x = 5     # (a) x hat jetzt den Wert 5 – Zuweisung
x == 5    # (b) fragt "Ist x gleich 5?" → True, aber weist nichts zu – x bleibt 5

# (c) if antwort = "links" → SyntaxError, weil = Zuweisung ist
# Korrekt:
antwort = "links"
if antwort == "links":
    print("korrekt")
```
</details>

<details>
<summary>Aufgabe 4 – Trials filtern</summary>


```python
reaktionszeiten = [512, 189, 834, 1200, 445, 301, 78, 956, 1050, 523]

gueltige_trials = 0

for rt in reaktionszeiten:
    if 200 <= rt <= 1000:
        print(f"{rt} ms → gültig")
        gueltige_trials = gueltige_trials + 1
    else:
        print(f"{rt} ms → ausgeschlossen")

print(f"\nGültige Trials: {gueltige_trials} von {len(reaktionszeiten)}")
```

</details>

<details>
<summary>Aufgabe 5 – Accuracy</summary>


```python
trials = [
    ["Haus",  512,  True],
    ["Baum",  678,  False],
    ["Auto",  445,  True],
    ["Hund",  390,  True],
    ["Stuhl", 820,  False],
    ["Tisch", 334,  True],
]

korrekt_n   = 0
inkorrekt_n = 0

for stimulus, rt, korrekt in trials:
    if korrekt:
        korrekt_n = korrekt_n + 1
    else:
        inkorrekt_n = inkorrekt_n + 1

accuracy = korrekt_n / len(trials)
print(f"Korrekt:   {korrekt_n}")
print(f"Inkorrekt: {inkorrekt_n}")
print(f"Accuracy:  {accuracy:.0%}")
```

</details>

<details>
<summary>Aufgabe 6 – Vorhersagen</summary>


```python
# Block A: 0, 2, 4
# Block B: 3  (drei True-Werte)
# Block C: "A", "C"  (Index 0 und 2 sind gerade)
```
</details>

<details>
<summary>Aufgabe 7 – Stroop</summary>

```python
stroop_trials = [
    ["ROT",   "kongruent",   412, True],
    ["BLAU",  "inkongruent", 587, True],
    ["GRÜN",  "kongruent",   389, True],
    ["ROT",   "inkongruent", 634, False],
    ["BLAU",  "kongruent",   401, True],
    ["GRÜN",  "inkongruent", 712, True],
]

rt_kongr_sum   = 0
rt_inkongr_sum = 0
n_kongr        = 0
n_inkongr      = 0
korrekt_n      = 0

for stimulus, bedingung, rt, korrekt in stroop_trials:
    if bedingung == "kongruent":
        rt_kongr_sum = rt_kongr_sum + rt
        n_kongr      = n_kongr + 1
    else:
        rt_inkongr_sum = rt_inkongr_sum + rt
        n_inkongr      = n_inkongr + 1
    if korrekt:
        korrekt_n = korrekt_n + 1

rt_kongr_mean   = rt_kongr_sum / n_kongr
rt_inkongr_mean = rt_inkongr_sum / n_inkongr
stroop_effekt   = rt_inkongr_mean - rt_kongr_mean
accuracy        = korrekt_n / len(stroop_trials)

print(f"Mittlere RT kongruent:   {rt_kongr_mean:.0f} ms")
print(f"Mittlere RT inkongruent: {rt_inkongr_mean:.0f} ms")
print(f"Stroop-Effekt:           {stroop_effekt:.0f} ms")
print(f"Accuracy:                {accuracy:.0%}")
```

</details>

<details>
<summary>Aufgabe 8 – Fehler finden</summary>


```python
# (a) Fehlender Doppelpunkt → SyntaxError
if rt > 400:   # Doppelpunkt ergänzt
    print("langsam")

# (b) Logikfehler: rt = 250 ist nicht < 200, also "ungültig"
# Gemeint war wahrscheinlich:
if rt > 200:
    print("gültig")
else:
    print("ungültig")

# (c) "haus" != "Haus" – Python unterscheidet Groß-/Kleinschreibung
if stimuli[i] == "Haus":   # Großschreibung korrigiert
    print("Treffer")

# (d) treffer wird auf 1 gesetzt statt hochgezählt
for w in werte:
    treffer = treffer + 1  # oder: treffer += 1
```

</details>

---

## Zusammenfassung

| Konzept | Zweck | Experimenteller Einsatz |
|---|---|---|
| `if / elif / else` | Entscheidungen treffen | Reaktionen bewerten, Trials filtern |
| Vergleichsoperatoren | Werte vergleichen | RT-Grenzen, Korrektheit prüfen |
| `and` / `or` / `not` | Bedingungen verknüpfen | RT gültig **und** Antwort korrekt |
| `for`-Schleife | Wiederholungen | Stimulusliste durchlaufen |
| `range()` | Zähler erzeugen | Trial-Nummern |
| `enumerate()` | Index + Wert | Trial-Nummer und Stimulus gleichzeitig |
| Kombination | Experimentlogik | Auswerten, Zählen, Filtern |

**Drei Dinge, die schiefgehen können:**

1. `=` statt `==` in Bedingungen → `SyntaxError`
2. Zählvariable vergessen vor der Schleife zu initialisieren → `NameError`
3. Logikfehler an Grenzen (`<` vs. `<=`) → kein Fehler, falsche Ergebnisse

---

## Ausblick: Woche 4

Nächste Woche lernen Sie **Funktionen** – damit wird der Code aus dieser Woche wiederverwendbar:

```python
# Statt diesen Code jedes Mal neu zu schreiben ...
korrekt_n = 0
for stimulus, rt, korrekt in trials:
    if korrekt:
        korrekt_n = korrekt_n + 1

# ... schreiben Sie einmal eine Funktion:
def berechne_accuracy(trials):
    ...
```

Funktionen sind das Werkzeug, mit dem aus einzelnen Code-Schnipseln  
ein strukturiertes, lesbares Experiment wird.
