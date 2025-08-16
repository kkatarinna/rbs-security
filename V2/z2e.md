## Spoofing
- Klijent misli da komunicira sa serverom, a zapravo komunicira sa napadačem.
- Moguće je da su eksterni servisi (banka, email servis, transport servis, hotel rezervacija) kompromitovani, a da aplikacija ne zna da zapravo komunicira sa napadačem.
- Baza može da odgovara drugoj mašini koja nije pravi server jer misli da jeste server.
- Klijent ili napadač se predstavlja kao neko sa većom autorizacijom (npr. admin).
- Krađa identiteta ili krađa tokena za autentifikaciju.

### Generalno rešenje:
- Dodatne provere identiteta (challenge-response, validacije, tajne).
- Upotreba TLS/SSL i sertifikata.
- Zaštita tokena i sesija.
- Višefaktorska autentifikacija (2FA).
- Ograničavanje životnog veka sesija i tokena.

---

## Tampering
- **DNS poisoning** (preusmeravanje saobraćaja).
- Izmena dokumenata ili ugovora između aplikacije i partnerskih firmi (hotel, banka…).
- Izmena rezervacija, korisničkih podataka, finansijskih transakcija.
- Izmena logova da bi se narušio integritet sistema i sakrili tragovi.
- Modifikacija podataka u bazi (npr. SQL Injection).

### Generalno rešenje:
- Kontrola pristupa i privilegija: onemogućiti izmenu podataka od strane nepoverljivih korisnika.
- Logovanje i nadgledanje izmena.
- Rollback i backup podataka.
- Digitalni potpisi i hash provere integriteta fajlova/podataka.

---

## Repudiation
- Poricanje korisnika ili zaposlenih da su izvršili neku akciju.
- Poricanje admina da je nešto izmenio.
- Poricanje da je uplata izvršena (uspešno ili neuspešno).
- Poricanje rezervacije ili otkazivanja rezervacije.
- Generalno poricanje bilo koje akcije u sistemu.

### Generalno rešenje:
- Detaljni i sigurni logovi (audit log).
- Digitalni potpisi i vremenski žigovi (timestamping).
- Mehanizmi vraćanja i analize logova (forenzika).
- Neporecivost kroz kriptografske protokole (npr. ne-repudiation tokeni).

---

## Information Disclosure
- Zaposleni, admin ili napadač je došao do osetljivih podataka i podelio ih javno ili sa neovlašćenim licima.
- Objavljeni poverljivi dokumenti između kompanija (ugovori, poslovni planovi…).
- Kritične informacije: korisničke šifre, brojevi kartica, finansijski izveštaji.

### Generalno rešenje:
- Otežati pristup osetljivim podacima: enkripcija u transportu (TLS) i mirovanju (AES, TDE).
- Princip najmanjih privilegija.
- Maskiranje i pseudonimizacija podataka.
- Praćenje i alarmiranje kod neovlašćenog pristupa.
- Honeypot/lažni podaci da bi stvarni podaci bili manje primamljivi i napadač zbunjen.

---

## Denial Of Service (DoS/DDoS)
- Pretrpavanje zahtevima same aplikacije dok ne postane nedostupna.
- Pretrpavanje zahtevima eksternih servisa potrebnih za rad aplikacije (banka, mail servis…).
- Infrastrukturni napadi (gašenje struje, blokada mreže).

### Generalno rešenje:
- Load balancing i skaliranje sistema.
- Rate limiting i throttling.
- Detekcija i filtriranje sumnjivog saobraćaja (WAF, IDS/IPS).
- Rezervne mašine i servisi (failover sistemi).
- Cloud zaštite od DDoS (AWS Shield, Cloudflare, Akamai…).

---

## Elevation of Privilege
- Napad na sisteme za autentifikaciju i autorizaciju (npr. Keycloak, LDAP).
- Dodeljivanje sebi administratorskih prava.
- Zaobilaženje kontrola pristupa.

### Generalno rešenje:
- Stroga kontrola uloga i privilegija korisnika u sistemu (RBAC/ABAC).
- Sigurna implementacija autentifikacije i autorizacije.
- Princip najmanjih privilegija.
- Redovno ažuriranje i patchovanje servisa.
- Monitoring i alerting za sumnjive eskalacije privilegija.
