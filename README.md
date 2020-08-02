# Liniowe modele mieszane (LMM): Porównanie efektywności różnych metod implementacji w językach Python i R


## Streszczenie
W niniejszej pracy porównano efektywność różnych metod implementacji mieszanych modeli liniowych (Linear Mixed Model - LMM) w językach Python i R - bibliotekach: "statsmodel" (Python), "lme4" (R), "nlme" (R) i "mgcv" (R). W przypadku biblioteki "statsmodel" modele skonstruowane zostały na podstawie dwóch możliwych deklaracji: formuły i macierzy. Analizy przeprowadzono nad małym (1 tys. obserwacji: 2 efekty stałe i 1 efekt losowy) zbiorem danych, a także wygenerowanymi zbiorami danych z różną liczbą obserwacji (od 10.000 do 1.000.000), grup (od 100 do 10.000), 1 efektem stałym i 1 losowym. Porównane zostały średnie czasy wykonywania zaimplementowanych funkcji w obrębie jednego języka, jak i w obrębie dwóch języków, a także ilość przydzielanej pamięci RAM przeznaczanej na inicjację funkcji i jej rezultat. Sprawdzono, jaka jest zależność między czasem wykonania a: liczbą obserwacji, liczbą grup, a także interakcji.
Wykazano, że średnie czasy wykonywania różnią się pomiędzy językami Python i R, bibliotekami "lme4" (R), "nlme" (R) i "mgcv" (R), a także implementacją modelu w postaci formuły i macierzy (Python). Zważywszy na samą liczbę obserwacji, różnice dają się szczególnie zauważyć w przypadku dużych zbiorów danych (już od ok. 250 tys.). Wzrost liczby obserwacji i liczby grup miał istotny wpływ na wzrost czasu wykonywania dla obu językach, choć w przypadku języka Python liczba grup miała większy wpływ. Deklaracja modelu z użyciem macierzy w języku Python potrzebowała mniej pamięci RAM na wykonanie instrukcji niż deklaracją formułą, ale obie te okazały się skuteczniejsze w kwestii alokowanej pamięci alokacyjnej w porównaniu z językiem R. Biblioteka "mgcv" efektywniej zapewniała rezultaty i zużywała mniej pamięci dla danych wielkoskalowych.
Wybierając pomiędzy językami Python, R i danymi deklaracjami/bibliotekami, należy zważyć na objętość analizowanych danych, liczbę struktur zagnieżdżonych, liczbę efektów stałych i losowych, a także oczekiwany rezultat (biblioteka "lme4" nie zapewnia wartości p). Wszystkie analizowane deklaracje/biblioteki efektywnie spisowały się dla danych o małej objętości, natomiast największe różnice pojawiały się wraz ze zwiększaniem rozmiaru. Wybór właściwej metody konstrukcji mieszanego modelu liniowego może mieć istotne znaczenie na czas oczekiwania, jak i sam wynik - może się zdarzyć, że biblioteka "lme4" nie poradzi sobie na danej maszynie z obliczeniami (ze względu na niedostateczną pamięć RAM), a biblioteka "mgcv" już tak.

## Spis treści
1. [Liniowe modele w statystyce](#1-liniowe-modele-w-statystyce)
	- [Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie](#11-ogólne-modele-liniowe-glm---estymacja-parametrów-i-zastosowanie)
	- [Mieszane modele liniowe (LMM) - estymacja parametrów i zastosowanie](#12-mieszane-modele-liniowe-mlm---estymacja-parametrów-i-zastosowanie)
2. [Języki Python i R](#2-języki-python-i-r)
	- [Porównanie efektywności, zastostowań i popularności](#21-porównanie-efektywności-zastostowań-i-popularności)
	- [Implementacja metod statystycznych](#22-implementacja-metod-statystycznych)
3. [Porównanie efektywności algorytmów LMM](#3-porównanie-efektywności-algorytmów-lmm)
	- [Metodyka](#31-metodyka)
	- [Wyniki](#32-wyniki)
		- [Zbiór danych "cows"](#321-zbiór-danych-cows)
			- [Opis zbioru danych](#3211-opis-zbioru-danych)
			- [Czas wykonania](#3212-czas-wykonania)
			- [RAM](#3213-ram)
		- [Symulacje](#322-symulacje)
			- [Opis zbiorów danych](#3221-opis-zbiorów-danych)
			- [Czas wykonania](#3222-czas-wykonania)
			- [RAM](#3223-ram)
4. [Wnioski](#4-wnioski)


## 1. Liniowe modele w statystyce

### 1.1 Ogólne modele liniowe (GLM) - estymacja parametrów i zastosowanie

### 1.2 Mieszane modele liniowe (LMM) - estymacja parametrów i zastosowanie


## 2. Języki Python i R
### 2.1 Porównanie efektywności, zastostowań i popularności
### 2.2 Impementacja metod statystycznych


## 3. Porównanie efektywności algorytmów LMM
### 3.1 Metodyka
Porównania wielkości przeznaczanych zasobów systemowych na konstrukcję modelu zostały w językach Python i R zestawione z użyciem biblioteki "statsmodel" w języku Python (która umożliwia deklarację modelu za pomocą formuły bądź macierzy, więc uwzględniono te dwa aspekty) i bibliotek "lme4", "nlme" i "mgcv" w języku R. Biblioteki "lme4" i "nlme" są wysoce spopularyzowane i używane w celach estymacji współczynników, natomiast biblioteka "mgcv", a w szczególności funkcja "bam()" polecana jest do pracy nad dużymi zbiorami danych, gdyż w przeciwieństwie do "lme4" i "nlme", które do estymacji używają metody REML, ta korzysta z metody fREML (Functional Relationship Estimation by Maximum Likelihood). Sprawdzano średnie różnice czasów wykonywania pomiędzy językami, bibliotekami w obrębie języków i jednego języka, ilości przydzielanego RAM-u na proces, a także w charakterze interpretacyjnym obszerność informacji i ich dokładność w rezultatach wywołań.

&nbsp;

#### Python
W języku python skorzystano z biblioteki "statsmodel" w wersji 0.11.1. Biblioteka ta umożliwia deklarację modelu w postaci formuły i macierzy, więc obie metody zostały sprawdzone. Ponadto, w celu określenia macierzy, jak i podstawowych statystyk, zostały zaimportowane dodatkowe komponenty, takie jak biblioteka "pandas" w wersji 1.1.0, funkcja "dmatrices" z biblioteki "patsy" w wersji 0.5.1, funkcje "mean" i "stdev" będące podstawowymi statystykami opisującymi przeciętną wartość populacji (średnia) i odchylenie od średniej (odchylenie standardowe) z biblioteki "statistics" i biblioteka "time". W większości analizy przeprowadzane były z wykorzystaniem internetowej aplikacji Jupyter Notebook, w związku z czym dodatkowo zostały użyte "magiczne polecenia" IPython ("IPython Magic Commands") bazujące na jądrze IPython: "line_profiler" i "memory_profiler", które posłużyły do pomiarów kolejno czasu wykonywania i użytego RAM-u. Część analiz (przede wszystkim sporządzenie wykresów zależności zużytej pamięci od czasu) została przeprowadzona w programie PyCharm.

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

W celu optymalizacji pracy utworzone zostały następujące funkcje:

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
W R do konstrukcji modelu użyto funkcji z dwóch najpopularniejszych bibliotek przeznaczonych do konstrukcji modeli mieszanych - "lme4" (funkcja "lmer()") w wersji 1.1-23 i "nlme" (funkcja "lme()") w wersji 3.1-148. W obu funkcjach modele zostały przedstawione w postaci formuły. Użyto ponadto biblioteki "mgcv" (funkcja "bam()") w wersji 1.8-31, która przeznaczona jest do operacji nad dużymi zbiorami danych. W celu efektywnego składowania i sprawnej obróbki danych użyto również różne funkcje z bibliotek "dplyr" i "tidyr". Do pomiaru RAM-u wykorzystana została biblioteka "profmem", a także wbudowany w program RStudio profiler.
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
3. Funkcja "check_RAM" do pomiaru zużytego RAM-u - przyjmująca jeden argument: "model", określający bibliotekę. Pierwotnie, funkcja ta zwraca ilość w bajtach, dlatego w ramach przejrzystości zostaje ona zamieniona na megabajty. Ponadto wbudowana w używaną tu bibliotekę "profmem" funkcja nie uwzględnia ilości pamięci uwalnianej, dlatego też jej wynik zasadniczo może niejednokrotnie znacząco różnic się od profilera pamięci w programie RStudio, który takowe uwzględnia.
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
Testy statystyczne, jak i wizualizacja wyników zostały sporządzone w R z wykorzystaniem bibliotek "dplyr", "ggplot2" i "FSA". Na podstawie wykresów pudełkowych zestawiono czas wykonywania funkcji wszystkich bibliotek i deklaracji obydwu języków, a także pojedyncze dla każdego języka i poszczególnych kombinacji bibliotek. Porównano średnie czasy konstrukcji modelu za pomocą deklaracji formuły i macierzy w języku Python z wykorzystaniem testu Wilcoxona, ponieważ przeprowadzony test Shapiro-Wilka stwierdził, że rozkład wartości deklaracji formuły nie jest normalny. Porównano także średnie czasy wszystkich bibliotek/deklaracji w obrębie dwóch języków, przeprowadzając test Kruskala-Wallisa, a w celu uzyskania szczegółów - Dunneta.


### 3.2 Wyniki
#### 3.2.1 Zbiór danych "cows"

##### 3.2.1.1 Opis zbioru danych

##### 3.2.1.2 Czas wykonania

##### 3.2.1.3 RAM




#### 3.2.2 Symulacje
##### 3.2.2.1 Opis zbiorów danych

##### 3.2.2.2 Czas wykonania

##### 3.2.2.3 RAM



## 4. Wnioski
Analizy przeprowadzone nad dużym (milionowym), a także wieloma innymi kombinacjami zbiorów danych dostarczają istotnych spostrzeżeń w zakresie podobieństwa i różnic dla określania tego samego modelu mieszanego, ale z użyciem różnych języków (tu: Python i R) i metod (tu: deklaracja z użyciem formuły i macierzy dla Python i bibliotek "lme4", "nlme" i "mgcv" w R). Trzeba podkreślić, że w dużej mierze nie są ważne liczby, ponieważ zależą one od mocy obliczeniowej komputera, w związku z czym na dwóch różnych maszynach mogą się one całkowicie różne - z użyciem nawet tego samego bloku instrukcji i działań - **ale na największą uwagę zasługują proporcje między różnymi czynnikami, bo to one właśnie stanowią o możliwościach danej metody/biblioteki.** Ponadto, w niniejszej pracy nacisk położono na nierozbudowany model, tzn. z jednym efektem stałym i jednym losowym, ale z dokładnym zbadaniem wpływu liczby obserwacji i grup (jak i interakcji) na czas konstrukcji modelu i przydział RAM-u. Choć nie zostało to szerzej opisane, to wykryto także istotny i duży wpływ liczby parametrów modelu (przede wszystkim efektów losowych) na czas wykonywania - nie zostało to dokładniej sprawdzone ze względu na długi czas oczekiwania konstrukcji jednego zbioru danych o kilkudziesięciu obserwacjach, wynoszącego nawet kilkadziesiąt minut. Podsumowując:

 - **Dla języka Python istotne znaczenie na czas wykonywania modelu ma liczba grup** - wraz ze stopniem złożoności grupowej zbioru, spodziewać się można długiego czasu oczekiwania (rzędu kilkudziesięciu, a nawet kilkuset sekund - jeśli rozpatrywany byłby duży zbiór danych). **Deklaracja modelu za pomocą macierzy wydaje się mieć lepszy wpływ, jeśli chodzi o czas wykonania (tu w minimalnym stopniu) i przydzielaną pamięć operacyjną.**  **Python dostarcza także wartości p dla współczynników i przedziały ufności**, czego nie zapewniają biblioteki "lme4" (wartości p i przedziałów ufności), "nlme" (przedziałów ufności) i "mgcv" (przedziałów ufności). Python jest językiem oszczędnym, jeśli mowa o przydzielanej na dopasowanie modeli pamięci operacyjnej.
 - Dla języka R, podczas analizy względnie małych zbiorów danych (do 200.000) i niskiej złożoności grupowej (do 1.000) warto rozważyć wybór pomiędzy bibliotekami "lme4" a "nlme", ponieważ "mgcv", pomimo użycia fREML, stabilizuje swój czas wykonywania dla obserwacji wynoszących ok. powyżej 300.000. Biblioteka "lme4" miała przewagę nad "nlme" dla niewielkich objętościowo zbiorów danych (do kilku tysięcy) i dużej złożoności grupowej. **Również biblioteka "mgcv" jest oszczędna w kwestii użytkowanej pamięci RAM na konstrukcję modelu, gdzie biblioteka "nlme" zużywa jej najwięcej spośród badanych.** 

Biblioteka "mgcv" w R wydaje się zatem dobrą alternatywą w zestawieniu z pozostałymi bibliotekami języka R i deklaracji z "statsmodel" języka Python, jeśli analizowany zbiór danych jest duży (od ok. 300.000 obserwacji) lub kiedy występują problemy z pamięcią. Używana metoda estymacji fREML również jest efektywna dla struktur o skomplikowanej złożoności - z wieloma klastrami. Głównym problemem funkcji "bam()" jest jednak nie liczba obserwacji, a parametrów do oszacowania. Pomimo zastosowanej w 4/5 przypadków tej samej metody estymacji (REML) różnice w czasach wykonywania (jak i alokowanej pamięci RAM) wynikają nie tyle, ile z weryfikowanych tu liczby obserwacji, grup i możliwe interakcji między czynnikami, a (idąc głębiej) używanej metody optymalizacji. Przykładowo, funkcja "lmer()" z biblioteki "lme4" w R estymuje parametry na podstawie optymalizacji profilowania log-wiarygodności w odniesieniu do macierzy kowariancji dla efektów losowych. Oznacza to, że wraz ze wzrostem liczby estymowanych parametrów, rozmiar macierzy zostaje równorzędnie zwiększany, a w ostateczności i czas wykonania. Aby znacząco przyspieszyć oczekiwanie, można zmienić ustawienia optymalizatora, np. dla "lme4" w "lmer()" dla:
```r
[g]lmerControl(calc.derivs = FALSE)
```
zostanie wyłączona opcja obliczania pochodnej po optymalizacji, ale z kolei przełoży się to na dokładność obliczeń. 
Wybierając pomiędzy językami Python i R, a także pomiędzy poszczególnymi deklaracjami/bibliotekami w nich zawartymi, należy zwrócić uwagę na strukturę posiadanego zbioru danych, a przede wszystkim jego objętość. Dla zbiorów małolicznych zasadne wydaje się użycie dowolnej. Trzeba też zwrócić uwagę na rezultat, tzn. biblioteka "lme4" nie zapewnia wartości p dla parametrów - takowe trzeba "własnoręcznie" oszacować, korzystając np. z testu Walda bądź też za pomocą zewnętrznych, dopełniających bibliotek, np. "lmerTest". Nie należy jednak się zbytnio rozwodzić nad wyborem języka/deklaracji/biblioteki, ponieważ koniec końców może się okazać, że przemyślenia i próby implementacji zajmą więcej czasu niż sama konstrukcja (która wcale dużo czasu może nie kosztować). Jeśli występują problemy z niedostateczną ilością pamięci RAM, warto strukturę zaimplementować do języka Python. Zaawansowanym użytkownikom poleca się skorzystanie z innych języków programowania (np. Julia) bądź skorzystanie z obliczeń równoległych pozwalających na użycie większej liczby rdzeni procesora (np. biblioteka "paralell" w języku R).
