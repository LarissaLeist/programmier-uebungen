# Woche 2: Python Grundlagen II
## Listen, Tupel und Strings

> **Voraussetzung:** Woche 1 – Variablen und Datentypen

---

## Lernziele

Nach dieser Sitzung können Sie:

- Listen erstellen, auslesen und verändern
- Tupel von Listen unterscheiden und sinnvoll einsetzen
- Strings als Zeichensequenzen verstehen und zerlegen
- Indexing und Slicing sicher anwenden
- typische Fehler erkennen, lesen und beheben
- erste einfache Datenstrukturen für psychologische Experimente modellieren

---

## 0. Orientierung: So arbeiten Sie diese Woche

Diese Woche dreht sich alles um **Sequenzen** – Datenstrukturen, die mehrere Werte in einer bestimmten Reihenfolge speichern.

**Aktiv mitmachen bedeutet:**

- Jeden Codeblock selbst ausführen
- Werte, Indizes und Slices abändern und beobachten, was passiert
- Vor der Ausführung eine **Vorhersage** notieren
- Fehlermeldungen aufmerksam lesen – sie sind Hinweise, keine Strafe

> 💡 **Tipp:** Öffnen Sie ein Spyder oder Google Colab parallel zu diesem Dokument und tippen Sie den Code ab (nicht kopieren!). Das Abtippen hilft beim Lernen.

---

## Theorieteil

---

## 1. Listen

Listen sind **veränderbare** Sammlungen von Werten in einer festen Reihenfolge.  
Sie sind eines der wichtigsten Werkzeuge in Python – besonders für Experimente:

| Anwendung | Beispiel |
|---|---|
| Stimulusnamen speichern | `["Haus", "Baum", "Auto"]` |
| Reaktionszeiten sammeln | `[512, 489, 601, 534]` |
| Trial-Reihenfolge festlegen | `[1, 3, 2, 4, 1, 3]` |
| Versuchspersonen-IDs verwalten | `["VP01", "VP02", "VP03"]` |

### 1.1 Eine Liste erstellen

```python
farben = ["rot", "grün", "blau"]
print(farben)
```

Listen können **verschiedene Datentypen** enthalten (in der Praxis aber besser vermeiden):

```python
mixed = ["Anna", 3, 4.5, True]
```

### 1.2 Indexing – einzelne Elemente auslesen

Python zählt ab **0**. Das ist anfangs ungewohnt, aber schnell zur Gewohnheit.

```
Index:     0       1       2
        ["rot", "grün", "blau"]
Index:    -3      -2      -1    ← negative Indizes zählen von hinten
```

```python
farben = ["rot", "grün", "blau"]

print(farben[0])    # → "rot"
print(farben[2])    # → "blau"
print(farben[-1])   # → "blau"  (letztes Element)
print(farben[-2])   # → "grün"  (vorletztes Element)
```

> ❓ **Vorhersage:** Was gibt `farben[-3]` aus?

### 1.3 Elemente verändern

```python
farben[1] = "gelb"
print(farben)   # → ["rot", "gelb", "blau"]
```

### 1.4 Elemente hinzufügen

```python
farben.append("lila")
print(farben)   # → ["rot", "gelb", "blau", "lila"]
```

### 1.5 Länge einer Liste

```python
print(len(farben))   # → 4
```

---

## 2. Tupel

Tupel sehen aus wie Listen – aber sie sind **unveränderlich**.

```python
koordinaten = (10, 20)
rgb_rot = (255, 0, 0)
```

**Wann Tupel verwenden?**

- Werte, die sich nicht ändern sollen (z. B. Bildschirmposition, Farben)
- Als Schutz vor versehentlichen Änderungen
- Tupel sind minimal schneller als Listen

### 2.1 Zugriff – genau wie bei Listen

```python
print(koordinaten[0])   # → 10
print(koordinaten[1])   # → 20
```

### 2.2 Tupel können nicht verändert werden

```python
koordinaten[0] = 99   # → TypeError: 'tuple' object does not support item assignment
```

Das ist kein Bug – das ist die Absicht. Tupel schützen Ihre Daten.

### 2.3 Liste oder Tupel – woran erkenne ich den Unterschied?

```python
meine_liste = [1, 2, 3]    # eckige Klammern
mein_tupel  = (1, 2, 3)    # runde Klammern

print(type(meine_liste))   # → <type 'list'>
print(type(mein_tupel))    # → <type 'tuple'>
```

---

## 3. Strings als Sequenzen

Strings (`str`) kennen Sie schon. Neu ist die Perspektive:  
Ein String ist eine **Sequenz von Zeichen** – und verhält sich in vielem wie eine Liste.

```python
wort = "Python"

print(wort[0])    # → "P"
print(wort[-1])   # → "n"
print(len(wort))  # → 6
```

### 3.1 Slicing – Teilbereiche auswählen

Slicing hat die Form `[start:stop]` – **start** ist inklusive, **stop** ist exklusiv.

```
Index:  0   1   2   3   4   5   6   7   8   9   10
       "P   s   y   c   h   o   l   o   g   i   e"
```

```python
wort = "Psychologie"

print(wort[0:5])    # → "Psych"   (Index 0 bis 4)
print(wort[:4])     # → "Psyc"    (Anfang bis Index 3)
print(wort[4:])     # → "ologie"  (ab Index 4 bis Ende)
print(wort[-5:])    # → "logie"   (letzte 5 Zeichen)
```

> 💡 **Merkhilfe:** `wort[0:5]` – stellen Sie sich vor, der Cursor steht *vor* dem Zeichen mit dem Index. `[0:5]` schneidet von Lücke 0 bis Lücke 5.

### 3.2 Strings sind unveränderlich

```python
wort = "Python"
wort[0] = "J"   # → TypeError: 'str' object does not support item assignment
```

Genau wie Tupel. Um einen String zu ändern, erstellen Sie einen neuen:

```python
wort = "J" + wort[1:]
print(wort)   # → "Jython"
```

---

## 4. Anwendung: Datenstrukturen für Experimente

In der Kognitionspsychologie und Neurowissenschaft begegnen Ihnen Listen ständig.

### Stimulusliste

```python
stimuli = ["Haus", "Baum", "Auto", "Hund"]

print(stimuli[0])    # erster Stimulus
print(stimuli[-1])   # letzter Stimulus
print(len(stimuli))  # Anzahl der Stimuli
```

### Verschachtelte Listen (Listen von Listen)

Jeder Trial kann mehrere Informationen enthalten:

```python
trials = [
    ["Haus",  500],
    ["Baum",  700],
    ["Auto",  450],
]

# Auf einzelne Trials zugreifen:
print(trials[0])        # → ["Haus", 500]
print(trials[0][0])     # → "Haus"
print(trials[0][1])     # → 500
print(trials[2][1])     # → 450  (RT des dritten Trials)
```

---

## Übungsteil

> Die Aufgaben sind in drei Stufen gegliedert:
> - 🟢 **Basis** – Kernkonzepte direkt anwenden
> - 🟡 **Aufbau** – Konzepte kombinieren und übertragen
> - 🔴 **Vertiefung** – experimentieren und eigenständig denken

---

### 🟢 Aufgabe 1a – Meine erste Liste

Erstellen Sie eine Liste `lieblingsgerichte` mit drei Einträgen.  
Geben Sie das **erste** und das **letzte** Element aus – das letzte mit einem negativen Index.

```python
# Ihr Code hier
```

---

### 🟢 Aufgabe 1b – Logfile einer kognitiven Aufgabe

In einem Experiment werden pro Trial folgende Informationen gespeichert:  
Versuchspersonen-ID, Stimulus, Reaktionszeit (in ms), Antwort (korrekt/nicht korrekt).

Erstellen Sie eine Liste `trial`, die einen einzigen Trial repräsentiert.  
Geben Sie das erste und das letzte Element aus.

> 💡 *Überlegen Sie: Welche Datentypen passen zu welcher Information?*

```python
# Ihr Code hier
# Beispiel-Struktur: ["VP01", "Haus", 512, True]
```

---

### 🟢 Aufgabe 2 – Listen vs. Tupel

Gegeben sind diese zwei Variablen:

```python
a = [1, 2, 3]
b = (1, 2, 3)
```

Beantworten Sie **ohne Code** zuerst, dann überprüfen Sie:

1. Welche Variable ist eine Liste, welche ein Tupel?
2. Was gibt `type(a)` und `type(b)` aus?
3. Welche der folgenden Zeilen erzeugt einen Fehler, und warum?

```python
a[0] = 9   # (i)
b[0] = 9   # (ii)
```

```python
# Ihr Code zur Überprüfung hier
```

---

### 🟢 Aufgabe 3 – Tupel erstellen und testen

Erstellen Sie ein Tupel `position` mit den Werten `(960, 540)` – einer Bildschirmposition in Pixeln.  
Versuchen Sie anschließend, das erste Element auf `0` zu ändern.  
Lesen Sie die Fehlermeldung genau: Welche Information steckt darin?

```python
# Ihr Code hier
```

---

### 🟡 Aufgabe 4 – Versuchspersonen-ID zerlegen

In vielen Experimenten werden Dateinamen nach einem Schema kodiert.  
Gegeben:

```python
vp_id = "223_CPT_16-04-26"
```

Das Schema lautet: `[VP-Nummer]_[Aufgabe]_[Datum als TT-MM-JJ]`

Extrahieren Sie folgende Informationen mit Slicing und speichern Sie sie in **sinnvoll benannten Variablen**:

1. Die VP-Nummer (erste 3 Zeichen)
2. Den Aufgaben-Code (Zeichen 4–6)
3. Das Datum (letzte 8 Zeichen)
4. Nur den Tag (aus dem Datum, erste 2 Zeichen des Datums)

```python
# Ihr Code hier

vp_nummer  = ...
aufgabe    = ...
datum      = ...
tag        = ...

print(vp_nummer)
print(aufgabe)
print(datum)
print(tag)
```

> 💡 *Tipp: Sie können Slices auch hintereinander anwenden: `datum[:2]`*

---

### 🟡 Aufgabe 5 – Stimulusliste aufbauen

Führen Sie die folgenden Schritte **nacheinander** aus – überprüfen Sie nach jedem Schritt mit `print()`:

1. Erstellen Sie eine Liste `stimuli` mit drei Wörtern Ihrer Wahl.
2. Fügen Sie einen vierten Stimulus mit `.append()` hinzu.
3. Ersetzen Sie den zweiten Stimulus durch `"Tisch"`.
4. Geben Sie die Länge der Liste aus.
5. Geben Sie den zweiten Stimulus aus.

```python
# Ihr Code hier
```

---

### 🟡 Aufgabe 6 – Vorhersagen treffen

Notieren Sie zuerst **schriftlich**, was jede Zeile ausgeben wird.  
Führen Sie den Code danach aus und vergleichen Sie.

```python
# Block A: Listen
liste = [10, 20, 30, 40, 50]
print(liste[1])
print(liste[-1])
print(liste[1:3])
print(liste[:2])
print(liste[3:])
```

```python
# Block B: Strings
s = "Psychologie"
print(s[2:6])
print(s[:3])
print(s[5:])
print(s[-5:])
print(len(s))
```

```python
# Ihre Vorhersagen und der Code zum Überprüfen hier
```

---

### 🔴 Aufgabe 7 – Verschachtelte Trialstruktur

Erstellen Sie eine Liste `experiment`, die **drei Trials** enthält.  
Jeder Trial ist selbst eine Liste mit: `[stimulus, reaktionszeit, korrekt]`

Verwenden Sie realistische Werte (Reaktionszeiten zwischen 400 und 800 ms).

Beantworten Sie danach:

1. Wie greifen Sie auf die Reaktionszeit des zweiten Trials zu?
2. Wie greifen Sie auf den Stimulus des dritten Trials zu?
3. Wie viele Trials enthält das Experiment? (Nutzen Sie `len()`.)

```python
# Ihr Code hier
```

---

### 🔴 Aufgabe 8 – Fehler finden und erklären

Jeder der folgenden Codeblöcke enthält **einen Fehler**.  
Beschreiben Sie zuerst, was falsch ist – führen Sie den Code dann aus, um es zu überprüfen.

```python
# (a)
farben = ["rot", "grün", "blau"]
print(farben[3])
```

```python
# (b)
t = (10, 20, 30)
t[0] = 99
```

```python
# (c)
liste = (1, 2, 3]
```

```python
# (d)
wort = "Python"
print(wort[0:2])
# Behauptung: Das gibt "Pyt" aus. Stimmt das?
```

```python
# Ihre Analyse und korrigierter Code hier
```

---

## Typische Fehler – Übersicht

| Fehler | Code | Fehlermeldung | Ursache |
|---|---|---|---|
| Index außerhalb | `liste[5]` bei len=2 | `IndexError` | Index existiert nicht |
| Tupel ändern | `t[0] = 9` | `TypeError` | Tupel sind unveränderlich |
| String ändern | `s[0] = "X"` | `TypeError` | Strings sind unveränderlich |
| Falsche Klammern | `(1, 2, 3]` | `SyntaxError` | Öffnende und schließende Klammer müssen passen |
| Slicing falsch verstehen | `wort[0:2]` | kein Fehler, aber falsches Ergebnis | stop-Index ist exklusiv |

---

## Lösungen

<details>
<summary>Aufgabe 1a – Meine erste Liste</summary>

```python

lieblingsgerichte = ["Pizza", "Sushi", "Pasta"]
print(lieblingsgerichte[0])    # → "Pizza"
print(lieblingsgerichte[-1])   # → "Pasta"

```
</details>


<details>
<summary>Aufgabe 1b – Logfile</summary>

```python
trial = ["VP01", "Haus", 512, True]
print(trial[0])    # → "VP01"
print(trial[-1])   # → True
```
Datentypen: `str` für ID und Stimulus, `int` für Reaktionszeit, `bool` für Korrektheit.

</details>

<details>
<summary>Aufgabe 2 – Listen vs. Tupel</summary>

```python
a = [1, 2, 3]
b = (1, 2, 3)

print(type(a))   # → <class 'list'>
print(type(b))   # → <class 'tuple'>

a[0] = 9   # funktioniert – Liste ist veränderlich
b[0] = 9   # → TypeError – Tupel ist unveränderlich
```

</details>

<details>
<summary>Aufgabe 3 – Tupel</summary>

```python
position = (960, 540)
position[0] = 0   # → TypeError: 'tuple' object does not support item assignment
```
Die Fehlermeldung sagt genau, was nicht geht: Tupel unterstützen keine Zuweisung per Index.

</details>

<details>
<summary>Aufgabe 4 – VP-ID zerlegen</summary>

```python
vp_id = "223_CPT_16-04-26"

vp_nummer = vp_id[:3]     # → "223"
aufgabe   = vp_id[4:7]    # → "CPT"
datum     = vp_id[-8:]    # → "16-04-26"
tag       = datum[:2]     # → "16"

print(vp_nummer)
print(aufgabe)
print(datum)
print(tag)
```

</details>

<details>
<summary>Aufgabe 5 – Stimulusliste aufbauen</summary>

```python
stimuli = ["Haus", "Baum", "Auto"]
stimuli.append("Hund")
stimuli[1] = "Tisch"
print(len(stimuli))    # → 4
print(stimuli[1])      # → "Tisch"
```

</details>

<details>
<summary>Aufgabe 6 – Vorhersagen</summary>

```python
# Block A:
# liste[1]   → 20
# liste[-1]  → 50
# liste[1:3] → [20, 30]
# liste[:2]  → [10, 20]
# liste[3:]  → [40, 50]

# Block B:
# s[2:6]  → "ychol"  (Index 2,3,4,5)
# s[:3]   → "Psy"
# s[5:]   → "logie"
# s[-5:]  → "logie"
# len(s)  → 11
```

</details>

<details>
<summary>Aufgabe 7 – Trialstruktur</summary>

```python
experiment = [
    ["Haus", 512, True],
    ["Baum", 678, False],
    ["Auto", 445, True],
]

print(experiment[1][1])   # → 678  (RT des 2. Trials)
print(experiment[2][0])   # → "Auto" (Stimulus des 3. Trials)
print(len(experiment))    # → 3
```
</details>

<details>
<summary>Aufgabe 8 – Fehler finden</summary>

```python
# (a) IndexError: Liste hat Indizes 0–2, Index 3 existiert nicht
farben = ["rot", "grün", "blau"]
print(farben[2])   # korrigiert: letzter gültiger Index ist 2

# (b) TypeError: Tupel unveränderlich
t = [10, 20, 30]   # korrigiert: Liste statt Tupel

# (c) SyntaxError: öffnende runde Klammer, schließende eckige
liste = (1, 2, 3)   # oder: [1, 2, 3]

# (d) wort[0:2] gibt "Py" aus, nicht "Pyt"
# stop-Index 2 ist exklusiv – Zeichen 0 und 1, also "P" und "y"
wort = "Python"
print(wort[0:3])   # → "Pyt"
```

</details>

---

## Methodenreferenz: Listen und Strings

> 📌 **Zum Nachschlagen** – kein Lernstoff für diese Sitzung.  
> Sie müssen das nicht auswendig kennen. Schauen Sie hier nach, wenn Sie in einer Aufgabe oder im Projekt etwas brauchen.  
> Methoden werden in Woche 4 konzeptionell erklärt (was ist überhaupt eine Methode?).

### Listen-Methoden

```python
liste = [3, 1, 4, 1, 2]
```

| Methode | Beispiel | Ergebnis / Effekt |
|---|---|---|
| `append(x)` | `liste.append(9)` | fügt `9` am Ende hinzu |
| `extend(iterable)` | `liste.extend([7, 8])` | fügt mehrere Elemente hinzu |
| `insert(i, x)` | `liste.insert(0, 99)` | fügt `99` an Position 0 ein |
| `remove(x)` | `liste.remove(1)` | entfernt erstes Vorkommen von `1` |
| `pop()` | `liste.pop()` | entfernt letztes Element und gibt es zurück |
| `sort()` | `liste.sort()` | sortiert die Liste aufsteigend (in-place) |
| `reverse()` | `liste.reverse()` | dreht die Reihenfolge um (in-place) |
| `index(x)` | `liste.index(4)` | gibt die Position von `4` zurück |
| `count(x)` | `liste.count(1)` | gibt an, wie oft `1` vorkommt |

> ⚠️ **In-place** bedeutet: `sort()` und `reverse()` verändern die Liste direkt und geben `None` zurück.  
> `liste = liste.sort()` ist ein häufiger Fehler – danach ist `liste` gleich `None`.

```python
# Beispiel: Reaktionszeiten sortieren
reaktionszeiten = [512, 389, 678, 445]
reaktionszeiten.sort()
print(reaktionszeiten)   # → [389, 445, 512, 678]
```

### String-Methoden

```python
text = "  Hallo Welt  "
```

| Methode | Beispiel | Ergebnis |
|---|---|---|
| `lower()` | `"Haus".lower()` | `"haus"` |
| `upper()` | `"Haus".upper()` | `"HAUS"` |
| `strip()` | `text.strip()` | `"Hallo Welt"` (Leerzeichen entfernt) |
| `replace(a, b)` | `"Haus".replace("H", "M")` | `"Maus"` |
| `split()` | `"a,b,c".split(",")` | `["a", "b", "c"]` |
| `find(x)` | `"Python".find("t")` | `2` (Position von "t") |
| `startswith(x)` | `"VP01".startswith("VP")` | `True` |
| `endswith(x)` | `"data.csv".endswith(".csv")` | `True` |

> ⚠️ **Strings sind unveränderlich.** String-Methoden verändern den Original-String nicht,  
> sondern geben immer einen neuen String zurück:
> ```python
> vp_id = "  VP01  "
> vp_id.strip()        # gibt "VP01" zurück, aber vp_id ist noch "  VP01  "
> vp_id = vp_id.strip()  # so wird der neue Wert gespeichert
> ```

```python
# Typischer Anwendungsfall: Dateinamen auswerten
dateiname = "223_CPT_16-04-26.csv"

print(dateiname.endswith(".csv"))        # → True
print(dateiname.startswith("223"))       # → True
print(dateiname.replace(".csv", ""))     # → "223_CPT_16-04-26"
teile = dateiname.replace(".csv", "").split("_")
print(teile)                             # → ["223", "CPT", "16-04-26"]
```

---


## Zusammenfassung

| | Liste | Tupel | String |
|---|---|---|---|
| **Klammern** | `[ ]` | `( )` | `" "` |
| **Veränderlich?** | ✅ Ja | ❌ Nein | ❌ Nein |
| **Indexing** | ✅ | ✅ | ✅ |
| **Slicing** | ✅ | ✅ | ✅ |
| **Typischer Einsatz** | Stimuli, RTs | Koordinaten, Farben | Texte, IDs |

**Kernregeln:**
- Indexing beginnt bei `0`
- Negativer Index zählt von hinten: `-1` ist das letzte Element
- Slicing `[start:stop]` – start inklusive, stop **exklusiv**
- `len()` gibt die Anzahl der Elemente zurück

---

## Ausblick: Woche 3

Nächste Woche lernen Sie:
- **Bedingungen** (`if` / `elif` / `else`) – Code trifft Entscheidungen
- **For-Schleifen** – automatisch über Stimuluslisten iterieren
- **Erste Automatisierung:** eine Trial-Schleife schreiben
