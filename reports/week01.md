# 1. Johdanto

Kuvaile lyhyesti ympäristön tarkoitus.

---

# 2. Verkkokaavio

Lisää laatimasi verkkokaavio.

---

# 3. Laiteluettelo

╭───────────────────────────────────────────────┬──────────────────────────────────┬───────────┬────────────────╮
│                      Name                     │            Kind/Image            │   State   │ IPv4/6 Address │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-ansible       │ linux                            │ running   │ 172.20.20.9    │
│                                               │ ubuntu:24.04                     │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-attacker      │ linux                            │ running   │ 172.20.20.4    │
│                                               │ kalilinux/kali-rolling:latest    │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-branch-client │ linux                            │ running   │ 172.20.20.8    │
│                                               │ ubuntu:24.04                     │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-cadvisor      │ linux                            │ running   │ 172.20.20.11   │
│                                               │ gcr.io/cadvisor/cadvisor:v0.49.1 │ (healthy) │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-client1       │ linux                            │ running   │ 172.20.20.16   │
│                                               │ ubuntu:24.04                     │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-db1           │ linux                            │ running   │ 172.20.20.5    │
│                                               │ ubuntu:24.04                     │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-grafana       │ linux                            │ running   │ 172.20.20.14   │
│                                               │ grafana/grafana:latest           │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-mgmt-bp       │ linux                            │ running   │ 172.20.20.3    │
│                                               │ alpine:latest                    │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-prometheus    │ linux                            │ running   │ 172.20.20.6    │
│                                               │ prom/prometheus:latest           │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-r1            │ linux                            │ running   │ 172.20.20.12   │
│                                               │ frrouting/frr:latest             │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-r2            │ linux                            │ running   │ 172.20.20.13   │
│                                               │ frrouting/frr:latest             │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-r3            │ linux                            │ running   │ 172.20.20.15   │
│                                               │ frrouting/frr:latest             │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-srv-bp        │ linux                            │ running   │ 172.20.20.7    │
│                                               │ alpine:latest                    │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-syslog        │ linux                            │ running   │ 172.20.20.50   │
│                                               │ rsyslog/syslog_appliance_alpine  │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-web1          │ linux                            │ running   │ 172.20.20.2    │
│                                               │ ubuntu:24.04                     │           │ N/A            │
├───────────────────────────────────────────────┼──────────────────────────────────┼───────────┼────────────────┤
│ clab-hamk-verkonhallinta-golden-zabbix        │ linux                            │ running   │ 172.20.20.10   │
│                                               │ zabbix/zabbix-appliance:latest   │           │ N/A            │
╰───────────────────────────────────────────────┴──────────────────────────────────┴───────────┴────────────────╯

---

# 4. IP-suunnitelma

Dokumentoi:

verkot
aliverkot
yhdyskäytävät
tärkeimmät IP-osoitteet

---

# 5. Reitityksen analyysi

Sisällytä:

ping-testit
traceroute
reittitauluanalyysi

---

# 6. Yhteenveto

Pohdi:

Mitkä asiat verkon dokumentaation muodostamisessa kuluttivat eniten aikaa ja miksi?
Miten dokumentaatio mielestäsi auttaa palvelusta vastaavaa it-asiantuntijaa työssään?