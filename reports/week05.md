# 1. Johdanto

Tietoturva on tärkeä osa verkonhallintaa, yhdessä valvonnan ja automaation kanssa.
On hyvä osata tunnistaa mm. tietoturvauhat, poikkeava liikenne, virheelliset konfiguraatiot sekä väärinkäytökset.

Tässä tehtävässä toteutetaan pienimuotoinen porttiskannaus mahdollisen hyökkääjän toimien tunnistamiseksi ja lokitietojen lukemisen oppimiseksi.

---

# 2. Kaappaus

Lähtötilanteessa kirjauduin yhdellä terminaalilla web1-palvelimelle ja toisella attacker-koneelle.
Aloitin web1-palvelimella liikenteen kaappauksen komennolla *tcpdump -i eth1 -w web1.pcap*.

---

# 3. Porttiskannaus

Suoritin porttiskannauksen attacker-koneelta komennolla *nmap -sV 10.10.20.101*. Lisäsin parametrit -sV, joita todellinen hyökkääjäkin todennäköisesti käyttäisi pelkän nmapin sijaan saadakseen itsekin tietoa skannauksen tuloksista. Tämä paljastaa avoimet portit sekä versiotietoja käytetyistä palveluista. Hyökkääjä voi tämän avulla hakea tietokannasta toimivan hyökkäyksen kyseiselle ohjelmistoversiolle. Komennon tulos hyökkääjälle:

*Starting Nmap 7.991 ( https://nmap.org ) at 2026-10-20 07:14 +0000*
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

Wiresharkilla web1.pcap-tiedostoa tutkiessa huomaa, että hyökkääjän IP-osoite on 10.10.10.200. Tämän avulla liikenne on helppo rajata vain hyökkääjän saamiin vastauksiin. Nmap skannasi alle sekunnissa noin 1000 eri porttia, joka on valtava määrä. Näistä vain kaksi oli avoimia portteja (22 ja 9100), joista hyökkääjä voisi yrittää tunkeutua järjestelmään. Wireshark näyttää selkeästi vihreällä, että näiden porttien osalta kyselyyn on vastattu. Estetty liikenne, eli suljetut portit näkyvät punaisena.

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_1.png)

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/wireshark_2.png)

---

# 5. Loki-analyysi

Tutkitut lokit ja löydökset.

---

# 6. Turvallisuusarvio

Parannusehdotukset.

---

# 7. Yhteenveto

Opitut asiat.