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

---


---

## Begreper

- SOHO ruter

*Small office home office ruter*
Dette er en betegnelse for en ruter som også har en del andre tilleggsfunksjoner slik som større bedrifter har oppdelt i flere enheter.
Den inneholder en ruter som ruter traffikken inn & ut av ip skopet til nettverket.
I tillegg inneholder den en switch som styrer traffikken innad i nettverket og sørger for at klientmaskiner kan nå serveren og andre nettverksenheter slik som printer.
Den fungerer også som ett trådløst aksespunkt for klientmaskiner eller skrivere som måtte ha behov for å koble seg til nettverket trådløst
