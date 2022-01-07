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
Prozesse die auf demselben Gerät durchgeführt werden, teilen sich möglicherweise diesselben Speicherbereiche (einzelne Register, Chache oder ganze Blöcke).
Hier kann also der benutze Speicher von einem Prozess Rückschluss auf den anderen Prozess ermöglichen oder sogar Zugriff auf sonst beschränkte Daten erlauben. Ein prominentes Beispiel ist die Sicherheitslücke "Spectre", bei dem ausgenutzt wurde das der Prozessor durch "Out-of-order" execution (nach Konditionierung durch häufiges Aufrufen einer bestimmten Speicherzelle), Zugriff auf sonst durch Sicherheitsmechanismen geschützte Daten erlaubte.

## 5) Cold Boot Attack
Bei der Cold Boot Attack wird das Phänomen der Datenremanenz ausgenutzt. Ladungen in z.B. RAM-Modulen verflüchtigen sich nicht sofort bei der Systemabschaltung, sondern benötigen teilweise Sekunden bis Minuten um das System vollständig zu verlassen.
Eine Kühlung der Speichermodule verstärkt diesen Effekt dramatisch.
So erhält man Zugriff auf sensible Daten, die die extraktion von kryptographischen Schlüsseln ermöglichen.

## 6) Electromagnetic Fault Injection (EMFI)
EMFI versucht geziehlt die Ergbebnisse von Securitychecks zu manipulieren oder sogar ganz zu überspringen. Hierzu wird ein Werkzeug eingesetzt welches das Gerät (sehr punktuell) einer hohen Stromspannung ausetzt. Dies kann persistente änderungen wie Bitflips in Registern provozieren oder aber kurzeitige Fehler bei Abfragen hervorrufen, die dann ein falsches Ergebnis liefern. So können Sicherheitsroutinen, ohne jemals durchgeführt zu werden, umgangen werden.

## 7) Bug Attack
Eine Bug Attack ziehlt auf die fehlerhafte implementierung einer Funktion in Mikroprozessoren ab. Ein prominentes Besipiel hierzu ist der Pentium-Fdiv Bug der im November 1994 entdeckt wurde. Ein Bug in der Gleitkomma-Division bei bestimmten Wertpaaren führte zu ungenauen Ergebnissen.
In den meisten Anwendungen wäre so ein Bug (es gibt noch viele andere weniger bekannte Beispiele) nicht relevant, jedoch bei kryptographischen Anwendunge wie RSA, dem Pohlig-Hellman-Algorithmus (berechnet den diskreten Logarithmus in eine zyklischen Gruppe) oder der ElGamal Verschlüsselung kann eine einzige falsche Berechnung zu den Schlüssel preisgeben. 

## 8) Differential Fault Analysis (DFA) 
Ähnlich einer Bug Attack wird hier das Fehlverhalten von Hardware ausgenutzt, jedoch werden keine Fehler der Hersteller oder Ingenieure ausgenutzt, sondern aktiv Fehler von außen hinzugefügt. Angriffsvektoren sind unter anderem: veränderung der Spannung, manipulation der Systemuhr, Strahlung oder ein Resetimpuls zum falschen Zeitpunkt.
Derselbe Klartext wird dann einmal unter normalen Bedingung und unter Manipulation von außen verschlüsselt. Die entstandenen Chiffrentexte werden dann verglichen und Unterschiede in den Bits erlauben Rückschlüsse auf Beispielsweise den Schlüssel.
Eine zerstörung der Hardware ist bei diesem Angriff eine reele Möglichkeit.

## 8) Sound Analysis
Eine Analyse der Betriebsgeräuschen (Spuhlenfiepen, Vibration von Bauelementen etc.) kann, ähnlich wie bei der SPA zu Rückschlüssen auf den verwendetet RSA-Schlüssel führen. Hierbei kann schon mit einem handelsüblichen Handymikrofon, das kanpp 30 cm von dem Gerät platziert wurde und die entsprechende Software besitzt, ein 4096-bit RSA-Schlüssel extrahiert werden.

## 9) Van-Eck-Phreaking (Tempest)
Die von eine Gerät produzierte Elektormagnetische Strahlung lässt sich noch auf einige Entfernung messen (ca. 100 m) und erlaubt Rückschlüsse auf die durchgeführten Operationen. Besonders hiervon betroffene Geräte sind Computerbildschirme (DVI, HDMI und LCD) und ungeschirmte Datenleitungen.
Dieser Fakt kann genutzt werden um Datenverkehr abzuhören. Besonders das Videosignal kann hier effektiv rekonstruiert werden. Aber auch Stromschwankungen bei unterschiedlichen Operationen in Kombination mit einer SPA oder DPA bieten hier eine große Angriffsfläche.
Das Wort Tempest (ehemals ein Lauschprogramm der NSA das auf diesen Angriff basierte) steht heute für ein Gütesiegel, das genau gegen solche Angriffe schützt.

# Hardware Security Modules (HSM)
## Einleitung
- deutsch: Hardware Sicherheits Module
- Hardware, die kryptografische Operationen in einer sicheren und effizienten Umgebung ermöglicht
- Erstellung und Verwaltung von Schlüsselmaterial
- Funktionen:
  + Symmetrische Ver- und Entschlüsselung
  + Asymetrische Ver- und Entschlüsselung
  + Signaturen
  + Hash Funktionen
  + True/Random Number Generator
- Standardmäßig als PCI Karte
- Erstes HSM für militärische Zwecke von IBM entwickelt (1989)
- Anschließend vor allem für ATMs, heutzutage in fast allen Industrien


## Standards

- Federal Information Processing Standards (FIPS 140 - 2) vom National Institute of Standards and Technology (NIST)
  + Level 1: Korrekte Implementierung in Software
  + Level 2: Nachweisbarkeit von Angriffen
  + Level 3 (Standard HSM): Schutz, Erkennung und Reaktion auf Angriffe
  + Level 4:
    + Schutz Ummantelung des kompletten HSMs, um Angriffe zu erkennen und darauf zu reagieren
    + Schutz vor Temperaturen und Spannungen außerhalb des normalen Betriebs
    + geeignet für Nutzung in einer physisch ungesicherten Umgebung
- Außerdem Common riteria und Payment Card Industry

## Sicherheit

### Physische Sicherheit
- Nachweisbarkeit von Angriffen: z.B. Siegel auf dem Deckel des HSMs, welches bei physischem Zugang zerstört wird
- Definition Nullstellung (engl. Zeroisation): Löschen von sensiblen Parametern (Schlüsseln) aus einem kryptographischen Modul, um die Offenlegung zu verhindern
- Schutz vor Angriffen:
  + Platine wird mit Vergussmasse aus Harz geschützt
    + Visueller Schutz gegen Oszilloskop
    + Bei Entfernung wird die Platine zerstört
  + Metallkörper, der auf der Platine sitzt und einzelne Komponenten schützt
- Erkennung und Reaktion auf Angriffe:
  + Lichtsensor: Kommt Licht an die Platine -> Nullstellung
  + Deckelschalter: Wurde Schutzkörper entfernt -> Nullstellung
- Umsetzung der Level 4 Anforderungen:
  + Ummantelung: Sensorfolie mit verschränkten Leiterbahnen. Wird eine Leiterbahn durchtrennt -> Nullstellung
  + Angriffe mit Temperatur oder Spannung
    + Einfrieren der Speichermodule -> interne Ladung wird aufrecht erhalten
    + Hohe Temperatur oder zu hohe Spannung: Fehlverhalten des HSMs wird wahrscheinlicher
  + Schutz: Temperatur oder Spannungsversorgung wird gemessen. Zu hohe oder zu niedrige Werte -> Nullstellung

### Logische Sicherheit

- Schutz der Serverräume (Zugangskontrollen, etc.)
- Rollenbasierte Rechte eines HSM-Nutzers (normaler Nutzer vs. Administrator)
- Strenge Authentifizierung der Nutzer
  - Chip Karte und Kartenlesegerät mit PIN zu Erzeugung eines temporären Passworts
  - 2-Faktor-Authentifizierung
- Strenge Autorisierung von Handlungen
  - Vier-Augen-Prinzip
  - M-out-of-N-Autorisierung
- Regelmäßige Firmware Updates
- Wofür und wie lange darf ein Schlüssel genutzt werden
- Sämtliche Kommunikation mit dem HSM muss verschlüsselt stattfinden, damit unverschlüsselte Daten nur im HSM verarbeitet werden

## Anwendungsbereiche

### Banken
- Kontrolle, ob eine eingegebene PIN mit der PIN übereinstimmt, die die Bank für die Karte gespeichert hat
- Überprüfung von Kredit-/Debitkartentransaktionen durch Kontrolle der Sicherheitscodes der Karten

### Zertifizierungsstellen
- X.509 - Zertifizierungsstellen nutzen HSMs zum Generieren, Speichern und Verwalten von asymmetrischen Schlüsselpaaren
  - elektronische Signaturen
  - Transport Layer Security (TLS)

### weitere Industrien
- Militär
- Automobil
- Telekommunikation
- Lotterie
- etc.

# Quellenangabe

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
