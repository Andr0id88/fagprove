# Fagprøve IKT 2019
![Forside](https://proxy.duckduckgo.com/iu/?u=http%3A%2F%2Fwww.kinyu-z.net%2Fdata%2Fwallpapers%2F59%2F907086.jpg&f=1)

---

# Viktig og huske på:

Vedrørende fagprøven:

- Planlegg. Ta med alt av verktøy og hva du trenger for å gjøre jobben. Eventuelle endringer fra planlegging til selve oppgavegjennomføringen gjør ingenting. Bemerk eventuelle endringer.

- HMS! Les HMS, fokuser på HMS. Alt fra viftestøy, ergonomi, sikkerhet for deg selv, utstyret mm.

- Risikoanalyse, ikke kjøp ting du ikke trenger, ikke overdimensjoner - planlegg og vis dine kunnskaper

- Dokumenter !! Nesten alle lærlinger svikter på dette og blir straffet for det. Skriv alt du gjør

- Bruk dine kolleger og ressurser. Du har som lærling og fagprøve-kandidat lov til å ringe etter hjelp, søke opp informasjon. Dokumenter dette - og - ikke misbruk det. Du skal være mest mulig selvstendig, men bruk dem fremfor å stå fast eller kom med feile data.

- Personvern. Hvorfor deler du disse dataene? Hvem kan nå dem? Har du behandleravtale?

- Skriv at du gjemmer sensitive passord utenfor selve besvarelsedokumentet, men i et eget dokument. Ha dette som vedlegg.

----

# Dokumentasjon til fagprøve 2019

## Oversikt og oppstart

Her skal det dokumenteres hvilket utstyr som er brukt under avlegging av fagprøven. Alt fra hvor mange nettverkskabler til hvor mange tastatur som er i bruk. Eksempel:

- To tastatur til klientmaskiner
- 1 minnepenn med driver til skriver og chrome
- 3 skjermer til klientmaskiner
- SOHO ruter[^1] fra ASUS (modelnavn ++)

Lisensnøkkel til Windows server 2012 Foundation ble anskaffet ved å ringe Torgeir Mørk 2019-06-17.

osv....

---

## Oppsett av arbeidsplass og utstyr

Etter opptelling av utstyr startet jeg med og sette sammen utstyret jeg har fått tildelt.
Klientmaskiner ble satt opp som arbeidsstasjoner med tilkoblet mus, tastatur, skjerm, strømkabel til PSU og nettverkskabel som er koblet i SOHO ruteren, Serveren ble tilkoblet på samme måte.

Switch ble koblet til strøm samt ble det koblet en nettverkskabel fra uplink porten på switchen til veggen, veggkontakten var tilkoblet port 22 på patchpanelet som gikk til port 18 på switchen. Denne switchen var untagget på VLAN 1 som er vårt server VLAN. Dette VLAN-et har ikke DHCP men det hadde ikke gjort noe om det hadde det siden mitt nett vil NAT'es gjennom ruter uansett og ikke bli *forstyrret* av ting som er satt opp bak ruteren min.

Skriver ble koblet til strøm samt ble det satt en nettverkskabel fra printeren til ruteren slik at den kan ses og kommunisere med resten av nettverket av klientmaskiner og server.

Jeg koblet i tillegg en bærbar maskin med nettverkskabel i ruteren for og kunne feilsøke på eventuelle problemer som kunne oppstå underveis.
Teknikkerpc-en er installert med Arch Linux og har programvare slik som Wireshark for lettere kunne avdekke eventuelle nettverks problemer.
Samt alt av unix verktøy som jeg er godt kjent med fra før.

### Klientmaskin
Klientmaskinene ble startet opp, også ble det satt BIOS passord på alle maskinene. Passordet til BIOS legges som vedlegg. Instaslasjonen går for det meste av seg selv, det ble satt datamaskinnavn brukernavn lisens og lokaladministrator passord. Lokaladministrator passordet finnes også under vedlegg.For og kunne oppdatere maskinen til med windows update kreves internett tilgang og tanken er at dette skal pushes ut til alle klientmaskinene fra DHCP noe som enda ikke er satt opp. Det avventes derfor med oppdateringer til klientmaskinen er meldt inn i AD og serveren er ferdig konfigurert.

### Server
Server ble startet opp samtidig med klientmaskinene med boot medium windows server 2019 fra DVD plate. Under installasjonsvinduet språkvalg ble språket angitt til Engelsk siden Norsk ikke finnes, men også fordi at det vil være lettere og feilsøke på eventuelle problemer når man slipper og oversette eventuelle feilmeldinger om Norsk skulle vært tilgjengelig. tidsinstillinger, dato, tall og tastatur instillinger ble satt til Norsk. Norsk tastatur er viktig for å få rett tegnsetting som for eksempel at shift + 7 blir / osv. Lisensnøkkel ligger som vedlegg, og ble lagt til etter endt installasjon av serveren. Jeg gikk for Windows server 2019 Essentials siden dette er en liten bedrift og denne lisensnøkkelen dekker 25 brukere med 50 enheter.
Dette er noe jeg ville diskutert nærmere med bedriftsleder først og hannes forutsikter for hvor fort bedriftens antall ansatte vil vokse fremover.

Når man får opp valg med hvor man ønsker og installere serveren valgte jeg og slette gammle partioner på HDD-en samt lage en ny partion på 50 000MB noe som tilsvarer nærmere 48.8GB ikke 50 GB siden 1 GB er 1024 MB(50 000\1024). Dette er tilstrekkelig med plass for installasjonen siden Windows 2019 krever 32 GB med ledig plass, samt kan det være lurt og ha tilgjengelig 12GB mer lagringsplass til SWAP disk som normalt sett settes til 1.5 ganger mengde RAM som i mitt tilfelle er 8 GB noe som tilsvarer SWAP disk på ca 12GB.
Noe av plassen på hele disken går også med til MFT(Master File Table).

Etter installasjonen og serveren starter opp blir man spurt om administratorpassord. Dette passordet blir lagt som vedlegg.

### Oppsett av SOHO ruter
Søkte opp manualen til ASUS wireless 1750 på internett og gikk inn på siden www.router-reset.com/reset-manuals/ASUS/RT-AC1750. Her sto fremgangsmåte for resetting av switch samt default brukernavn og passord for å logge seg inn. For å resette den holdt jeg inne reset knappen på baksiden til strømlampen på ruteren blinket(ca 5 sekunder) Logget deretter inn på ruter fra siden router.asus.com (kunne eventuelt brukt default ip som er 192.168.0.1)

Ruteren var tilkoblet i veggkontakt som går til patchpanel port 18 og viderekoblet derifra til en switch på port 20. Logget meg inn med SSH på switchen på kontoret som har IP: 10.164.0.135 fra teknikker-pcen min som har en statisk ip satt til managment vlan: 10.164.0.169. Siden det er radius pålogging på switchen brukte jeg FEIDE pålogging på switchen, og gikk inn på menu, VLAN, Port settings og sørget for at VLAN 1 var untagget til Server Felles. Dette gjorde jeg siden dette vlanet har ikke DHCP og jeg kan dermed enkelt sette statisk IP addresse på WAN settingen på ruteren uten at dette skulle skape noe IP konflikt i fremtiden.

På siden som kom opp under oppsett av ruteren satt jeg IP addressen til 10.164.1.100, dette var en IP addresse jeg sørget for var ledig ved å prøve å pinge den først fra teknikker-pcen.

På neste side ble det spurt om SSID til 2.4Ghz nett samt PSK(pre shared key også gjerne omtalt som wifi nøkkelen)
SSID til wifi samt passord blir lagt som vedlegg til dokumentasjonen.

Etter basic oppsettet av ruteren logget jeg meg inn på router.asus.com via nettleser med default brukernavn og passord som jeg fant i manualen på router-reset.com.
Default passordet ligger også som vedlegg til dokumentasjonen.

Det første jeg gjorde under oppsett av ruteren var og skru av DHCP, dette er fordi at en egen server rolle skal ta seg av denne jobben og dette blir konfigurert under server konfigurasjonen.

## Server konfigurasjon
Det første jeg gjorde på serveren var og sette opp statisk IP addresse siden DHCP er deaktivert på ruteren.

IPV4:
IP: 192.168.1.2
Subnet: 255.255.255.0
Gateway: 192.168.1.1

DNS:
192.168.1.1
127.0.0.1

### MERK denne ip addressen er på ett annet VLAN en default IP addressen til ruteren.

Byttet så default passord til ett sikkrere passord som ikke er allmenn tilgjengelig på nett for og forhindre at uvedkommende skal få adgang til ruteren. Dette passordet ligger også som vedlegg.

Satt opp gjestenett under generelt og gjestenett la inn ett eget nettverksnavn til det og satt en tilgangstid til 1 dag.


## Begreper
- SOHO ruter

*Small office home office ruter*
Dette er en betegnelse for en ruter som også har en del andre tilleggsfunksjoner slik som større bedrifter har oppdelt i flere enheter.
Den inneholder en ruter som ruter traffikken inn & ut av ip skopet til nettverket.
I tillegg inneholder den en switch som styrer traffikken innad i nettverket og sørger for at klientmaskiner kan nå serveren og andre nettverksenheter slik som printer.
Den fungerer også som ett trådløst aksespunkt for klientmaskiner eller skrivere som måtte ha behov for å koble seg til nettverket trådløst


## Rettigheter
Brukere blir lagt til manuelt som lokal administrator, dette er fordi hvis en ansatt ønsker og installere programvare, bytte instillinger på pc-en lokalt så er dette noe han kan administrerer selv. Det har vært mulig og lagt de inn som lokal administrator gjennom GPO men dette ville forårsaket ett ganske massivt sikkerhets hull i systemet. Siden da kunne vær enkel ansatt kommet seg inn på noen andres private filer lagret i profilen deres.
