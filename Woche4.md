# Woche 4: Python Grundlagen IV  
**Funktionen, Modularisierung und saubere Experimentlogik**

---

## Lernziele

- Eigene Funktionen schreiben  
- Parameter und Rückgabewerte verstehen  
- Code modular strukturieren  
- Wiederverwendbare Trial-Logik entwickeln  
- Einfache experimentelle Abläufe kapseln  
- Typische Designfehler vermeiden  

---

## 0. Orientierung: Warum Funktionen im Experiment entscheidend sind

In EEG-Experimenten wiederholen sich ständig dieselben Abläufe:

- Stimulus präsentieren  
- Reaktion erfassen  
- Reaktionszeit bewerten  
- Trial speichern  

Ohne Funktionen:

❌ viel doppelter Code  
❌ schwer zu debuggen  
❌ fehleranfällig  

Mit Funktionen:

✅ klar strukturierter Code  
✅ wiederverwendbare Bausteine  
✅ bessere Kontrolle über Experimente  

---

## 1. Funktionen: Grundprinzip

Eine Funktion ist ein **benannter Codeblock**, der eine Aufgabe erfüllt.

---

### 1.1 Einfache Funktion

```python
def begruessung():
    print("Experiment startet")
```
