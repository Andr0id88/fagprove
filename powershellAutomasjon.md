# Automatisere server installasjon ved bruk av Powershell
![](https://i0.wp.com/blogit.create.pt/wp-content/uploads/2017/02/powershell-cim_1.jpg?fit=1118%2C628&ssl=1)

Hvis dette var mitt levebrød og noe jeg hadde gjort dag ut og dag inn hadde jeg i såfall automatisert denne prosessen.
Under er noen eksempel skript som jeg mener kunne gjort at denne server installasjonen hadde tatt **mye** kortere tid.

Powershell hadde også vært nødvendig om serveren bedriften ga meg i oppdrag og installere
ikke hadde desktop enviroment installert.

Fordelene med og bruke skript kontra GUI er at jeg kunne konsentrert meg mer om og installert
klientmaskinene og meldt de inn i AD mens server installasjonen pågår og dermed spare tid og bli mye mer konkuransedyktig.

Disse ville selvfølgelig ikke vært på github men lagret som i ps1 format slik at jeg kunne kjøre hele eller deler av skriptet
i powershell ISE.

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
New-NetIPAddress –IPAddress $ServerIp -DefaultGateway $DefautlGateway -PrefixLength 24 \\
-InterfaceIndex (Get-NetAdapter).InterfaceIndex
```
*(Get-NetAdapter er nødvendig for å finne ut index nummeret som er tilknyttet ethernet tilkoblingen. Man kan finne den manuelt også ved å bruke mer primitive verktøy som netsh ipv4 show interfaces i cmd f.eks. Koden ble delt opp
for og bli lettere og lese.)*

##### Denne linjen setter opp DNS, den setter serveren som primary DNS og defautl gateway som nr2. #####
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

##### For og installere AD-DC og legge til serveren i en ny skog med gitt domenenavn #####
```
$DomeneNavn = Read-Host -Prompt "Skriv inn ønsket domenenavn?"
Install-ADDSForest -DomainName $DomeneNavn
Install-ADDSDomainController -InstallDns -Credential (Get-Credential) -DomainName $DomeneNavn
```

#Kommando for og sjekke om pc-er er blitt innmeldt i domene. Husk og bruk * på spørsmål om filter dette vil vise alle.
#get-ADComputer | Format-Table DNSHostName, Enabled, Name, SamAccountName

#Kommando for sjekk at DNS er installert
#Get-WindowsFeature | where {($_.name -like “DNS”)}


#Selv
Install-WindowsFeature DHCP -IncludeManagementTools

netsh dhcp add securitygroups


#Konfigurasjon av DHCP:
$ScopeNavn = Read-Host -Prompt "Hva ønsker du og kalle DHCP skopet?"
$IpStartScope = Read-Host -Prompt "Hvilken IP addresse skal DHCP serveren starte på?"
$IpEndScope = Read-Host -Prompt "Hvilken IP addresse skal DHCP serveren slutte på?"

#Prøv og finn ut en måte og manipuler scope stringen til å bli det samme som DNS serveren bare med 0 på slutten.
Add-DHCPServerv4Scope -Name $ScopeNavn -StartRange $IpStartScope -EndRange $IpEndScope -SubnetMask 255.255.255.0 -State Active
Set-DhcpServerv4Scope -ScopeId 192.168.1.0 -LeaseDuration 1.00:00:00
Set-DHCPServerv4OptionValue -ScopeID 192.168.1.0 -DnsDomain $Domenenavn -DnsServer 127.0.0.1 -Router 192.168.1.1



