# Automatisere prosessen

![Powershell]("https://i0.wp.com/blogit.create.pt/wp-content/uploads/2017/02/powershell-cim_1.jpg?fit=1118%2C628&ssl=1")

Hvis dette var mitt levebrød og noe jeg hadde gjort dag ut og dag inn hadde jeg i såfall denne prosessen.
Under er noen eksempel skript som jeg mener kunne gjort at denne server installasjonen hadde tatt **mye** kortere tid.

Powershell hadde også vært nødvendig om serveren bedriften ga meg i oppdrag og installere
ikke hadde desktop enviroment installert.

Fordelene med og bruke skript kontra GUI er at jeg kunne konsentrert meg mer om og installert
klientmaskinene og meldt de inn i AD mens server installasjonen pågår og dermed spare tid og bli mye mer konkuransedyktig.

---

### Etter ren installasjon:

*For og bytte navn på serveren.*
```{PowerShell}
$ServerName = Read-Host -Prompt 'Skriv inn ønsket server navn'
Rename-Computer -NewName $ServerName
```

*Variabler for setting av IP addresser.*
```
$Interface = "Ethernet"
$ServerIp = Read-Host -Prompt "Hvilken IP ønsker du at serveren skal ha?"
$DefautlGateway = Read-Host -Prompt "Hva er IP addressen til default gateway?"
```

*Dette er for og fjerne gammel konfigurasjon, om dette er en helt ny server er ikke disse 3 linjene nødvendig.*
```
Remove-NetRoute -InterfaceAlias $Interface -AddressFamily IPv4
Set-NetIPInterface -InterfaceAlias $Interface -Dhcp Enabled -AddressFamily IPv4
Set-DnsClientServerAddress -InterfaceAlias $Interface -ResetServerAddresses
```

*Setter ny ip adresse til serveren med valgt IP fra variablen over kalt, ServerIp.*
*Get-NetAdapter er nødvendig for å finne ut index nummeret som er tilknyttet ethernet kabelen*
*Man kan finne den manuelt også ved å bruke netsh interface ipv4 show interfaces i cmd f.eks*
```
New-NetIPAddress –IPAddress $ServerIp -DefaultGateway $DefautlGateway -PrefixLength 24 -InterfaceIndex (Get-NetAdapter).InterfaceIndex
```

*Denne linjen setter opp DNS, den setter serveren som primary DNS og defautl gateway som nr2.*
```
Set-DnsClientServerAddress -InterfaceAlias $Interface -ServerAddresses $ServerIp, $DefautlGateway
```
