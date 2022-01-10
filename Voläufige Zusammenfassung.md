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
Eine Bug Attack ziehlt auf die fehlerhafte implementierung einer Funktion in Mikroprozessoren ab. Ein prominentes Besipiel hierzu ist der Pentium-Fdiv Bug der im November 1994 entdeckt wurde. Ein Bug in der Gleitkomma-Division bei bestimmten Wertpaaren führte zu ungenauen Ergebnissen.
In den meisten Anwendungen wäre so ein Bug (es gibt noch viele andere weniger bekannte Beispiele) nicht relevant, jedoch bei kryptographischen Anwendunge wie RSA, dem Pohlig-Hellman-Algorithmus (berechnet den diskreten Logarithmus in eine zyklischen Gruppe) oder der ElGamal Verschlüsselung kann eine einzige falsche Berechnung zu den Schlüssel preisgeben. 

## 8) Sound Analysis
Mit machine learning

## 9) Van-Eck-Phreaking (TEMPEST)
NSA is watching you   

# Hardware Security Modules (HSM)
## Einleitung

Hardware Security Modules (deutsch: Hardware Sicherheitsmodule, HSM) sind Hardware, die kryptographische Operationen in einer sicheren und effizienten Umgebung ermöglicht. Die Kernfunktion eines HSMs besteht in der Erstellung und Verwaltung von Schlüsselmaterial. 
Daraus ergeben sich die Funktionen der symmetrischen und asymmetrischen Ver- und Entschlüsselung, digitale Signaturen, Hash Funktionen und der (True) Random Number Generation.  

Ein HSM tritt üblicherweise in Form einer PCI Karte auf, es gibt jedoch weitere Formen. Das erste HSM wurde 1989 von IBM für militärische Zwecke entwickelt. Anschließend kam es vor allem in ATMs (Automated Teller Machines) zum Einsatz, heutzutage ist es in fast jeder Industrie zu finden.

## Standards

Es gibt verschiedene Standards, die die nötigen Anforderungen an ein HSM regeln. Der wichtigste Standard ist FIPS 140 - 2 (Federal Information Processing Standards) vom NIST (National Institute of Standards and Technology). Er beschreibt die Sicherheitsanforderungen an ein kryptographisches Modul in vier Leveln mit ansteigenden Anforderungen.  
In Level 1 wird die korrekte Implementierung der kryptographischen Algorithmen in der Software des HSMs gefordert.  
In Level 2 müssen physische Angriffe nachweisbar sein.  
Ein Standard HSM befindet sich in Level 3. Hier muss ein Schutz (Widerstand), die Erkennung und die Reaktion auf Angriffe gewährleistet sein.  
Fortgeschrittene HSMs erfüllen Level 4. Dafür ist eine Schutz-Ummantelung des kompletten Moduls vorgeschrieben, um Angriffe zu erkennen und darauf zu reagieren. Außerdem muss ein Schutz vor Temperaturen und Spannungen außerhalb des normalen Betriebs vorhanden sein, wodurch Level 4 Module besonders geeignet sind für die Nutzung in einer physisch ungesicherten Umgebung.   
Weitere anerkannte Standards sind Common Criteria und PCI HSM (Payment Card Industry).


## Sicherheit

### Physische Sicherheit - Schutz vor Angriffen
Vorab eine Definition: Unter einer Nullstellung (engl. "Zeroisation") versteht man das Löschen von sensiblen Parametern (z.B. Schlüsseln) aus einem kryptographischen Modul, um die Offenlegung zu verhindern.  
Gegen mögliche Angriffe bietet ein HSM folgende Maßnahmen:
#### 1) Power Analysis
Die beim hohen Energieverbrauch entstehenden Stromspitzen werden durch Kondensatoren im HSM abgefangen, wodurch der erhöhte Energieverbrauch nach außen nicht mehr zu erkennen ist.

#### 2) Timing Attack
Die kryptographischen Algorithmen sind in der Firmware so implementiert, dass die Rechenoperationen eine konstante Dauer haben. Somit führen verschiedene Eingaben zur selben Rechenzeit.

#### 3) Cold Boot Attack
Die Temperatur des HSMs wird fortlaufend gemessen. Sinkt die Temperaratur unter einen bestimmten Wert, kommt es zu einer Nullstellung.

#### 4) physischer Zugang
Ein Angreifer kann auch versuchen, sich direkten Zugang zu den einzelnen Komponenten des HSM zu verschaffen, um diese auswerten zu können. Dazu können spezielle Werkzeuge oder Säure verwendet werden.  
Ein HSM kann folgende Maßnahmen für einen solchen Angriff bieten: 
- Ein Siegel auf dem Deckel des HSMs, welches bei physischem Zugang automatisch zerstört wird und somit einen Angriff sichtbar macht
- Die Platine wird mit einer Vergussmasse aus Harz schützt. Versucht man das Harz zu entfernen, wird die Platine wahrscheinlich zerstört
- Die Platine kann mit einem Metallkörper geschützt werden, der die einzelnen Komponenten verdeckt. Der Metallkörper kann mit einem Schalter oder einem Lichtsensor kombiniert werden, die eine Nullstellung auslösen, sobald der Metallkörper entfernt wurde, bzw. sobald Licht an die Platine kommt.
- Das HSM kann mit einer Sensorfolie aus verschränkten Leiterbahnen ummantelt werden. Sobald bei einem Angriff eine Leiterbahn durchtrennt wird, kommt es zur Nullstellung

### Logische Sicherheit
Um das HSM zusätzlich vor Angriffen zu schützen und diese vor allem zu verhindern, ist logische Sicherheit wichtig. Dazu gehört zum Beispiel der Schutz der Serverräume durch Zugangskontrollen.  
Außerdem gibt es rollenbasierte Rechte eines HSM-Nutzers. Dabei hat ein normaler HSM Nutzer weniger Rechte als ein Administrator. Jeder Nutzer muss sich einer strengen Authentifizierung unterziehen, dafür kann zum Beispiel eine 2-Faktor-Authentifizierung verwendet werden.  
Selbst wenn die Authentifizierung erfolgreich war, gibt es noch Sicherheitsmechanismen. So müssen folgenschwere Handlungen (wie das Löschen von Schlüsseln) zum Beispiel von 3 von insgesamt 5 Administratoren (allgemein M-out-of-N-Authorization) bestätigt werden. Hinzu kommen regelmäßige Firmware-Updates und strenge Regeln zur Verwendung der Schlüssel.  
Wichtig ist außerdem, dass jegliche Kommunikation mit dem HSM verschlüsselt ist, sodass jeglicher unverschlüsselter Inhalt nur im HSM verarbeitet wird.

## Anwendungsbereiche

### Banken
Banken verwenden spezielle HSMs, die eigene Standards erfüllen. Mithilfe der HSMs wird zum Beispiel kontrolliert, ob eine eingegebene PIN mit der PIN übereinstimmt, die die Bank für diese Karte gespeichert hat. Außerdem ermöglich ein HSM die sichere Überprüfung von Kredit-/Debitkartentransaktionen durch Kontrolle der Sicherheitscodes der Karten.

### Zertifizierungsstellen
Die Zertifizierungsstellen, die z.B. die X.509 Zertifikate austellen, nutzen HSMs zum Generieren, Speichern und Verwalten von asymmetrischen Schlüsselpaaren. Somit können die Zertifikate für elektronische Signaturen, Transport Layer Security (TLS) und Weiteres eingesetzt werden.

### weitere Industrien
Darüber hinaus werden HSMs unter anderem in der Autombil-Industrie, Telekommunikation, Lotterie und im Militär eingesetzt.

