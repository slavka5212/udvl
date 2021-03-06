Cvičenie 6
==========

**Riešenie odovzdávajte podľa
[pokynov na konci tohoto zadania](#technické-detaily-riešenia)
do Štvrtka 23.4. 23:59:59.**

## Kto zabil agátu (4b)

Someone in Dreadsbury Mansion killed Aunt Agatha. Agatha, the butler, and
Charles live in Dreadsbury Mansion, and are the only ones to live there. A
killer always hates, and is no richer than his victim. Charles hates noone that
Agatha hates. Agatha hates everybody except the butler. The butler hates
everyone not richer than Aunt Agatha. The butler hates everyone whom Agatha
hates. Noone hates everyone. Who killed Agatha?

Niekto v Dreadsburskom panstve zabil Agátu. Agáta, komorník a Karol bývajú v
Dreadsburskom panstve a nikto iný okrem nich tam nebýva. Vrah vždy nenávidí
svoje obete a nie je od nich bohatší. Z ľudí, ktorých Agáta nenávidí nie je
taký, ktorého by Karol nenávidel. Agáta nenávidí každého okrem komorníka.
Komorník nenávidí každého, kto nie je bohatší ako Agáta. Komorník nenávidí
každého, koho nenávidí Agáta. Niet toho, kto by nenávidel všetkých. Kto zabil
Agátu?

Zistite a **dokážte** kto zabil Agátu.


*Pomôcka:* Najprv treba jednotlive veci zo zadania prepísať do logiky. Napríklad:

    The butler hates everyone whom Agatha hates.

    ∀X ( hates(Agatha,X) → hates(butler,X) )

Toto samozrejme nie je výroková logika, takže budeme potrebovať program, ktorý
vyrobí inštancie pravidel pre správne doplnené premenné.

*Pomôcka:* Takto sformulovaný problém prerobíme na vstup pre SAT solver. Keď ho
len tak pustíme, nájde nám jedno z možných riešení, v ktorom sa môžeme pozrieť,
kto by mohol byť vrahom. Potrebujeme však ukázať, že to neplatí len náhodou v
tom jednom modeli, ale že to naozaj výplýva z našej teórie, teda platí to vo
všetkých modeloch. Takže musíme pridať negáciu toho tvrdenia k teórii. Ak bude
nesplniteľná, tak naše tvrdenie (kto bol vrahom) vyplýva z teórie, teda platí
vo všetkých modeloch našej teórie. 

**Pozor**: Je dôležite najskôr (pred pridaním negácie toho, čo chceme dokázať)
naozaj overiť, že naša teória je splniteľná (má aspoň nejaký model). Keby sme
niečo pokazili a vyrobili nekonzistentnú (nesplniteľnú) teóriu, tak by z nej
samozrejme vyplývalo všetko, vrátane nášho cieľa.

*Pomôcka:* Podobne ako v predchadzajúcich úlohách potrebujeme zakódovať
(výrokovologické) premenné na čísla. Máme 3 druhy premenných, zodpovedajúce
binárnym predikátom `killed(X,Y)`, `hates(X,Y)` a `richer(X,Y)`.

Keďže máme 3 binárne predikáty, ktoré majú ako parametre jedného z troch ľudí,
môže premenné zakódovat ako číslo v trojkovej sústave PXY<sub>3</sub> + 1, kde
P je číslo predikátu a X a Y sú čísla osôb od 0 do 2 (teda v desiatkovej
sústave to bude `P*3*3 + X*3 + Y + 1`).

Ak predíkát killed a Agáta budú mať číslo 0 a keď vymeníte poradie parametrov v
predikáte killed(aby vrah bola najnižšia cifra, dobré je vymeniť to len vo
výpočte čísla premennej, nie poradie argumentov vo funkcii ;), tak potom prvé 3
premenné (1,2,3) budú zodpovedať možnostiam kto zabil Agátu.

Pomôcka: Veľmi pomôže spraviť si pár pomocných funkcií podobne ako v
predchádzajúcich úlohách:

```python
P = 3 # pocet ludi
Agatha = 0
Butler = 1
Charles = 2

def killed(p1, p2):
    # p1 a p2 su vymenene
    # aby killed(X,Agatha) zodpovedalo 1, 2, 3
    return 0 * P * P + p2 * P + p1 + 1

def hates(p1, p2):
    return 1 * P * P + p1 * P + p2 + 1

def richer(p1, p2):
    return 2 * P * P + p1 * P + p2 + 1
```

Formuly z prvej pomôcky môžeme potom vygenerovať tatkto:
```python
# The butler hates everyone whom Agatha hates.
# ∀X ( hates(Agatha,X) → hates(butler,X) )
for p in range(P):
    w.writeImpl( hates(Agatha, p), hates(Butler, p) )
```

## Technické detaily riešenia

Riešenie odovzdajte do vetvy `cv08` v adresári `cv08`.

Odovzdávajte program (`cv08.py`, `cv08.cpp` alebo `cv08.java`), ktorý
pomocou SAT solvera dokáže, kto zabil Agátu.

Program by mal najskôr zakódovať iba teóriu a pustiť na ňu SAT solver, aby
ste sa ubezpečili, že vaša teória má model (riešenie). Potom by mal znovu
zakódovať teóriu spolu aj s (správne zakódovaným) tvrdením, ktoré chcete
dokázať a znovu zavolať SAT solver (a skontrolovať, či dá očakávaný výstup).

Ak v programe do komentárov napíšete tvrdenia / formuly ktoré ste
sformalizovali, predídete prípadným nedorozumeniam / nejasnostiam pri
opravovaní.

Ak chcete v pythone použiť knižnicu z [examples/sat](../examples/sat), nemusíte
si ju kopírovať do aktuálne adresára, stačí ak na začiatok svojej knižnice
pridáte:
```python
import os
import sys
sys.path[0:0] = [os.path.join(sys.path[0], '../examples/sat')]
import sat
```
