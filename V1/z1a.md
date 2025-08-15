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

- Parametri:
- Salt ≥ 16B
- Iteracije ≥ 100.000 (do 1M)
- Key length ≥ 256 bit
- Kompatibilan i FIPS-140

- Provajderi i njihove najnovije verzije:
  - **OpenSSL (C)** — implementacija je deo OpenSSL paketa  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za PBKDF2 u najnovijim verzijama.
  - **.NET (`Rfc2898DeriveBytes`)** — ugrađeno u .NET Framework  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za PBKDF2 u najnovijim verzijama.
  - **Node.js `crypto.pbkdf2` (native)** — ugrađeno u Node.js  
    - **Poznate ranjivosti:**  
      - **CVE-2025-6545**: Signature spoofing zbog nevalidnog inputa (`lib/to-buffer.js`) — pogađa verzije 3.0.10 do 3.1.2.  
      - **CVE-2025-6547**: Ignorisanje `Uint8Array` inputa → generisanje statičkog ključa — pogađa verzije ≤ 3.1.2.
  - **browserify/pbkdf2 (JavaScript)** — **v3.1.3**
    - **Poznate ranjivosti:**  
      - **CVE-2025-6545** i **CVE-2025-6547** — pogađa verzije do 3.1.2, nova verzija v3.1.3 je zakrpljena.
  - **Crypto-JS** — **v4.2.0**
    - **Poznate ranjivosti:**  
      - **CVE-2023-46233**: PBKDF2 je slabiji od originalne specifikacije iz 1993. godine, zbog korišćenja SHA1 i niskog broja iteracija.
  - **wolfSSL (C)** — implementacija je deo wolfSSL paketa  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za PBKDF2 u najnovijim verzijama.
  - **Libgcrypt (C)** — implementacija je deo Libgcrypt paketa  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za PBKDF2 u najnovijim verzijama.


### Bcrypt (1999)
- Koristi Blowfish key schedule
- Memory-hard dizajn otežava paralelizam

- Parametri:
- Salt ≥ 16B
- Cost ≥ 10–12 (broj iteracija 2^cost)


- Provajderi:
  - **bcrypt** (Node.js, native) — **v6.0.0**  — **nema poznatih ranjivosti** za ovu verziju.
  - **bcryptjs** (JavaScript) — **v3.0.2** — **nema poznatih ranjivosti** (nema direktnih CVE zapisa).{index=1}
  - **Python** `bcrypt` — **v4.3.0** — **nema poznatih ranjivosti** za ovu verziju prema PyPI.
  - **Java**:
    - `jBCrypt` *(org.mindrot:jbcrypt)* — **v0.4**  — **nema poznatih ranjivosti**. 
    - `at.favre.lib:bcrypt` — **v0.10.2**— **nema poznatih ranjivosti**. 
    - `Spring Security BCryptPasswordEncoder` *(spring-security-crypto)* — deo Spring Security; 
    
      postoji **CVE-2025-22228** (nepravilno poređenje lozinki dužih od 72 znaka). Rešeno u zakrpljenim izdanjima Spring Security prema advisory-ju — ažurirati na **ispravljenu** verziju navedenu u njihovom biltenu.
  - **C# (.NET)**:
    - `BCrypt.Net-Next` — *(NuGet Verzija)* — **nema poznatih ranjivosti**
  - **Ruby**:
    - `bcrypt` gem — **v3.1.20** *(najnovija)* — **nema poznatih ranjivosti**
  - **Go**:
    - `golang.org/x/crypto/bcrypt` — deo modula **golang.org/x/crypto** *(koristite >= **v0.31.0** zbog bezbednosne ispravke; novija izdanja takođe OK)* — **nema poznatih ranjivosti** specifično u `bcrypt` delu; napomena: u decembru 2024. ispravljena je ranjivost u **x/crypto/ssh** pa se preporučuje bar v0.31.0 ili novije.
  - **C**:
    - OpenBSD implementacija `bcrypt()` u `libc`/`crypt(3)` — nema semver verziju; **nema poznatih aktuelnih ranjivosti** u modernim distribucijama.

### Scrypt (2009)
- Koristi SHA-256
- Memory-hard: popunjava memoriju pseudo-random blokovima i nasumično ih pristupa
- Salt ≥ 16B

- Parametri:
  - N = 2^17 (CPU/memory cost)
  - r = 8 (blok size)
  - p = 1 (paralelizam)


- Provajderi i njihove najnovije verzije:
  - **Node.js `crypto.scrypt` (native)**  
    - Verzija: deo Node.js od verzije 10.5.0  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za `crypto.scrypt` u najnovijim verzijama.  
    - Reference: [Node.js Crypto Documentation](https://nodejs.org/api/crypto.html)  

  - **libsodium**  
    - Verzija: 1.0.18  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama. 

  - **.NET `Rfc2898DeriveBytes` alternativa**  
    - Verzija: Deo .NET Framework-a  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama.

  - **Go `golang.org/x/crypto/scrypt`**  
    - Verzija: v0.41.0  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama.  

  - **Python `scrypt`**  
    - Verzija: 0.9.4  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama.  

  - **PHP `scrypt`**  
    - Verzija: 2.0.0  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama.  

  - **Rust `scrypt`**  
    - Verzija: 0.2  
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Scrypt u najnovijim verzijama.  

### Argon2id (2015)
- Koristi internu Blake2b hash funkciju
- Pobednik PHC, trenutno najpreporučeni KDF
- Memory-hard: pravi matricu u memoriji i meša blokove
- Salt ≥ 16B

- Parametri:
  - m = 46 MiB (memorija)
  - t = 1 (iteracije / prolazi kroz memoriju)
  - p = 1 (paralelizam / broj jezgara)

- Provajderi i njihove najnovije verzije:
  - **libsodium** (C)
    - Verzija: 1.0.18
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.

  - **.NET (`NetDevPack.Security.PasswordHasher`)**
    - Verzija: 1.0.0
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.

  - **argon2id npm**
    - Verzija: 1.0.1
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.

  - **Python `argon2-cffi`**
    - Verzija: 21.3.0
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.

  - **Rust `argon2` crate**
    - Verzija: 0.3.0
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.

  - **Java implementacije (BouncyCastle, Jargon2)**
    - Verzija: Varira
    - **Poznate ranjivosti:** Nema poznatih ranjivosti specifičnih za Argon2id u najnovijim verzijama.
