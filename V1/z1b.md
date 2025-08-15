# Auditing - Log datoteke
Log datoteke predstavljaju zapis hronološkog niza događaja koje softver, operativni sistem ili hardverske komponente generišu tokom rada. One služe kao „dnevnik“ sistema. Beleže informacije o akcijama, greškama, upozorenjima, promenama i interakcijama korisnika ili procesa.

## Osnovne karakteristike
Iako sadržaj log datoteka može da varira u zavisnosti od izvora i svrhe, većina log zapisa sadrži sledeće zajedničke elemente:

* **Vremenska oznaka (timestamp)** – označava tačan trenutak kada se događaj desio.
* **Izvor događaja (source)** – komponenta, servis ili aplikacija koja je generisala zapis.
* **Korisnik ili ID procesa (user/process ID)** – identifikuje ko ili šta je iniciralo događaj.
* **Status kod (status code)** – posebno koristan kod web servera i API servisa za označavanje uspeha ili greške.
* **Tip ili ID događaja (event type/ID)** – numerička ili kategorizovana oznaka vrste događaja.
* **IP adresa ili hostname** – naročito važni kod pristupnih i mrežnih logova.
* **Nivo ozbiljnosti (log severity)** – označava važnost i hitnost događaja (npr. INFO, WARNING, ERROR).

Tipovi log fajlova koji se često koriste su:
* **Sistemski logovi** – beleže aktivnosti na nivou operativnog sistema, uključujući pokretanje i zaustavljanje servisa, sistemske greške, promene konfiguracije i instalaciju ažuriranja.
* **Aplikativni logovi** – pružaju detaljan uvid u rad aplikacija, uključujući greške, upozorenja, uspešne i neuspešne pokušaje povezivanja, kao i specifične događaje unutar aplikacije.
* **Bezbednosni logovi** – sadrže podatke o prijavljivanju i odjavljivanju korisnika, pokušajima neovlašćenog pristupa, promenama dozvola, detekciji malvera i neuobičajenom ponašanju korisnika.
* **Mrežni logovi** – prate komunikaciju između uređaja u mreži i mogu uključivati informacije o pokušajima upada, promenama konfiguracije firewall-a ili aktivnostima VPN konekcija.
* **Change logovi** – beleže promene izvršene na sistemima, konfiguracijama, softveru ili infrastrukturi. Sadrže podatke o tome ko je napravio promenu, kada i zašto, što je ključno za reviziju, otklanjanje problema, usklađenost i bezbednost.
* **Logovi firewall-a** – potiču od firewall-ova, IDS/IPS sistema i drugih bezbednosnih uređaja, beleže neuspele pokušaje povezivanja, pokušaje iskorišćavanja ranjivosti i nadgledaju sumnjive aktivnosti.

Jedna od uloga aplikativnog logovanja jeste i non-repudiation tj. nemogućnost poricanja odgovornosti. Cilj je obezbediti da akteri (korisnici ili procesi) ne mogu negirati svoje akcije, kroz kreiranje pouzdane evidencije o svim relevantnim događajima.

U praksi je to teško potpuno ostvariti kada su logovi jedini izvor dokaza. Njihova verodostojnost često zavisi od toga da li je sistem logovanja pravilno auditovan i da li se veruje strani koja vodi logove.
Naprednije metode, poput digitalnog potpisivanja logova, mogu značajno povećati integritet i neosporivost, ali se u praksi teže primenjuju zbog složenosti i troškova implementacije.

## Mehanizmi logovanja
Mehanizam za logovanje mora stremiti ka tome da su logovi uredni, da je “pretrpanost” minimalizovana.
### Log rotate
Logrotate je standardni Linux alat namenjen automatskoj rotaciji, kompresiji i brisanju starih log fajlova, kako bi se sprečilo njihovo prekomerno zauzimanje prostora i održala preglednost sistema. Rotacija se obično vrši periodično (dnevno, nedeljno ili mesečno), a konfiguracija se podešava putem globalnog fajla `ect/logrotate.conf`

#### Tok rada
1. **Rotacija** - preimenovanje postojećeg log fajla
2. **Kompresija** - stari fajlovi se kompresuju 
3. **Brisanje** - najstariji fajlovi se brišu nakon zadatog broja iteracija
4. **Restart aplikacije** - opcionalno, može se poslati signal aplikaciji da nastavi logovanje u novi fajl

Logrotate se najčešće pokreće automatski putem cron job-a ili systemd timer-a, što obezbeđuje redovno izvršavanje bez potrebe za manuelnom intervencijom.

#### Bezbednosni aspekti
Iako je logrotate pouzdan alat, u praksi se mogu javiti sledeći problemi:

* **Log injection** - ako aplikacija zapisuje nefiltrirane korisničke podatke, napadač može ubaciti lažne ili manipulativne linije u log fajl, koje će se sačuvati i u rotiranim verzijama.
* **Race condition** - ako aplikacija i logrotate istovremeno pristupaju log fajlu, može doći do gubitka podataka ili oštećenja fajla.
* **Neovlašćen pristup** - pogrešne permisije na rotiranim logovima mogu omogućiti čitanje osetljivih podataka neovlašćenim korisnicima.

#### Docker log rotacija
Za razliku od klasičnih log fajlova na sistemu, Docker kontejneri po defaultu koriste `json-file` log driver, koji čuva izlaz (`stdout` i `stderr`) kontejnera u JSON formatu u fajlu na host mašini. Ako se rotacija logova ne podesi, ovi fajlovi mogu neograničeno rasti i zauzeti veliki deo diska.

Docker omogućava rotaciju logova na nivou `log driver` konfiguracije, a ne pomoću `logrotate` alata, i to preko opcija `max-size` i `max-file`

**Bezbednosni aspekti docker rotacije**
* **Preopterećenje diska** - bez ograničenja veličine, Docker log fajlovi mogu zauzeti ceo disk i izazvati pad servisa.
* **Neovlašćen pristup** - Docker log fajlovi mogu sadržati sve što aplikacija ispisuje, uključujući osetljive podatke kao što su korisnička imena, lozinke, API ključevi, tokeni ili delovi poslovne logike. Ako permisije nad ovim fajlovima nisu adekvatno podešene, postoji rizik da neovlašćeni korisnici pristupe ovim informacijama.
* **Kompresija** - za razliku od logrotate, Docker ne nudi ugrađenu kompresiju rotiranih logova; ako je potrebna, mora se kombinovati sa eksternim alatima.

## Podešavanje log pipeline-a - Logstash
Pipeline za prijem logova obično se organizuje kroz tri ključne faze:
1. **Input** - logovi dolaze iz različitih izvora, kao što su plikovi, syslog, API, ili agenti poput Filebeat.
2. **Filter** - podaci se parsiraju, normalizuju i eventualno obogaćuju metapodacima koji su važni za analizu ili bezbednost.
3. **Output** - logovi se šalju na ciljne sisteme za skladištenje i analizu, poput Elasticsearch-a, SIEM rešenja...
![slika pipeline-a](./lg2x.avif)

Tipična konfiguracija pipeline-a izgleda ovako:
```
input {
      plugin_name{...}
 }

filter {
     plugin_name{...}
}

output {
     plugin_name{...}
}
```
1. **Input (prijem podataka)**
Input plugin-i omogućavaju prikupljanje logova iz različitih izvora, uključujući:

    * HTTP – prijem log zapisa putem HTTP endpoint-a.

    * Beats – preuzimanje logova sa Beats framework agenta.

    * Redis – čitanje log zapisa iz Redis instance.

    * Unix socket – prijem logova preko Unix socket veze.

2. **Filter (obrada i obogaćivanje podataka)**
Filter plugin-i služe za parsiranje, transformaciju i obogaćivanje log zapisa, npr.:

    * JSON – parsiranje logova u JSON formatu.

    * Grok – strukturisanje i izdvajanje podataka iz sirovih log zapisa.

    * I18n – uklanjanje specijalnih karaktera iz logova.

    * GeoIP – dodavanje geografskih informacija na osnovu IP adrese.

3. **Output (prosleđivanje podataka)**
Nakon obrade, output plugin-i omogućavaju prosleđivanje logova ka ciljnom sistemu, uključujući:

    * WebSocket – slanje logova na WebSocket endpoint.

    *  S3 – skladištenje log zapisa na Amazon S3 servis.

    * Syslog – prosleđivanje logova Syslog serveru.

    * Elasticsearch – upis logova u Elasticsearch bazu kao deo Elastic stack-a.

## Čuvanje i indeksiranje u Elasticsearch-u
Elasticsearch je distribuirani pretraživački i analitički engine zasnovan na Apache Lucene biblioteci, dizajniran da omogućava brzu pretragu i analizu velikih količina podataka, uključujući i logove.

Logovi u Elasticsearch-u se čuvaju u indeksima, koji funkcionišu slično kao baze podataka optimizovane za pretragu. Proces indeksiranja podrazumeva parsiranje, normalizaciju i struktuisanje podataka iz log zapisa kako bi se omogućila brza pretraga i agregacija. Tipično se koriste index template-i i ingest pipeline-ovi koji automatski definišu kako se logovi mapiraju, obogaćuju metapodacima (timestamp, nivo loga, IP adresa) i skladište u indeksima. Ovakav pristup omogućava korisnicima da precizno filtriraju, analiziraju i vizualizuju događaje iz logova u realnom vremenu, često kroz Kibana interfejs ili druge analitičke alate.

**Ingest pipeline** opisuje kako se logovi parsiraju i obogaćuju (npr. timestamp, log level i host IP). Primer:
``` json
   PUT _ingest/pipeline/logs-example-default {
     "description": "Extracts the timestamp log level and host ip",
     "processors": [
       {
         "dissect": {
           "field": "message",
           "pattern": "%{@timestamp} %{log.level} %{host.ip} %{message}"
         }
       }
     ]
   }
```

**Index template** definiše pravila koja se primenjuju na buduće indekse — poput korišćenja gore navedenog `ingest_pipeline` kao podrazumevanog

``` json
   PUT _index_template/logs-example-default-template {
     "index_patterns": [ "logs-example-*" ],
     "data_stream": { },
     "priority": 500,
     "template": {
       "settings": {
         "index.default_pipeline":"logs-example-default"
       }
     },
     "composed_of": [
       "logs-mappings", "logs-settings", "logs@custom", "ecs@dynamic_templates"
     ],
     "ignore_missing_component_templates": ["logs@custom"]
   }
```

Konfigurisanje za Elasticsearch:

Input - daje ime source fajla za podatke
```
input {
  file {
    path => "/var/log/app/*.log"
    start_position => "beginning"
  }
}
```
Filter - odredjuje način obrade podataka
```
filter {
   grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
  date {
    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
}
```

Output - definiše fajl i ime servisa kome idu podaci nakon obrade
```
output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

## Kibana
Kibana je vizualizacioni i analitički alat koji se koristi zajedno sa Elasticsearch-om za pregled, pretragu i analizu podataka. Dok Elasticsearch služi za skladištenje i indeksiranje podataka (logova, metrike, događaja), Kibana omogućava korisnicima da te podatke interaktivno pregledaju i vizualizuju.

Vizualizacija se satoji iz nekoliko koraka:
1. Pretraga i filtriranje podataka

    - Korisnici kreiraju upite koristeći Elasticsearch Query DSL ili Kibana Search Bar.

    - Podaci se mogu filtrirati po poljima kao što su timestamp, log level, IP adresa, korisnik ili aplikacija.

    - Kibana poseduje razlicite filtere:
        * Search bar - direktan unos upita
        * Filter panel - panel za dodavanje filtera po poljima ( na primer filtriranje logova odredjenog host-a)
        * Vremenski filteri

2. Kreiranje vizualizacija

    - Podaci se prikazuju kroz različite tipove vizualizacija:

        - Linijski, stubičasti ili area grafikon

        - Pie chart ili donut chart

        - Heatmap

        - Tabele i metrike

    - Vizualizacije mogu koristiti agregacije (npr. broj događaja po satu, po nivou loga ili po korisniku).

3. Kreiranje dashboard-a
    - Više vizualizacija se kombinuje u dashboard za pregled kompletnog seta podataka u realnom vremenu.

    - Dashboard može biti interaktivan: filteri i vremenski intervali primenjuju se na sve vizualizacije odjednom.

4. Deljenje i eksportovanje

    - Dashboard-i se mogu deliti sa drugim korisnicima ili eksportovati kao PDF/PNG.

    - Omogućava saradnju i jednostavan pregled ključnih metrika i logova.

5. Alerting i monitoring

    - Kibana može slati upozorenja kada određeni uslovi u logovima budu zadovoljeni (npr. broj grešaka preko određene granice).

## ELK Stack
ELK stack je skup alata koji omogućava centralizovano prikupljanje, obradu, skladištenje i vizualizaciju podataka, posebno logova i metrika iz IT sistema. Naziv ELK dolazi od inicijala tri ključna alata:

1. **Elasticsearch** – Distribuirani pretraživački i analitički engine. Služi za indeksiranje, čuvanje i pretragu logova u realnom vremenu.

2. **Logstash** – Alat za prikupljanje i obradu logova. Omogućava parsiranje, filtriranje, obogaćivanje i prosleđivanje logova ka Elasticsearch-u.

3. **Kibana** – Vizualizacioni i analitički alat. Omogućava pretragu, filtriranje i vizualizaciju logova kroz grafove, tabele i dashboard-e.