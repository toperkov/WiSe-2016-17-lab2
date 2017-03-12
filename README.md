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

## Optimizacija memorije Arduino programa

Kada sastaviti svoj program, IDE će vam reći koliko je velik. Ako ste dosegli ili prešli raspoloživi prostor, primjena nekih optimizacijskih mehanizama ga može vratiti u granice.

### Uklonite nepotrebne (mrtve) dijelove koda

Može se dogoditi da je rezultirajući kod nastao kao kombinacija koda iz više izvora, pa se lako može dogoditi da su dijelovi koda nekorišteni te ih se može ukloniti:
 - Nekorištene biblioteke
 - Nekorištene funkcije
 - Nekorištene varijable
 - Nedostupni dijelovi koda
 
## Optimizacija SRAM-a

Postoje razni načini na koji možete smanjiti korištenje SRAM memorije. Ovo su samo neki od njih.
### Eliminirajte nekorištenje varijable

### Smanjite nepotrebnu veličinu varijabli

Ne koristite *float* kada je *int* dovoljan. Nemojte koristiti *int* kada je *byte* dovoljan. Pokušajte koristiti najmanji tip podataka koji može pohraniti informaciju.

![learn_arduino_datatypes](https://cloud.githubusercontent.com/assets/8695815/23830238/5eeaa534-0706-11e7-9eae-bfd397b6aed7.jpg)

### Upotreba lokalnih varijabli

Globalne i statičke varijable se prve učitaju u SRAM te one guraju Heap gore prema Stacku. Ukoliko je moguće, incijaliziraje lokalne varijable unutar funkcija. Naime, prilikom izvršavanja funkcije alocira se dio Stack memorije. Unutar te memorije će biti sadržani:
 - svi parametri koji su dodijeljeni funkciji
 - sve lokalne varijable koje su deklarirane u funkciji
Ovi se podaci koriste u funkciji te se prilikom izlaska iz funkcije dio memorije Stacka kojeg je funkcija koristila u potpunosti oslobađa!

### Upotreba PROGMEM za const podatke

U mnogim slučajevima, velika količina RAM-a je preuzeta od strane statičke memorije kao rezultat korištenja globalne varijable (kao što su *Strings* ili *Int*). Kad znate da se vrlo vjerojatno varijabla neće promijeniti, ona se jednostavno može pohraniti u tzv. PROGMEM (programsku memoriju). Kao jednostavan primjer je upotreba ``F()`` makro-a koji kaže kompajleru da se String pohrani u PROGMEM-u. 

Npr. ako zamijenite:

```arduino
Serial.println("Sram sram sram sram. Lovely sram! Wonderful sram! Sram sra-a-a-a-a-am sram sra-a-a-a-a-am sram. Lovely sram! Lovely sram! Lovely sram! Lovely sram! Lovely sram! Sram sram sram sram!");
```

sa 

```arduino
Serial.println(F("Sram sram sram sram. Lovely sram! Wonderful sram! Sram sra-a-a-a-a-am sram sra-a-a-a-a-am sram. Lovely sram! Lovely sram! Lovely sram! Lovely sram! Lovely sram! Sram sram sram sram!"));
```

sačuvat ćete 180 byte-ova SRAM memorije.

### Ne koristite rekurzivne funkcije

Na sljedećem primjeru ćete moći vidjeti jednostavno kako možete korištenjem rekurzivne funkcije napuniti Stack memoriju. Što mislite bi se trebalo dogoditi? *Vaš zadatak je da korištenjem PlatformIO-a testirate navedenu skriptu i komentirate što se dogodilo.*

```arduino
// Pin 13 has an LED connected on most Arduino boards.
// give it a name:
int led = 13;
uint16_t a = 2;

#define DELAY 20 // wait for a predefined delay (in mililseconds)

void powerUp();
void powerDown();
int freeRam();
void incVar();

// the setup routine runs once when you press reset:
void setup() {
  // initialize serial communication at 9600 bits per second:
  Serial.begin(9600);
  // initialize the digital pin as an output.
  pinMode(led, OUTPUT);
}

// the loop routine runs over and over again forever:
void loop() {
  powerUp();
}

void powerUp() {
  Serial.print("Free SRAM: ");
  Serial.print(freeRam());
  Serial.println(" bytes");
  incVar();
  digitalWrite(led, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(DELAY);
  powerDown();
};

void powerDown() {
  Serial.print("Free SRAM: ");
  Serial.print(freeRam());
  Serial.print(" bytes");
  incVar();
  digitalWrite(led, LOW);    // turn the LED off by making the voltage LOW
  delay(DELAY);
  powerUp();

};

int freeRam ()
{
  extern int __heap_start, *__brkval;
  int v;
  return (int) &v - (__brkval == 0 ? (int) &__heap_start : (int) __brkval);
}

void incVar() {
  a = a + 2;
  Serial.print("a + 2 = ");
  Serial.println(a);
};
```

# Zadatak

Skinite ``TempHumLight.ino`` datoteku koja se nalazi na GitHub repozitoriju te je stavite u ``scr`` direktorij u kojemu ćete raditi današnju vježbu (npr. ``C:\Users\Student\Desktop\Bubli\scr``). Kao što ćete vidjeti, navedena skripta čita temperaturu i vlagu senzora (DHT11 ili DHT22) te razinu osvjetljenja (pomoću senzora BH1750). Pije pokretanja/kompajliranja skripte potrebno je instalirati biblioteke za senzor DHT i BH1750.

Da biste to realizirali, otvorite novi terminal u PlatformIO-u te upišite sljedeću naredbu:
``platformio lib search DHT``
Nakon toga će vam se prikazati popis biblioteka koje možete instalirati. Instalirajte biblioteku pod rednim brojem ``[19]`` tako da ćete utipkati:
``platformio lib -g install 19``
Sličnu stvar ponovite i za senzor BH1750
``platformio lib search BH1750``
``platformio lib -g install 439``

Nakon toga povežite DHT i BH1750 senzore kako je prikazano na slici te testirajte navedeni kod



Vaš zadatak je da razumijete kod te ga optimizirate na način da zauzima što manje SRAM-a korištenjem gore navedenih uputa.
