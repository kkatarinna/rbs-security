# Višefaktorska autentikacija
Višefaktorska autentikacija (MFA) se zasniva na tome da korisnik mora dokazati svoj identitet pomoću najmanje dva različita tipa faktora iz različitih kategorija.

1. **Nešto što znaš** (knowledge factors)
    * Lozinka ili PIN
    * Bezbednosna pitanja
    * Fraze ili kodovi poznati samo korisniku
2. **Nešto što imaš** (possession factors)
    * Mobilni telefon (SMS kod, poziv)
    * Hardverski token (npr. RSA SecurID, YubiKey)
    * Pametna kartica
    * Softverski token u aplikaciji (Google Authenticator, Microsoft Authenticator)
    * E-mail kod (manje bezbedno, ali se koristi)
3. **Nešto što jesi** (inherence factors)
    * Otisak prsta
    * Prepoznavanje lica
    * Skener zenice ili oka
    * Glasovna identifikacija
    * Prepoznavanje obrasca kucanja na tastaturi (keystroke dynamics)
4. **Negde gde jesi** (location factors)
    * Geolokacija (GPS koordinata)
    * IP adresa iz dozvoljenog raspona
    * Prijava samo sa određene mreže
5. **Nešto što radiš** (behavioral factors)
    * Način držanja telefona
    * Obrazac korišćenja aplikacije
    * Pokreti miša ili prstom po ekranu

## Višefaktorska autentikacija - possession factors
## Hardverski token
Hardverski token predstavlja fizički uređaj namenjen verifikaciji identiteta korisnika prilikom prijave na sistem. On se najčešće koristi kao drugi faktor u okviru višefaktorske autentikacije (MFA), zajedno sa lozinkom. Za razliku od softverskih tokena, hardverski tokeni funkcionišu nezavisno od pametnih telefona ili računara, čime se smanjuje rizik od infekcije malverom, fišing napada ili kompromitacije uređaja. Ovi bezbedni mehanizmi autentikacije doprinose zaštiti pristupa korisničkim nalozima.

Primeri hardverskih tokena uključuju:
* USB uređaje (npr. YubiKey)
* Ključeve za generisanje jednokratnih lozinki (OTP fob)
* Pametne kartice sa ugrađenim čipom
* Token uređaje sa NFC ili Bluetooth podrškom za bežičnu autentikaciju

### YubiKey 
Yubico je kompanija koja proizvodi kriptografske uredjaje koji mogu raditi na više načina. Svaki od tih načina ima svoj princip generisanja i verifikacije koda.

### YubiKey OTP generator
U memoriji uredjaja se nalazi **secret key** i **algoritam za generisanje OTP**

Yubico OTP je jedinstveni 44-karakterni ModHex string (OTP), koji se sastoji iz dve glavne komponente: prvih 12 karaktera koji čine **Public ID** (ne menja se—identifikuje uređaj) i preostalih 32 karaktera koji su **kriptografski generisan kod**

Kod se pravi tako što se u 128-bitnu strukturu kombinuju sledeći podaci: privatni ID, brojač upotrebe, vremenski pečat, sesijski brojač, slučajni broj i kontrolni zbir. Ta struktura se zatim AES-128 enkriptuje i enkodira u ModHex formatu

![slika yubikey-otp-a](./otp_details.png)

### YubiKey OTP bezbednosni propusti i najčešće greške

**Greške u implementaciji**
1. **Neadekvatna sinhronizacija brojača (counter drift)**
    Ako server i uređaj nisu sinhronizovani, OTP može biti odbijen. Preporučuje se implementacija "look-ahead" prozora kako bi server prihvatio nekoliko narednih brojača.

2. **Nevalidna registracija uređaja**
Ako se public ID uređaja ne poveže pravilno sa tajnim ključem na serveru, OTP neće biti validiran. Važno je osigurati ispravnu registraciju uređaja.
3. **Neadekvatna validacija na serveru**
Ignorisanje HMAC potpisa ili poverenje u OTP bez verifikacije može dovesti do sigurnosnih propusta. Preporučuje se rigorozna verifikacija OTP-a na serveru.

**Bezbednosni propusti**
1. **Fizička krađa uređaja**
Ako napadač fizički ukrade YubiKey, može pristupiti zaštićenim resursima. Preporučuje se implementacija politike blokiranja izgubljenog uređaja i resetovanja MFA.
2. **Kompromitacija servera**
Ako server ili baza podataka sačuva tajni ključ za OTP nešifrovano, napadač može generisati validne OTP kodove. Preporučuje se upotreba enkripcije za zaštitu tajnog ključa.
3. **Man-in-the-middle napadi sa OTP-om**
Napadač može presresti OTP i iskoristiti ga za neovlašćen pristup. Preporučuje se korišćenje HTTPS protokola i dodatnih sigurnosnih mera za zaštitu od ovih napada.

### YubiKey Challenge-Response
Razlikuje se od OTP režima po tome što se šifra/token ne prikazuje korisniku. Uredjaj kriptografski odgovara na izazov koji mu server pošalje.

1. Registracija uredjaja
    * Challange-Response rezim radi preko simetričnog algoritma za enkripciju
    * Yubikey uredjaj je inicijalno konfigurisan sa tajnim ključem 
    * Isti tajni ključ postoji i na serveru u svrhu provere odgovora

2. Slanje izazova
    * Server generiše niz bajtova i šalje korisniku ili aplikaciji koja komunicira sa YubiKey

3. Potpisivanje izazova
    * Korisnik prisloni ili pritisne touch senzor na YubiKey-u.
    Touch senzor služi kao fizička potvrda da korisnik želi da se izazov potpiše — sprečava da YubiKey automatski odgovara na izazove bez prisustva korisnika.
    * Uređaj koristi HMAC-SHA1 algoritam (ili po konfiguraciji neki drugi HMAC algoritam) da izračuna potpis:
    $$
    response = HMAC-SHA1 (secret key,challenge)
    $$
    * HMAC je hash based message autentication code koji ovde sluzi za enkripciju i potpisivanje izazova

    * HMAC funkcija kombinuje tajni ključ i challenge tako da svaka promena challenge-a daje potpuno drugačiji rezultat

    * Rezultat HMAC funkcije je response, koji je u suštini digitalni potpis izazova.

4. Verifikacija
    * Server koristi tajni ključ (ili javni ključ ako je asimetrična konfiguracija) da izračuna očekivani odgovor na isti challenge.
    * Ako response sa YubiKey-a i očekivani odgovor poklapaju, server potvrđuje identitet korisnika i dozvoljava pristup.

### Bezbednosni propusti YubiKey u Challenge-Response režimu
1. Zavisnost od statičkog ključa u KeeChallenge
    KeeChallenge koristi statički HMAC ključ za enkripciju baze podataka, što ga čini podložnim napadima ponovnog korišćenja (replay attacks).  Ovaj pristup je napustio razvoj i nije bezbedan za upotrebu.
2. Oslanjanje na fizičku sigurnost uređaja
    Ako napadač fizički ukrade YubiKey, može generisati validne odgovore bez potrebe za PIN-om ili dodatnim autentifikacijama. Preporučuje se korišćenje PIN-a ili drugih metoda zaštite uređaja.
3. Neadekvatna sinhronizacija izazova
    Ako server ne implementira jedinstvene izazove ili ne proverava prethodno korišćene, može doći do napada ponovnog korišćenja (replay attacks). Važno je koristiti jedinstvene izazove sa vremenskim oznakama ili brojačima događaja.
4. Slaba implementacija HMAC verifikacije
    Neadekvatna implementacija HMAC verifikacije može omogućiti napadaču da generiše validne odgovore. 

## Integrisanje MFA na ELK okruženju
Integracija MFA u ELK stack (Elasticsearch, Logstash, Kibana) se radi na nivou **autentikacije korisnika**

Elastic Security (ranije X-Pack) nudi ugrađenu **autentikaciju i autorizaciju.**
Podržava različite izvore identiteta:
* Native Elasticsearch users (lozinke)
* LDAP / Active Directory
* SAML / OpenID Connect (OIDC)
* MFA se obično integriše preko SAML ili OIDC IdP-a, npr.:
    * Okta
    * Keycloak
    * Duo
    * Azure AD


**Toka autentikacije:**
1. Korisnik pokuša da se prijavi u Kibana UI.
2. Kibana preusmerava na IdP.
3. IdP traži MFA (OTP, YubiKey, push notifikaciju, biometriju).
4. Ako je MFA uspešan, IdP vraća SAML/OIDC token Kibani.
5. Kibana prosleđuje token Elasticsearch-u za autorizaciju.