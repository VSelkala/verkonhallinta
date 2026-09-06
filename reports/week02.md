# 1. Johdanto

Mikä on SNMP?

---

# 2. Asennus

Ensimmäisenä kirjauduin web1-palvelimen hallintaan ja päivitin käyttöjärjestelmän komennoilla "apt update" ja "apt upgrade".
Tämän jälkeen asensin SNMP-agentin komennolla: "apt install snmp snmpd -y".

Oletusarvoisesti agentti kuunteli vain localhost-osoitteesta, jolloin siihen ei saanut yhteyttä muualta.
Muokkasin agentin konfiguraatiota komennolla "nano /etc/snmp/snmpd.conf" ja vaihdoin agentaddress-kohtaan udp:161, jotta agenttiin saa yhteyden kaikkialta ympäristöstä.


---

# 3. Kerätyt tiedot

Otin yhteyden Ansiblesta web1- ja db1-palvelimien sekä branch-clientin SNMP-agentteihin ja hain seuraavat järjestelmätiedot:

| Laite | Nimi | Käyttöjärjestelmä | Uptime |
|---------|---------|---------|---------|
| web1 | web1 | Linux web1 6.18.33.2-microsoft-standard-WSL2 | Timeticks: (138224) 0:23:02.24 |
| db1 | db1 | Linux db1 6.18.33.2-microsoft-standard-WSL2 | Timeticks: (20892) 0:03:28.92 |
| branch-client | branch-client | Linux branch-client 6.18.33.2-microsoft-standard-WSL2 | Timeticks: (21192) 0:03:31.92 |


---

# 4. Verkkorajapinnat

- IF-MIB::ifDescr.1 = STRING: lo (localhost)
- IF-MIB::ifDescr.2 = STRING: eth0 (hallintayhteys, IP 172.20.20.4)
- IF-MIB::ifDescr.5819 = STRING: eth1 (verkkoyhteys, IP 10.10.20.101)

Näiden saamiseksi muokkasin taas agentin konfiguraatiota ja vaihdoin kohdan "rocommunity  public default -V systemonly" muotoon "rocommunity  public". Ilman tätä muutosta oikeudet olivat liian rajattuja eikä verkkorajapintoihin päässyt käsiksi.

---

# 5. OID-analyysi

OID-objektien käyttötarkoitus:

| MIB-objekti | OID | Tarkoitus |
|------|------|------|
| sysName.0 | .1.3.6.1.2.1.1.5.0 | Järjestelmän nimi |
| sysDescr.0 | .1.3.6.1.2.1.1.1.0 | Käyttöjärjestelmä |
| sysUpTime.0 | .1.3.6.1.2.1.1.3.0 | Kauanko järjestelmä on ollut käynnissä yhtäjaksoisesti |
| ifDescr | .1.3.6.1.2.1.2.2.1.2 | Verkkorajapinnat |
| ifOperStatus | .1.3.6.1.2.1.2.2.1.8 | Verkkorajapintojen statustieto (UP/DOWN) |

---

# 6. Pohdinta

1. Mitä hyötyä SNMP:stä on verkonhallinnassa?
- SNMP on tärkeä osa verkon valvontaa ja hallintaa ja sen avulla voidaan nopeasti havaita ongelmia verkkolaitteissa tai palvelimissa.
2. Mitä tietoa SNMP:n avulla voidaan kerätä?
- SNMP:llä voidaan kerätä mm. tietoa laitteiden käyttöjärjestelmistä, käyttöajoista, verkkorajapinnoista sekä liikennemääristä.
3. Mitä ongelmia yhteisöpohjaisessa SNMPv2:ssa on?
- SNMPv2 on merkittävä parannus ensimmäiseen versioon, mutta siitä puuttuu mm. käyttäjän vahva tunnistus sekä liikenteen salaus. Salasanat säilytetään clear-text -muodossa, joten ne ovat helppoja kohteita mahdollisessa tietomurrossa.
4. Missä tilanteissa käyttäisit mieluummin SNMPv3:a?
- SNMPv3 tarjoaa parempaa tietoturvaa, esim. käyttäjäkohtainen tunnistus, datan kryptaus sekä timestamp-pohjaiset tietoturvamekaniikat. Se soveltuu paremmin tuotantoympäristöihin sekä tilanteisiin, jossa tietoturva on ensiluokkaisen tärkeää. SNMPv3 on kuitenkin raskaampi, joten se vaatii enemmän resursseja ympäristöltä.

# 7. Muut huomiot

Viime viikon tehtävässä en saanut Netboxia toimimaan ympäristössä. Tällä kertaa paneuduin asiaan hieman lisää ja sain palvelun toimimaan seuraavilla toimenpiteillä:
- Selvitin dockerin logeista komennolla "docker logs --tail 100 netbox-netbox-1" seuraavan virheen: "OperationalError: connection failed: connection to server at "172.18.0.3", port 5432 failed: FATAL: password authentication failed for user "netbox""
- Kirjauduin PostgreSQL:ään komennolla "docker exec -it netbox-postgres-1 psql -U netbox -d netbox" korjaamaan salasanan oikeaksi. Tämä ei siis ollut valunut .env-tiedostosta suoraan, vaan vaati manuaalisen korjauksen.
