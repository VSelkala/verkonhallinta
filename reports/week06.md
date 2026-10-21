# 1. Johdanto

Zabbix on avoimen lähdekoodin valvontajärjestelmä, jolla seurataan palvelimien, verkkolaitteiden ja sovellusten toimintaa.

Sitä voidaan käyttää esim.:
- Suorituskyvyn seurantaan: CPU, muisti, levytila ja verkkoliikenne.
- Saatavuuden valvontaan: ovatko palvelimet ja palvelut toiminnassa.
- Hälytyksiin: ilmoittaa, jos järjestelmässä havaitaan ongelmia tai raja-arvot ylittyvät.
- Ongelmien ennaltaehkäisyyn: auttaa havaitsemaan poikkeamat ennen kuin ne aiheuttavat suurempia häiriöitä.

Zabbix muistuttaa kolmannella viikolla opeteltua Prometheusta, sillä molempia käytetään infrastruktuurin valvontaan ja ongelmien havaitsemiseen.

- Hosts: Hallitaan valvottavia laitteita ja palvelimia, esim. web1- ja db1-palvelimia. Hostien asetuksissa määritellään mm. IP-osoitteet ja valvontaan liittyvät tiedot.
- Templates: Valmiita määrityspohjia, jotka sisältävät mittareita, valvontasääntöjä ja hälytyksiä. Niiden avulla samat valvonta-asetukset voidaan ottaa helposti käyttöön useilla hosteilla.
- Monitoring: Näyttää valvottavien laitteiden ja palveluiden tilan, kerätyt mittaustiedot ja havaitut ongelmat.
- Dashboards: Koontinäkymiä, joihin voidaan koota mm. suorituskykyä, saatavuutta ja hälytyksiä kuvaavia mittareita.
- Alerts: Hallitaan hälytyksiä, joita syntyy esim. palvelimen toimintahäiriöstä tai mittausarvon rajojen ylityksestä.
- Reports: Tuotetaan raportteja järjestelmän toiminnasta. Raporttien avulla voidaan seurata ympäristön tilaa ja arvioida sen toimintaa.

---

# 2. Hostien lisääminen

Kirjauduin ensin web1-palvelimelle ja asensin Zabbix-agent2 -ohjelmiston. Versiona toimi *zabbix-release_latest_7.4+ubuntu24.04_all*. Tämän jälkeen muokkasin agentin configuraatiotiedostoon oikeat IP-osoitteet.
Seuraavaksi toistin prosessin myös db1-palvelimelle sekä branch-clientille.

Tein Zabbixiin Host Groupin nimeltä Containerlab hostit ja lisäsin sinne web1- ja db1-palvelimet sekä branch-clientin.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_1.png)

---

# 3. Dashboard

Kuvakaappaus dashboardista.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_1.png)

---

# 4. Mittarit

Valitut mittarit ja niiden merkitys.

---

# 5. Triggerit

Määritetyt triggerit ja niiden toiminta.

---

# 6. Hälytys- ja häiriötestit

Suoritetut testit ja tulokset.

---

# 7. Vertailu

| **Ominaisuus** | **SNMP** | **Prometheus** | **Zabbix** |
|---|---|---|---|
| Tiedonkeruu | Kerää laitteiden tietoja kyselyillä ja vastaanottaa tarvittaessa trap-ilmoituksia. | Kerää mittareita säännöllisesti esimerkiksi exportereilta. | Kerää tietoja agenteilla, SNMP:llä ja muilla menetelmillä. |
| Dashboardit | Vaatii erillisen työkalun tietojen visualisointiin. | Tukee visualisointia esim. Grafanalla. | Sisältää omat dashboardit ja visualisointityökalut. |
| Hälytykset | SNMP-trapit mahdollistavat ilmoitukset, mutta kattavampi hälytysjärjestelmä vaatii erillisen työkalun. | Hälytykset voidaan toteuttaa Alertmanagerilla. | Sisäänrakennetut ongelmien tunnistus- ja hälytystoiminnot. |
| Käyttöönotto | Usein helppo, jos laite tukee SNMP:tä. | Vaatii Prometheuksen ja yleensä sopivat exporterit sekä määritykset. | Vaatii palvelimen ja tietokannan sekä valvontakohteiden ja sääntöjen määritykset. |
| Skaalautuvuus | Soveltuu kaiken kokoisiin verkkoympäristöihin. Ominaisuudet riippuvat laitteista ja hallintatyökaluista. | Soveltuu pienistä ympäristöistä suuriin pilvi- ja konttiympäristöihin. | Soveltuu pienistä ympäristöistä suuriin yritysverkkoihin. |
| Yrityskäyttö | Yleinen verkkolaitteiden ja infrastruktuurin valvontaprotokolla. | Sopii erityisesti pilvi-, kontti- ja sovellusympäristöjen valvontaan. | Sopii kokonaisvaltaiseen IT-infrastruktuurin, palvelimien, verkkolaitteiden ja sovellusten valvontaan. |

---

# 8. Yhteenveto

Kurssin tärkeimmät opit.
