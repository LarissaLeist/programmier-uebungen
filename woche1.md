# Woche 1: Python Grundlagen I

## Lernziele

* Arbeitsumgebung einrichten (Installation Anaconda oder Miniconda)
* Python-Grundlagen verstehen
* Erste Variablen verwenden 
* grundlegende Datentypen unterscheiden
* Operationen anwenden
* erste Fehlermeldungen verstehen und beheben

---
## 0. Orientierung: Wie Sie diese Woche bearbeiten
Diese Seite ist so aufgebaut, dass Sie Schritt für Schritt durch die Grundlagen geführt werden.
Bitte arbeiten Sie aktiv mit:
* Führen Sie jeden Codeblock selbst aus (entweder in Google Colab oder Spyder)
* Ändern Sie Werte und beobachten Sie, was passiert.
* Notieren Sie Ihre Vermutung über das Ergebnis, bevor Sie den Code ausführen.



---

## 1. Was ist Python?

Python ist eine **Programmiersprache**, die besonders geeignet ist für:
* wissenschaftliches Arbeiten  
* Datenanalyse  
* Experimente in der Psychologie  

Python ist **interpretiert**:  
Das bedeutet, dein Code wird Zeile für Zeile ausgeführt.
Das hat Vorteile:
* Sie sehen schnell Ergebnisse
* Fehler lassen sich gut lokalisieren
* Sie können interaktiv arbeiten

---

## 2. Ihre Arbeitsumgebung
Es gibt verschiedene IDEs, mit denen Python-Code ausgeführt werden kann.

### **Option A: Google Colab (empfohlen für Woche 1–2)**
Colab ist ideal für den Einstieg, weil:
* keine Installation notwendig ist
* Codezellen einzeln ausgeführt werden können
* Sie sofort Rückmeldungen erhalten

**Hinweis:**
Wenn Sie eine Zelle erneut ausführen, werden Variablen überschrieben.
Wenn Sie eine neue Zelle ausführen, stehen frühere Variablen weiterhin zur Verfügung.

https://colab.research.google.com/drive/1TwjsH7Vfq8q27BesvJgrsBPZ_8a9K3nc?usp=sharing

### **Option B: Spyder**
Ideal für Experimente, größere Projekte, ausführen von PsychoPy‑Code und zur Datenauswertung.

### **Option C: PsychoPy Coder**
Wird ab Woche 5 wichtig.


## Erstes Programm

```python
print("Hallo Welt!")
```

Aufgabe:
Ändern Sie in Google Colab den Text so, dass Ihr eigener Name ausgegeben wird.

---

## Variablen

Variablen speichern Werte. Es gibt verschiedene Typen:
* str -> Text
* int -> ganze Zahl
* float -> Kommazahl
* bool -> True/False

```python
name = "Anna"
alter = 20
rt = 0.532

print(name)
print(alter)
print(type(rt))
```

**Warum sind Variablen wichtig?**
In Experimenten speichern Variablen z. B.:
* Reaktionszeiten
* Stimulusnamen
* Tastendrücke
* Versuchspersonen‑IDs
Sie sind das Fundament jedes Experiments.


---

## Aufgabe 1: Variablen

Erstellen Sie drei Variablen:

* Ihren Namen
* Ihr Alter
* eine beliebige Zahl

Geben Sie alle Werte aus.

```python
# Ihr Code hier
```

---

## Aufgabe 2: Vorhersage

Was wird ausgegeben?

```python
x = 5
y = 3
print(x + y)
```

Notieren Sie Ihre Antwort und überprüfen Sie sie anschließend.

---

## Datentypen

Python entscheidet anhand des Typs:
* welche Operationen erlaubt sind
* wie Werte gespeichert werden
* wie sie ausgegeben werden


```python
a = 5        # int
b = 3.14     # float
c = "Hallo"  # str
```

---

## Aufgabe 3: Datentypen

Bestimmen Sie den Datentyp der Variablen:

```python
a = 10
b = "10"
c = 10.0
```

Antwort:

* a =
* b =
* c =

---

## Aufgabe 4: String-Verarbeitung

Was wird ausgegeben?

```python
print("2" + "3")
```

Überprüfen Sie Ihre Vermutung durch Ausführen des Codes.

---

## Aufgabe 5: Fehleranalyse

Warum funktioniert dieser Code nicht?

```python
name = Anna
print(name)
```

Identifizieren Sie den Fehler und korrigieren Sie ihn.

---

## Aufgabe 6: Anwendung

Schreiben Sie ein Programm, das:

1. Ihren Namen speichert
2. eine Begrüßung ausgibt

Erwartete Ausgabe (Beispiel):
Hallo Larissa!

```python
# Ihr Code hier
```

---

## Zusatzaufgabe

Was wird ausgegeben?

```python
x = "5"
y = 2
print(x * y)
```

---

## 7. Typische Anfängerfehler

Beim Einstieg in Python treten häufig ähnliche Fehler auf. Diese sind völlig normal und gehören zum Lernprozess dazu. Nutzen Sie Fehlermeldungen als Hinweise — sie helfen Ihnen, Ihren Code besser zu verstehen.

### Fehlende Anführungszeichen
Strings müssen immer in Anführungszeichen stehen.

```python
name = Anna      # Fehler
name = "Anna"    # korrekt
```

### Falsche Groß- und Kleinschreibung
```python
Print("Hallo")   # Fehler
print("Hallo")   # korrekt
```

### Variablen vor ihrer Definition verwenden
Variablen müssen definiert sein, bevor sie verwendet werden.
```python
print(x)   # Fehler
x = 5
```

### Verwechslung von Zahl und Text
Zahlen und Strings können nicht direkt kombiniert werden.
```python
print("RT: " + 0.5)        # Fehler
print("RT: " + str(0.5))   # korrekt
```

### Fehlende oder falsche Klammern
Python verwendet Einrückungen, um Codeblöcke zu strukturieren.
```python
if True:
print("Hallo")   # Fehler
```
Korrekt wäre:
```python
if True:
    print("Hallo")

```

### Einrückungsfehler
Python benötigt korrekte Syntax.
```python
print "Hallo"    # Fehler
print("Hallo")   # korrekt
```

### Tippfehler in Variablennamen
Schon ein kleiner Schreibfehler führt zu einem Fehler.
```python
reaction_time = 0.5
print(reation_time)   # Fehler
```

  
**Fehlermeldungen sind keine Katastrophe, sondern Hinweise. Jede Fehlermeldung bringt Sie näher zum funktionierenden Code.**

---

## Lösungen

<details>
<summary>Aufgabe 1</summary>

```python
name = "Larissa"
alter = 35
zahl = 7

print(name)
print(alter)
print(zahl)
```

</details>

---

<details>
<summary>Aufgabe 2</summary>

Ausgabe: 8

</details>

---

<details>
<summary>Aufgabe 3</summary>

* a = int
* b = str
* c = float

</details>

---

<details>
<summary>Aufgabe 4</summary>

Ausgabe: 23

Strings werden aneinandergehängt.

</details>

---

<details>
<summary>Aufgabe 5</summary>

Fehler:
Der String "Anna" steht nicht in Anführungszeichen.

Korrektur:

```python
name = "Anna"
```

</details>

---

<details>
<summary>Aufgabe 6</summary>

```python
name = "Larissa"
print("Hallo " + name + "!")
```

</details>

---

<details>
<summary>Zusatzaufgabe</summary>

Ausgabe: 55

Ein String wird mehrfach wiederholt.

</details>

---

## Zusammenfassung

* Variablen speichern Werte
* Datentypen bestimmen, wie Werte verarbeitet werden
* kleine syntaktische Unterschiede (z. B. Anführungszeichen) sind entscheidend
* Vorhersagen unterstützt das Verständnis von Code

---

## Ausblick

In der nächsten Einheit werden Schleifen und Bedingungen eingeführt.
