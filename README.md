# Bežične senzorske mreže - Lab 2

### FESB, smjer 110/111/112/114/120, akademska godina 2016/2017

Low-level programiranje za ugradbene sustave je sasvim drugačije od programiranja za uređaje opće namjene, kao što su računala i mobiteli. Učinkovitost (u smislu brzine i prostora) je daleko važnije, jer su resursi na visokoj cijeni. Drugim rječima veoma bitan naglasak se stavlja na **optimiziaciju** dijelova koda.

## Mikrokontroleri

Mikrokontroleri poput onih koji pokreću Arduino su dizajnirani za ugradbene aplikacije. Za razliku od računala opće namjene, ugradbeni procesor obično ima dobro definiran zadatak koji mora pouzdano i učinkovito obavljati - i uz minimalne troškove.

Najveća razlika između mikrokontrolera kojeg koristite na vašim vježbama i računala opće namjene je količina dostupne memorije. Arduino UNO ima samo 32KB flash memorije i 2KB SRAM-a što je otprilike **100.000 puta** manje od fizičke memorije od PC-a! (ne uključujući memoriju vanjskog diska)

Ponekad se može dogoditi da Arduino bez ikakvih razloga "poludi" ili se resetira sam po sebi, iako je kod 100% točan. U takvim slučajevima, jedan od mogućih uzroka je nedostatak slobodnog RAM (*Random Access Memory*). Drugim riječima, vaš MCU nema dovoljno slobodnog RAM-a za obavljanje traženog zadatka.

## Memorija Arduino mikrokontrolera

Memorija Arduina se sastoji od tri dijela:
 - Flash ili programska memorija
 - SRAM
 - EEPROM
 
## SRAM

SRAM ili *Static Random Access Memory* je tip memorije u koji se može čitati i pisati prilikom izvršavanja programa. SRAM memorija ima višestruku ulogu prilikom izvršavanja programa:
 - **Statički Podaci** - Ovaj blok memorije SRAM-a je rezerviran prostor za sve globalne i statičke varijable iz svog programa.
 - **Heap** - koristi se za dinamički alocirane dijelove podatke kao što su ``malloc``
 - **Stack** - upotrebljava se za lokalne varijable i za održavanje evidencije prekida (*interrupts*) i poziva funkcija. *Stack* raste od vrha memorije dolje prema dnu (prema *Heap-u*). Svaki interrupt, poziv funkcije i/ili lokalna varijabla raspodjela uzrokuje rasti Stack-a. Po povratku iz interrupta ili poziva funkcije će zauzeti dio memorije koji upotrebljava taj interrupt ili funkciju se oslobađa.
