# 1. Johdanto

Mikä on Infrastructure as Code?

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

# 5. Vertailu

Käsin vs. automaatio.

---

# 6. Yhteenveto

Opitut asiat.