# Enkripcija tajnix
---
## Pravljenje ključa


### Heš Funkcija
SHA-256, SHA-3, BLAKE2b

SHA-1, MD5 razbijeni (git koristi SHA-1 :D)

### So
dodavanje pseudo random stringa na šifru kako bi na primer iste šifre dva različita korisnika imala dva različita heša


koriste ga algoritmi za odbranu od brute force napada i
za ojačavanje šifre

### Ključno Razvlačenje
provlačenje ključa kroz key "derivative function" kako bi dobili nove ključeve
koji mogu ponovo da se provlače kako bi šifre bile otporne na brute force napade
super za master ključ pristup


## Napadi
### Brute Force
isprobavanje svih mogućnosti dok se heš vrednosti ne poklope

pomoć rainbow table (već predefinisane šifre iz nekog data leaka recimo)


## KDF Algoritmi (najbolji za enkripciju pomoću master ključa)
Svaki od ovih algoritama koristi **salt** i razvlačenje ključa (key stretching).

### PBKDF2 (2000)
- Koristi SHA-256
- Salt ≥ 16B
- Iteracije ≥ 100.000 (do 1M)
- Key length ≥ 256 bit
- Kompatibilan i FIPS-140

### Bcrypt (1999)
- Koristi Blowfish key schedule
- Memory-hard dizajn otežava paralelizam
- Salt ≥ 16B
- Cost ≥ 10–12 (broj iteracija 2^cost)
  
### Scrypt (2009)
- Koristi SHA-256
- Memory-hard: popunjava memoriju pseudo-random blokovima i nasumično ih pristupa
- Salt ≥ 16B
- Parametri:
  - N = 2^17 (CPU/memory cost)
  - r = 8 (blok size)
  - p = 1 (paralelizam)

### Argon2id (2015)
- Koristi internu Blake2b hash funkciju
- Pobednik PHC, trenutno najpreporučeni KDF
- Memory-hard: pravi matricu u memoriji i meša blokove
- Salt ≥ 16B
- Parametri:
  - m = 46 MiB (memorija)
  - t = 1 (iteracije / prolazi kroz memoriju)
  - p = 1 (paralelizam / broj jezgara)

## Provajderi