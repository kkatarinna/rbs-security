# Authentication Napad

Ova klasa se oslanja na lošu implementaciju autentikacije ili autorizacije

autentikacija - ko je korisnik

autorizacija - da li korisnik ima prava da nešto uradi

autentikacija, dokazivanje da ste vi korisnik za koji tvrdite da jeste, je moguće na sledeća tri načina:
- nešto znate (šifru, ključ...)
- nešto imate (telefon, mail, token...)
- nešto jeste(otisak prsta, skeniranje lica,skeniranje oka)

problem dolazi kada korisnik probije autentikaciju, uspešno se predstavi kao neko ko nije, ili probije autorizaciju, radi akcije koje mu nisu dozvoljene

### Uspešan napad
 od pregleda podataka od strane korisnika koji ne bi smeo da ih vidi, širenje površine napada (pristupačnost više stranica omogućava više mogućnosti za napade), do odradjivanja akcija koji su destruktivni za sistem (izmena bitnih podataka ili celokupno brisanje)


### Ranjivosti koje dovode do uspeha ovog napada su
- loša implementacija autorizacije
    - ne vrši se dovoljno dobra provera da li neki korisnik sme da odradi nešto

- loša implementacija autentikacija
    - šifra je previše jednostavna i može da se brute forcuje ili username i šifra prate neki patern što dodatno olakšava brute force, takodje DEFAULT šifra (redovna greška)
    - ne postoji 2fa
    - baze gde se čuvaju šifre nisu dovoljno zaštićene 


### Primerene kontramere
- za bolju autorizaciju
    - potreban je sistem uloga ili bilo koji sistem za dodeljivanje dozvola odredjenim korisnicima ili grupama za neku akciju
    - neki sistemi: rbac, MAC, ABAC, PBAC, Rule-Based, ACL,
    - za svaku stranicu provera da li imaju pristup toj stranici (npr. authguard u angularu)
    - zbog mogućnosti  zaobilaska fronteda potrebna je provera na backendu da li korisnik sme da odradi neku akciju
    - nije loše logovati takve sumnjive pokušaje za pristup nečemu što nije dozvoljeno
- za bolju autentikaciju
    - potrebno implementirati 2fa ili čak 3fa najbolje iz različitih klasa (znaš, imaš, jesi), npr lozinka(znaš), email(imaš)
    - postaviti uslove za moguće lozinke, da ne bude prekratka, da ima cifre, specijalne karaktere kako bi otežalo brute force
    - nemati paterne u username npr `{prezime}_{broj_indeksa}_{godina}@uns.ac.rs` ili šiframa
    - nikad ne ostavljati default šifre
    - ne davati odgvor napadaču u zavisnosti da li je username tačan ili ne ili bilo koju naznaku da je uername tačan u slučajevima kada napadač pokušava da brute forcuje i username i šifru
    - šifre i slični podaci trebaju da se skladište na bezbedan i siguran način da napadači ne mogu da dodju do njih iako će uvek doći do data leaka
    - redovno menjanji šifara (značajna izmena, ne mypassword1! -> mypassword2!)