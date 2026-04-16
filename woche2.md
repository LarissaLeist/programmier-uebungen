# Woche 2: Python Grundlagen I
Listen, Tupel und Strings

## Lernziele

* Listen erstellen, verändern und auslesen
* Tupel verstehen und von Listen unterscheiden
* Strings als Sequenzen begreifen
* Indexing und Slicing anwenden
* typische Fehler erkennen und beheben
* erste kleine Datenstrukturen für Experimente modellieren (z. B. Stimuluslisten)

---
## 0. Orientierung: Wie Sie diese Woche bearbeiten
Diese Woche arbeiten Sie mit Sequenzen — also Datenstrukturen, die mehrere Werte enthalten können.

Bitte arbeiten Sie aktiv mit:
* Führen Sie jeden Codeblock selbst aus.
* Ändern Sie Werte, Indizes und Slices.
* Notieren Sie Ihre Vorhersage, bevor Sie Code ausführen.
* Nutzen Sie Fehlermeldungen als Hinweise.


---

## 1. Listen (lists)
Listen sind veränderbare Sammlungen von Werten.
Sie sind eines der wichtigsten Werkzeuge in Python — besonders für Experimente, weil man damit z. B.:

* Stimuluslisten speichern
* Trial‑Reihenfolgen mischen
* Reaktionszeiten sammeln
* Bedingungen organisieren

### 1.1 Eine Liste erstellen

```python
farben = ["rot", "grün", "blau"]
print(farben)
```

Listen können verschiedene Datentypen enthalten:
```python
mixed = ["Anna", 3, 4.5, True]
```

### 1.2 Elemente auslesen (Indexing)

Python beginnt bei 0 zu zählen.
```python
farben = ["rot", "grün", "blau"]
print(farben[0])   # erstes Element
print(farben[2])   # drittes Element
```

### 1.3 Elemente verändern
Aus grün mach gelb ...

```python
farben[1] = "gelb"
print(farben)
```
### 1.4 Elemente hinzufügen

```python
farben.append("lila")
print(farben)
```
### 1.5 Länge einer Liste

```python
len(farben)
```

---

## 2. Tupel (tuples)

Tupel sehen aus wie Listen, sind aber unveränderlich.
Warum Tupel?
* Sie sind schneller
* Sie schützen Daten vor versehentlicher Veränderung
* Sie eignen sich für feste Paare (z. B. Positionen, RGB‑Werte)

```python
koordinaten = (10, 20)
```

### 2.1 Zugriff wie bei Listen

```python
print(koordinaten[0])
```

## 2.2 Aber: Tupel können nicht verändert werden

```python
koordinaten[0] = 99   # Fehler!
```

# 3. Strings als Sequenzen
Strings (str) haben wir schon einmal kennengerlent. Sie sind Folgen von Zeichen.
Das bedeutet: Sie können sie wie Listen behandeln.
```python
wort = "Python"
print(wort[0])
print(wort[3])
```

## 3.1 Slicing
```python
wort = "Psychologie"
print(wort[0:5])   # von Index 0 bis 4
print(wort[:4])    # Anfang bis Index 3
print(wort[4:])    # ab Index 4 bis Ende
```

## 3.3 Länge eines Strings

```python
len("Hallo")
```




## Aufgabe 1a: Listen

Erstellen Sie eine Liste mit drei Ihrer Lieblingsgerichte.
Geben Sie das erste und das letzte Element aus.

```python
# Ihr Code hier
```

## Aufgabe 1b: Listen

Erstellen Sie eine Liste mit Einträgen, wie sie in einem Logfile einer kognitiven Aufgabe zu erwarten wäre. 
Geben Sie das erste und das letzte Element aus.

```python
# Ihr Code hier
```

## Aufgabe 2: Listen vs. Tupel

Woran erkenne ich, ob eine Variable eine Liste oder ein Tupel enthält?

## Aufgabe 3: Tupel
Erstellen Sie ein Tupel mit zwei Zahlen.
Versuchen Sie anschließend, ein Element zu ändern.
Was passiert?

```python
# Ihr Code hier
```

### Aufgabe 4: Strings
Gegeben:

```python
vp = "223_CPT_16-04-26"
```

Geben Sie die ersten drei Buchstaben aus.
Geben Sie die letzten 8 Buchstaben aus.
Geben Sie die 5-7. Position des Strings an.
Verwenden Sie direkt verständliche Variablennamen!

```python
# Ihr Code hier
```


```python
# Ihr Code hier
```

