# Seitenkanalangriffe und Hardware-Security-Modules
Zusammenfassung von Mick Dahlhaus und Daniel Bachmann

## Was sind Seitenkanalangriffe?
Ein Seitenkanalangriff ist eine Methode der Kryptanalyse (dem Gewinnen von Informationen aus verschlüsselten Texten, ohne den Schlüssel zu besitzen).
Hierbei wird nicht das kryptographische Verfahren selbst angegriffen, sondern seine physische Implementierung auf einem Endgerät, bspw. einer Chipkarte, einem Security-Token oder einem HSM (siehe unten).
Bei Seitenkanalangriffen gibt es eine Vielzahl von Angriffsvektoren, wie z.B. eine Kamera welche auf das Pineingabefeld gerichtet ist, einen Keylogger der den Klartext direkt bei der Eingabe abfängt oder das aufgeschriebene Passwort was am Bildschirmrand klebt. Im Folgenden werden einige nicht-triviale Angriffsvektoren erläutert, welche aber nur den Tellerrand dieses Themas darstellen.

## Passive Angriffe
Ein passiver Angriff stört nicht den Ablauf des kryptographischen Verfahrens und gewinnt meist Informationen aus der Kombination des Ergebnisses einer Analyse und Informationen über die jeweilige Eingabe oder Zustand des verwendeten Verfahrens.

### 1) Simple Power Analysis (SPA)
Eine einfache Poweranalyse beobachtet den Energieverbrauch der Hardware bei der Ausführung des kryptographischen Verfahrens.
Als Beispiel sei hier die Nutzung von RSA gegeben.
Square-and-multiply Operationen bei RSA verbrauchen, je nachdem ob sie nur "squaren" oder "squaren" und "multiplyen", verschiedene Mengen an Energie. Diese kleinen Unterschiede können schon mit einem handelsüblichen Oszilloskop beobachtet werden und ermöglichen Rückschlüsse auf die Eingabe. 

### 2) Differential Power Analysis (DPA) 
Ähnlich wie bei SPA wird hier der Energieverbrauch analysiert. Jedoch ist diese Attacke etwas raffinierter und komplexer, da sie eine statistische Analyse über mehrere Schritte des kryptographischen Verfahrens macht.
Deswegen gibt es bei der DPA auch Möglichkeiten zur Fehlerkorrektur und Signalverarbeitung, was ihr die Fähigkeit verleiht, auch Messungen zu analysieren, die für die SPA zu ungenau oder verwaschen sind.

### 3) Sound Analysis
Eine Analyse der Betriebsgeräusche (Spulenfiepen, Vibration von Bauelementen etc.) kann, ähnlich wie bei der SPA, zu Rückschlüssen auf den verwendetet RSA-Schlüssel führen. Hierbei kann schon mit einem handelsüblichen Handymikrofon, das knapp 30 cm von dem Gerät entfernt platziert wurde und die entsprechende Software besitzt, ein ansonten sicherer RSA-Schlüssel extrahiert werden.

### 4) Timing Attack 
Dieser Angriff misst die Rechenzeit des implementierten Verfahrens für unterschiedliche Eingaben.
Die Veränderungen in der Zeit ermöglichen dann einen Rückschluss (ähnlich SPA und DPA) auf den verwendeten Schlüssel oder die Eingabedaten.

### 5) Van-Eck-Phreaking (Tempest)
Die von einem Gerät produzierte elektromagnetische Strahlung lässt sich noch auf einige Entfernung messen (ca. 100 m) und erlaubt Rückschlüsse auf die durchgeführten Operationen. Besonders hiervon betroffene Geräte sind Computerbildschirme (DVI, HDMI und LCD) und ungeschirmte Datenleitungen.
Dieses Wissen kann genutzt werden, um Datenverkehr am Endgerät abzuhören. Besonders das Videosignal kann hier effektiv rekonstruiert werden. Aber auch Stromschwankungen bei unterschiedlichen Operationen in Kombination mit einer SPA oder DPA bieten hier eine große Angriffsfläche.
Das Wort Tempest (ehemals ein Lauschprogramm der NSA) steht heute für ein Gütesiegel, das genau gegen solche Angriffe schützt.

### 6) Shared Memory
Prozesse, die auf demselben Gerät durchgeführt werden, teilen sich möglicherweise dieselben Speicherbereiche (einzelne Register, Cache oder ganze Blöcke).
Hier kann also der benutzte Speicher von einem Prozess Rückschluss auf den anderen Prozess ermöglichen oder sogar Zugriff auf sonst beschränkte Daten erlauben. Ein prominentes Beispiel ist die Sicherheitslücke "Spectre", bei der ausgenutzt wurde, dass der Prozessor durch "Out-of-order" execution (nach Konditionierung durch häufiges Aufrufen einer bestimmten Speicherzelle) Zugriff auf sonst durch Sicherheitsmechanismen geschützte Daten erlaubte.

### 7) Bug Attack
Eine Bug Attack zielt auf die fehlerhafte Implementierung einer Funktion in Mikroprozessoren ab.
In den meisten Anwendungen ist ein solcher Bug nicht relevant, bei kryptographischen Anwendungen wie RSA oder der ElGamal Verschlüsselung kann jedoch eine einzige falsche Berechnung den Schlüssel preisgeben.  

## Aktive Angriffe
Ein aktiver Angriff stört oder manipuliert das Verfahren von außen, um so Fehler zu provozieren, die Rückschlüsse auf den verwendeten Schlüssel ermöglichen.

### 1) Differential Fault Analysis (DFA) 
Ähnlich einer Bug Attack wird hier das Fehlverhalten von Hardware ausgenutzt, jedoch werden keine Fehler der Hersteller oder Ingenieure ausgenutzt, sondern aktiv Fehler von außen hinzugefügt. Angriffsvektoren sind unter anderem: Veränderung der Spannung, Manipulation der Systemuhr, Strahlung oder ein Resetimpuls zum falschen Zeitpunkt.
Derselbe Klartext wird dann einmal unter normalen Bedingungen und einmal unter Manipulation von außen verschlüsselt. Die entstandenen Chiffrentexte werden dann verglichen und Unterschiede in den Bits erlauben Rückschlüsse auf bspw. den Schlüssel.
Eine Zerstörung der Hardware ist bei diesem Angriff eine reelle Möglichkeit.

### 2) Electromagnetic Fault Injection (EMFI)
EMFI versucht gezielt, die Ergebnisse von Securitychecks zu manipulieren oder sogar ganz zu überspringen. Hierzu wird ein Werkzeug eingesetzt, welches das Gerät (sehr punktuell) einer hohen Stromspannung aussetzt. Dies kann persistente Änderungen wie Bitflips in Registern provozieren oder aber kurzzeitige Fehler bei Abfragen hervorrufen, die dann ein falsches Ergebnis liefern. So können Sicherheitsroutinen, ohne jemals durchgeführt zu werden, umgangen werden.

### 3) Cold Boot Attack
Bei der Cold Boot Attack wird das Phänomen der Datenremanenz ausgenutzt. Ladungen in bspw. RAM-Modulen verflüchtigen sich nicht sofort bei der Systemabschaltung, sondern benötigen teilweise Sekunden bis Minuten, um das System vollständig zu verlassen.
Eine Kühlung der Speichermodule verstärkt diesen Effekt dramatisch.
Nun muss man sich noch Zugriff auf diese Daten beschaffen, indem man die Speicherelemente aus dem System entfernt oder ausliest. Die Analyse dieser so gewonnenen Daten kann Rückschlüsse auf den verwendeten Schlüssel ermöglichen.

# Hardware Security Modules (HSM)
## Einleitung


Hardware Security Modules (deutsch: Hardware Sicherheitsmodule, HSM) sind Hardware, die kryptographische Operationen in einer sicheren und effizienten Umgebung ermöglichen. Die Kernfunktion eines HSMs besteht in der Erstellung und Verwaltung von Schlüsselmaterial. 
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
Vorab eine Definition: Unter einer **Nullstellung** (engl. "Zeroisation") versteht man das Löschen von sensiblen Parametern (z.B. Schlüsseln) aus einem kryptographischen Modul, um die Offenlegung zu verhindern.  

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
Um das HSM zusätzlich vor Angriffen zu schützen und diese vor allem zu verhindern, ist logische Sicherheit wichtig. Dazu gehört z.B. der Schutz der Serverräume durch Zugangskontrollen.  
Außerdem gibt es rollenbasierte Rechte eines HSM-Nutzers. Dabei hat ein normaler HSM Nutzer weniger Rechte als ein Administrator. Jeder Nutzer muss sich einer strengen Authentifizierung unterziehen, dafür kann z.B eine 2-Faktor-Authentifizierung verwendet werden.  
Selbst wenn die Authentifizierung erfolgreich war, gibt es noch Sicherheitsmechanismen. So müssen folgenschwere Handlungen (wie das Löschen von Schlüsseln) z.B. von 3 von insgesamt 5 Administratoren (allgemein M-out-of-N-Authorization) bestätigt werden. Hinzu kommen regelmäßige Firmware-Updates und strenge Regeln zur Verwendung der Schlüssel.  
Wichtig ist außerdem, dass jegliche Kommunikation mit dem HSM verschlüsselt ist, sodass jeglicher unverschlüsselter Inhalt nur im HSM verarbeitet wird.

## Anwendungsbereiche

### Banken
Banken verwenden spezielle HSMs, die eigene Standards erfüllen. Mithilfe der HSMs wird zum Beispiel kontrolliert, ob eine eingegebene PIN mit der PIN übereinstimmt, die die Bank für diese Karte gespeichert hat. Außerdem ermöglich ein HSM die sichere Überprüfung von Kredit-/Debitkartentransaktionen durch Kontrolle der Sicherheitscodes der Karten.

### Zertifizierungsstellen
Die Zertifizierungsstellen, die z.B. die X.509 Zertifikate austellen, nutzen HSMs zum Generieren, Speichern und Verwalten von asymmetrischen Schlüsselpaaren. Somit können die Zertifikate für elektronische Signaturen, Transport Layer Security (TLS) und Weiteres eingesetzt werden.

### weitere Industrien
Darüber hinaus werden HSMs unter anderem in der Autombil-Industrie, Telekommunikation, Lotterie und im Militär eingesetzt.

## Quellenangaben GEHÖRT NICHT HIEREIN

https://de.wikipedia.org/wiki/Seitenkanalattacke

https://github.com/phonchi/awesome-side-channel-attack#side-channel-attack

https://circuitcellar.com/research-design-hub/electromagnetic-fault-injection/

https://de.wikipedia.org/wiki/Spectre_(Sicherheitsl%C3%BCcke)

https://blog.f-secure.com/cold-boot-attacks/

https://de.wikipedia.org/wiki/Kaltstartattacke

https://de.wikipedia.org/wiki/Van-Eck-Phreaking

https://www.heise.de/security/meldung/l-f-Hackerin-demonstiert-Van-Eck-Phreaking-trotz-HDMI-4123699.html

http://www.cs.tau.ac.il/~tromer/acoustic/

https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Informationen-und-Empfehlungen/Kryptografie/Seitenkanalresistenz/seitenkanalresistenz_node.html

https://www.security-insider.de/was-ist-ein-hardware-sicherheitsmodul-hsm-a-727090/

https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf

https://application.wiley-vch.de/HSM_for_Dummies_html/page_1.html

https://en.wikipedia.org/wiki/Hardware_security_module

https://en.wikipedia.org/wiki/Zeroisation
