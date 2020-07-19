## Liniowe modele mieszane (MLM): Porównanie efektywności algorytmów w językach Python i R

### Streszczenie
W niniejszej pracy porównano efektywność algorytmów do konstrukcji mieszanych modeli liniowych (Linear Mixed Model - LMM) z wykorzystaniem dwóch najpopularniejszych języków do analiz statystycznych: Python i R, a przede wszystkim bibliotek: statsmodel (Python), lme4 (R), nlme (R) i bam (R). W przypadku biblioteki statsmodel skonstruowano modele w oparciu o formułę i macierze. Analizy przeprowadzono nad względnie małym (1 tys. obserwacji: 2 efekty stałe i 1 efekt losowy) i dużym (1 mln. obserwacji: 3 efekty stałe i 1 efekt losowy) zbiorem danych. Porównane zostały średnie czasy wykonywania zaimplementowanych funkcji w obrębie jednego języka (test Wilcoxona), jak i w obrębie dwóch języków (test Kruskala-Wallisa), a także pomiaru pamięci RAM przeznaczanej na inicjację funkcji i jej rezultat. Wykazano, że średnie czasy wykonywania różnią się pomiędzy językami Python i R (p-value < 2e-16), jak i pomiędzy bibliotekami lme4 (R) i nlme (R) (p-value < 2e-16). Również wystąpiły istotne różnice pomiędzy implementacją modelu w postaci formuły i macierzy, co tyczy się biblioteki statsmodel w języku Python. W obydwu przypadkach różnice szczególnie dają się zauważyć dla analiz przeprowadzonych nad dużym zbiorem danych (1 mln. obserwacji). Pomimo zastosowanych metod z biblioteki bam w R, która z założenia została stworzona do pracy nad dużymi zbiorami danych, to język Python okazał się być wydajniejszym w kwestii przydzielanej na proces pamięci, jak i średniego czasu potrzebnego na konstrukcję modelu. Wybierając pomiędzy językami Python i R, należy zważyć na rozmiar analizowanych danych i pożądany rezultat.

### Spis treści
- [Liniowe modele w statystyce](#liniowe-modele-w-statystyce)
	- [Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie](#ogólne-modele-liniowe-glm---estymacja-parametrów-i-zastosowanie)
	- [Mieszane modele liniowe (MLM) - estymacja parametrów i zastosowanie](#mieszane-modele-liniowe-mlm---estymacja-parametrów-i-zastosowanie)
- [Języki Python i R](#języki-python-i-r)
	- [Porównanie efektywności, zastostowań i popularności](#porównanie-efektywności-zastostowań-i-popularności)
	- [Impementacja metod statystycznych](#impementacja-metod-statystycznych)
- [Porównanie efektywności algorytmów MLM](#porównanie-efektywności-algorytmów-mlm)
	- [Metodyka](#metodyka)
	- [Wyniki](#wyniki)
		- [Zbiór danych "cows"](#zbiór-danych-cows)
	- [Wnioski](#wnioski)


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
#### Wnioski
