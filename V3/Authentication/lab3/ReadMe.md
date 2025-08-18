# Lab: Username enumeration via response timing

u ovom zadatku potrebno je testirati logovanje sa poznatim kredencijalima i primetiti koliko duže vremena treba u slučaju da je korisničko ime tačno jer u tom slučaju server proverava šifru da li je tačna i ta ekstra provera duže traje i što je lozinka duža to će proces provere biti duži

sa tim na umu uz alat intruder možemo da prolazimo kroz listu korisničkih imena i jednom dugačkom šifrom

primetićemo da jednom korisničkom imenu treba duže a to je ad.

sada kad smo našli korisničko ime možemo da ponovimo proces kao u prethodnim zadacima da nadjemo lozinku

*Bitan Detalj* u ovom zadatku je da je server postavio malu odbranu protiv brute force tako da se ne može više od 3 puta pogrešiti šifra inače nam zaključa IP adresu to se zaobilazi tako što se u headeru doda X-Forwarded-For: i bilo koji broj iako nije validna adresa jer je provera veoma slaba i jadna.