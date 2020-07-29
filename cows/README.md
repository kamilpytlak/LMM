## Zbiór danych "cows"

1. [Opis zbioru danych](#opis-zbioru-danych)
2. [Metodyka](#metodyka)
3. [Czas wykonania](#czas-wykonania)
4. [RAM](#ram)
5. [Rezultaty](#rezultaty)


### Opis zbioru danych
Zbiór danych "cows" posłużył do konstrukcji modelu mieszanego z jednym komponentem losowym. Na łączną liczbę obserwacji w wysokości 1000 obserwacji składa się:

 - 409 osobników, nad którymi zastosowano powtarzalne pomiary; o unikalnych identyfikatorach zgrupowanych w kolumnie **"cow.id"**,
 - genotyp dla ortologa genu BTN1 będący zmienną dychotomiczną, tzn. przyjmującą wartość "1" w przypadku wystąpienia homozygoty z allelem dzikim lub "2" w przypadku heterozygoty/homozygoty mutanta - zgrupowane w kolumnie **"btn3a1"**,
 - numer laktacji - zgrupowane w kolumnie "**lactation**",
 - roczny udój w kilogramach mleka (kg/rok) - zgrupowane w kolumnie "**milk**",
 - ilość tłuszczu w zebranym mleku (w kg) - zgrupowane w kolumnie "**fat**".

Ogląd 6. pierwszych obserwacji:
| cow.id | btn3a1 | lactation | milk | fat |
|:------:|:------:|:---------:|:----:|:---:|
|    1   |    1   |     1     | 7770 | 358 |
|    2   |    1   |     1     | 7341 | 376 |
|    3   |    1   |     1     | 6998 | 294 |
|    3   |    1   |     2     | 8564 | 331 |
|    3   |    1   |     3     | 8621 | 330 |
|    4   |    1   |     1     | 9536 | 365 |

Sprawdzano, czy obserwowana mutacja (kolumna "btn3a1") ma wpływ na wydajność mleczną krów (kolumna "milk"). Z racji tego, że wśród tych samych osobników pomiary były powtarzane wielokrotnie, a przedmiotem analiz jest zbadanie zmienności pomiędzy osobnikami niż same wartości ich efektów, **efekt osobniczy (kolumna "cow.id") potraktowany został jako efekt losowy**. Przykładowy zbiór danych jest względnie niewielkim wycinkiem całej populacji krów z badanymi cechami, więc uwzględnienie tego efektu jako efektu stałego wiązałoby się z dodaniem do modelu 409 zmiennych niezależnych, a dodatkowo nie są znane poszczególne wartości parametrów pozostałych osobników, ponieważ niemożliwym jest zebranie ich w komplecie ze względu na wielkość całej populacji (co tyczy się zresztą niemal każdego badania statystycznego). **Jako efekty stałe potraktowane zostały genotyp (kolumna "btn3a1", o wartościach "1" lub "2") i numer laktacji (kolumna "lactation", o wartościach całkowitych od "1" do "4")**.

Następujący model można zatem przedstawić w formie równania:
\begin{equation}
$$y_{milk} = {X_{btn3a1} * \beta_{btn3a1} + X_{lactation} * \beta_{lactation} + (1|cow.id)} + \epsilon$$
\end{equation}
, gdzie $y_{milk}$ jest zmienną zależną, $X_{btn3a1}$ i $X_{lactation}$ zmiennymi niezależnymi, $\beta_{btn3a1}$ i $\beta_{lactation}$ estymowanymi współczynnikami kierunkowymi, $(1|cow.id)$ efektem losowym, a $\epsilon$ zakłóceniem losowym.

### Metodyka
Porównania wielkości przeznaczanych zasobów systemowych na konstrukcję modelu zostały w językach Python i R zestawione z użyciem biblioteki "statsmodel" w języku Python (która umożliwia deklarację modelu za pomocą formuły bądź macierzy, więc uwzględniono te dwa aspekty) i bibliotek "lme4", "nlme" i "mgcv" w języku R. Biblioteki "lme4" i "nlme" są wysoce spopularyzowane i używane w celach estymacji współczynników, natomiast biblioteka "mgcv", a w szczególności funkcja "bam()" polecana jest do pracy nad dużymi zbiorami danych, gdyż w przeciwieństwie do "lme4" i "nlme", które do estymacji używają metody REML, ta korzysta z metody fREML (Functional Relationship Estimation by Maximum Likelihood). Sprawdzano średnie różnice czasów wykonywania pomiędzy językami, bibliotekami w obrębie języków i jednego języka, ilości przydzielanego RAMu na proces, a także w charakterze interpretacyjnym obszerność informacji i ich dokładność w rezultatach wywołań.

#### Python
W języku python skorzystano z biblioteki "statsmodel" w wersji 0.11.1.[^1]. Biblioteka ta umożliwia deklarację modelu w postaci formuły i macierzy, więc obie metody zostały sprawdzone. Ponadto, w celu określenia macierzy, jak i podstawowych statystyk, zostały zaimportowane dodatkowe komponenty, takie jak biblioteka "pandas" w wersji 1.1.0 [^2], funkcja "dmatrices" z biblioteki "patsy" w wersji 0.5.1 [^3], funkcje "mean" i "stdev" będące podstawowymi statystykami opisującymi przeciętną wartość populacji (średnia) i odchylenie od średniej (odchylenie standardowe) z biblioteki "statistics" [^4] i biblioteka "time" [^5]. W większości analizy przeprowadzane były z wykorzystaniem internetowej aplikacji Jupyter Notebook, w związku z czym dodatkowo zostały użyte "magiczne polecenia" IPython ("IPython Magic Commands") bazujące na jądrze IPython: "line_profiler" i "memory_profiler" [^6], które posłużyły do pomiarów kolejno czasu wykonywania i użytego RAMu. Część analiz (przede wszystkim sporządzenie wykresów zależności zużytej pamięci od czasu) została przeprowadzona w programie PyCharm.

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

[^1]: [https://www.statsmodels.org/stable/index.html](https://www.statsmodels.org/stable/index.html)
[^2]: [https://pandas.pydata.org/](https://pandas.pydata.org/)
[^3]: [https://patsy.readthedocs.io/en/latest/overview.html](https://patsy.readthedocs.io/en/latest/overview.html)
[^4]: [https://docs.python.org/3/library/statistics.html](https://docs.python.org/3/library/statistics.html)
[^5]: [https://docs.python.org/3/library/time.html](https://docs.python.org/3/library/time.html)
[^6]: [https://jakevdp.github.io/PythonDataScienceHandbook/01.07-timing-and-profiling.html](https://jakevdp.github.io/PythonDataScienceHandbook/01.07-timing-and-profiling.html)

#### R
W R do konstrukcji modelu użyto funkcji z dwóch najpopularniejszych bibliotek przeznaczonych do konstrukcji modeli mieszanych - "lme4" (funkcja "lmer()") w wersji 1.1-23[^7] i "nlme" (funkcja "lme()") w wersji 3.1-148[^8]. W obu funkcjach modele zostały przedstawione w postaci formuły. Użyto ponadto biblioteki "mgcv" (funkcja "bam()") w wersji 1.8-31[^9], która przeznaczona jest do operacji nad dużymi zbiorami danych. W celu efektywnego składowania i sprawnej obróbki danych, użyto również różne funkcje z bibliotek "dplyr" i "tidyr". Do pomiaru RAMu wykorzystana została biblioteka "profmem"[^10], a także wbudowany w program RStudio profiler.
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

[^7]: [https://cran.r-project.org/web/packages/lme4/lme4.pdf](https://cran.r-project.org/web/packages/lme4/lme4.pdf)
[^8]: [https://cran.r-project.org/web/packages/nlme/nlme.pdf](https://cran.r-project.org/web/packages/nlme/nlme.pdf)
[^9]: [https://cran.r-project.org/web/packages/mgcv/mgcv.pdf](https://cran.r-project.org/web/packages/mgcv/mgcv.pdf)
[^10]: [https://cran.r-project.org/web/packages/profmem/vignettes/profmem.html](https://cran.r-project.org/web/packages/profmem/vignettes/profmem.html)


#### Metody statystyczne
Testy statystyczne, jak i wizualizacja wyników zostały sporządzone w R z wykorzystaniem bibliotek "dplyr", "ggplot2" i "FSA". W oparciu o wykresy pudełkowe, zestawiono czas wykonywania funkcji wszystkich bibliotek i deklaracji obydwu języków, a także pojedyncze dla każdego języka i poszczególnych kombinacji bibliotek. Porównano średnie czasy konstrukcji modelu za pomocą deklaracji formuły i macierzy w języku Python z wykorzystaniem testu Wilcoxona, ponieważ przeprowadzony test Shapiro-Wilka stwierdził, że rozkład wartości deklaracji formuły nie jest normalny. Porównano także średnie czasy wszystkich bibliotek/deklaracji w obrębie dwóch języków, przeprowadzając test Kruskala-Wallisa, a w celu uzyskania szczegółów - Dunneta.

### Czas wykonania
#### Python
#### R

### RAM
#### Python
#### R

### Rezultaty
#### Python
#### R
#### Ogólne
