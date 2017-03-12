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

![learn_arduino_stack_operation](https://cloud.githubusercontent.com/assets/8695815/23830029/cf1517aa-0700-11e7-8b63-5432481523f4.gif)

## SRAM

SRAM ili *Static Random Access Memory* je tip memorije u koji se može čitati i pisati prilikom izvršavanja programa. SRAM memorija ima višestruku ulogu prilikom izvršavanja programa:
 - **Statički Podaci** - Ovaj blok memorije SRAM-a je rezerviran prostor za sve globalne i statičke varijable iz svog programa.
 - **Heap** - koristi se za dinamički alocirane dijelove podatke kao što su ``malloc``
 - **Stack** - upotrebljava se za lokalne varijable i za održavanje evidencije prekida (*interrupts*) i poziva funkcija. *Stack* raste od vrha memorije dolje prema dnu (prema *Heap-u*). Svaki interrupt, poziv funkcije i/ili lokalna varijabla raspodjela uzrokuje rasti Stack-a. Po povratku iz interrupta ili poziva funkcije će zauzeti dio memorije koji upotrebljava taj interrupt ili funkciju se oslobađa.

## Usporedba Arduino memorije

U sljedežoj tablici možete vidjeti kapacitete memorije za nekoliko popularnih Arduino i Arduino kompatibilnih uređaja.

![learn_arduino_arduinochart](https://cloud.githubusercontent.com/assets/8695815/23830043/2fd4be2e-0701-11e7-875f-32ff3a364fd2.jpg)

## Mjerenje memorije Arduina

Jedan od načina dijagnoze problema memorije je detekcija zauzeća memorije.

### Flash memorija

Kompajler prilikom kompajliranja koda za Vas izračuna zauzeće flash memorije.

----DODAJ SLIKU FLASHA---

### SRAM memorija

Korištenje SRAM memorije je dinamičnije i stoga ju je (malo) teže mjeriti. ``free_ram ()`` funkcija dana u nastavku je jedan od načina na koji možete to ralizirati. Korištenje SRAM-a je dinamično s vremenom će se mijenjati. Dakle, važno je zvati ``free_ram ()`` u različitim vremenima i iz različitih mjesta u vašem kodu da biste vidjeli kako se mijenja tijekom vremena izvršavanja koda.

```arduino
int freeRam () 
{
  extern int __heap_start, *__brkval; 
  int v; 
  return (int) &v - (__brkval == 0 ? (int) &__heap_start : (int) __brkval); 
}
```

U osnovi, funkcija ``free_ram ()`` je zapravo daje informaciju o slobodnom prostoru između Stack-a i Heap-a. Upravo taj prostor zaista treba pratiti ako pokušavate izbjeći rušenje programa.
