# 1. Johdanto

Tietoturva on tärkeä osa verkonhallintaa, yhdessä valvonnan ja automaation kanssa.
On hyvä osata tunnistaa mm. tietoturvauhat, poikkeava liikenne, virheelliset konfiguraatiot sekä väärinkäytökset.

Tässä tehtävässä toteutetaan yksittäinen porttiskannaus attacker-koneelta web1-palvelimelle mahdollisen hyökkääjän toimien tunnistamiseksi ja lokitietojen lukemisen oppimiseksi.

---

# 2. Kaappaus

Lähtötilanteessa kirjauduin yhdellä terminaalilla web1-palvelimelle ja toisella attacker-koneelle.
Aloitin web1-palvelimella liikenteen kaappauksen komennolla *tcpdump -i eth1 -w web1.pcap*.

---

# 3. Porttiskannaus

Suoritin porttiskannauksen attacker-koneelta komennolla *nmap -sV 10.10.20.101*. Lisäsin parametrit -sV, joita todellinen hyökkääjäkin todennäköisesti käyttäisi pelkän nmapin sijaan saadakseen itsekin tietoa skannauksen tuloksista. Tämä paljastaa avoimet portit sekä versiotietoja käytetyistä palveluista. Hyökkääjä voi tämän avulla hakea tietokannasta toimivan hyökkäyksen kyseiselle ohjelmistoversiolle. Komennon tulos hyökkääjälle:

*Nmap scan report for 10.10.20.101*
*Host is up (0.000024s latency).*
*Not shown: 998 closed tcp ports (reset)*
*PORT     STATE SERVICE    VERSION*
*22/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.20 (Ubuntu Linux; protocol 2.0)*
*9100/tcp open  jetdirect?*
*Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel*

*Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .*
*Nmap done: 1 IP address (1 host up) scanned in 0.78 seconds*

---

# 4. Wireshark

Wiresharkilla web1.pcap-tiedostoa tutkiessa huomaa, että hyökkääjän IP-osoite on 10.10.10.200. Tämän avulla liikenne on helppo rajata vain hyökkääjän saamiin vastauksiin. Nmap skannasi alle sekunnissa 1000 eri porttia. Näistä vain kaksi oli avoimia portteja (22 ja 9100), joista hyökkääjä voisi yrittää tunkeutua järjestelmään. Wireshark näyttää selkeästi vihreällä, että näiden porttien osalta kyselyyn on vastattu. Estetty liikenne, eli suljetut portit näkyvät punaisena.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_1.png)

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_2.png)

---

# 5. Loki-analyysi

Komennolla *journalctl* sain 132508 riviä lokitietoja. Tämän läpi käyminen on työlästä ja aikaa vievää, mutta voi olla hyödyllistä esim. selvitettäessä mitä komentoja ympäristössä on ajettu.
Valtaosa riveistä on kuitenkin WSL:n tuottamaa tarpeetonta tietoa. Esim. virheilmoitus *North wsl-pro-service[198505]: WARNING Daemon: could not connect to Windows Agent: could not get address: could not read agent port file* toistuu lokissa lukuisia kertoja. En kuitenkaan ole huomannut tällä mitään vaikutusta Ubuntun käyttöön WSL:n kautta.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/journalctl.png)

---

# 6. Turvallisuusarvio

Ympäristö vaikuttaa kohtalaisen turvalliselta, sillä ainakin web1-palvelimen portit ovat lähes poikkeuksetta kiinni.
Portti 9100 oli kuitenkin avoinna ja Node Exporterin käytössä. Tämä on tarpeellista valvonnan kannalta, mutta todellisessa ympäristössä käyttö tulisi rajata vain valvontapalvelimille, jotta kaikki verkon laitteet eivät pääse näkemään näitä mittareita.
Tällä hetkellä mittarit pääsee näkemään mm. attacker-koneelta. Tämä voi antaa hyökkääjälle lisätietoa verkosta.

Toisessa avoimessa portissa 22 toimiva OpenSSH 9.6p1 Ubuntu 3ubuntu13.20 vaikuttaa ajantasaiselta enkä ainakaan äkkiseltään löytänyt siitä haavoittuvuuksia.
Todellisessa ympäristössä olisi järkevää ottaa käyttöön Zero Trust -suojausmalli, jotta vain luotetut laitteet pääsevät yrityksen verkkoon käsiksi.

---

# 7. Yhteenveto

Porttiskannaus oli helppo tunnistaa lokeista, koska muuta liikennettä ei ollut. Todellisen elämän ympäristössä liikennettä on niin paljon, että yksittäinen porttiskannaus voi jäädä helposti huomaamatta. Tätä varten olisi hyvä olla monitoroinnissa jonkinlainen hälytys, joka ilmoittaa kun porttiskannaus havaitaan. Tästä voidaan nopeasti tutkia mistä osoitteesta skannaus on tullut ja sitä kautta esim. estää kyseinen IP verkosta.