# Oppskrift:

Installer server på en ren installasjon med cd key:
Denne nøkkelen er til server 2012 R2 datacenter, den lisensen jeg egentlig skulle brukt er; windows server 2012 R2 foundation.
Bruk elev-pcer som klienmaskiner, det er viktig og huske og melde de inn i rett domene når serveren er satt opp.

## Installasjon & konfigurering

### Vindu1, settings
Username: Administrator
Password: Fagpr0ve!
Reenter Password: Fagpr0ve!

## Oppsett av ruter

Resett ruter på baksiden, hold inn til power lampen blinker.
Logg inn på ruter fra siden router.asus.com (evt bruk default ip som er 192.168.0.1)

Sett denne opp med IP addresse som er untagget i veggen, når jeg gjorde dette på kontoret brukte jeg f.eks (10.164.1.100)
Dette fordi ruteren var koblet i veggen som var untagget på vlan 1 (server felles)

Satt opp trådløsnettverk med info:
2.4Ghz fagprove2019
2.4Ghz Trådløs sikkerhet: Fagpr0ve!

5Ghz fagprove2019
5Ghz Trådløs sikkerhet: Fagpr0ve!

Etter dette kommer ruter siden opp, her er det viktig og skru av DHCP siden vi skal legge inn vår egen som en server tjeneste.
Opplevde at når jeg setter statisk IP etter DHCP er deaktivert så må jeg sette statisk IP på serveren til:
IPV4:
IP: 192.168.1.2
Subnet: 255.255.255.0
Gateway: 192.168.1.1

DNS:
192.168.1.1
127.0.0.1

### MERK denne ip addressen er på ett annet VLAN en default IP addressen til ruteren.

For og logge inn på ruteren etterpå nå kan man bruke nett addressen router.asus.com
Logg inn info:
-Bruker: admin
-Passord: admin
Bytt dette til ett annet passord!

### Gjestenett:
Gå til gjestenett under generelt, legg inn nettverksnavn, sett tilgangstid til 1 dag.

### Oppstart
Etter oppstart det første som må gjøres:
Bytt pc navn: fagprove2019
Sett IP til dynamisk slik at den kan få kontakt med ruter.
Installer Chrome fra minnepenn

# Windows settings, roller:

### Kjør Windows Update!

Partioner disken slik at windows installasjonen har 100GB ledig
Og 1 ekstra partion med resterende GB som du kaller for Data, det er her vi legger våre shares.
Dette gjøres slik at ikke C disken skal eventuelt fylles opp og skape problemer.

I min installasjon trenger jeg roller for:
- DHCP
- DNS
- Active Directory Domain Services
- Print and document services

## Active Directory Domain Services
- Start med AD Domain Services
- Oppgrader til domain controller
- Legg til i ny skog (Add a new forest) i min test bedrift valgte jeg kolos.local.
- Legg inn DSRM passord, i min test bedrift valgte jeg samme som admin passordet (Fagpr0ve!)
- Neste neste neste
- Husk og oppgrader serveren til Domain Controller, dette gjøres ved å trykke på utropstegnet øverst i server manageren!
- Dette er fordi at da settes DNS opp automatisk slik at man slipper og gjøre det selv.

## OU og grupper
- Først legg til OU med navn grupper og ett med navn brukere
- Legg så til grupper alt ettersom hva oppgaven sier

## Brukere
- Kjør legg til brukere powershell script
- Dobbeltsjekk at brukerne er blitt lagt inn i rett grupper.

## DHCP
- Legg til DHCP fra add roles and features.
- (Må konfigureres )
- Åpne DHCP under tools
- Høyreklikk på IPV4 og velg nytt scope
- Skriv inn navn på DHCP (i mitt tilfelle valgte jeg brukere, med description: IP addresser til klient maskiner)
- Valgte IP range: 192.168.1.100 - 192.168.1.254, med en netmask på 255.255.255.0.
- Dette gjør at jeg får frigjort IP addresser fra 192.168.1.3 192.168.100

## Print and document services
- Installer printer driver fra USB penn slik at den kommer opp i listen senere.
- Åpne Tools & Print Management
- Gå til Printer servers, printers, høyreklikk og legg til printer.
- Søk på nettverket etter skriver eller skriv inn IP addressen direkte.
- Legg til driver skriver som ble installert fra exe fil på usb penn tidligere(den skal nå være i listen kalt EPSON)
### Push ut skriver med GPO
- Høyreklikk på skriver og velg Deploy with group policy
- Browse, og høyreklikk og lag en ny policy kalt Printer & velg denne og trykk OK.
- Kryss av for "The computer that this GPO applies to (per machine)" og trykk Add.

*For video på GPO: Windows server 2012 - How to Deploy Network Printer by using GPO.*

## Shares
- Legg til en mappe på egen disk kalt det du vil den skal være, f.eks Felles, Applikasjoner, Home osv.
- Høyreklikk på mappen og velg share with spesific people, her fjerner du alle og velger everyone.
- Når du har gjort det høyreklikker du en gang til på mappen og velger properties og går til security.
- Hær kan vi trykke på edit for å legge til grupper som skal ha rettigheter til mappen, for og gi forskjellige rettigheter krysser man av boksene for f.eks lese, skrive, tilganger.
- Når du skal teste sharet er det VIKTIG! og bruke \\servernavnet\Felles f.eks, IKKE FQDN navnet som kan være f.eks kolos.local\Felles.
- Deretter kan man bruke GPO til å pushe disse ut til klientmaskinene.

## Hjem Mapper
- Disse fungerer litt på samme måte som Shares og lages på samme måte, men trengs litt konfigurering.
- Først lag en mappe med navn Home på Datadisken
- Høyreklikk og velg share with spesific people, legg til Domain Users i denne listen og sørg for at de har lestetilgang ikke noe mer.
-

## Policies

###Account Policies

####Passord####
- Det kan være greit og gå igjennom passord policies, man trenger ikke lage ny policy det eksisterer allerede en policy som heter Default Domain Policy velg denne og trykk edit
- Du finner passord policies under Policies,Windows Settings, Security Settings, Account Policies.
- Sett maximum password age til 90 days
- Minimum Passwordlength til 10
- Resten kan stå som default

####Account Lockout Policy####
- Sett account lockout threshold til 5
- Account lockout duration + reset account lockout counter after: til 15 min.



###
