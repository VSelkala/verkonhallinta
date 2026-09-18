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



---

# 4. Node Exporter Playbook

Koodi ja tulokset.

---

# 5. Vertailu

Käsin vs. automaatio.

---

# 6. Yhteenveto

Opitut asiat.