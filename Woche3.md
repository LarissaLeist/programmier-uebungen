# Woche 3: Python Grundlagen III
Bedingungen (if/elif/else) und Schleifen (for-Loops) im Kontext von Experimenten

### Lernziele
* Bedingungen formulieren und auswerten (z. B. korrekte Reaktionen prüfen)
* Vergleichsoperatoren gezielt einsetzen
* for-Schleifen zur Trial-Steuerung nutzen
* Stimuluslisten automatisiert durchlaufen
* Einfache experimentelle Logik implementieren
* Typische Fehler erkennen und beheben


---
## 0. Orientierung: Warum das für EEG-Experimente zentral ist

In Experimenten müssen Sie ständig:

* Trials sequenziell präsentieren
* Stimuli systematisch variieren
* Reaktionen auswerten (korrekt vs. inkorrekt)
  
Genau dafür brauchen Sie:
* Bedingungen → Entscheidungen treffen
* Schleifen → Wiederholungen automatisieren

---

## 1. Bedingungen (if / elif / else)
Bedingungen steuern experimentelle Entscheidungen.

### 1.1 Grundstruktur
Einrückungen sind Pflicht!

```python
rt = 450  # Reaktionszeit in ms
if rt > 400:
    print("langsame Reaktion")
```


### 1.2 Beispiel: Reaktionsbewertung

```python
rt = 350

if rt < 300:
    print("sehr schnell")
elif rt < 600:
    print("normal")
else:
    print("langsam")
```

### 1.3 Vergleichsoperatoren

| Operator | Bedeutung      | Beispiel        |
| -------- | -------------- | --------------- |
| `==`     | gleich         | Taste korrekt   |
| `!=`     | ungleich       | falsche Antwort |
| `>`      | größer         | RT zu langsam   |
| `<`      | kleiner        | RT sehr schnell |
| `>=`     | größer/gleich  | Cutoff          |
| `<=`     | kleiner/gleich | Einschluss      |


---

## 2. for-Schleifen: Trial-Strukturen
for-Schleifen bilden das Rückgrat jedes Experiments.
### 2.1 Stimulusliste durchlaufen

```python
stimuli = ["rot", "grün", "blau"]

for stim in stimuli:
    print("Stimulus:", stim)
```


### 2.2 Trial-Nummern

```python
for trial in range(5):
    print("Trial:", trial)
```



### 2.3 Index-basierte Schleife

```python
stimuli = ["A", "B", "C"]

for i in range(len(stimuli)):
    print("Trial", i, "Stimulus", stimuli[i])
```


---
## 3. Bedingungen + Schleifen
### 3.1 
```python

```


### 3.2 
```python

```



### 3.3 
```python

```





## Aufgaben
### Aufgabe 1: Reaktionsbewertung

```python
rt = 720

# Ihr Code hier
```
Schreiben Sie ein Feedback.
* rt < 300 → „zu schnell“
* rt < 600 → „gültig“
* sonst → „zu langsam“


### Aufgabe 2: Trial-Ausgabe

* Liste mit 4 Stimuli erstellen
* Mit Trial-Nummer ausgeben

```python
# Ihr Code hier
```

### Aufgabe 3: Mini Experiment


## Typische Fehler

### Fehlender Doppelpunkt

```python
if rt > 500
    print("Fehler")
```

### Vergleich vs. Zuweiseung

```python
x = 5
x == 5
```

### range

```python
range(3)  # 0,1,2
```

### Logikfehler

```python
if rt < 200:
    print("gültig")  # falsch!
```

---
# Lösungen

### Aufgabe 1

### Aufgabe 2

### Aufgabe 3

---
## Zusammenfassung
* Bedingungen = Entscheidungen
* Schleifen = Wiederholungen
* Kombination = Experimentlogik
* Kleine Fehler → große Datenprobleme

## Ausblick auf nächste Woche
* Funktionen schreiben
* Code strukturieren
* Experimente modular aufbauen
