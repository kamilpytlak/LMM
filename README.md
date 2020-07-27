## Liniowe modele mieszane (MLM): Porównanie efektywności algorytmów w językach Python i R


### Streszczenie
W niniejszej pracy porównano efektywność algorytmów konstrukcji mieszanych modeli liniowych (Linear Mixed Model - LMM) z wykorzystaniem języków Python i R - bibliotek: statsmodel (Python), lme4 (R), nlme (R) i bam (R). W przypadku biblioteki statsmodel modele skonstruowane zostały w oparciu o dwie możliwe deklaracje: formułę i macierze. Analizy przeprowadzono nad względnie małym (1 tys. obserwacji: 2 efekty stałe i 1 efekt losowy) zbiorem danych, a także wygenerowanymi zbiorami danych z różną liczbą obserwacji (od 10.000 do 1.000.000), grup (od 10 do 1.000), 3 efektami stałymi i 2 losowymi. Porównane zostały średnie czasy wykonywania zaimplementowanych funkcji w obrębie jednego języka, jak i w obrębie dwóch języków, a także ilość przydzielanej pamięci RAM przeznaczanej na inicjację funkcji i jej rezultat. Sprawdzono, jaka jest zależność między czasem wykonania a: liczbą obserwacji, liczbą grup, liczbą efektów stałych i liczbą efektów losowych z uwzględnieniem interakcji pomiędzy poszczególnymi czynnikami.
Wykazano, że średnie czasy wykonywania różnią się pomiędzy językami Python i R (p-value < 2e-16), bibliotekami lme4 (R), nlme (R)i bam (R), a także implementacją modelu w postaci formuły i macierzy (Python). Zważywszy na samą liczbę obserwacji, różnice dają się szczególnie zauważyć w przypadku dużych zbiorów danych (już od ok. 250 tys.). Sama liczba grup znacząco wpłynęła na wzrost czasu oczekiwania w przypadku formuły i macierzy w języku Python, natomiast nie zaobserwowano istotnego wzrostu w bibliotekach języka R. Wybierając pomiędzy językami Python, R i danymi bibliotekami, należy zważyć na rozmiar analizowanych danych, liczbę struktur zagnieżdżonych i liczbę efektów stałych.

### Spis treści
1. [Liniowe modele w statystyce](#liniowe-modele-w-statystyce)
	- [Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie](#ogólne-modele-liniowe-glm---estymacja-parametrów-i-zastosowanie)
	- [Mieszane modele liniowe (MLM) - estymacja parametrów i zastosowanie](#mieszane-modele-liniowe-mlm---estymacja-parametrów-i-zastosowanie)
2. [Języki Python i R](#języki-python-i-r)
	- [Porównanie efektywności, zastostowań i popularności](#porównanie-efektywności-zastostowań-i-popularności)
	- [Implementacja metod statystycznych](#implementacja-metod-statystycznych)
3. [Porównanie efektywności algorytmów MLM](#porównanie-efektywności-algorytmów-mlm)
	- [Metodyka](#metodyka)
	- [Wyniki](#wyniki)
		- [Zbiór danych "cows"](#zbiór-danych-cows)
			- [Opis zbioru danych](#opis-zbioru-danych)
			- [Czas wykonania](#czas-wykonania)
			- [RAM](#ram)
			- [Rezultaty](#rezultaty)
		- [Symulacje](#symulacje)
			- [Opis zbiorów danych](#opis-zbiorów-danych)
			- [Czas wykonania](#czas-wykonania)
			- [RAM](#ram)
			- [Rezultaty](#rezultaty)
	4. [Wnioski](#wnioski)


### Liniowe modele w statystyce

#### Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie

#### Mieszane modele liniowe (MLM) - estymacja parametrów i zastosowanie


### Języki Python i R
#### Porównanie efektywności, zastostowań i popularności
#### Impementacja metod statystycznych


### Porównanie efektywności algorytmów MLM

#### Metodyka

#### Wyniki

##### Zbiór danych "cows"
##### Opis zbioru danych
##### Czas wykonania
##### RAM
##### Rezultaty

##### Symulacje
##### Opis zbiorów danych
##### Czas wykonania
##### RAM
##### Rezultaty

#### Wnioski

