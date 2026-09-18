# 1. Johdanto

Infrastructure as Code (IaC) on nykyaikainen tapa rakentaa ja ylläpitää verkkoympäristöjä automaatioiden avulla. IaC nopeuttaa huomattavasti suurten laitemäärien ylläpitoa ja vähentää inhimillisiä erehdyksiä. Telia Cygatella työskennellessäni tätä käytettiin oletuksena, sillä manuaalityötä haluttiin vähentää.

---

# 2. Inventory

Ympäristön rakenne:

@all:
  |--@ungrouped:
  |--@user_network:
  |  |--client1
  |  |--attacker
  |--@server_network:
  |  |--web1
  |  |--db1
  |--@branch_office:
  |  |--branch-client
  |--@network_devices:
  |  |--@routers:
  |  |  |--r1
  |  |  |--r2
  |  |  |--r3
  |--@linux_hosts:
  |  |--@clients:
  |  |  |--client1
  |  |  |--attacker
  |  |  |--branch-client
  |  |--@servers:
  |  |  |--web1
  |  |  |--db1
  |  |--@monitoring:
  |  |  |--prometheus
  |  |  |--grafana
  |  |  |--zabbix
  |  |  |--cadvisor
  |  |--@management:
  |  |  |--ansible
  |--@ubuntu_hosts:
  |  |--client1
  |  |--web1
  |  |--db1
  |  |--branch-client
  |--@node_exporter:
  |  |--@routers:
  |  |  |--r1
  |  |  |--r2
  |  |  |--r3
  |  |--@clients:
  |  |  |--client1
  |  |  |--attacker
  |  |  |--branch-client
  |  |--@servers:
  |  |  |--web1
  |  |  |--db1

Kuten nähdään, niin ryhmiä on useita ja laitteet voivat kuulua useaan eri ryhmään.
Ryhmien käyttö mahdollistaa ympäristön hallinnan ryhmittäin sen sijaan, että samat toimenpiteet tulisi tehdä jokaiselle laitteelle erikseen.

---

# 3. SNMP Playbook

Komento "ansible-playbook -i ../inventory.ini ping.yml" suoritti tarkastuksen siitä, mitkä laitteet ovat löydettävissä:

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/ansible_playbook_ping.png)

Tämän jälkeen tuli asentaa SNMP-agentit laitteisiin web1, db1 ja branch-client. Päivitin install-snmp.yml-tiedostoa vastaamaan annettua ohjeistusta ja ajoin sen komennolla "ansible-playbook -i ../inventory.ini install-snmp.yml". Toiminto antoi kuitenkin erroria viimeisessä vaiheessa:

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/snmp_playbook1.png)

Vastaava ongelma oli ollut aiemmin SNMP-viikolla ja johtui siitä, että ympäristöä ajetaan WSL:n yli. Sain ongelman korjattua vaihtamalla .yml-tiedostoon "service: -> name: snmpd - enabled: yes - state: started" sijaan komennoksi "command: service snmpd start -> changed_when: false". Tämän jälkeen ajoin playbookin uudelleen ja kaikki meni ok:

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/snmp_playbook2.png)

---

# 4. Node Exporter Playbook

Node Exporter tuli asentaa laitteisiin web1 ja db1. Päivitin install-node-exporter.yml-tiedostoa vastaamaan annettua ohjeistusta. Tähän tarvitsi vain lisätä halutut kohteet sekä Node Exporterin versio riveille, joista se puuttui. Asennus meni kerralla maaliin:

![Kuvakaappaus](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/node_exporter_playbook.png)


---

# 5. Järjestelmätiedot

Seuraavaksi keräsin järjestelmätietoja komennolla "ansible all -i ../inventory.ini -m setup".
Tuloksena oli valtava määrä dataa, jota ei ollut helppoa selata.

Halusin harjoitella automaatiota ja uuden playbookin tekoa, joten toteutin tiedonkeruun automaation avulla. Asiaa aikani pohdittuani totesin, että tässä tehtävässä tarvitsen apua ChatGPT:ltä, jotta saisin halutut tiedot valmiiksi Markdown-muotoon. Avun jälkeen ajoin playbookin ja sain suoraan seuraavat tiedot:

| Laite | Käyttöjärjestelmä | IP-osoite | Prosessorien määrä | Muistin määrä |
|---|---|---|---:|---:|
| client1 | Ubuntu 24.04 | 10.10.10.101 | 6 | 15530 MB |
| attacker | Kali 2026.3 | 10.10.10.200 | 6 | 15530 MB |
| web1 | Ubuntu 24.04 | 10.10.20.101 | 6 | 15530 MB |
| db1 | Ubuntu 24.04 | 10.10.20.102 | 6 | 15530 MB |
| branch-client | Ubuntu 24.04 | 10.10.30.101 | 6 | 15530 MB |

---

# 6. Vertailu

Pienen ympäristön ylläpito käsin on yksinkertaista ja selkeää. Automaatio kuitenkin auttaa jo pienessä ympäristössä, mikäli jokin laite rikkoutuu tai muusta syystä halutaan korvata. Tällöin on helppoa tuoda uuteen laitteeseen samat asetukset, kuin edellisessä.
Ympäristön kasvaessa automaation hyödyt korostuvat eksponentiaalisesti. Satojen laitteiden ylläpito käsin vaatii jo valtavasti aikaa ja resursseja. Automaation avulla ympäristö on kuitenkin helppoa ylläpitää, koska manuaalityön osuus vähenee huomattavasti.

---

# 7. Yhteenveto

Automaatio on oiva työkalu, mutta ei korvaa täysin käsin tekemistä. Etenkin oppimisen kannalta käsin tekeminen on ensiarvoisen tärkeää, ennen kuin voi siirtyä automatisoimaan asioita. Esim. tämän viikon tehtävissä olleita haasteita olisi ollut äärimmäisen hankalaa taklata ilman samojen asioiden oppimista manuaalisesti tekemällä. Nykyisessä tehokkuutta yli kaiken arvostavassa ajassa  automaatio on kuitenkin välttämättömyys ja erinomainen työkalu, kun sitä oppii käyttämään.