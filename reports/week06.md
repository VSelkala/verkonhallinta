# 1. Johdanto

Zabbix on avoimen lähdekoodin valvontajärjestelmä, jolla seurataan palvelinten, verkkolaitteiden ja sovellusten toimintaa.

Sitä voidaan käyttää esim.:
- Suorituskyvyn seurantaan: CPU, muisti, levytila ja verkkoliikenne.
- Saatavuuden valvontaan: ovatko palvelimet ja palvelut toiminnassa.
- Hälytyksiin: ilmoittaa, jos järjestelmässä havaitaan ongelmia tai raja-arvot ylittyvät.
- Ongelmien ennaltaehkäisyyn: auttaa havaitsemaan poikkeamat ennen kuin ne aiheuttavat suurempia häiriöitä.

Zabbix muistuttaa kolmannella viikolla opeteltua Prometheusta, koska molempia käytetään infrastruktuurin valvontaan ja ongelmien havaitsemiseen.

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

Tein Zabbixiin Host Groupin nimeltä Containerlab hostit ja lisäsin sinne web1- ja db1-palvelimet sekä branch-clientin. Yritin ensin käyttää templatena "Template OS Windows by Zabbix agent active" -vaihtoehtoa. Tällä templatella sain dataa näkyviin, mutta vain osittain. Lisäksi hostien tilaksi jäi "unknown". Kun vaihdoin templateksi "Template OS Windows by Zabbix agent" -vaihtoehdon, alkoi data näkymään oikein ja hostit näkyivät pian available-tilassa.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/zabbix_hostgroup.png)

---

# 3. Dashboard

Kuvakaappaus rakentamastani dashboardista.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/zabbix_dashboard.png)

Mielestäni tästä dashboardista seurattavien kohteiden tila avautuu hyvin yhdellä näkymällä ilman, että tietoa tulee liikaa.

---

# 4. Mittarit

Valitsin mittareiksi Prosessorien kuormitus, Muistin käyttöaste, Levytilan käyttöaste, Hostien tila, Uptime, Verkkoliikenne, Prosessorien keskiarvokuormitus sekä Ongelmat.

| **Mittari** | **Arvo** | **Merkitys** |
|---|---|---|
| Prosessorien kuormitus | 0-100 % | Nähdään laitteen prosessorien kuormitustilanne ja mahdolliset pullonkaulat |
| Muistin käyttöaste | 0-100 % | Nähdään laitteen muistin kuormitustilanne ja mahdolliset pullonkaulat |
| Levytilan käyttöaste | 0-100 % | Tiedetään milloin levytilaa tarvitsee lisätä tai vapauttaa |
| Hostien tila | Available / Not available / Unknown | Nopea näkymä siihen, onko valvottavat hostit toiminnassa |
| Uptime | DD:HH:mm:ss | Nähdään kauanko palvelu on ollut yhtäjaksoisesti toiminnassa |
| Verkkoliikenne | Kbps | Nähdään paljonko dataa kyseinen laite siirtää verkossa |
| Prosessorien keskiarvokuormitus (1 min) | 0-X (X = prosessorien lukumäärä) | Nähdään montako prosessia on ollut käynnissä tai odottamassa prosessoria viimeisen minuutin aikana |
| Ongelmat | Disaster / High / Average / Warning / Information / Not classified | Näyttää ympäristössä oleva ongelmat prioriteetin mukaan |

---

# 5. Triggerit

Templatessa oli valmiina valtava määrä triggereitä. Loin kuitenkin kaksi uutta triggeriä ehdotuksen mukaan:
- CPU > 80 %
- Free disk space < 20 %

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/zabbix_triggers.png)

---

# 6. Hälytys- ja häiriötestit

Suoritin CPU-kuormitustestin ensin branch-clientilla komennolla *yes > /dev/null*. Zabbixissa käyrä nousi hitaasti, mutta vain n. 25 % kohdille. Tämän jälkeen avasin kaksi uutta Powershelliä myös web1- ja db1-palvelimia varten. Kirjauduin näihin ja suoritin saman komennon molempiin. Käyrä nousi jo n. 58% kohdalle. Triggeriä ei syntynyt. Tarkastin Windowsin Task Managerista koneeni CPU-kuormaa, joka oli sama. Totesin, että virtuaaliympäristön vuoksi Zabbixin CPU-load valvookin koko ympäristöä, eikä vain yhtä virtuaalilaitetta, kuten kuuluisi.

Seuraava häiriötesti koski levytilan täyttymistä. Toteutin sen jälleen branch-clientilla, komennolla *dd if=/dev/zero of=testfile.img bs=1M count=1000*. Tästäkään testistä triggeriä ei syntynyt ja levytilan käyttöaste ei muuttunut graafeissa käytännössä lainkaan. Ainoa huomattava muutos oli testin aikainen lievä prosessorin kuorman nousu.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/zabbix_testit.png)

Tästä jäi ikävä maku, koska nähtävästi Zabbix (tai agentit) olisi pitänyt konfiguroida eri tavalla. Pyrin kuitenkin tekemään käyttöönoton aivan kuten luentovideolla ja kirjallisessa ohjeistuksessa opastettiin.

---

# 7. Vertailu

| **Ominaisuus** | **SNMP** | **Prometheus** | **Zabbix** |
|---|---|---|---|
| Tiedonkeruu | Kerää laitteiden tietoja kyselyillä ja vastaanottaa tarvittaessa trap-ilmoituksia. | Kerää mittareita säännöllisesti esimerkiksi exportereilta. | Kerää tietoja agenteilla, SNMP:llä ja muilla menetelmillä. |
| Dashboardit | Vaatii erillisen työkalun tietojen visualisointiin. | Tukee visualisointia esim. Grafanalla. | Sisältää omat dashboardit ja visualisointityökalut. |
| Hälytykset | SNMP-trapit mahdollistavat ilmoitukset, mutta kattavampi hälytysjärjestelmä vaatii erillisen työkalun. | Hälytykset voidaan toteuttaa Alertmanagerilla. | Sisäänrakennetut ongelmien tunnistus- ja hälytystoiminnot. |
| Käyttöönotto | Usein helppo, jos laite tukee SNMP:tä. | Vaatii Prometheuksen ja yleensä sopivat exporterit sekä määritykset. | Vaatii palvelimen ja tietokannan sekä valvontakohteiden ja sääntöjen määritykset. |
| Skaalautuvuus | Soveltuu kaiken kokoisiin verkkoympäristöihin. Ominaisuudet riippuvat laitteista ja hallintatyökaluista. | Soveltuu pienistä ympäristöistä suuriin pilvi- ja konttiympäristöihin. | Soveltuu pienistä ympäristöistä suuriin yritysverkkoihin. |
| Yrityskäyttö | Yleinen verkkolaitteiden ja infrastruktuurin valvontaprotokolla. | Sopii erityisesti pilvi-, kontti- ja sovellusympäristöjen valvontaan. | Sopii kokonaisvaltaiseen IT-infrastruktuurin, palvelinten, verkkolaitteiden ja sovellusten valvontaan. |

---

# 8. Yhteenveto

Kurssin tärkeimmät opit:
- Viikko 1 – Dokumentaatio: Dokumentointi auttaa ymmärtämään ympäristön rakennetta, asetuksia ja riippuvuuksia sekä helpottaa ongelmien selvittämistä.
- Viikko 2 – SNMP: SNMP:n avulla voidaan kerätä tietoa verkkolaitteista ja palvelimista. Opin myös, että yhteyden toimivuus, kuunteluosoitteet ja käyttöoikeudet vaikuttavat tiedonkeruuseen.
- Viikko 3 – Valvonta / Prometheus: Prometheus kerää järjestelmien mittareita, joita voidaan visualisoida esimerkiksi Grafanalla. Mittareiden avulla havaitaan kuormitusta ja poikkeamia.
- Viikko 4 – Ansible: Ansible automatisoi palvelinten konfigurointia ja ohjelmistojen asennusta. Yhdellä playbookilla voidaan tehdä samat muutokset useille palvelimille.
- Viikko 5 – Tietoturva: Nmapilla voidaan kartoittaa avoimia portteja ja palveluita. Avoin portti ei automaattisesti tarkoita haavoittuvuutta, mutta sen käyttötarkoitus ja pääsynhallinta pitää selvittää.
- Viikko 6 – Zabbix: Zabbixilla voidaan valvoa palvelimia, verkkolaitteita ja palveluita keskitetysti. Hostit, templatet, dashboardit ja triggerit auttavat hallitsemaan valvontaa ja hälytyksiä.

1. Mitä hyötyä keskitetystä valvonnasta on?
- Keskitetty valvonta kokoaa eri palvelinten ja verkkolaitteiden tilatiedot yhteen paikkaan. Se helpottaa ongelmien havaitsemista, vähentää manuaalista työtä ja nopeuttaa vianmääritystä.

2. Mitkä mittarit ovat mielestäsi tärkeimpiä?
- Tärkeimpiä ovat palvelinten saatavuus, CPU-kuormitus, muistin käyttö, levytila sekä verkkoliikenne. Lisäksi on tärkeää seurata keskeisten palveluiden toimintaa ja vasteaikoja.

3. Millaisista tilanteista ylläpitäjän pitäisi saada hälytys?
- Hälytys pitäisi saada esimerkiksi palvelimen tai palvelun kaatuessa, levytilan loppuessa, CPU-kuormituksen pysyessä pitkään korkeana tai muistin käytön kasvaessa liian suureksi. Myös verkkoyhteyden katkeamisesta ja epäilyttävästä toiminnasta pitäisi ilmoittaa.

4. Missä tilanteissa käyttäisit Prometheusta?
- Käyttäisin Prometheusta erityisesti palvelinten, sovellusten ja konttiympäristöjen suorituskykymittareiden keräämiseen ja analysointiin. Yhdessä Grafanan kanssa se sopii hyvin mittareiden visualisointiin ja trendien seuraamiseen.

5. Missä tilanteissa käyttäisit Zabbixia?
- Käyttäisin Zabbixia silloin, kun haluaisin hallita erilaisten palvelinten, verkkolaitteiden ja palveluiden valvontaa yhdestä järjestelmästä. Se soveltuu hyvin ympäristöihin, joissa tarvitaan valmiita valvontamalleja, dashboardeja ja hälytyksiä.

6. Mitä valvontatoimintoja lisäisit tähän ympäristöön?
- Lisäisin tietoturvatapahtumien keskitetyn seurannan. Lisäksi ottaisin käyttöön automaattiset ilmoitukset esimerkiksi sähköpostiin, jotta ongelmiin voitaisiin reagoida nopeasti.