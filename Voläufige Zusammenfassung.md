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


##Standards

- Federal Information Processing Standards (FIPS 140 - 2) vom National Institute of Standards and Technology (NIST)
    + Level 1: Korrekte Implementierung in Software
    + Level 2: Nachweisbarkeit von Angriffen
    + Level 3 (Standard HSM): Schutz, Erkennung und Reaktion auf Angriffe
    + Level 4: 
      + Schutz Ummantelung des kompletten HSMs, um Angriffe zu erkennen und darauf zu reagieren
      + Schutz vor Temperaturen und Spannungen außerhalb des normalen Betriebs
      + geeignet für Nutzung in einer physisch ungesicherten Umgebung
- Außerdem Common Criteria und Payment Card Industry

##Sicherheit

###Physische Sicherheit
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
  
###Logische Sicherheit

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

##Anwendungsbereiche

###Banken
- Kontrolle, ob eine eingegebene PIN mit der PIN übereinstimmt, die die Bank für die Karte gespeichert hat
- Überprüfung von Kredit-/Debitkartentransaktionen durch Kontrolle der Sicherheitscodes der Karten

###Zertifizierungsstellen
- X.509 - Zertifizierungsstellen nutzen HSMs zum Generieren, Speichern und Verwalten von asymmetrischen Schlüsselpaaren
  - elektronische Signaturen
  - Transport Layer Security (TLS)

###weitere Industrien
- Militär
- Automobil
- Telekommunikation
- Lotterie
- etc.

