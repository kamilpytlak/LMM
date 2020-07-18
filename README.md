## Liniowe modele mieszane (MLM): Porównanie efektywności algorytmów w językach Python i R

### Streszczenie
W niniejszej pracy porównano efektywność algorytmów do konstrukcji mieszanych modeli liniowych (Linear Mixed Model - LMM) w wykorzystaniem dwóch najpopularniejszych języków do analiz statystycznych: Python i R, a przede wszystkim równie najczęściej wykorzystywanym bibliotekom: statsmodel (Python), lme4 (R) i nlme (R). Biblioteka statsmodel (Python) umożliwia implementację modelu za pomocą formuły lub macierzy, dlatego również to uwzględniono. Mając na uwadze fakt, że wynik funkcji z biblioteki lme4 (R) nie dostarcza poziomów istotności dla współczynników modelu, dodatkowo stworzono osobną funkcję, wykorzystującą wynik funkcji biblioteki lme4 (R) do oszacowania wartości p każdego efektu (test Walda), co w sumie składa się na 5 różnych sposobów implementacji i konstrukcji w obu językach. Porównania zaprojektowano w oparciu o porównania średnich czasów wykonywania zaimplementowanych funkcji w wyżej wspomnianych bibliotekach, jak i utworzonych-zmodyfikowanych (z "brakującymi" wartościami p) w obrębie jednego języka (test Wilcoxona), jak i w obrębie dwóch języków (test Kruskala-Wallisa), a także pomiaru pamięci RAM przeznaczanej na inicjację funkcji i jej rezultat. Wykazano, że średnie czasy wykonywania różnią się pomiędzy językami Python i R (p-value < 2e-16), jak i pomiędzy bibliotekami lme4 (R) i nlme (R) (p-value < 2e-16). Nie wykazano istotnej różnicy w czasach wykonywania dla sposobów (formuła i macierz) z biblioteki statsmodel (Python). Również język Python okazał się być wydajniejszym w porównaniu z R w kwestii przydzielanej na proces pamięci. Wybierając pomiędzy językami Python i R, należy zważyć na rozmiar analizowanych danych i pożądany rezultat.

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
