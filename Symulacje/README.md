
# Symulacje

1. [Opis zbiorów danych](#1-opis-zbiorow-danych)
2. [Czasy wykonania](#2-czasy-wykonania)
3. [RAM](#3-ram)
4. [Wnioski](#4-wnioski)<br />
<br />

## 1. Opis zbiorów danych
W języku R wygenerowano kilka zestawów zbiorów danych o następujących właściwościach:
 - od 10.000 do 1.000.000 obserwacji (co 10.000) z parametrami "y", "x_1", "x_2", "x_3" i dwoma zmiennymi grupującymi: "g_1" (25 grup) i g_2 (1.000 grup),
 -   od 10.000 do 1.000.000 obserwacji (co 10.000) z parametrami z parametrami "y", "x_1" i jedną zmienną grupującą: "g_1" (1.000 grup),
 - od 20.000 do 1.000.000 obserwacji (co 10.000) z parametrami z parametrami "y", "x_1" i jedną zmienną grupującą: "g_1" (10.000 grup).
 - 1 zbiór danych z 1.000.000 obserwacji, z parametrami "y", "x0" i jedną zmienną grupującą "fac" (4 grupy), używając funkcji "gamSim()" z biblioteki "mgcv".

Tak stworzone zestawy posłużyły do określenia wpływu objętości zbioru (liczby obserwacji), liczby grup, liczby parametrów, a także interakcji pomiędzy poszczególnymi czynnikami na czas wykonania modelu i użytą pamięć operacyjną.

Ogląd 6. pierwszych obserwacji z milionowego zbioru wygenerowanego z użyciem funkcji "gamSim()":
|         y        |         x0         | fac |
|:----------------:|:------------------:|:---:|
| 11.5640116760671 |  0.319636012427509 |  1  |
| 21.0272668936847 | 0.0188121555838734 |  2  |
| 19.5681800055627 |  0.831518376944587 |  3  |
| 19.4919352004216 |  0.152270128484815 |  4  |
| 12.1072589322815 |  0.778154832078144 |  1  |
| 16.6358024128427 |  0.775473831919953 |  2  |

<br />

Następujący model można zatem przedstawić w formie równania:<br />
<img src="https://render.githubusercontent.com/render/math?math=y = X_{0} * \beta_{0} %2B Z_{fac} * u_{fac} %2B \epsilon"><br />
, gdzie:
- <img src="https://render.githubusercontent.com/render/math?math=y"> jest wektorem obserwacji ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(y) = X_{0} * \beta_{0}"> (zmienna zależna)
 - <img src="https://render.githubusercontent.com/render/math?math=X_{0}"> i <img src="https://render.githubusercontent.com/render/math?math=Z_{fac}"> znanymi macierzami projektowymi składającymi się z realizacji opisywanych czynników,
 -  <img src="https://render.githubusercontent.com/render/math?math=\beta_{0}"> nieznanym wektorem efektów stałych,
 - <img src="https://render.githubusercontent.com/render/math?math=u_{fac}"> nieznanym wektorem efektów losowych ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(u) = 0">,
 - <img src="https://render.githubusercontent.com/render/math?math=\epsilon"> nieznanym wektorem zakłóceń losowych o średniej <img src="https://render.githubusercontent.com/render/math?math=E({\epsilon}) = 0">.<br />
<br />

### 1.1 Estymacja metodą REML
*Szczegółowe analizy dot. estymacji metodą REML (dla zbioru milionowego):*
- w języku Pythomn: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb),
- w języku R: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb)<br />
<br />

#### 1.1.1 Efekty stałe<br />
**> R**<br />
Oceny estymatorów z wykorzystaniem metody ograniczonej największej wiarygodności przedstawiają się następująco:

|            | model_lme4 (REML) | model_nlme (REML) | model_mgcv (REML) |
|:-----------:|:---------:|:----------:|:----------:|:---------:|
| (Intercept) |  15.39033 |  15.390336 | 7.88429 |
|   x0   |  -0.04068 |  -0.040677 | -0.04064 |


W pierwszej kolumnie zebrane zostały efekty stałe - wyraz wolny i "x0", natomiast w następnych kolejno: model mieszany z użyciem biblioteki lme4, model mieszany z użyciem biblioteki nlme, model mieszany z użyciem biblioteki mgcv.<br />
<br />
*Oceny efektów stałych z wykorzystaniem biblioteki "lme4":*
|             | Estimate | Std.Error | t-value |
|:-----------:|:--------:|:---------:|:-------:|
| (Intercept) | 15.39033 |  1.93838  |  7.940  |
|      x0     | -0.04068 |  0.01346  |  -3.022 |

<br />

*Oceny efektów stałych z wykorzystaniem biblioteki "nlme":*
|             |   Value   | Std.Error |   DF   |  t-value  | p-value |
|:-----------:|:---------:|:---------:|:------:|:---------:|:-------:|
| (Intercept) | 15.390336 | 1.9380667 | 999995 |  7.941077 |  0.0000 |
|      x0     | -0.040677 | 0.0134595 | 999995 | -3.022182 |  0.0025 |

<br />

*Oceny efektów stałych z wykorzystaniem biblioteki "mgcv":*
|             | Estimate | Std.Error | t-value | Pr(>\|t\|) |     |
|:-----------:|:--------:|:---------:|:-------:|:----------:|:---:|
| (Intercept) |  7.88429 |  0.01166  |  675.93 |   <2e-16   | *** |
|      x0     | -0.04064 |  0.01346  |  -3.02  |   0.00253  |  ** ||

<br />

**> Python**
<br />
_Oceny efektów stałych z wykorzystaniem deklaracji formuły:_
|           |  Coef. | Std.Err. |    z   | P>\|z\| | [0.025 | 0.975] |
|:---------:|:------:|:--------:|:------:|:-------:|:------:|:------:|
| Intercept | 15.390 |   1.943  |  7.921 |  0.000  | 11.582 | 19.198 |
|     x0    | -0.041 |   0.013  | -3.022 |  0.003  | -0.067 | -0.014 |

<br />

_Oceny efektów stałych z wykorzystaniem deklaracji macierzy:_
|           |  Coef. | Std.Err. |    z   | P>\|z\| | [0.025 | 0.975] |
|:---------:|:------:|:--------:|:------:|:-------:|:------:|:------:|
| Intercept | 15.390 |   1.943  |  7.921 |  0.000  | 11.582 | 19.198 |
|     x0    | -0.041 |   0.013  | -3.022 |  0.003  | -0.067 | -0.014 |

<br />


**1.1.2 Efekty losowe**<br />
**> R**
<br />
_Oceny efektów losowych z wykorzystaniem biblioteki “lme4”:_
|  Groups  |     Name    | Variance | Std.Dev. |
|:--------:|:-----------:|:--------:|:--------:|
|  fac  | (Intercept) |  15.03 |   3.877   |
| Residual |             |  15.10 |   3.886   |

<br />

_Oceny efektów losowych z wykorzystaniem biblioteki “nlme”:_

Random effects:
 Formula: ~1 | cow.id
|         | (Intercept) | Residual |
|:-------:|:-----------:|:--------:|
| StdDev: |   3.876102  | 3.885725 |

<br />

_Oceny efektów losowych z wykorzystaniem biblioteki “mgcv”:_
|           |  edf  | Ref.df |   F   | p-value |     |
|:---------:|:-----:|:------:|:-----:|:-------:|:---:|
| s(fac) | 1 |   1  | 746288 |  <2e-16 | *** |

<br />


**> Python**
<br />
_Oceny efektów losowych z wykorzystaniem deklaracji formuły:_
| Group Var  | 15.099 | 3.189 |
|:----------:|:------:|:-----:|

<br />

_Oceny efektów losowych z wykorzystaniem deklaracji macierzy:_
| Group Var  | 15.099 | 3.189 |
|:----------:|:------:|:-----:|

<br />


## 2. Czasy wykonania
W przypadku jednego zbioru milionowego czasy zebrano w ten sam sposób, co dla zbioru "cows", tzn. ze 100 powtórzeniami i kalkulacją średniej arytmetycznej i odchylenia standardowego do 4 miejsc po przecinku. Zostały one zebrane do pliku "LMM_times_BIG_1e6_gam": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv)
Czasy dla wielu różnych kombinacji zestawów (z różnymi połączeniami liczby obserwacji i grup) zostały zebrano do pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv). Dla danej obserwacji złożonej z pary liczba obserwacji-liczba grup obliczono czas wykonania jako średnia z 3 niezależnych od siebie pomiarów cząstkowych.

### 2.1 Dla jednego zbioru milionowego

#### 2.1.1 Python
*Szczegółowe analizy dot. czasu wykonywania w języku Python: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb)

Ogląd 6. pierwszych czasów wykonywania:
| statsmodel_formula | statsmodel_matrix |
|:------------------:|:-----------------:|
|       1.5731       |       1.5631      |
|       1.6571       |        1.83       |
|       1.9209       |       1.713       |
|        1.755       |       1.5611      |
|       1.9279       |       1.4991      |
|       1.8369       |       1.4582      |

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe. **Dla deklaracji modelu za pomocą formuły średnia wynosiła 1.8467 sek., odchylenie standardowe - 0.2006 sek. a dla macierzy - średnia: 1.6672 sek., a odchylenie standardowe - 0.2507 sek.** 

![Wykres rozrzutu czasów wykonywania w zależności od deklaracji w języku Python](https://i.imgur.com/xMT8pNY.png)

Ze względu na zbliżone do siebie średnie czasy oczekiwania obu deklaracji, postanowiono sprawdzić, czy różnią się one istotnie. Z racji tego, że rozkład czasów wykonywania obu metod nie jest normalny na poziomie 0.05, wykonano test Wilcoxona. Założono hipotezę, że czasy nie różnią się przeciwko hipotezie, że czas wykonywania konstrukcji modelu metodą "formula" jest większy niż "matrix".

```r
wilcox.test(csv_data$statsmodel_formula, csv_data$statsmodel_matrix, alternative="greater")

Wilcoxon rank sum test with continuity correction

data:  csv_data$statsmodel_formula and csv_data$statsmodel_matrix
W = 8265.5, p-value = 7.45e-16
alternative hypothesis: true location shift is greater than 0
```

**Zatem dla analizowanego zbioru danych o wielkości 1.000.000 obserwacji z jednym efektem stałym i jednym losowym sposób konstrukcji za pomocą macierzy jest szybszy niż za pomocą formuły, co odrzuca hipotezę o równości obu średnich.**

Czasy zostały również jednostkowo zmierzone za pomocą "IPython Magic Commands" w Jupyter Notebook: %timeit, odpowiednio dla deklaracji formułą:
```python
Timer unit: 1e-07 s

Total time: 2.93819 s File: <ipython-input-3-55d36bd81f50> Function: LMM at line 1

Line # Hits Time Per Hit % Time Line Contents
 1                                           def LMM(model):
 2         1        109.0    109.0      0.0      if model == "formula":
 3         1   21009131.0 21009131.0     71.5          LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
 4         1    8372628.0 8372628.0     28.5          LMMF_formula = LMM_formula.fit()
 5         1         17.0     17.0      0.0          return LMMF_formula
 6                                           
 7                                               elif model == "matrix":
 8                                                   LMM_matrix = sm.MixedLM(y, X, groups=groups)
 9                                                   LMMF_matrix = LMM_matrix.fit()
10                                                   return LMMF_matrix
```

i macierzy:
```python
Timer unit: 1e-07 s

Total time: 2.51752 s File: <ipython-input-3-55d36bd81f50> Function: LMM at line 1

Line # Hits Time Per Hit % Time Line Contents
 1                                           def LMM(model):
 2         1         60.0     60.0      0.0      if model == "formula":
 3                                                   LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
 4                                                   LMMF_formula = LMM_formula.fit()
 5                                                   return LMMF_formula
 6                                           
 7         1        228.0    228.0      0.0      elif model == "matrix":
 8         1   17742568.0 17742568.0     70.5          LMM_matrix = sm.MixedLM(y, X, groups=groups)
 9         1    7432297.0 7432297.0     29.5          LMMF_matrix = LMM_matrix.fit()
10         1         19.0     19.0      0.0          return LMMF_matrix
```

**Aż 71.5% czasu w przypadku formuły i 70.5% w przypadku macierzy składa się na ogólną konstrukcję modelu**, a 28.5% dla formuły i 29.5% dla macierzy na dopasowanie, czyli oszacowanie macierzy kowariancji i reszt.

### 2.1.2 R
*Szczegółowe analizy dot. czasu wykonywania w języku R:* [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb)


Ogląd 6. pierwszych pomiarów:
|   lme4  |   nlme  |   mgcv  |
|:-------:|:-------:|:------:|
|  9.2887 | 11.5224 | 3.6673 |
| 10.3971 | 11.7103 | 3.4722 |
|  9.3047 | 11.7643 |  3.767 |
|  9.6025 | 11.1846 |  3.817 |
|  9.0728 | 10.0722 |  4.076 |
| 10.3841 |  12.217 | 3.9451 |

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe. **Dla konstrukcji modelu za pomocą biblioteki "lme4" średnia wyniosła 9.7396 sek., odchylenie standardowe - 0.5314 sek., "nlme" - średnia: 11.4261 sek., odchylenie standardowe: 0.621 sek., a "mgcv" - średnia: 3.9247 sek., odchylenie standardowe - 0.2585 sek.**

![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/VhxUHNJ.png)

Wykres pudełkowy wyraźnie uwidacznia dużą rozbieżność pomiędzy czasami wykonywania bibliotek "lme4", "nlme" a "mgcv".

![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/Dqn13R4.png)


### 2.2 Dla symulacji-kombinacji
Bazując na wielu różnych kombinacjach liczby obserwacji-grup sztucznie wygenerowanych zbiorów, sprawdzano wpływ liczby obserwacji, liczby grup, a także liczby obserwacji i liczby grup w interakcji na czas konstrukcji modelu w zależności od danej biblioteki. Same kombinacje zostały zaprojektowane w taki sposób, by każdy z zestawów uwzględniał od 10.000 do 1.000.000 obserwacji (z przeskokiem co 10.000) i zawierał po: 25, 1000 i 10000 grup, przykładowo:
| Liczba_obserwacji | Liczba_grup | Czas_wykonania | Biblioteka |   RAM   |
|:-----------------:|:-----------:|:--------------:|:----------:|:-------:|
|       10000       |      25     |     0.0959     |    lme4    | 14.3831 |
|       20000       |      25     |     0.1580     |    lme4    | 28.6927 |
|       30000       |      25     |     0.2302     |    lme4    | 41.8227 |
|       40000       |      25     |     0.3035     |    lme4    | 57.3128 |
|       50000       |      25     |     0.3971     |    lme4    | 70.4420 |
|       60000       |      25     |     0.4710     |    lme4    | 83.5720 |

W tej części analizy skupione były wokół modelu liniowego. Rozpatrywany model liniowy z interakcjami można przedstawić następującym równaniem:
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas} = X_{L. obserwacji} * B_{L. obserwacji} %2B X_{L. grup} * B_{L. grup} %2B X_{L. obserwacji} * X_{L. grup} * B_{L.obserwacji:L.grup}">

#### 2.2.1 Python
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i metody "formula":**
```python
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "statsmodel_formula"))

Residuals:
     Min       1Q   Median       3Q      Max 
-16.5448  -3.0124  -0.6893   3.2582  23.2348 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   1.122e+00  6.719e-01   1.670    0.096 .  
Liczba_obserwacji             1.010e-05  1.155e-06   8.741  < 2e-16 ***
Liczba_grup                   6.286e-03  1.173e-04  53.566  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 9.173e-10  2.011e-10   4.562 7.44e-06 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 4.469 on 295 degrees of freedom
Multiple R-squared:  0.9793,	Adjusted R-squared:  0.9791 
F-statistic:  4663 on 3 and 295 DF,  p-value: < 2.2e-16
```

Wszystkie badane komponenty mają dodatni wpływ na czas wykonywania modelu - zarówno liczba obserwacji, liczba grup, jak i przyrost liczby obserwacji i grup łącznie (jako interakcja). Odnosząc się do zamieszczonego wyżej ogólnego modelu liniowego tu rozpatrywanego, w tym przypadku zależność przedstawia się następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (formula)} = 0.0000101 * X_{L. obserwacji} %2B  0.006286 * X_{L. grup} %2B 0.0000000009173 * X_{L. obserwacji} * X_{L. grup}">

Jak można zauważyć, dla wzrostów liczby obserwacji czy grup rzędów jedności różnice wydają się być minimalne. Jednakże już dla wielkoskalowych danych, różnice są już znaczące, np. dla 100.000 obserwacji (bez uwzględniania grup) jest to wzrost o 1.01 sekundy. Uwzględniając grupy, czas wzrasta intensywniej, bowiem już nawet dla 1.000 grup (bez uwzględniania obserwacji) następuje wzrost o 6.286 sekund.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i metody "matrix":**
```python
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "statsmodel_matrix"))

Residuals:
     Min       1Q   Median       3Q      Max 
-16.9370  -3.0332  -0.7543   3.2501  20.9384 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   9.955e-01  6.314e-01   1.576 0.115993    
Liczba_obserwacji             1.033e-05  1.091e-06   9.460  < 2e-16 ***
Liczba_grup                   6.333e-03  1.112e-04  56.974  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 7.490e-10  1.902e-10   3.937 0.000103 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 4.188 on 293 degrees of freedom
Multiple R-squared:  0.9816,	Adjusted R-squared:  0.9814 
F-statistic:  5212 on 3 and 293 DF,  p-value: < 2.2e-16
```

W przypadku macierzy również wszystkie badane komponenty mają dodatni wpływ na czas wykonywania modelu - zarówno liczba obserwacji, liczba grup, jak i przyrost liczby obserwacji i grup łącznie (jako interakcja). Odnosząc się do zamieszczonego wyżej ogólnego modelu liniowego tu rozpatrywanego, w tym przypadku zależność przedstawia się następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (matrix)} = 0.00001033 * X_{L. obserwacji} %2B  0.006333 * X_{L. grup} %2B 0.000000000749 * X_{L. obserwacji} * X_{L. grup}">

Średni czas konstrukcji modeli z wykorzystaniem macierzy jest mniejszy od średniego czasu z wykorzystaniem formuły o ok. 2.3%. Oznacza to, że dla zbioru danych z 1.000.000 obserwacjami szacunkowo w przypadku formuły operacja zajmie 10.1 sekundy, a dla macierzy - 10.33 sekund, więc ta różnica wydaje się być minimalna i nieznacząca. Inaczej jednak jest, mając na uwadze liczbę grup - tu wraz ze wzrostem liczby grup o 1000, czas wykonywania wzrasta o 6.333 sekund, natomiast dla formuły byłoby to 6.286 sekund. **Dlatego w analizowanym przypadku jednego efektu stałego i losowego dla dwóch możliwych deklaracji modelu w języku Python, to nie liczba obserwacji, a liczba grup ma znaczący wpływ na rezultat.**

![enter image description here](https://i.imgur.com/cxAguA6.png)

Załączony wykres zależności czasu wykonania modelu od liczby obserwacji i (potraktowane jako osobne regresje) użytej metody w sposób zauważalny uwidacznia istotne różnice pomiędzy liczbą grup a czasami wykonywania.

![enter image description here](https://i.imgur.com/DJGvzuQ.png)

Tę specyfikę potwierdza także skonstruowany trójwymiarowy wykres trzech zmiennych. Posuwając się wzdłuż osi x ("liczba obserwacji") i rozważając wyniki osi y ("czas wykonania"), zauważa się nieznaczący wzrost. Gwałtownie wznoszący się "płaszcz" zaczyna się natomiast tworzyć po uwzględnieniu dodatkowej osi z ("liczba grup"). Ważnej uwadze zasługuje również kant "płaszcza" - przy obserwacjach wynoszących do ok. 500.000 czasy wykonywania są dość jednostajne, a gwałtowny ich wzrost odnotowuje się przy liczbie obserwacji wynoszącej od ok. 800.000 (co jest widoczne na wykresie w postaci zawiniętego ku górze prawego brzegu).

#### 2.2.2 R
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "lme4":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "lme4"))

Residuals:
   Min     1Q Median     3Q    Max 
-4.996 -2.525 -0.452  0.917 98.569 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -1.412e+00  9.422e-01  -1.499  0.13499    
Liczba_obserwacji              1.552e-05  1.620e-06   9.581  < 2e-16 ***
Liczba_grup                    4.890e-04  1.646e-04   2.972  0.00321 ** 
Liczba_obserwacji:Liczba_grup -1.645e-10  2.819e-10  -0.583  0.56003    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 6.266 on 295 degrees of freedom
Multiple R-squared:  0.3613,	Adjusted R-squared:  0.3548 
F-statistic: 55.62 on 3 and 295 DF,  p-value: < 2.2e-16
```

**Dla funkcji "lme()" z biblioteki "lme4" zarówno liczba obserwacji, jak i liczba grup dodatnio korelują z czasem wykonywania**; nie stwierdzono jednak interakcji. Powołując się na wcześniej zamieszczony ogólny model liniowy, w tym przypadku przedstawia się on następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (lme4)} = 0.00001552 * X_{L. obserwacji} %2B 0.000489 * X_{L. grup}">

Dla liczby obserwacji wzrost czasu wykonania jest niemal analogiczny do tego z języka Python. **Duża różnica tkwi jednak w zależności od grupy** - dla języka Python wraz ze wzrostem liczby grup o 1.000, czas wykonania wzrasta średnio ok. 6 sekund, natomiast w przypadku biblioteki "lme4" - o ok. 0.5 sekundy. **Nie ma więc co się dziwić, że konstrukcja modelu dla sztucznego zbioru danych z 1.000.000 obserwacji i 10.000 grup wyniosła ok. 100 sekund, a dla tego samego zbioru, ale dla biblioteki "lme4" - ok. 20 sekund.**


**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "nlme":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "nlme"))

Residuals:
    Min      1Q  Median      3Q     Max 
-2.2838 -0.3946 -0.0335  0.1744  3.4765 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -2.094e-01  1.289e-01  -1.624    0.105    
Liczba_obserwacji              1.213e-05  2.217e-07  54.711  < 2e-16 ***
Liczba_grup                    1.632e-04  2.252e-05   7.247 3.76e-12 ***
Liczba_obserwacji:Liczba_grup  4.730e-11  3.859e-11   1.226    0.221    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.8576 on 295 degrees of freedom
Multiple R-squared:  0.9482,	Adjusted R-squared:  0.9477 
F-statistic:  1799 on 3 and 295 DF,  p-value: < 2.2e-16
```


**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "mgcv":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "mgcv"))

Residuals:
     Min       1Q   Median       3Q      Max 
-0.58959 -0.15780 -0.03521  0.14095  1.22612 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                    1.478e+00  4.147e-02  35.636  < 2e-16 ***
Liczba_obserwacji              2.334e-06  7.130e-08  32.737  < 2e-16 ***
Liczba_grup                    7.872e-05  7.243e-06  10.868  < 2e-16 ***
Liczba_obserwacji:Liczba_grup -5.103e-11  1.241e-11  -4.112  5.1e-05 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.2758 on 295 degrees of freedom
Multiple R-squared:  0.8557,	Adjusted R-squared:  0.8543 
F-statistic: 583.2 on 3 and 295 DF,  p-value: < 2.2e-16
```



## 3. RAM


### 3.1 Dla zbioru milionowego
#### 3.1.1 Python
#### 3.1.2 R

### 3.2 Dla symulacji-kombinacji
#### 3.2.1 Python
#### 3.2.2 R


## 4. Wnioski

### 4.1 Interpretacja modeli - Python i R



### 4.2 Czasy wykonania
#### 4.2.1 Python

#### 4.2.2 R

### 4.3 RAM
#### 4.3.1 Python

#### 4.3.2 R
