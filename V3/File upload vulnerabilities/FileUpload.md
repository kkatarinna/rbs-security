# File upload vulnerabilities

Ova vrsta napada dešava se kada programer nema validaciju za upload fajla. Kod validacije fajlova bitno je proveriti:
    * Ime fajla - da li sadrži odgovarajuću ekstenziju
    * Tip fajla (MIME type) - da li je zaista traženi fajl (npr slika)
    * Sadržaj fajla - da li odgovara deklarisanom tipu
    * Veličinu fajla 
    * Lokaciju čuvanja fajla - da li korisnik ima kontrolu nad time
Nepostojanje validacije ili nepravilna/nedovoljno jaka validacija može da dozvoli korisniku da ubacijue maliciozne fajlove poput skripti ili binarnih fajloval u aplikaciju. 

## Uticaj file upload ranjivosti na sistem
Uticaj ove ranjivosti zavisi od dva slučaja neuspešne validacije:
    1. Koji aspekt fajla web alplikacija ne uspeva da validira (veličina, tip ..)
    2. Koja su ograničenja primenjena na datoteku nakon što se uspešno otpremila

### RCE - Remote Code Execute + Web Shell
Ako server ne proverava tip fajla i dozvoljava izvršavanja .php .asp .jsp ... onda napadač može otpremiti skriptu (Web Shell) koja omogućava daljinsko komandovanje serverom. Time dobija potpunu kontrolu nad serverom što je najgori scenario jer je ceo sistem kompromitovan.

### Overwritting kritičnih fajlova
Ako se ne proverava ime fajla napadač može otpremiti fajl sa imenom koje već postoji (index.html) i time potencijalno prebrisati važne fajlove za aplikaciju poput konfiguracija. 

Ako je kombinovano sa directory traversal ranjivošću fajl se može ubaciti u potpuno drugu lokaciju. Time se postiže oštećenje postojećih fajlova, njihovo brisanje i njihova zamena.

### Denial of Service 
Ako se ne ograniči veličina fajla napadač može otpremiti prevelike fajlove i time izazvati pad sistema i nemogućnost rada aplikacije. 

### XSS - Cross-Site Scripting
Ako server dozvoljava otpremanje fajlova koji se renderuju u browser-u (.html .svg) napadač može ubaciti maliciozni .js kod. Kad drugi korisnici otvore taj fajl izvršava se XSS napad. 

## Zadaci 
### <span style="color:green">Lab:</span> Remote code execution via web shell upload
1. Preuzeti burp sa sajta.
2. Otvoriti Chromium iz burp-a. Ulogovati se na nalog i pronaći zadatak.
3. Iz Proxy > HTTP history na Burp-u pratiti zahteve koji se šalju.
4. Definisati php web shell [Prikaži file.php](./file.php)
5. Ulogovati se na lab vežbu sa datim kredencijalima (wiener:peter)
6. Uploadovati web shell umesto svoje profilne slike [slika upload fajla](./sc/upload.png)
7. Vratiti se na svoj nalog nakon uspešnog otpremanja slike
8. Pročitati response iz tajnog fajla [slika respons-a](./sc/upload.png)