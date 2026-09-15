# 1. Johdanto

Prometheus-monitoroinnin tarkoituksena on kerätä verkossa olevien järjestelmien tilaa kuvaavia mittareita. Yhdistettynä Grafanan visualisointiin, mittareita on helppo tulkita ja muodostaa selkeä tilannekuva ympäristöstä. Tämä helpottaa esim. poikkeamien ja ongelmien havaitsemista tai suorituskyvyn loppumista.

---

# 2. Node Exporterin käyttöönotto

Annetuilla ohjeilla Node Exporterin asennus oli helppoa. Kirjauduin web1-palvelimelle komennolla "docker exec -it clab-hamk-verkonhallinta-golden-web1 bash" ja asensin wget-työkalun komennolla "apt update && apt install wget tar -y".

Node Exporterin uusin versio tätä kirjoittaessa on 1.12.1, joten latasin wget-työkalun avulla GitHubista node_exporter-1.12.1.linux-amd64.tar.gz.gz-tiedoston, purin sen ja käynnistin Node Exporterin komennolla "./node_exporter".

Tämän jälkeen ajoin komennon "curl http://localhost:9100/metrics" uudessa terminal-ikkunassa ja sain vastaukseksi suuren määrän mittareita.

![Kuvakaappaus]: (https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/node_exporter_metrics.png)
Koko vastaus mittareista tekstitiedostona: (https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/node_exporter_metrics.txt)

---

# 3. Prometheus

Prometheuksen Target Health -sivulta kävi heti ilmi, että web1-palvelimen status on "UP". Näin ollen yhteys toimii. Laajempaa tilannekuvaa varten tulisi Node Exporter asentaa myös muihin laitteisiin. Tämän viikon harjoitusta varten web1-palvelin on kuitenkin riittävä.
![Kuvakaappaus]: (https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/prometheus_targets.png)

---

# 4. Dashboard

![Kuvakaappaus]: (https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/prometheus_dashboard.png)

---

# 5. Kuormitustesti

Kuormitustestit suoritin aiheuttamalla ensin levykuormitusta komennolla "dd if=/dev/zero of=testfile.img bs=1M count=500" sekä tämän jälkeen CPU-kuormitusta komennolla "stress-ng --cpu 4 --timeout 60s".

Molempien testien aiheuttamat muutokset ovat selkeästi havaittavissa Dashboardilla. Disk Usage % ennen kuormitusta oli 9,31% ja levykuormitustestin jälkeen pysyvästi 9,58%. 

CPU-kuormitustestin vaikutus oli hyvin erilainen. Vaikka testin kesto oli 60 sekuntia, näkyy kuormitus mittareissa kuuden minuutin ajan. Kuormitus ennen testiä oli n. 2,6%. Testin alettua kuormitus nousi n. 17%:iin kuudeksi minuutiksi, jonka jälkeen kuormitus laski taas samalle lukemalle kuin ennen testiä.

![Kuvakaappaus]: (https://github.com/VSelkala/verkonhallinta/blob/main/reports/images/load-test.png)

---

# 6. SNMP vs Prometheus

Vertailutaulukko ja johtopäätökset.
Vertaa viikon 2 SNMP-ratkaisua viikon 3 Prometheus-ratkaisuun.

| Ominaisuus | SNMP | Prometheus |
|------------|------|------------|
| Tiedonkeruu | Perustuu hallintapalvelun ja agentin välisiin kyselyihin | Kerää mittarit aktiivisesti scrape-kyselyillä |
| Käyttöönotto | Helppo ottaa käyttöön pienessä mittakaavassa (v2) | Vaativampi käyttöönotto, edellyttää oikeat Exporterit ja konfiguroinnit |
| Mittarien määrä | Rajallinen, riittävä perustarpeisiin | Erittäin monipuolinen ja laaja |
| Visualisointi | Ei visualisointia | Valmiit rajapinnat esim. Grafanaan |
| Hälytysmahdollisuudet | Erillisen järjestelmän kautta | Alert Manager mahdollistaa monipuoliset hälytykset |
| Soveltuvuus pilviympäristöihin | Voidaan käyttää, mutta ei lähtökohtaisesti ole siihen suunniteltu | Soveltuu erittäin hyvin moderneihin ympäristöihin |

# 7. Yhteenveto

1. Mitä hyötyä Prometheuksesta on verrattuna SNMP:hen?
- Prometheus mahdollistaa monipuolisemman tavan kerätä mittareita sekä mahdollistaa selkeän visualisoinnin ja hälytykset
2. Millaisia mittareita ylläpitäjän kannattaa seurata jatkuvasti?
- Esim. CPU:n ja muistin käyttöä, levytilaa, verkkoliikennettä sekä palveluiden saatavuutta.
3. Mitä tietoa dashboardisi tarjoaa ylläpitäjälle?
- Palvelimen oleellisimmat suorituskyky- ja tilatiedot helposti seurattavassa muodossa.
4. Mitä uusia mittareita lisäisit dashboardiin?
- Lisäisin ainakin selkeän UP/DOWN-mittarin, josta näkee heti ellei palvelin ole pystyssä.
5. Miten monitorointitiedosta voisi olla hyötyä vianetsinnässä?
- Niistä löytää helposti mahdollisia poikkeamia aikaleimoineen, jotka helpottavat vikojen havaitsemista.