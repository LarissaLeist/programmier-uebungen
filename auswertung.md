# Auswertung: Stroop-Aufgabe

**Umgebung:** Spyder — kein PsychoPy nötig.  
**Datei:** `stroop_logfiles.csv`

Die Versuchspersonen haben ein **Farbwort** gesehen, das in einer bestimmten **Farbe** dargestellt war. Aufgabe war, die Farbe der Schrift zu nennen, unabhängig vom Wortinhalt. Wenn Wort und Farbe übereinstimmen (z.B. „red" in Rot), ist der Trial **kongruent**. Wenn nicht (z.B. „red" in Blau), ist er **inkongruent**.


## Kurz: Was sind Pandas und Seaborn?

**Pandas** ist ein Modul für Tabellen. Die wichtigste Datenstruktur heißt **DataFrame** — stellt euch eine Excel-Tabelle vor, die ihr mit Python steuern könnt. Zeilen sind Trials, Spalten sind Variablen. Ihr ladet eine CSV-Datei einmal mit `pd.read_csv(...)` und habt danach alles in einem Objekt (`df`), mit dem ihr rechnen, filtern und gruppieren könnt.

```python
df["Reaction Time"]                          # eine Spalte → wie eine Liste
df["Reaction Time"].mean()                   # Mittelwert
df[df["korrekt"] == 1]                       # Zeilen filtern
df.groupby("Congruency")["Reaction Time"].mean()   # Mittelwert pro Gruppe
```

**Seaborn** ist ein Modul für Grafiken, das auf pandas aufbaut. Ihr gebt an: welche Tabelle (`data=`), was auf die x-Achse (`x=`), was auf die y-Achse (`y=`), und seaborn erledigt den Rest — Mittelwert berechnen, Fehlerbalken zeichnen, Achsen beschriften.

```python
sns.barplot(data=df, x="Congruency", y="Reaction Time")
sns.histplot(data=df, x="Reaction Time")
```

`matplotlib.pyplot` (`plt`) brauchen wir für Titel, Achsenbeschriftung und Speichern:

```python
plt.title("Mein Plot")
plt.savefig("plot.png")
plt.show()
```

---

## E1 — Daten laden und anschauen

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Achtung: Spalten sind durch Semikolon getrennt, nicht durch Kommas
df = pd.read_csv("stroop_logfiles.csv", sep=";")

print(df.shape)       # (Zeilen, Spalten)
print(df.head())      # erste 5 Zeilen
print(df.dtypes)      # Datentypen jeder Spalte
```

Worauf es ankommt:
- `sep=";"` — diese Datei verwendet Semikolon als Trennzeichen. Vergisst man das, liest pandas die ganze Zeile als eine einzige Spalte ein.
- `df.dtypes` zeigt, ob Zahlen wirklich als Zahlen erkannt wurden.

### 🟦 Übung E1a — wie viele Probanden?

Wie viele verschiedene Versuchspersonen stecken in der Datei?

<details>
<summary>Lösung E1a</summary>

```python
print(df["Participant"].nunique())
```

`nunique()` zählt die eindeutigen Werte in einer Spalte.
</details>

### 🟦 Übung E1b — wie viele Trials pro Person?

Gibt es gleich viele Trials bei allen Participants?

<details>
<summary>Lösung E1b</summary>

```python
print(df.groupby("Participant")["newindex"].count())
```

Unterschiede deuten auf abgebrochene Sitzungen oder fehlende Daten hin.
</details>

---

## E2 — Korrektheit

Die Spalte `Result` enthält `"correct"` oder `"incorrect"` als Text. Für Berechnungen wandeln wir sie in 1 und 0 um.

```python
korrekt_liste = []
for wert in df["Result"]:
    if wert == "correct":
        korrekt_liste.append(1)
    else:
        korrekt_liste.append(0)
df["korrekt"] = korrekt_liste

# Anteil korrekter Trials
print(df["korrekt"].mean())   # z.B. 0.85 = 85 % korrekt
```

### 🟦 Übung E2a — Fehlerrate pro Person

Berechne die Fehlerrate getrennt für jede Versuchsperson.

<details>
<summary>Lösung E2a</summary>

```python
fehler = df.groupby("Participant")["korrekt"].mean()
print(1 - fehler)
```
</details>

### 🟦 Übung E2b — Korrektheit nach Kongruenz

Die Spalte `Congruency` ist bereits vorhanden (`"con"` / `"incon"`). Berechne die mittlere Korrektheit getrennt nach Kongruenz.

<details>
<summary>Lösung E2b</summary>

```python
print(df.groupby("Congruency")["korrekt"].mean())
```

Erwartetes Muster: inkongruente Trials haben die niedrigere Korrektheit.
</details>

---

## E3 — Korrektheit visualisieren

```python
sns.barplot(data=df, x="Congruency", y="korrekt",
            order=["con", "incon"])
plt.xlabel("Kongruenz")
plt.ylabel("Anteil korrekt")
plt.title("Korrektheit nach Kongruenz")
plt.ylim(0, 1)
plt.tight_layout()
plt.savefig("korrektheit_kongruenz.png")
plt.show()
```

`order=` legt die Reihenfolge der Balken fest — ohne das sortiert seaborn alphabetisch.

### 🟦 Übung E3a — Korrektheit pro Person

Zeige die Korrektheit für jede Versuchsperson als eigenen Balken.

<details>
<summary>Lösung E3a</summary>

```python
sns.barplot(data=df, x="Participant", y="korrekt")
plt.ylabel("Anteil korrekt")
plt.title("Korrektheit pro Person")
plt.ylim(0, 1)
plt.show()
```
</details>

---

## E4 — Reaktionszeit

RT-Analyse macht nur mit **korrekten Trials** Sinn — bei falschen Antworten ist die RT durch den Fehler verzerrt.

```python
# Nur korrekte Trials
korrekte = df[df["korrekt"] == 1]

# Mittlere RT nach Kongruenz
print(korrekte.groupby("Congruency")["Reaction Time"].mean())
```

### 🟦 Übung E4a — RT-Verteilung

Zeige die Verteilung aller Reaktionszeiten (nur korrekte Trials) als Histogramm.

<details>
<summary>Lösung E4a</summary>

```python
sns.histplot(data=korrekte, x="Reaction Time", bins=30)
plt.xlabel("Reaktionszeit (ms)")
plt.title("RT-Verteilung (korrekte Trials)")
plt.show()
```

Typisch: rechtsschiefe Verteilung und viele schnelle Antworten, langer Schweif nach rechts.
</details>

### 🟨 Übung E4b — Ausreißer entfernen

Reaktionszeiten unter 100 ms sind zu schnell (Vorwegnahme), über 2000 ms wahrscheinlich Ausreißer. Filtere sie heraus und vergleiche die mittlere RT vorher und nachher.

<details>
<summary>Lösung E4b</summary>

```python
print("Mittlere RT vorher:", korrekte["Reaction Time"].mean())

korrekte_clean = korrekte[(korrekte["Reaction Time"] >= 100) &
                           (korrekte["Reaction Time"] <= 2000)]

print("Mittlere RT nachher:", korrekte_clean["Reaction Time"].mean())
print("Trials entfernt:", len(korrekte) - len(korrekte_clean))
```

`&` verknüpft zwei Bedingungen — beide müssen gleichzeitig wahr sein. Die Klammern um jede Bedingung sind Pflicht.
</details>

---

## E5 — RT nach Kongruenz visualisieren

```python
korrekte = df[df["korrekt"] == 1]

sns.barplot(data=korrekte, x="Congruency", y="Reaction Time",
            order=["con", "incon"])
plt.xlabel("Kongruenz")
plt.ylabel("Mittlere Reaktionszeit (ms)")
plt.title("Stroop-Effekt: RT nach Kongruenz")
plt.tight_layout()
plt.savefig("rt_kongruenz.png")
plt.show()
```

### 🟨 Übung E5a — beide Plots nebeneinander

Zeige Korrektheit und RT nebeneinander in einer Grafik.

<details>
<summary>Lösung E5a</summary>

```python
korrekte     = df[df["korrekt"] == 1]
reihenfolge  = ["con", "incon"]

fig, achsen = plt.subplots(1, 2, figsize=(10, 4))

sns.barplot(data=df, x="Congruency", y="korrekt",
            order=reihenfolge, ax=achsen[0])
achsen[0].set_title("Korrektheit")
achsen[0].set_ylim(0, 1)

sns.barplot(data=korrekte, x="Congruency", y="Reaction Time",
            order=reihenfolge, ax=achsen[1])
achsen[1].set_title("Reaktionszeit (nur korrekt)")

plt.tight_layout()
plt.savefig("stroop_ergebnisse.png")
plt.show()
```

`plt.subplots(1, 2)` erzeugt zwei nebeneinanderliegende Achsen. `ax=achsen[0]` und `ax=achsen[1]` sagen seaborn, in welche Achse es zeichnen soll.
</details>

### 🟨 Übung E5b — Effekt pro Person prüfen

Tritt der Stroop-Effekt bei allen Personen auf?

<details>
<summary>Lösung E5b</summary>

```python
korrekte = df[df["korrekt"] == 1]

sns.barplot(data=korrekte, x="Participant", y="Reaction Time",
            hue="Congruency", hue_order=["con", "incon"])
plt.xlabel("Person")
plt.ylabel("Mittlere RT (ms)")
plt.title("Stroop-Effekt pro Person")
plt.tight_layout()
plt.show()
```

`hue="Congruency"` fügt für jede Person zwei Balken ein. So sieht man sofort, ob das Muster konsistent über alle Personen ist.
</details>

### 🟨 Übung E5c — welches Wort macht die meisten Fehler?

Berechne die Fehlerrate getrennt nach dem gezeigten Wort (`Word shown`). Gibt es Unterschiede?

<details>
<summary>Lösung E5c</summary>

```python
fehler = df.groupby("Word shown")["korrekt"].mean()
print(1 - fehler)

sns.barplot(data=df, x="Word shown", y="korrekt")
plt.ylabel("Anteil korrekt")
plt.title("Korrektheit nach gezeigtem Wort")
plt.ylim(0, 1)
plt.show()
```

</details>
