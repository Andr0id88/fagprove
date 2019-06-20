# Automatisere installasjon ved bruk av Powershell
![](https://i0.wp.com/blogit.create.pt/wp-content/uploads/2017/02/powershell-cim_1.jpg?fit=1118%2C628&ssl=1)

Hvis dette var mitt levebrød og noe jeg hadde gjort dag ut og dag inn hadde jeg i såfall automatisert denne prosessen.
Under er noen eksempel skript som jeg mener hadde gjort at denne server installasjonen hadde tatt **mye** kortere tid.

Powershell hadde også vært nødvendig om serveren bedriften ga meg i oppdrag og installere
ikke hadde desktop enviroment installert.Fordelene med og bruke skript kontra GUI er at jeg kunne konsentrert meg mer om andre arbeidsoppgaver mens server installasjonen pågår og dermed spare tid og bli mye mer konkuransedyktig.

*Disse ville selvfølgelig ikke vært på github men lagret som ps1 format slik at jeg kunne kjøre hele eller deler av skriptet i powershell ISE fra en minnepenn eller skylagringstjeneste.*

---
## Skript som kjøres etter ren installasjon  ##

##### Bytte navn på serveren. #####
```{PowerShell}
$ServerName = Read-Host -Prompt 'Skriv inn ønsket server navn'
Rename-Computer -NewName $ServerName
```

##### Variabler der IP addresser skrives inn av bruker. #####
```
$Interface = "Ethernet"
$ServerIp = Read-Host -Prompt "Hvilken IP ønsker du at serveren skal ha?"
$DefautlGateway = Read-Host -Prompt "Hva er IP addressen til default gateway?"
```

##### Dette er for og fjerne gammel konfigurasjon, om dette er en helt ny server er ikke disse 3 linjene nødvendig. #####
```
Remove-NetRoute -InterfaceAlias $Interface -AddressFamily IPv4
Set-NetIPInterface -InterfaceAlias $Interface -Dhcp Enabled -AddressFamily IPv4
Set-DnsClientServerAddress -InterfaceAlias $Interface -ResetServerAddresses
```
##### Denne kommandoen setter ny IP addresse til serveren med IP hentet fra variablen $ServerIP #####
```
New-NetIPAddress –IPAddress $ServerIp -DefaultGateway $DefautlGateway -PrefixLength 24 `
-InterfaceIndex (Get-NetAdapter).InterfaceIndex
```
*(Get-NetAdapter er nødvendig for å finne ut index nummeret som er tilknyttet ethernet tilkoblingen. Man kan finne den manuelt også ved å bruke mer primitive verktøy som netsh ipv4 show interfaces i cmd f.eks. Koden ble delt opp
for og bli lettere og lese.)*

##### Denne kommandoen setter opp DNS, den setter serveren som primary DNS og default gateway som secondary. #####
```
Set-DnsClientServerAddress -InterfaceAlias $Interface -ServerAddresses $ServerIp, $DefautlGateway
```

---

## OPPSETT AV ROLES AND FEATURES! ##

*Get-WindowsFeature viser alle mulige roller & features man kan installere.*
*Grep funker selvsagt ikke i windows powershell men man kan få samme funksjonalitet med pipes ved og bruke*
*Get-WindowsFeature | findstr -i Installed, denne kommandoen vil vise alt som er installert på serveren i en liste.*

#### Installasjon av AD domain services ####
```
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

*Denne viser de forskjellige modulene som tilhører ADDSDeployment
Get-Command -Module ADDSDeployment*

##### For og installere AD samt oppgradere den til DC, samt legge til serveren i en ny skog med gitt domenenavn #####
```
$DomeneNavn = Read-Host -Prompt "Skriv inn ønsket domenenavn?"
Install-ADDSForest -DomainName $DomeneNavn
Install-ADDSDomainController -InstallDns -Credential (Get-Credential) -DomainName $DomeneNavn
```

*Det kan være lurt og teste ut om alt har fungerte ved å prøve og melde inn en pc i domenet. For og sjekke om pc-er er blitt innmeldt i domene kan man*
*skrive get-ADComputer | Format-Table DNSHostName, Enabled, Name, SamAccountName. Når spørsmål om filter kommer opp velger man * som betyr vis alle.*
```
get-ADComputer | Format-Table DNSHostName, Enabled, Name, SamAccountName
```
##### Kommando for og sjekke om DNS er installert #####
```
Get-WindowsFeature | where {($_.name -like “DNS”)}
```

#### Installasjon av DHCP ####
```
Install-WindowsFeature DHCP -IncludeManagementTools
netsh dhcp add securitygroups
```
##### Konfigurasjon av DHCP #####
```
$Domain = $env:USERDNSDOMAIN
$ScopeNavn = Read-Host -Prompt "Hva ønsker du og kalle DHCP skopet?"
$IpStartScope = Read-Host -Prompt "Hvilken IP addresse skal DHCP serveren starte på?"
$IpEndScope = Read-Host -Prompt "Hvilken IP addresse skal DHCP serveren slutte på?"
Add-DHCPServerv4Scope -Name $ScopeNavn -StartRange $IpStartScope -EndRange $IpEndScope `
-SubnetMask 255.255.255.0 -State Active
Set-DhcpServerv4Scope -ScopeId 192.168.1.0 -LeaseDuration 1.00:00:00
Set-DHCPServerv4OptionValue -ScopeID 192.168.1.0 `
-DnsDomain $Domain -DnsServer 192.168.1.1, 192.168.1.2 -Router 192.168.1.1
```

##### Lag OU #####
```
$OU = Read-Host -Prompt "Skriv inn navn på OU du ønsker og legge til:"
New-ADOrganizationalUnit -Name $OU
```
*I mitt eksempel ville jeg lagt til OU-ene, grupper, brukere*

##### Legg til grupper #####
```
$Domain = $env:USERDNSDOMAIN
$DC1, $DC2 = $Domain.split('.')
$DcPath = "DC="+$DC1 + "," + "DC="+$DC2

$Gruppenavn = Read-Host -Prompt "Skriv inn navn på gruppen du ønsker og legge til:"
$Description = Read-Host -Prompt "Skriv inn en description til gruppen"
New-ADGroup -GroupScope Global -GroupCategory Security -Name $Gruppenavn `
-Description $Description -Path "ou=grupper,dc=$DC1,dc=$DC2"
```
*Mulighet for å bruke andre OU om man lager en variabel for det eller hardkoder det inn hvis det er behov, i mitt eksempel holder det med ett OU for grupper.*

##### Legg til brukere #####

```
$Domain = $env:USERDNSDOMAIN
$DC1, $DC2 = $Domain.split('.')
$DcPath = "DC="+$DC1 + "," + "DC="+$DC2

$Fornavn = Read-Host -Prompt "Skriv inn fornavn"
$Etternavn = Read-Host -Prompt "Skriv inn etternavn"
$Grupper = Read-Host -Prompt "Hvilken gruppe skal brukeren være medlemm av?"
$FultNavn = $Fornavn + " " + $Etternavn
$BrukerNavn = $Fornavn.SubString(0,3) + $Etternavn.SubString(0,3)

New-ADUser -Name $FultNavn -GivenName $Fornavn -Surname $Etternavn -DisplayName "$FultNavn" `
-UserPrincipalName "$BrukerNavn@kolos.local" -SamAccountName "$BrukerNavn" `
-Path "OU=Brukere,$DcPath"  -AccountPassword(Read-Host -AsSecureString "Input Password") `
-Enabled $true -HomeDirectory "\\$env:computername\%username%" -HomeDrive "W:"
Add-ADGroupMember -Identity "$Grupper" -Members $BrukerNavn
```

