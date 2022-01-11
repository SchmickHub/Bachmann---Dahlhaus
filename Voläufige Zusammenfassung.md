# Seitenkanalangriffe und Hardware-Security-Modules
Vorläufige Zusammenfassung von Mick Dahlhaus und Daniel Bachmann

## Was sind Seitenkanalangriffe?
Ein Seitenkanalangriff ist eine Methode der Kryptanalyse (dem Gewinnen von Informationen aus verschlüsselten Texten).
Hierbei wird nicht das kryptographische Verfahren selbst angegriffen, sondern seine physiche Implementierung auf einem Endgerät, z.B. einer Chipkarte, einem Security-Token oder einem HSM (siehe unten).

## Ein Auszug möglicher Seitenkanalangriffe

### 1) Simple Power Analysis (SPA)
Eine einfache Poweranalyse beobachtet den Energieverbrauch der Hardware bei der Ausführung des kryptographischen Verfahrens.
Als Beispiel sei hier die Nutzung von RSA gegeben.
Square-and-multiply Operationen bei RSA verbrauchen, je nachdem ob sie nur "squaren" oder "squaren" und "multiplyen", verschiedene Mengen an Energie. Diese kleinen Unterschiede können schon mit einem handelsüblichen Oszilloskop beobachtet werden und ermöglichen Rückschlüsse auf die Eingabe. 

### 2) Differential Power Analysis (DPA) 
Ähnlich wie bei SPA wird hier der Energieverbauch analysiert. Jedoch ist diese Attacke etwas raffinierter und komplexer, da sie eine statistische Analyse über mehrere Schritte des kryptographischen Verfahrens macht.
Deswegen gibt es bei der DPA auch Möglichkeiten zur Fehlerkorrektur und Signalverarbeitung, was ihr die Fähigkeit verleiht, auch Messungen zu analysieren die für die SPA zu ungenau oder verwaschen sind.

### 3) Timing Attack 
Dieser Angriff, schon 1996 von Paul C. Kocher entdeckt, misst die Rechenzeit des implementierten Verfahrens für unterschiedliche Eingaben.
Die Veränderungen in der Zeit ermöglichen dann einen Rückschluss (ähnlich SPA und DPA) auf den verwendeten Schlüssel oder die Eingabedaten. 

### 4) Shared Memory
Prozesse die auf demselben Gerät durchgeführt werden, teilen sich möglicherweise diesselben Speicherbereiche (einzelne Register, Cache oder ganze Blöcke).
Hier kann also der benutzte Speicher von einem Prozess Rückschluss auf den anderen Prozess ermöglichen oder sogar Zugriff auf sonst beschränkte Daten erlauben. Ein prominentes Beispiel ist die Sicherheitslücke "Spectre", bei der ausgenutzt wurde, dass der Prozessor durch "Out-of-order" execution (nach Konditionierung durch häufiges Aufrufen einer bestimmten Speicherzelle) Zugriff auf sonst durch Sicherheitsmechanismen geschützte Daten erlaubte.

### 5) Cold Boot Attack
Bei der Cold Boot Attack wird das Phänomen der Datenremanenz ausgenutzt. Ladungen in bspw. RAM-Modulen verflüchtigen sich nicht sofort bei der Systemabschaltung, sondern benötigen teilweise Sekunden bis Minuten um das System vollständig zu verlassen.
Eine Kühlung der Speichermodule verstärkt diesen Effekt dramatisch.
So erhält man Zugriff auf sensible Daten, die die Extraktion von kryptographischen Schlüsseln ermöglichen.

### 6) Electromagnetic Fault Injection (EMFI)
EMFI versucht geziehlt, die Ergbebnisse von Securitychecks zu manipulieren oder sogar ganz zu überspringen. Hierzu wird ein Werkzeug eingesetzt welches das Gerät (sehr punktuell) einer hohen Stromspannung ausetzt. Dies kann persistente Änderungen wie Bitflips in Registern provozieren oder aber kurzeitige Fehler bei Abfragen hervorrufen, die dann ein falsches Ergebnis liefern. So können Sicherheitsroutinen, ohne jemals durchgeführt zu werden, umgangen werden.

### 7) Bug Attack
Eine Bug Attack ziehlt auf die fehlerhafte Implementierung einer Funktion in Mikroprozessoren ab. Ein prominentes Besipiel hierzu ist der Pentium-Fdiv Bug der im November 1994 entdeckt wurde. Ein Bug in der Gleitkomma-Division bei bestimmten Wertpaaren führte zu ungenauen Ergebnissen.
In den meisten Anwendungen wäre ein solcher Bug (es gibt noch viele andere weniger bekannte Beispiele) nicht relevant, jedoch bei kryptographischen Anwendunge wie RSA, dem Pohlig-Hellman-Algorithmus (berechnet den diskreten Logarithmus in eine zyklischen Gruppe) oder der ElGamal Verschlüsselung kann eine einzige falsche Berechnung den Schlüssel preisgeben. 

### 8) Differential Fault Analysis (DFA) 
Ähnlich einer Bug Attack wird hier das Fehlverhalten von Hardware ausgenutzt, jedoch werden keine Fehler der Hersteller oder Ingenieure ausgenutzt, sondern aktiv Fehler von außen hinzugefügt. Angriffsvektoren sind unter anderem: Veränderung der Spannung, Manipulation der Systemuhr, Strahlung oder ein Resetimpuls zum falschen Zeitpunkt.
Derselbe Klartext wird dann einmal unter normalen Bedingung und unter Manipulation von außen verschlüsselt. Die entstandenen Chiffrentexte werden dann verglichen und Unterschiede in den Bits erlauben Rückschlüsse auf Beispielsweise den Schlüssel.
Eine Zerstörung der Hardware ist bei diesem Angriff eine reele Möglichkeit.

### 9) Sound Analysis
Eine Analyse der Betriebsgeräusche (Spuhlenfiepen, Vibration von Bauelementen etc.) kann, ähnlich wie bei der SPA zu Rückschlüssen auf den verwendetet RSA-Schlüssel führen. Hierbei kann schon mit einem handelsüblichen Handymikrofon, das knapp 30 cm von dem Gerät entfernt platziert wurde und die entsprechende Software besitzt, ein 4096-bit RSA-Schlüssel extrahiert werden.

### 10) Van-Eck-Phreaking (Tempest)
Die von einem Gerät produzierte elektromagnetische Strahlung lässt sich noch auf einige Entfernung messen (ca. 100 m) und erlaubt Rückschlüsse auf die durchgeführten Operationen. Besonders hiervon betroffene Geräte sind Computerbildschirme (DVI, HDMI und LCD) und ungeschirmte Datenleitungen.
Dieses Wissen kann genutzt werden um Datenverkehr abzuhören. Besonders das Videosignal kann hier effektiv rekonstruiert werden. Aber auch Stromschwankungen bei unterschiedlichen Operationen in Kombination mit einer SPA oder DPA bieten hier eine große Angriffsfläche.
Das Wort Tempest (ehemals ein Lauschprogramm der NSA das auf diesem Angriff basierte) steht heute für ein Gütesiegel, das genau gegen solche Angriffe schützt.

# Hardware Security Modules (HSM)
## Einleitung

Hardware Security Modules (deutsch: Hardware Sicherheitsmodule, HSM) sind Hardwareprodukte, die kryptographische Operationen in einer sicheren und effizienten Umgebung ermöglichen. Dazu gehört die sichere Erstellung und Verwaltung von Schlüsseln. Außerdem bieten HSMs symmetrische und asymmetrische Ver- und Entschlüsselung, digitale Signaturen, Hash Funktionen und die Generierung von echten Zufallszahlen oder Pseudozufallszahlen.

Das erste HSM wurde 1989 von IBM für militärische Zwecke entwickelt. Anschließend kam es vor allem in ATMs (Automated Teller Machines) zum Einsatz. Heutzutage gibt es viele weitere Anwendungsbereiche.

## Physikalische Sicherheit - Schutz vor Angriffen

#### 1) Power Analysis
In der Firmware der HSMs sind die kryptographischen Verfahren so implementiert, dass die Rechenoperationen immer gleich viel Energie benötigen, wodurch die Power Analysis keine Rückschlüsse auf die Eingaben ziehen kann.  
Eine weitere Schutzmaßnahme sind Kondensatoren, die bei hohem Energieverbrauch die auftretenden Stromspitzen abgefangen, wodurch der erhöhte Energieverbrauch nach außen nicht mehr zu erkennen ist.

#### 2) Timing Attack
Die kryptographischen Algorithmen sind in der Firmware so implementiert, dass die Rechenoperationen eine konstante Dauer haben. Somit führen verschiedene Eingaben zur selben Rechenzeit. Dadurch lassen sich keine Rückschlüsse auf verwendete Schlüssel ziehen.

#### 3) Cold Boot Attack
Die Temperatur des HSMs wird fortlaufend gemessen. Sinkt die Temperatur unter einen bestimmten Wert, kommt es zu einer Nullstellung. Unter einer **Nullstellung** (engl. “Zeroisation”) versteht man das Löschen von sensiblen Parametern (z.B. Schlüsseln) aus einem kryptographischen Modul, um die Offenlegung zu verhindern.

#### 4) physischer Zugang
Um zu verhindern, dass sich Angreifer physischen Zugang zu den einzelnen Komponenten des HSMs  verschaffen (z.B. mithilfe spezieller Werkzeuge oder Säure) und diese anschließend auswerten, bietet ein HSM folgende Maßnahmen:
- Ein Siegel auf dem Deckel des HSMs, welches bei physischem Zugang automatisch zerstört wird und somit einen Angriff sichtbar macht
- Die Platine wird mit einer Vergussmasse aus Harz geschützt. Versucht man das Harz zu entfernen, wird die Platine wahrscheinlich ebenfalls zerstört
- Die Platine kann mit einem Metallkörper geschützt werden, der die einzelnen Komponenten verdeckt. Der Metallkörper kann mit einem Schalter oder einem Lichtsensor kombiniert werden, die eine Nullstellung auslösen, sobald der Metallkörper entfernt wurde, bzw. sobald Licht an die Platine kommt, weil der Körper entfernt wurde
- Das HSM kann mit einer Sensorfolie aus verschränkten Leiterbahnen ummantelt werden. Sobald bei einem Angriff eine Leiterbahn durchtrennt wird, kommt es zur Nullstellung

## Standards

Es gibt verschiedene Standards, die die nötigen Anforderungen an ein HSM regeln. Der wichtigste Standard ist FIPS 140 - 2 (Federal Information Processing Standards) vom NIST (National Institute of Standards and Technology). Er beschreibt die Sicherheitsanforderungen an ein kryptographisches Modul in vier Leveln mit ansteigenden Anforderungen.
1. Level: Es wird mindestens ein korrekt implementierter kryptographischer Algorithmus verwendet
2. Level: Physikalische Angriffe müssen nachweisbar sein. Außerdem ist im Hardwaremodul eine rollenbasierte Authentifizierung der Nutzer implementiert
3. Level: Das Modul bietet einen Schutz, bzw. Widerstand gegen Angriffe. Außerdem werden Angriffe erkannt und anschließend darauf reagiert. Die rollenbasierte Authentifizierung wird durch die Identifizierung der Nutzer erweitert
4. Level: Für das höchste Level benötigt es eine Schutz-Ummantelung des kompletten Moduls, um Angriffe zu erkennen und darauf zu reagieren.  Außerdem muss ein Schutz vor Temperaturen und Spannungen außerhalb des normalen Betriebs vorhanden sein, wodurch Level 4 Module besonders geeignet sind für die Nutzung in einer physisch ungesicherten Umgebung. 

Ein Standard HSM befindet sich in Level 3, fortgeschrittene HSMs bedienen Level 4. Die Anforderungen bezüglich Authentifizierung und Identifizierung der Nutzer eines HSMs zeigen, dass nicht nur physikalische Schutzmaßnahmen wichtig sind. Außerdem ist sichere Infrastruktur wichtig, dazu gehört unter anderem der Schutz der Serverräume, verschlüsselte Kommunikation und strenge Regeln zur Verwendung der Schlüssel. Hierbei handelt es sich jedoch nicht um HSM-spezifische Maßnahmen, sondern um wichtige, allgemeine Sicherheitsmechanismen.

## Anwendungsbereiche
HSMs werden in fast allen Industrien (Automobil-Industrie, Militär, Telekommunikation, Lotterie, etc.) eingesetzt. Hier sind nur zwei Beispiele:

### Banken
Mithilfe von HSMs kontrollieren Banken, ob eine eingegebene PIN mit der PIN übereinstimmt, die die Bank für diese Karte gespeichert hat. Außerdem ermöglich ein HSM die sichere Überprüfung von Kredit-/Debitkarten-Transaktionen durch Kontrolle der Sicherheitscodes der Karten.

### Zertifizierungsstellen
Die Zertifizierungsstellen, die z.B. X.509 Zertifikate ausstellen, nutzen HSMs zum Generieren, Speichern und Verwalten von asymmetrischen Schlüsselpaaren. Somit können die Zertifikate für elektronische Signaturen, Transport Layer Security (TLS) und Weiteres eingesetzt werden.

## Quellenangaben

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
