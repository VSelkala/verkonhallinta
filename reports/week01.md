# 1. Johdanto

Ympäristön tarkoituksena on harjoitella verkon hallintaa virtuaalisen yritysverkon pohjalta.
Tärkeimpänä asiana on dokumentoida verkon tilaa ja mahdollisia muutoksia mahdollisimman reaaliaikaisesti.

---

# 2. Verkkokaavio

Containerlab oletuskaavio: 
![Containerlab oletuskaavio](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/topology.png).
Vaihtoehtoinen kaavio (draw.io): 
![Draw.io-kaavio](https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/topology_drawio.png)

---

# 3. Laiteluettelo

| Laite | Tarkoitus |
|---------|---------|
| r1 | Toimii reitittimenä lähiverkon laitteille |
| r2 | Reititin, joka yhdistää kaikki palvelut ja palvelimet toimipisteisiin |
| r3 | Toisen branchin(toimipisteen) reititin |
| client1 | Lähiverkossa sijaitseva tietokone |
| attacker | Hyökkääjää simuloiva toimija |
| web1 | Web-palvelin |
| db1 | Tietokantapalvelin |
| branch-client | Toisessa branchissa(toimipisteessä) sijaitseva tietokone |
| ansible | Automaatiotyökalu configuraatioiden hallintaan |
| prometheus | Työkalu verkon valvontaan ja hälytyksille |
| grafana | Työkalu verkon valvonnan analytiikkaa ja visualisointia varten |
| zabbix | Työkalu verkon suorituskyvyn ja laadun valvontaan |
| cadvisor | Työkalu konttien resurssien valvontaan |
| mgmt-br | Management Bridge |
| srv-bp | Service Bridge |


Serverit:
- clab-hamk-verkonhallinta-golden-db1 (Tietokantapalvelin)
- clab-hamk-verkonhallinta-golden-web1 (Web-palvelin)

Reitittimet:
- clab-hamk-verkonhallinta-golden-r1
- clab-hamk-verkonhallinta-golden-r2
- clab-hamk-verkonhallinta-golden-r3

Clientit:
- clab-hamk-verkonhallinta-golden-client1
- clab-hamk-verkonhallinta-golden-branch-client

Palvelut:
- clab-hamk-verkonhallinta-golden-ansible
- clab-hamk-verkonhallinta-golden-cadvisor (Container Advisor)
- clab-hamk-verkonhallinta-golden-grafana
- clab-hamk-verkonhallinta-golden-prometheus
- clab-hamk-verkonhallinta-golden-syslog
- clab-hamk-verkonhallinta-golden-zabbix

Muut:
- clab-hamk-verkonhallinta-golden-attacker (Hyökkääjää simuloiva toimija)
- clab-hamk-verkonhallinta-golden-mgmt-bp (Management Bridge)
- clab-hamk-verkonhallinta-golden-srv-bp (Service Bridge)


---

# 4. IP-suunnitelma

| Verkko | Tarkoitus | Yhdyskäytävä |
|---------|---------|---------|
| 10.10.10.0/24 | Päätoimipisteen sisäverkko | 10.10.10.1 |
| 10.10.20.0/24 | Palvelinten verkko | 10.10.20.1 |
| 10.10.30.0/24 | Sivutoimipisteen (branch) sisäverkko | 10.10.30.1 |
| 10.10.99.0/24 | Hallinnan verkko | 10.10.99.1 |
| 10.255.12.0/30 | Service Bridgen ja Management Bridgen verkko | 10.255.12.2 |
| 10.255.23.0/30 | Päätoimipisteen ja sivutoimipisteen välinen yhteys | 10.255.23.2 |

Oletusnimipalvelin: 10.255.255.254


---

# 5. Reitityksen analyysi

**Ping-testi:**

*root@client1:/# ping -c 3 10.10.30.101*
PING 10.10.30.101 (10.10.30.101) 56(84) bytes of data.
64 bytes from 10.10.30.101: icmp_seq=1 ttl=61 time=0.164 ms
64 bytes from 10.10.30.101: icmp_seq=2 ttl=61 time=0.083 ms
64 bytes from 10.10.30.101: icmp_seq=3 ttl=61 time=0.061 ms
--- 10.10.30.101 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2048ms
rtt min/avg/max/mdev = 0.061/0.102/0.164/0.044 ms

**Traceroute:**

*root@client1:/# traceroute -n -m 10 10.10.30.101*
traceroute to 10.10.30.101 (10.10.30.101), 10 hops max, 60 byte packets
 1  10.10.10.1  0.388 ms  0.339 ms  0.326 ms
 2  10.255.12.2  0.314 ms  0.290 ms  0.273 ms
 3  10.255.23.2  0.258 ms  0.231 ms  0.212 ms
 4  10.10.30.101  0.194 ms  0.166 ms  0.144 ms

**Reittitauluanalyysit:**

*root@client1:/# ip route show*
default via 10.10.10.1 dev eth1
10.10.10.0/24 dev eth1 proto kernel scope link src 10.10.10.101
172.20.20.0/24 dev eth0 proto kernel scope link src 172.20.20.4

*docker exec clab-hamk-verkonhallinta-golden-r1 vtysh -c "show ip route"*
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
K>* 0.0.0.0/0 [0/0] via 172.20.20.1, eth0, 01:59:17
O   10.10.10.0/24 [110/10] is directly connected, eth2, weight 1, 01:59:17
C * 10.10.10.0/24 is directly connected, eth3, 01:59:17
C>* 10.10.10.0/24 is directly connected, eth2, 01:59:17
O>* 10.10.20.0/24 [110/20] via 10.255.12.2, eth1, weight 1, 01:58:28
O>* 10.10.30.0/24 [110/30] via 10.255.12.2, eth1, weight 1, 01:58:26
O>* 10.10.99.0/24 [110/20] via 10.255.12.2, eth1, weight 1, 01:58:28
O   10.255.12.0/30 [110/10] is directly connected, eth1, weight 1, 01:58:32
C>* 10.255.12.0/30 is directly connected, eth1, 01:59:17
O>* 10.255.23.0/30 [110/20] via 10.255.12.2, eth1, weight 1, 01:58:28
C>* 172.20.20.0/24 is directly connected, eth0, 01:59:17

---

# 6. Yhteenveto

Ympäristön käyttöönotto oli muilta osin sujuvaa, mutta yritin pitkään saada Netboxia toimimaan siinä kuitenkaan onnistumatta.
Dokumentaation luomisessa eniten aikaa vei laitelistauksen tekeminen riittävällä tarkkuudella.
Dokumentaation avulla on helppoa nähdä verkon kokonaiskuva laitteineen ja keskinäisine yhteyksineen.