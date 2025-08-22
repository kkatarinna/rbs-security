# Cross Site Reuqest Forgery (CSRF)

Falsifikovanje zahteva na više sajtova (poznato i kao CSRF) je ranjivost veb bezbednosti koja omogućava napadaču da navede korisnike da izvršavaju radnje koje ne nameravaju da izvršavaju. Omogućava napadaču da delimično zaobiđe politiku istog porekla, koja je osmišljena da spreči različite veb stranice da se međusobno ometaju.


### Uspešan napad

U uspešnom CSRF napadu, napadač uzrokuje da žrtva nenamerno izvrši radnju. Na primer, to može biti promena imejl adrese na nalogu, promena lozinke ili transfer sredstava. U zavisnosti od prirode radnje, napadač bi mogao da stekne potpunu kontrolu nad korisničkim nalogom. Ako ugroženi korisnik ima privilegovanu ulogu unutar aplikacije, onda bi napadač mogao da preuzme potpunu kontrolu nad svim podacima i funkcionalnostima aplikacije.


### Ranjivosti koje dovode do uspeha ovog napada su

- Da bi ovaj napad mogao da se izvede potrebno je znati koji su parametri koje bi neki zahtev uzimao, u slučaju da parametri nisu očekivani napadč teže može da falsifikuje zahtev
- Izvršavanje radnje podrazumeva izdavanje jednog ili više HTTP zahteva, a aplikacija se oslanja isključivo na kolačiće sesije da bi identifikovala korisnika koji je podneo zahteve. Ne postoji drugi mehanizam za praćenje sesija ili validaciju korisničkih zahteva.


### Primerene kontramere

- *CSRF tokeni* - CSRF token je jedinstvena, tajna i nepredvidiva vrednost koju generiše aplikacija na strani servera i deli sa klijentom. Prilikom pokušaja izvršenja osetljive radnje, kao što je slanje obrasca, klijent mora da uključi ispravan CSRF token u zahtev. Ovo veoma otežava napadaču da konstruiše validan zahtev u ime žrtve.

- *SameSite kolačići* - SameSite je bezbednosni mehanizam pregledača koji određuje kada su kolačići veb stranice uključeni u zahteve koji potiču sa drugih veb stranica. Pošto zahtevi za izvršavanje osetljivih radnji obično zahtevaju autentifikovani kolačić sesije, odgovarajuća SameSite ograničenja mogu sprečiti napadača da pokrene ove radnje na više sajtova. Od 2021. godine, Chrome podrazumevano primenjuje Lax SameSite ograničenja. Pošto je ovo predloženi standard, očekujemo da će drugi glavni pregledači usvojiti ovo ponašanje u budućnosti.

- *Validacija zasnovana na refereru* - Neke aplikacije koriste HTTP Referer zaglavlje da bi pokušale da se odbrane od CSRF napada, obično proveravajući da li zahtev potiče sa sopstvenog domena aplikacije. Ovo je generalno manje efikasno od validacije CSRF tokena.