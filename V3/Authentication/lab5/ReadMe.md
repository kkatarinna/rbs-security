# Lab: Username enumeration via account lock

u Ovom zadatku server blokira korisnika ako je pogrešio lozinku 3 puta
u slučaju da korisničko ime ne postoji neće blokirati

na taj način možemo da pronadjemo koje korisničko ime postoji

koristimo cluster bomb napad sa listom korisničkih imena
i 5 generativnih null vrednosti za lozinku na taj način ćemo pokušati da sa svakim korisnikom pogrešimo 5 lozinki

nakon što po dužini odgovora pronadjemo koje korisničko ime postoji tj koji odgovor vraća error

iskoristimo sniper napad da koristimo isto korisničko ime i datu listu lozinki

odgovori mogu da budu da je netačno ili da je korisnik blokiran, ali postoji treći odgovor koji tražimo a to je kada je tačna šifra
u tom slučaju server ne vraća ništa i znamo da smo pronašli šifru