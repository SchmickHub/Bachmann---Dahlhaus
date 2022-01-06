
# **Seitenkanalangriffe und Hardware-Security-Modules**   

Vorläufige Zusammenfassung von Mick Dahlhaus und Daniel Bachmann  

# **Was sind Seitenkanalangriffe?**
Ein Seitenkanalangriff ist eine Methode der Kryptanalyse (dem Gewinnen von Informationen aus verschlüsselten Texten).
Hierbei wird nicht das kryptographische Verfahren selbst angegriffen, sondern seine physiche Implementierung auf einem Endgerät, z.B. einer Chipkarte, Security-Token oder einem HSM (siehe unten).

# **Ein Auszug möglicher Seitenkanalangriffe**

## 1) Simple Power Analysis (SPA)
Eine einfache Poweranalyse beobachtet den Energieverbrauch der Hardware bei der Ausführung des kryptographischen Verfahrens.
Als Beispiel sei hier die Nutzung von RSA gegeben.
Square-and-multiply Operationen bei RSA verbrauchen, je nachdem ob sie nur "squaren" oder "squaren" und "multiplyen", verschiedene Mengen an Energie. Diese kleinen Unterschiede können schon mit einem handelsüblichen Oszilloskop beobachtet werden und ermöglichen Rückschlüsse auf die Eingabe. 

## 2) Differential Power Analysis (DPA) 
Ähnlich wie bei SPA wird hier der Energieverbauch analysiert. Jedoch ist diese Attacke etwas raffinierter und komplexer, da sie eine statistische Analyse über mehrere Schritte des kryptographischen Verfahrens macht.
Deswegen gibt es bei der DPA auch Möglichkeiten zur Fehlerkorrektur und Signalverarbeitung, was ihr die Fähigkeit verleiht, auch Messungen zu analysieren die für die SPA zu ungenau oder verwaschen sind.

## 3) Timing Attack 
Dieser Angriff, schon 1996 von Paul C. Kocher entdeckt, misst die Rechenzeit des implementierten Verfahrens für unterschiedliche Eingaben.
Die Veränderungen in der Zeit ermöglichen dann einen Rückschluss (ähnlich SPA und DPA) auf den verwendeten Schlüssel oder die Eingabedaten. 

## 4) Shared Memory
Spectre/Meltdown

## 5) Cold Boot Attack
Bei der Cold Boot Attack wird das Phänomen der Datenremanenz ausgenutzt. Ladungen in z.B. RAM-Modulen verflüchtigen sich nicht sofort bei der Systemabschaltung, sondern benötigen teilweise Sekunden bis Minuten um das System vollständig zu verlassen.
Eine Kühlung der Speichermodule verstärkt diesen Effekt dramatisch.
So erhält man Zugriff auf sensible Daten, die die extraktion von kryptographischen Schlüsseln ermöglichen.

## 6) Electromagnetic Fault Injection (EMFI)
Sehr punktuelles lautes schreien

## 7) Bug Attack
FDIV

## 8) Sound Analysis
Mit machine learning

## 9) Van-Eck-Phreaking (TEMPEST)
NSA is watching you   