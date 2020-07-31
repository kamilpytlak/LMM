# Liniowe modele mieszane (MLM): Porównanie efektywności bibliotek w językach Python i R


## Streszczenie
W niniejszej pracy porównano efektywność algorytmów konstrukcji mieszanych modeli liniowych (Linear Mixed Model - LMM) z wykorzystaniem języków Python i R - bibliotek: "statsmodel" (Python), "lme4" (R), "nlme" (R) i "mgcv" (R). W przypadku biblioteki statsmodel modele skonstruowane zostały w oparciu o dwie możliwe deklaracje: formułę i macierze. Analizy przeprowadzono nad względnie małym (1 tys. obserwacji: 2 efekty stałe i 1 efekt losowy) zbiorem danych, a także wygenerowanymi zbiorami danych z różną liczbą obserwacji (od 10.000 do 1.000.000), grup (od 10 do 1.000), 3 efektami stałymi i 2 losowymi. Porównane zostały średnie czasy wykonywania zaimplementowanych funkcji w obrębie jednego języka, jak i w obrębie dwóch języków, a także ilość przydzielanej pamięci RAM przeznaczanej na inicjację funkcji i jej rezultat. Sprawdzono, jaka jest zależność między czasem wykonania a: liczbą obserwacji, liczbą grup, liczbą efektów stałych i liczbą efektów losowych z uwzględnieniem interakcji pomiędzy poszczególnymi czynnikami.
Wykazano, że średnie czasy wykonywania różnią się pomiędzy językami Python i R (p-value < 2e-16), bibliotekami lme4 (R), nlme (R)i bam (R), a także implementacją modelu w postaci formuły i macierzy (Python). Zważywszy na samą liczbę obserwacji, różnice dają się szczególnie zauważyć w przypadku dużych zbiorów danych (już od ok. 250 tys.). Sama liczba grup znacząco wpłynęła na wzrost czasu oczekiwania w przypadku formuły i macierzy w języku Python, natomiast nie zaobserwowano istotnego wzrostu w bibliotekach języka R. Wybierając pomiędzy językami Python, R i danymi bibliotekami, należy zważyć na rozmiar analizowanych danych, liczbę struktur zagnieżdżonych i liczbę efektów stałych.

## Spis treści
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


## Liniowe modele w statystyce

### Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie

### Mieszane modele liniowe (MLM) - estymacja parametrów i zastosowanie


## Języki Python i R
### Porównanie efektywności, zastostowań i popularności
### Impementacja metod statystycznych


## Porównanie efektywności algorytmów MLM
### Metodyka
Porównania wielkości przeznaczanych zasobów systemowych na konstrukcję modelu zostały w językach Python i R zestawione z użyciem biblioteki "statsmodel" w języku Python (która umożliwia deklarację modelu za pomocą formuły bądź macierzy, więc uwzględniono te dwa aspekty) i bibliotek "lme4", "nlme" i "mgcv" w języku R. Biblioteki "lme4" i "nlme" są wysoce spopularyzowane i używane w celach estymacji współczynników, natomiast biblioteka "mgcv", a w szczególności funkcja "bam()" polecana jest do pracy nad dużymi zbiorami danych, gdyż w przeciwieństwie do "lme4" i "nlme", które do estymacji używają metody REML, ta korzysta z metody fREML (Functional Relationship Estimation by Maximum Likelihood). Sprawdzano średnie różnice czasów wykonywania pomiędzy językami, bibliotekami w obrębie języków i jednego języka, ilości przydzielanego RAMu na proces, a także w charakterze interpretacyjnym obszerność informacji i ich dokładność w rezultatach wywołań.

&nbsp;

#### Python
W języku python skorzystano z biblioteki "statsmodel" w wersji 0.11.1. Biblioteka ta umożliwia deklarację modelu w postaci formuły i macierzy, więc obie metody zostały sprawdzone. Ponadto, w celu określenia macierzy, jak i podstawowych statystyk, zostały zaimportowane dodatkowe komponenty, takie jak biblioteka "pandas" w wersji 1.1.0, funkcja "dmatrices" z biblioteki "patsy" w wersji 0.5.1, funkcje "mean" i "stdev" będące podstawowymi statystykami opisującymi przeciętną wartość populacji (średnia) i odchylenie od średniej (odchylenie standardowe) z biblioteki "statistics" i biblioteka "time". W większości analizy przeprowadzane były z wykorzystaniem internetowej aplikacji Jupyter Notebook, w związku z czym dodatkowo zostały użyte "magiczne polecenia" IPython ("IPython Magic Commands") bazujące na jądrze IPython: "line_profiler" i "memory_profiler", które posłużyły do pomiarów kolejno czasu wykonywania i użytego RAMu. Część analiz (przede wszystkim sporządzenie wykresów zależności zużytej pamięci od czasu) została przeprowadzona w programie PyCharm.

```python
import pandas as pd
from statistics import mean, stdev
import statsmodels.api as sm
import statsmodels.formula.api as smf
from patsy import dmatrices
import time
%load_ext line_profiler
%load_ext memory_profiler
```

W celu optymalizacji pracy, utworzone zostały następujące funkcje:

 1. **Funkcja "LMM" konstruująca model**, przyjmująca jeden argument "model", będący deklaracją ("formula" lub "matrix"):
```python
 def LMM(model):
    if model == "formula":
        LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
        LMMF_formula = LMM_formula.fit()
        return LMMF_formula

    elif model == "matrix":
        LMM_matrix = sm.MixedLM(y, X, groups=groups)
        LMMF_matrix = LMM_matrix.fit()
        return LMMF_matrix
```

2. **Funkcja "check_time" sprawdzająca czas**, przyjmująca dwa argumenty: "model" i "n", gdzie "model" oznacza sposób deklaracji modelu ("formula" lub "matrix", natomiast "n" - liczbę wywołań funkcji. Czas konstrukcji jest wyznaczany na podstawie różnicy między czasem systemowym końcowym i początkowym, do czego posłużyła funkcja "time()" z biblioteki standardowej "time". Następnie czas ten jest zapisywany z zaokrągleniem do 4 miejsc po przecinku do listy "times", a ta jest zwracana.
```python
def check_time(model, n):
    times = []

    for i in range(n):
        start_time = time.time()
        LMM(model)
        end_time = time.time()
        times.append(round(end_time - start_time, 4))
    return times
```

Szczegółowe analizy (wraz z wynikami) dla zbioru danych "cows" znaleźć można w pliku "Python.ipynb": [https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb).

&nbsp;

#### R
W R do konstrukcji modelu użyto funkcji z dwóch najpopularniejszych bibliotek przeznaczonych do konstrukcji modeli mieszanych - "lme4" (funkcja "lmer()") w wersji 1.1-23 i "nlme" (funkcja "lme()") w wersji 3.1-148. W obu funkcjach modele zostały przedstawione w postaci formuły. Użyto ponadto biblioteki "mgcv" (funkcja "bam()") w wersji 1.8-31, która przeznaczona jest do operacji nad dużymi zbiorami danych. W celu efektywnego składowania i sprawnej obróbki danych, użyto również różne funkcje z bibliotek "dplyr" i "tidyr". Do pomiaru RAMu wykorzystana została biblioteka "profmem", a także wbudowany w program RStudio profiler.
```r
library(dplyr)
library(tidyr)
library(lme4)
library(nlme)
library(mgcv)
library(profmem)
```

Podobnie jak w przypadku języka Python, stworzone zostały dodatkowe funkcje do optymalizacji pracy:

 1. Funkcja LMM do konstrukcji modeli - przyjmująca parametr "model", określający używaną bibliotekę: "lme4", "nlme" lub "mgcv".
```r
LMM <- function(model) {
  if(model == "lme4") {
    LMM_lme4 <- lmer(data=data_set, milk ~ btn3a1 + lactation + (1|cow.id))
    return(LMM_lme4)
  }
  else if(model == "nlme") {
    LMM_nlme <- lme(data = data_set, milk ~ btn3a1 + lactation, random = ~1|cow.id)
    return(LMM_nlme)
  }
  else if(model == "bam") {
    LMM_bam <- bam(data = data_set, milk ~ btn3a1 + lactation + s(cow.id, bs = "re"))
    return(LMM_bam)
  }
}
```
2. Funkcja "check_time" do sprawdzenia czasu - przyjmująca dwa argumenty: "model", określający bibliotekę, i "n" określający liczbę wywołań. Końcowy wynik zwraca w postaci wektora.
```r
check_time <- function(model, n) {
  times <- c()

  for(i in 1:n) {
    start_time <- Sys.time()
    LMM(model)
    end_time <- Sys.time()
    times <- c(times, round(end_time - start_time, 4))
    }
    return(times)
  }
```
3. Funkcja "check_RAM" do pomiaru zużytego RAMu - przyjmująca jeden argument: "model", określający bibliotekę. Pierwotnie, funkcja ta zwraca ilość w bajtach, dlatego w ramach przejrzystości zostaje ona zamieniona na megabajty. Ponadto wbudowana w używaną tu bibliotekę "profmem" funkcja nie uwzględnia ilości pamięci uwalnianej, dlatego też jej wynik zasadniczo może niejednokrotnie znacząco różnic się od profilera pamięci w programie RStudio, który takowe uwzględnia.
```r
check_RAM <- function(model) {
  prof_mem <- profmem({
    LMM(model)
      })
  sum_in_MB <- round(sum(prof_mem$bytes[!is.na(prof_mem$bytes)]) * 1e-6, 4)
  return(sum_in_MB)
}
```
Szczegółowe analizy (wraz z wynikami) dla zbioru danych "cows" znaleźć można w pliku "R.ipynb": [https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb).

&nbsp;

### Metody statystyczne
Testy statystyczne, jak i wizualizacja wyników zostały sporządzone w R z wykorzystaniem bibliotek "dplyr", "ggplot2" i "FSA". W oparciu o wykresy pudełkowe, zestawiono czas wykonywania funkcji wszystkich bibliotek i deklaracji obydwu języków, a także pojedyncze dla każdego języka i poszczególnych kombinacji bibliotek. Porównano średnie czasy konstrukcji modelu za pomocą deklaracji formuły i macierzy w języku Python z wykorzystaniem testu Wilcoxona, ponieważ przeprowadzony test Shapiro-Wilka stwierdził, że rozkład wartości deklaracji formuły nie jest normalny. Porównano także średnie czasy wszystkich bibliotek/deklaracji w obrębie dwóch języków, przeprowadzając test Kruskala-Wallisa, a w celu uzyskania szczegółów - Dunneta.


## Wyniki
### Zbiór danych "cows"

#### Opis zbioru danych

#### Czas wykonania

#### RAM

#### Rezultaty


### Symulacje
#### Opis zbiorów danych

#### Czas wykonania

#### RAM

#### Rezultaty

## Wnioski
