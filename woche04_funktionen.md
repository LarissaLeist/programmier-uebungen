# Woche 4: Funktionen
## Modularisierung und saubere Experimentlogik


### Lernziele

Nach dieser Sitzung können Sie:
- Eigene Funktionen schreiben und aufrufen
- Parameter und Rückgabewerte verstehen und einsetzen
- Den Unterschied zwischen `print()` und `return` erklären
- Code modular strukturieren und wiederverwenden
- Einfache experimentelle Abläufe in Funktionen kapseln
- Typische Designfehler erkennen und vermeiden

---

## 0. Warum Funktionen?

In den letzten Wochen haben Sie Code geschrieben, der funktioniert –
aber in einem echten Experiment würden Sie diesen Code **immer wieder brauchen**:

```python
# Das haben Sie in Woche 3 geschrieben:
korrekt_n = 0
for stimulus, rt, korrekt in trials:
    if korrekt:
        korrekt_n = korrekt_n + 1
accuracy = korrekt_n / len(trials)
print(f"Accuracy: {accuracy:.0%}")
```

Was passiert, wenn Sie das für fünf verschiedene Versuchspersonen brauchen?
Oder für zwei Bedingungsblöcke? Sie könnten den Code kopieren – aber das ist fehleranfällig.
Wenn Sie später einen Fehler finden, müssen Sie ihn an fünf Stellen beheben.

**Funktionen lösen dieses Problem:** Sie schreiben den Code einmal, geben ihm einen Namen,
und rufen ihn so oft auf wie nötig.

```
Ohne Funktionen         Mit Funktionen
─────────────────       ───────────────────────────────
[Code Block 1]          def berechne_accuracy(trials):
[Code Block 2]              ...
[Code Block 3]
[Code Block 4]          berechne_accuracy(block_1)
[Code Block 5]          berechne_accuracy(block_2)
                        berechne_accuracy(block_3)
```

---

## 1. Grundprinzip: Funktion definieren und aufrufen

Eine Funktion ist ein **benannter, wiederverwendbarer Codeblock**.
Sie wird einmal *definiert* und kann beliebig oft *aufgerufen* werden.

Drei Dinge sind Pflicht:
1. Das Schlüsselwort `def`
2. Ein Name (wie bei Variablen)
3. Runde Klammern und Doppelpunkt

```python
def begruessung():
    print("Willkommen zum Experiment.")
    print("Bitte lesen Sie die Instruktionen.")

# Definieren allein tut noch nichts – erst der Aufruf führt den Code aus:
begruessung()
```

```python
# Dieselbe Funktion beliebig oft aufrufen:
begruessung()
begruessung()
begruessung()
```

> ❓ **Vorhersage:** Was passiert, wenn Sie `begruessung` **ohne Klammern** schreiben? Probieren Sie es aus.

```python
# Ausprobieren:
begruessung
```

---

## 2. Parameter – Eingaben für Funktionen

Parameter sind Eingaben, die die Funktion beim Aufruf bekommt.
Die Variable `stimulus` existiert nur **innerhalb** der Funktion – das nennt man **lokale Variable**.

```python
def zeige_stimulus(stimulus):
    print(f"Aktueller Stimulus: {stimulus}")

zeige_stimulus("Haus")   # → "Aktueller Stimulus: Haus"
zeige_stimulus("Baum")   # → "Aktueller Stimulus: Baum"
```

```python
# Mehrere Parameter sind möglich:
def zeige_trial(stimulus, trial_nummer):
    print(f"Trial {trial_nummer}: {stimulus}")

zeige_trial("Haus", 1)
zeige_trial("Baum", 2)
```

---

## 3. Rückgabewerte mit `return`

Bisher haben Funktionen etwas *ausgegeben* (`print`).
Meistens wollen wir aber, dass eine Funktion etwas *berechnet und zurückgibt*.

Der Unterschied ist entscheidend:

```python
# print() zeigt etwas an – der Wert ist danach weg
def mit_print(rt):
    print(rt * 2)

ergebnis = mit_print(100)
print("ergebnis ist:", ergebnis)   # → None !
```

```python
# return gibt den Wert zurück – man kann damit weiterarbeiten
def mit_return(rt):
    return rt * 2

ergebnis = mit_return(100)
print("ergebnis ist:", ergebnis)   # → 200
print(ergebnis + 50)               # → 250
```

> 💡 **Faustregel:** Nutzen Sie `return`, wenn Sie das Ergebnis weiterverarbeiten wollen.
> Nutzen Sie `print()` nur zur Anzeige – nie als Ersatz für `return`.

`return` beendet die Funktion **sofort** – Code danach wird nicht mehr ausgeführt:

```python
def bewerte_rt(rt):
    if rt < 200:
        return "zu schnell"
    if rt > 1000:
        return "zu langsam"
    return "gültig"   # wird nur erreicht, wenn beide Bedingungen False sind

print(bewerte_rt(150))   # → zu schnell
print(bewerte_rt(512))   # → gültig
print(bewerte_rt(1200))  # → zu langsam
```

---

## 4. Lokale vs. globale Variablen

Variablen, die *innerhalb* einer Funktion erstellt werden, existieren nur dort:

```python
def berechnung():
    ergebnis = 42   # lokale Variable – existiert nur hier drin

berechnung()
# print(ergebnis)   # → NameError: name 'ergebnis' is not defined
```

```python
# Besser: Werte reingeben und zurückgeben
def verdopple(wert):
    return wert * 2

zahl = 10
zahl = verdopple(zahl)
print(zahl)   # → 20
```

---

## 5. Standardwerte für Parameter

Parameter können einen Standardwert haben, der gilt wenn kein Wert übergeben wird:

```python
def bewerte_rt(rt, untere_grenze=200, obere_grenze=1000):
    if rt < untere_grenze:
        return "zu schnell"
    if rt > obere_grenze:
        return "zu langsam"
    return "gültig"

print(bewerte_rt(450))                      # Standardwerte: 200, 1000
print(bewerte_rt(450, untere_grenze=150))   # nur untere Grenze geändert
print(bewerte_rt(450, 150, 800))            # beide Grenzen geändert
```

---

## 6. Funktionen im Experiment: Schritt für Schritt

### 6.1 Docstrings – Funktionen dokumentieren

```python
def ist_gueltiger_trial(rt, untere_grenze=200, obere_grenze=1000):
    """Prüft, ob eine Reaktionszeit im gültigen Bereich liegt.

    Args:
        rt: Reaktionszeit in ms.
        untere_grenze: Minimale gültige RT (Standard: 200).
        obere_grenze: Maximale gültige RT (Standard: 1000).

    Returns:
        True wenn gültig, False wenn außerhalb des Bereichs.
    """
    return untere_grenze <= rt <= obere_grenze

print(ist_gueltiger_trial(512))   # True
print(ist_gueltiger_trial(150))   # False
```

### 6.2 Funktion in einer Schleife nutzen

```python
reaktionszeiten = [512, 189, 834, 1200, 445]

for rt in reaktionszeiten:
    if ist_gueltiger_trial(rt):
        print(f"{rt} ms → gültig")
    else:
        print(f"{rt} ms → ausgeschlossen")
```

### 6.3 Funktionen aufeinander aufbauen

Kleine, klar benannte Funktionen lassen sich zu größeren zusammensetzen:

```python
def berechne_accuracy(trials):
    """Berechnet die Accuracy aus einer Trialstruktur."""
    if len(trials) == 0:
        return 0.0
    korrekt_n = 0
    for stimulus, rt, korrekt in trials:
        if korrekt:
            korrekt_n = korrekt_n + 1
    return korrekt_n / len(trials)


def filtere_trials(trials):
    """Gibt nur gültige Trials zurück (RT 200–1000 ms)."""
    gueltige = []
    for stimulus, rt, korrekt in trials:
        if ist_gueltiger_trial(rt):
            gueltige.append([stimulus, rt, korrekt])
    return gueltige


def werte_block_aus(trials):
    """Filtert Trials und berechnet Accuracy des Blocks."""
    gueltige_trials = filtere_trials(trials)
    accuracy = berechne_accuracy(gueltige_trials)
    print(f"Trials gesamt:  {len(trials)}")
    print(f"Trials gültig:  {len(gueltige_trials)}")
    print(f"Accuracy:       {accuracy:.0%}")


# Testen:
block_1 = [
    ["Haus", 512,  True ],
    ["Baum", 150,  False],   # zu schnell
    ["Auto", 445,  True ],
    ["Hund", 1200, True ],   # zu langsam
    ["Stuhl",820,  False],
]

werte_block_aus(block_1)
```

---

## Exkurs: Warum Funktionen in der Experimentalprogrammierung unverzichtbar sind

Funktionen sind keine Frage des Stils – sie sind eine Frage der **Datensicherheit**.

### Problem 1: Parameter ändern sich im Laufe der Entwicklung

Sie programmieren eine **Gedächtnis-Aufgabe**. Die Versuchsperson merkt sich eine Zahlensequenz.
Zu Beginn: Sequenzlänge 5 Items. Nach dem Pretest: zu leicht, Sie wollen **7** testen.

```python
# ❌ Ohne Funktion: 6 Stellen müssen geändert werden
sequenz = list(range(1, 6))           # → [1, 2, 3, 4, 5]
print(f"Merken Sie sich: {sequenz}")
# zeige_sequenz(sequenz, laenge=5)
# speichere_trial(vp_id, block=1, laenge=5)

sequenz = list(range(1, 6))
print(f"Merken Sie sich: {sequenz}")
# zeige_sequenz(sequenz, laenge=5)
# speichere_trial(vp_id, block=2, laenge=5)
```

```python
# ✓ Mit Funktion: eine einzige Stelle
SEQUENZLAENGE = 5   # ← hier ändern, alles andere passt sich an

def fuehre_memory_block_durch(block_nr, laenge=SEQUENZLAENGE):
    sequenz = list(range(1, laenge + 1))
    print(f"Block {block_nr} – Merken Sie sich: {sequenz}")
    # zeige_sequenz(sequenz, laenge=laenge)
    # speichere_trial(vp_id, block=block_nr, laenge=laenge)

fuehre_memory_block_durch(block_nr=1)
fuehre_memory_block_durch(block_nr=2)
fuehre_memory_block_durch(block_nr=3)
```

> ⚠️ Warum `laenge + 1`? Weil `range(1, 5)` nur bis 4 geht – stop ist exklusiv, genau wie beim Slicing.

### Problem 2: Inkonsistenz durch Copy-Paste

Finden Sie den Fehler in Bedingung C:

```python
# ❌ Ohne Funktion – Fehler schwer zu sehen:
# Bedingung A: stille_nach=500
# Bedingung B: stille_nach=500
# Bedingung C: stille_nach=200  ← Copy-Paste-Fehler! Sollte 500 sein

# ✓ Mit Funktion:
STILLE_VOR  = 500
TON_DAUER   = 200
STILLE_NACH = 500

def simuliere_tonsequenz(frequenz, bedingung,
                         stille_vor=STILLE_VOR,
                         ton_dauer=TON_DAUER,
                         stille_nach=STILLE_NACH):
    print(f"{bedingung}: {stille_vor}ms Stille | {ton_dauer}ms Ton ({frequenz}Hz) | {stille_nach}ms Stille")

simuliere_tonsequenz(frequenz=1000, bedingung="hoch")
simuliere_tonsequenz(frequenz=200,  bedingung="tief")
simuliere_tonsequenz(frequenz=500,  bedingung="mittel")
```

> 💡 **Faustregel:** Jede Zahl, die eine inhaltliche Entscheidung repräsentiert – Sequenzlänge, Zeitfenster, Schwellenwert – gehört in eine **Konstante** (`SEQUENZLAENGE = 7`) oder einen **Parameter mit Standardwert**.
> Zahlen, die nackt im Code stehen, nennt man **Magic Numbers** – sie sind eine häufige Quelle stiller Fehler.

---

## Übungsteil

> 🟢 **Basis** – einzelne Konzepte direkt anwenden  
> 🟡 **Aufbau** – Konzepte kombinieren  
> 🔴 **Vertiefung** – eigenständig strukturieren

---

### 🟢 Aufgabe 1 – Erste Funktion

Schreiben Sie eine Funktion `begruessung(vp_id)`, die folgende Ausgabe erzeugt:
```
Willkommen, VP03!
Das Experiment beginnt gleich.
```
Rufen Sie die Funktion mit drei verschiedenen VP-IDs auf.

```python
# Ihr Code hier
```



---

### 🟢 Aufgabe 2 – `print` vs. `return`

Gegeben sind zwei Funktionen:
```python
def version_a(rt):
    print(rt * 2)

def version_b(rt):
    return rt * 2
```

Beantworten Sie **ohne Ausführen**:
1. Was gibt `ergebnis = version_a(100)` → `print(ergebnis)` aus?
2. Was gibt `ergebnis = version_b(100)` → `print(ergebnis)` aus?
3. Welche Version können Sie für `print(version_?(100) + 50)` nutzen? Warum?

```python
def version_a(rt):
    print(rt * 2)

def version_b(rt):
    return rt * 2

# Überprüfung hier:
```



---

### 🟢 Aufgabe 3 – RT bewerten

Schreiben Sie eine Funktion `bewerte_rt(rt)` mit `return` (nicht `print`), die:
- `"zu schnell"` zurückgibt bei `rt < 200`
- `"gültig"` bei `200 <= rt <= 1000`
- `"zu langsam"` bei `rt > 1000`

Testen Sie mit mindestens vier Werten, darunter genau `200` und `1000`.

```python
# Ihr Code hier
```



---

### 🟡 Aufgabe 4 – Funktion mit Standardwert

Erweitern Sie `bewerte_rt()` so, dass die Grenzen als Parameter mit Standardwerten übergeben werden können:

```python
def bewerte_rt(rt, untere_grenze=200, obere_grenze=1000):
    ...
```

Rufen Sie die Funktion auf:
1. Mit Standardwerten: `bewerte_rt(450)`
2. Mit geänderter unterer Grenze: `bewerte_rt(450, untere_grenze=150)`
3. Mit beiden geänderten Grenzen: `bewerte_rt(450, 150, 800)`

```python
# Ihr Code hier
```



---

### 🟡 Aufgabe 5 – Funktion in Schleife

Schreiben Sie eine Funktion `verarbeite_trial(stimulus, rt, korrekt)`, die eine Zeile ausgibt:
```
Haus | 512 ms | gültig | korrekt
Baum | 1200 ms | zu langsam | inkorrekt
```
Nutzen Sie darin Ihre Funktion `bewerte_rt()` aus Aufgabe 3.
Iterieren Sie dann über die Trialstruktur.

```python
trials = [
    ["Haus",  512,  True ],
    ["Baum",  1200, False],
    ["Auto",  445,  True ],
    ["Hund",  178,  True ],
    ["Stuhl", 820,  False],
]

# Ihr Code hier
```



---

### 🟡 Aufgabe 6 – Accuracy-Funktion

Schreiben Sie eine Funktion `berechne_accuracy(trials)`, die:
1. Die Anzahl korrekter Trials zählt
2. Die Accuracy berechnet
3. Die Accuracy **zurückgibt** (nicht nur ausgibt)

Erwartete Ausgabe:
```
Block 1: 67%
Block 2: 100%
```

```python
block_1 = [["Haus", 512, True], ["Baum", 678, False], ["Auto", 445, True]]
block_2 = [["Hund", 390, True], ["Stuhl", 820, True], ["Tisch", 334, True]]

# Ihr Code hier
```



---

### 🔴 Aufgabe 7 – Trials filtern

Schreiben Sie eine Funktion `filtere_trials(trials)`, die:
1. Über alle Trials iteriert
2. Jeden Trial mit `ist_gueltiger_trial(rt)` prüft (schreiben Sie diese Funktion ebenfalls)
3. Eine neue Liste nur mit gültigen Trials zurückgibt

Geben Sie aus: wie viele Trials gefiltert wurden, und berechnen Sie die Accuracy der gefilterten Daten.

```python
rohdaten = [
    ["Haus",  512,  True ],
    ["Baum",  150,  False],   # zu schnell
    ["Auto",  445,  True ],
    ["Hund",  1200, True ],   # zu langsam
    ["Stuhl", 820,  False],
    ["Tisch", 334,  True ],
]

# Ihr Code hier
```



---

### 🔴 Aufgabe 8 – Stroop-Auswertung als Funktion

Aus Woche 3 kennen Sie die Stroop-Auswertung. Schreiben Sie jetzt eine Funktion
`stroop_auswertung(trials)`, die:
1. Mittlere RT für kongruente und inkongruente Trials berechnet
2. Den Stroop-Effekt berechnet (RT inkongruent − RT kongruent)
3. Die Gesamtaccuracy berechnet
4. Alle Werte in einem Dictionary zurückgibt

> 💡 Dictionaries wurden in Woche 2 kurz angesprochen – hier begegnen Sie ihnen als Rückgabewert.
> `{"schluessel": wert}` – nächste Woche schauen wir sie uns genauer an.

```python
stroop_trials = [
    ["ROT",  "kongruent",   412, True ],
    ["BLAU", "inkongruent", 587, True ],
    ["GRÜN", "kongruent",   389, True ],
    ["ROT",  "inkongruent", 634, False],
    ["BLAU", "kongruent",   401, True ],
    ["GRÜN", "inkongruent", 712, True ],
]

# Ihr Code hier
```



---

### 🔴 Aufgabe 9 – Fehler finden

Jede Funktion hat einen Fehler. Beschreiben Sie das Problem **bevor** Sie den Code ausführen.

```python
# (a) – die Funktion soll die doppelte RT zurückgeben
def verdopple_rt(rt):
    ergebnis = rt * 2

print(verdopple_rt(300))   # gibt None aus – warum?

# Ihre Analyse und Korrektur:
```

```python
# (b) – soll "gültig" oder "ungültig" zurückgeben
def pruefe_rt(rt):
    if rt > 200:
        return "gültig"
    return "ungültig"
    print("fertig")   # wird nie ausgeführt – warum?

# Ihre Analyse:
```

```python
# (c) – soll Accuracy berechnen
def berechne_acc(trials):
    korrekt_n = 0
    for stimulus, rt, korrekt in trials:
        if korrekt:
            korrekt_n = korrekt_n + 1
    return korrekt_n / len(trials)

berechne_acc([])   # was passiert hier?

# Ihre Analyse und Korrektur:
```

```python
# (d) – lokale Variable
def setze_wert():
    mein_wert = 99

setze_wert()
print(mein_wert)   # NameError – warum?

# Ihre Analyse und Korrektur:
```



---

## Typische Fehler – Übersicht

| Fehler | Ursache | Lösung |
|---|---|---|
| Funktion gibt `None` zurück | `print()` statt `return` | `return` verwenden |
| `NameError` nach Funktion | lokale Variable außerhalb genutzt | Wert mit `return` zurückgeben |
| Code nach `return` | `return` beendet Funktion sofort | Code davor platzieren |
| Division durch 0 | leere Liste übergeben | Länge vorher prüfen |
| Funktion nicht aufgerufen | `def` definiert nur, ruft nicht auf | `funktionsname()` aufrufen |
| Klammern vergessen | `funktionsname` statt `funktionsname()` | `()` nicht vergessen |

---

## Zusammenfassung

| Konzept | Syntax | Zweck |
|---|---|---|
| Funktion definieren | `def name(parameter):` | Codeblock benennen |
| Funktion aufrufen | `name(wert)` | Code ausführen |
| Rückgabewert | `return wert` | Ergebnis weitergeben |
| Standardwert | `def f(x=10):` | optionaler Parameter |
| Lokale Variable | innerhalb von `def` | nur dort sichtbar |
| Docstring | `"""..."""` unter `def` | Funktion dokumentieren |

**Drei Designprinzipien:**
1. **Eine Aufgabe** – eine Funktion tut genau eine Sache
2. **`return` statt `print`** – Ergebnisse zurückgeben, nicht nur anzeigen
3. **Kleine Funktionen stapeln** – `werte_block_aus()` ruft `filtere_trials()` und `berechne_accuracy()` auf

---

## Ausblick: Woche 5

Nächste Woche lernen Sie **Dictionaries** – die letzte wichtige Datenstruktur:

```python
# Statt einer unübersichtlichen Liste ...
trial = ["VP01", "Haus", 512, True]

# ... Daten mit Namen ansprechen:
trial = {
    "vp_id":    "VP01",
    "stimulus": "Haus",
    "rt":       512,
    "korrekt":  True,
}

print(trial["stimulus"])   # → "Haus"
print(trial["rt"])         # → 512
```

Und Sie sehen, warum Aufgabe 8 das Ergebnis bereits als Dictionary zurückgegeben hat.

---

## Lösungen

### Lösung anzeigen

```python
def begruessung(vp_id):
    print(f"Willkommen, {vp_id}!")
    print("Das Experiment beginnt gleich.")

begruessung("VP01")
begruessung("VP02")
begruessung("VP03")
```

---

### Lösung anzeigen

```python
ergebnis_a = version_a(100)   # gibt 200 aus (durch print)
print(ergebnis_a)              # gibt None aus – print() gibt nichts zurück!

ergebnis_b = version_b(100)   # keine Ausgabe
print(ergebnis_b)              # gibt 200 aus

# Nur version_b funktioniert:
print(version_b(100) + 50)    # → 250
# print(version_a(100) + 50)  # → TypeError: NoneType + int
```

---

### Lösung anzeigen

```python
def bewerte_rt(rt):
    if rt < 200:
        return "zu schnell"
    if rt > 1000:
        return "zu langsam"
    return "gültig"

print(bewerte_rt(150))    # → zu schnell
print(bewerte_rt(200))    # → gültig
print(bewerte_rt(512))    # → gültig
print(bewerte_rt(1000))   # → gültig
print(bewerte_rt(1200))   # → zu langsam
```

---

### Lösung anzeigen

```python
def bewerte_rt(rt, untere_grenze=200, obere_grenze=1000):
    if rt < untere_grenze:
        return "zu schnell"
    if rt > obere_grenze:
        return "zu langsam"
    return "gültig"

print(bewerte_rt(450))                       # → gültig
print(bewerte_rt(450, untere_grenze=150))    # → gültig
print(bewerte_rt(450, 150, 800))             # → gültig
print(bewerte_rt(850, 150, 800))             # → zu langsam
```

---

### Lösung anzeigen

```python
def bewerte_rt(rt):
    if rt < 200:
        return "zu schnell"
    if rt > 1000:
        return "zu langsam"
    return "gültig"

def verarbeite_trial(stimulus, rt, korrekt):
    gueltigkeit = bewerte_rt(rt)
    korrektheit = "korrekt" if korrekt else "inkorrekt"
    print(f"{stimulus} | {rt} ms | {gueltigkeit} | {korrektheit}")

for stimulus, rt, korrekt in trials:
    verarbeite_trial(stimulus, rt, korrekt)
```

---

### Lösung anzeigen

```python
def berechne_accuracy(trials):
    if len(trials) == 0:
        return 0.0
    korrekt_n = 0
    for stimulus, rt, korrekt in trials:
        if korrekt:
            korrekt_n = korrekt_n + 1
    return korrekt_n / len(trials)

print(f"Block 1: {berechne_accuracy(block_1):.0%}")   # → 67%
print(f"Block 2: {berechne_accuracy(block_2):.0%}")   # → 100%
```

---

### Lösung anzeigen

```python
def ist_gueltiger_trial(rt):
    return 200 <= rt <= 1000

def filtere_trials(trials):
    gueltige = []
    for trial in trials:
        if ist_gueltiger_trial(trial[1]):
            gueltige.append(trial)
    return gueltige

gefiltert = filtere_trials(rohdaten)
ausgeschlossen = len(rohdaten) - len(gefiltert)

print(f"Trials gesamt:         {len(rohdaten)}")
print(f"Trials ausgeschlossen: {ausgeschlossen}")
print(f"Trials gültig:         {len(gefiltert)}")
print(f"Accuracy (gefiltert):  {berechne_accuracy(gefiltert):.0%}")
```

---

### Lösung anzeigen

```python
def stroop_auswertung(trials):
    rt_kongr_sum   = 0
    rt_inkongr_sum = 0
    n_kongr        = 0
    n_inkongr      = 0
    korrekt_n      = 0

    for stimulus, bedingung, rt, korrekt in trials:
        if bedingung == "kongruent":
            rt_kongr_sum = rt_kongr_sum + rt
            n_kongr      = n_kongr + 1
        else:
            rt_inkongr_sum = rt_inkongr_sum + rt
            n_inkongr      = n_inkongr + 1
        if korrekt:
            korrekt_n = korrekt_n + 1

    rt_kongruent   = rt_kongr_sum / n_kongr
    rt_inkongruent = rt_inkongr_sum / n_inkongr

    return {
        "rt_kongruent":   rt_kongruent,
        "rt_inkongruent": rt_inkongruent,
        "stroop_effekt":  rt_inkongruent - rt_kongruent,
        "accuracy":       korrekt_n / len(trials),
    }

ergebnis = stroop_auswertung(stroop_trials)
print(f"RT kongruent:   {ergebnis['rt_kongruent']:.0f} ms")
print(f"RT inkongruent: {ergebnis['rt_inkongruent']:.0f} ms")
print(f"Stroop-Effekt:  {ergebnis['stroop_effekt']:.0f} ms")
print(f"Accuracy:       {ergebnis['accuracy']:.0%}")
```

---

### Lösungen anzeigen

```python
# (a) return fehlt
def verdopple_rt(rt):
    return rt * 2

# (b) print() nach return wird nie erreicht – kein Fehler, aber toter Code
# Einfach entfernen.

# (c) Division durch 0 bei leerer Liste
def berechne_acc(trials):
    if len(trials) == 0:
        return 0.0
    korrekt_n = 0
    for stimulus, rt, korrekt in trials:
        if korrekt:
            korrekt_n = korrekt_n + 1
    return korrekt_n / len(trials)

# (d) lokale Variable außerhalb nicht sichtbar
def setze_wert():
    return 99

mein_wert = setze_wert()
print(mein_wert)   # → 99
```
