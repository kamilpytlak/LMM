
# Zbiór danych "cows"

1. [Opis zbioru danych](#1-opis-zbioru-danych)
2. [Czas wykonania](#2-czas-wykonania)
3. [RAM](#3-ram)
4. [Wnioski](#4-wnioski)<br />
<br />

## 1. Opis zbioru danych
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

Sprawdzano, czy obserwowana mutacja (kolumna "btn3a1") ma wpływ na wydajność mleczną krów (kolumna "milk"). Z racji tego, że wśród tych samych osobników pomiary były powtarzane wielokrotnie, a przedmiotem analiz jest zbadanie zmienności pomiędzy osobnikami niż same wartości ich efektów, **efekt osobniczy (kolumna "cow.id") potraktowany został jako efekt losowy**. Przykładowy zbiór danych jest względnie niewielkim wycinkiem całej populacji krów z badanymi cechami, więc uwzględnienie tego efektu jako efektu stałego wiązałoby się z dodaniem do modelu 409 zmiennych niezależnych, a dodatkowo nie są znane poszczególne wartości parametrów pozostałych osobników, ponieważ niemożliwym jest zebranie ich w komplecie ze względu na wielkość całej populacji (co tyczy się zresztą niemal każdego badania statystycznego). **Jako efekty stałe potraktowane zostały genotyp (kolumna "btn3a1", o wartościach "1" lub "2") i numer laktacji (kolumna "lactation", o wartościach całkowitych od "1" do "4")**.<br />

Następujący model można zatem przedstawić w formie równania:<br />
<img src="https://render.githubusercontent.com/render/math?math=y_{milk} = X_{btn3a1} * \beta_{btn3a1} %2B X_{lactation} * \beta_{lactation} %2B Z_{cow.id} * u_{cow.id} %2B \epsilon"><br />
, gdzie:
- <img src="https://render.githubusercontent.com/render/math?math=y_{milk}"> jest wektorem obserwacji ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(y_{milk}) = X_{btn3a1} * \beta_{btn3a1} %2B X_{lactation} * \beta_{lactation}"> (zmienna zależna)
 - <img src="https://render.githubusercontent.com/render/math?math=X_{btn3a1}">, <img src="https://render.githubusercontent.com/render/math?math=X_{lactation}"> i <img src="https://render.githubusercontent.com/render/math?math=Z_{cow.id}"> znanymi macierzami projektowymi składającymi się z realizacji opisywanych czynników,
 -  <img src="https://render.githubusercontent.com/render/math?math=\beta_{btn3a1}"> i <img src="https://render.githubusercontent.com/render/math?math=\beta_{lactation}"> nieznanymi wektorami efektów stałych,
 - <img src="https://render.githubusercontent.com/render/math?math=u_{cow.id}"> nieznanym wektorem efektów losowych ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(u) = 0">,
 - <img src="https://render.githubusercontent.com/render/math?math=\epsilon"> nieznanym wektorem zakłóceń losowych o średniej <img src="https://render.githubusercontent.com/render/math?math=E({\epsilon}) = 0">.<br />
<br />

### 1.1 Estymacja metodą REML
*Szczegółowe analizy dot. estymacji metodą REML:*
- w języku Python: [https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb),
- w języku R: <br />
<br />

#### 1.1.1 Efekty stałe<br />
**> R**<br />
Oceny estymatorów z wykorzystaniem metody ograniczonej największej wiarygodności przedstawiają się następująco:
|             |  model_lm (LM) | model_lme4 (REML) | model_nlme (REML) | model_mgcv (REML) |
|:-----------:|:---------:|:----------:|:----------:|:---------:|
| (Intercept) | 7770.0000 |  6699.4479 |  6699.4479 | 6699.4479 |
|   btn3a12   | -328.6617 |  -244.0844 |  -244.0844 | -244.0844 |
|  lactation2 | 1253.3234 |  1307.0367 |  1307.0367 | 1307.0367 |
|  lactation3 | 1856.3374 |  1800.5379 |  1800.5379 | 1800.5379 |
|  lactation4 | 1856.6869 |  1669.2723 |  1669.2723 | 1669.2723 |

W pierwszej kolumnie zebrane zostały efekty stałe - wyraz wolny, ortolog genu i poziomy laktacji, natomiast w następnych kolejno: klasyczny model liniowy, model mieszany z użyciem biblioteki lme4, model mieszany z użyciem biblioteki nlme, model mieszany z użyciem biblioteki mgcv.<br />
<br />
*Oceny efektów stałych z wykorzystaniem biblioteki "lme4":*
|             | Estimate | Std. Error | t value |
|-------------|----------|------------|---------|
| (Intercept) | 6699.45  | 81.08      | 82.632  |
| btn3a12     | -244.08  | 235.12     | -1.038  |
| lactation2  | 1307.04  | 84.63      | 15.443  |
| lactation3  | 1800.54  | 102.10     | 17.635  |
| lactation4  | 1669.27  | 176.45     | 9.460   |

<br />

*Oceny efektów stałych z wykorzystaniem biblioteki "nlme":*
|             |   Value  | Std.Error |  DF |  t-value | p-value |
|:-----------:|:--------:|:---------:|:---:|:--------:|:-------:|
| (Intercept) | 6699.448 |  81.07595 | 588 | 82.63175 |  0.0000 |
|   btn3a12   | -244.084 | 235.11696 | 407 | -1.03814 |  0.2998 |
|  lactation2 | 1307.037 |  84.63442 | 588 | 15.44332 |  0.0000 |
|  lactation3 | 1800.538 | 102.10172 | 588 | 17.63475 |  0.0000 |
|  lactation4 | 1669.272 | 176.44866 | 588 |  9.46038 |  0.0000 |

<br />

*Oceny efektów stałych z wykorzystaniem biblioteki "mgcv":*
|             | Estimate | Std.Error | t-value | Pr(>\|t\|) |     |
|:-----------:|:--------:|:---------:|:-------:|:----------:|:---:|
| (Intercept) |  6699.45 |   81.08   |  82.632 |   <2e-16   | *** |
|   btn3a12   |  -244.08 |   235.12  |  -1.038 |     0.3    |     |
|  lactation2 |  1307.04 |   84.63   |  15.443 |   <2e-16   | *** |
|  lactation3 |  1800.54 |   102.10  |  17.635 |   <2e-16   | *** |
|  lactation4 |  1669.27 |   176.45  |  9.460  |   <2e-16   | *** |

<br />

**> Python**
<br />
_Oceny efektów stałych z wykorzystaniem deklaracji formuły:_
|                |   Coef.  | Std.Err. |    z   | P>\|z\| |  [0.025  |  0.975]  |
|:--------------:|:--------:|:--------:|:------:|:-------:|:--------:|:--------:|
|    Intercept   | 6699.448 |  81.078  | 82.630 |  0.000  | 6540.538 | 6858.358 |
|   btn3a1[T.2]  | -244.084 |  235.197 | -1.038 |  0.299  | -705.062 |  216.894 |
| lactation[T.2] | 1307.037 |  84.712  | 15.429 |  0.000  | 1141.005 | 1473.069 |
| lactation[T.3] | 1800.538 |  102.283 | 17.604 |  0.000  | 1600.067 | 2001.008 |
| lactation[T.4] | 1669.271 |  177.492 |  9.405 |  0.000  | 1321.394 | 2017.148 |

<br />

_Oceny efektów stałych z wykorzystaniem deklaracji macierzy:_
|                |   Coef.  | Std.Err. |    z   | P>\|z\| |  [0.025  |  0.975]  |
|:--------------:|:--------:|:--------:|:------:|:-------:|:--------:|:--------:|
|    Intercept   | 6699.448 |  81.078  | 82.630 |  0.000  | 6540.538 | 6858.358 |
|   btn3a1[T.2]  | -244.084 |  235.197 | -1.038 |  0.299  | -705.062 |  216.894 |
| lactation[T.2] | 1307.037 |  84.712  | 15.429 |  0.000  | 1141.005 | 1473.069 |
| lactation[T.3] | 1800.538 |  102.283 | 17.604 |  0.000  | 1600.067 | 2001.008 |
| lactation[T.4] | 1669.271 |  177.492 |  9.405 |  0.000  | 1321.394 | 2017.148 |

<br />


**1.1.2 Efekty losowe**<br />
**> R**
<br />
_Oceny efektów losowych z wykorzystaniem biblioteki “lme4”:_
|  Groups  |     Name    | Variance | Std.Dev. |
|:--------:|:-----------:|:--------:|:--------:|
|  cow.id  | (Intercept) |  1240403 |   1114   |
| Residual |             |  1252911 |   1119   |

<br />

_Oceny efektów losowych z wykorzystaniem biblioteki “nlme”:_

Random effects:
 Formula: ~1 | cow.id
|         | (Intercept) | Residual |
|:-------:|:-----------:|:--------:|
| StdDev: |   1113.734  | 1119.335 |

<br />

_Oceny efektów losowych z wykorzystaniem biblioteki “mgcv”:_
|           |  edf  | Ref.df |   F   | p-value |     |
|:---------:|:-----:|:------:|:-----:|:-------:|:---:|
| s(cow.id) | 277.4 |   407  | 2.351 |  <2e-16 | *** |

<br />


**> Python**
<br />
_Oceny efektów losowych z wykorzystaniem deklaracji formuły:_
| Group Var | 1240396.526 | 147.330 |
|:---------:|:-----------:|:-------:|

<br />

_Oceny efektów losowych z wykorzystaniem deklaracji macierzy:_
| Group Var | 1240396.526 | 147.330 |
|:---------:|:-----------:|:-------:|

<br />


## 2. Czas wykonania
Dla wszystkich badanych możliwości obydwu języków czasy wykonania zebrano dla każdego przypadku 100-krotnie, by zminimalizować ryzyko obciążenia średniej pomiarami odstającymi. Na tej podstawie oszacowano średnią i odchylenie standardowe, co posłużyło do zaprojektowania analiz porównawczych. Szczegóły dot. metodyki: [https://github.com/kamilpytlak/LMM#metodyka](https://github.com/kamilpytlak/LMM#metodyka).

### 2.1 Python
*Szczegółowe analizy dot. czasu wykonywania w języku Python: [https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb)*

W pętli 100-krotnie zebrano czasy wykonywania konstrukcji modelu z deklaracją formuły i macierzy, a wartości zaokrąglono do 4 miejsc po przecinku i kolumnowo zapisano w pliku "times_cows.csv", w którym w 1. kolumnie znajdują się czasy wykonywania dla formuły, a w 2. - macierzy.

Ogląd 6. pierwszych czasów wykonywania:
| statsmodel_formula | statsmodel_matrix |
|:------------------:|:-----------------:|
|       0.5667       |       0.5457      |
|       0.6446       |       0.6686      |
|       0.6416       |       0.6027      |
|       0.6846       |       0.6176      |
|       0.6816       |       0.6696      |
|       0.6506       |       0.6196      |

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe. **Dla deklaracji modelu za pomocą formuły średnia wynosiła 0.6765 sek., odchylenie standardowe - 0.0744 sek. a dla macierzy - średnia: 0.6242 sek., a odchylenie standardowe - 0.0289 sek.** 

Rozrzut wartości wraz z ich zagęszczeniem i zaznaczeniem median zilustrowano:
![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka Python](https://i.imgur.com/0AkZ8po.png)

Ze względu na zbliżone do siebie średnie czasy oczekiwania obu deklaracji, postanowiono sprawdzić, czy różnią się one istotnie. Z racji tego, że rozkład czasów wykonywania metody "formula" nie jest normalny (p-value = 9.33e-08), wykonano test Wilcoxona. Założono hipotezę, że czasy nie różnią się przeciwko hipotezie, że czas wykonywania konstrukcji modelu metodą "formula" jest większy niż "matrix".

```r
wilcox.test(csv_data$statsmodel_formula, csv_data$statsmodel_matrix, alternative = "greater")
Wilcoxon rank sum test with continuity correction

data:  csv_data$statsmodel_formula and csv_data$statsmodel_matrix
W = 7313, p-value = 7.988e-09
alternative hypothesis: true location shift is greater than 0
```

**Zatem dla analizowanego zbioru danych o wielkości 1000 obserwacji z dwoma efektami stałymi i jednym losowym sposób konstrukcji za pomocą macierzy jest szybszy niż za pomocą formuły, co odrzuca hipotezę o równości obu średnich.**

Czasy zostały również jednostkowo zmierzone za pomocą "IPython Magic Commands" w Jupyter Notebook: %timeit, odpowiednio dla deklaracji formułą:
```python
Timer unit: 1e-07 s

Total time: 1.08764 s
File: <ipython-input-3-55d36bd81f50>
Function: LMM at line 1

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     1                                           def LMM(model):
     2         1        127.0    127.0      0.0      if model == "formula":
     3         1     326454.0 326454.0      3.0          LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
     4         1   10549835.0 10549835.0     97.0          LMMF_formula = LMM_formula.fit()
     5         1         28.0     28.0      0.0          return LMMF_formula
     6                                           
     7                                               elif model == "matrix":
     8                                                   LMM_matrix = sm.MixedLM(y, X, groups=groups)
     9                                                   LMMF_matrix = LMM_matrix.fit()
    10                                                   return LMMF_matrix
```

i macierzy:
```python
Timer unit: 1e-07 s

Total time: 0.973522 s
File: <ipython-input-3-55d36bd81f50>
Function: LMM at line 1

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     1                                           def LMM(model):
     2         1         27.0     27.0      0.0      if model == "formula":
     3                                                   LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
     4                                                   LMMF_formula = LMM_formula.fit()
     5                                                   return LMMF_formula
     6                                           
     7         1          9.0      9.0      0.0      elif model == "matrix":
     8         1     169398.0 169398.0      1.7          LMM_matrix = sm.MixedLM(y, X, groups=groups)
     9         1    9565761.0 9565761.0     98.3          LMMF_matrix = LMM_matrix.fit()
    10         1         26.0     26.0      0.0          return LMMF_matrix
```

**Aż 97% czasu w przypadku formuły i 98.3% w przypadku macierzy składa się na dopasowanie modelu**, czyli oszacowanie macierzy kowariancji i reszt, zatem **przypuszcza się, że proces ten jest wymagający nie tylko pod względem czasowym, ale możliwe, że także i pod względem używanej pamięci operacyjne**j, ponieważ na samo "teoretyczne" określenie modelu (tu za pomocą "smf.mixedlm()" i "sm.MixedLM()") zużywane jest tylko odpowiednio 3% i 1.7% całości czasu.

### 2.2 R
*Szczegółowe analizy dot. czasu wykonywania w języku R:* [https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/R.ipynb)

W pętli 100-krotnie zebrano czasy wykonywania konstrukcji modelu z wykorzystaniem odpowiednich funkcji z popularnych bibliotek "lme4" (funkcja "lmer()") i "nlme" (funkcja "lme()"), jak i przeznaczonej do pracy nad dużymi zbiorami danych funkcji "bam()" z biblioteki "mgcv", a wartości zaokrąglono do 4 miejsc po przecinku i kolumnowo zapisano w pliku “times_cows.csv”, w którym w 3. kolumnie znajdują się czasy wykonywania dla biblioteki "lme4", w 4. - "nlme", a w 5. - "mgcv".

Ogląd 6. pierwszych pomiarów:
|  lme4 |  nlme |   bam  |
|:-----:|:-----:|:------:|
| 0.034 | 0.054 | 1.8668 |
| 0.027 | 0.054 | 1.8926 |
| 0.026 | 0.053 | 1.9927 |
| 0.026 | 0.053 | 1.8993 |
| 0.029 | 0.053 | 1.9611 |
| 0.029 | 0.053 | 1.9931 |

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe. **Dla konstrukcji modelu za pomocą biblioteki "lme4" średnia wyniosła 0.0282 sek., odchylenie standardowe - 0.0025 sek., "nlme" - średnia: 0.0551 sek., odchylenie standardowe: 0.0041 sek., a "bam" - średnia: 2.0296 sek., odchylenie standardowe - 0.2192 sek.**

Rozrzut wartości wraz z ich zagęszczeniem i zaznaczeniem median zilustrowano:
![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/jECbdBd.png)

Wykres pudełkowy wyraźnie uwidacznia dużą rozbieżność pomiędzy czasami wykonywania bibliotek "lme4", "nlme" a "mgcv". Aby graficznie uchwycić różnice pomiędzy bibliotekami "lme4" a "nlme", które na tym wykresie nie dają się zauważyć, stworzono kolejny wykres, uwzględniający jedynie te biblioteki:
![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/8Q6lgcM.png)

Różnice dają się zauważyć - **czasy wykonywania dla biblioteki "lme4" wahają się pomiędzy 0.025 a 0.035 sek., natomiast dla biblioteki "nlme" - ok. 0.05 aż do ok. 0.075 sek.** 

## 3. RAM
### 3.1 Python
*Szczegółowe analizy dot. użytego RAMu w języku Python:* [https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/cows/Python.ipynb)


### 3.2 R
*Szczegółowe analizy dot. użytego RAMu w języku R:*


## 4. Wnioski
### 4.1 Interpretacja modeli - Python i R
Model został poddany interpretacji w oparciu o porównanie wartości estymatorów uzyskanych metodą REML: [https://github.com/kamilpytlak/LMM#mieszane-modele-liniowe-mlm---estymacja-parametr%C3%B3w-i-zastosowanie](https://github.com/kamilpytlak/LMM#mieszane-modele-liniowe-mlm---estymacja-parametr%C3%B3w-i-zastosowanie).

Różnica w rezultatach dostarczanych przez funkcje bibliotek "lme4" i "nlme" tkwi w sposobie przedstawienia istotności wyników - biblioteka "lme4" nie dostarcza wartości p, a samą wartość t z powodu trudności w szacunkach w konfrontacji z obecnym w modelu przynajmniej jednym efektem losowym. W tym celu wartość p można oszacować, wykorzystując różne metody, jak np. test ilorazu wiarygodności, test permutacyjny czy **test Walda**:
```r
coeff <- summary(LMM("lme4"))$coefficients
p_value <- round(2 * (1 - pnorm(abs(coeff[, 3]))), 3)

p_value
```
| (Intercept) | btn3a12 | lactation2 | lactation3 | lactation4 |
|:-----------:|:-------:|:----------:|:----------:|:----------:|
|    0.000    |  0.299  |    0.000   |    0.000   |    0.000   |

Uzyskane wartości p na podstawie testu Walda są zgodne z tymi uzyskanymi z użyciem bibliotek "nlme" i "mgcv", a jedyna różnica między nimi polega na sposobie zaokrąglania.

Język Python również zapewnia wartości p dla efektów stałych, a także (dodatkowo) przedziały ufności dla parametrów. Choć biblioteki w R nie zawierają tej funkcjonalności, przedziały te również można łatwo tam wyprowadzić:
```r
confint(LMM("lme4"))
```
|             |    2.5%   |   97.5%  |
|:-----------:|:---------:|:--------:|
|    .sig01   |  999.0391 | 1229.868 |
|    .sigma   | 1055.9859 | 1183.258 |
| (Intercept) | 6540.7547 | 6858.203 |
|   btn3a12   | -705.4760 |  216.140 |
|  lactation2 | 1141.4091 | 1473.172 |
|  lactation3 | 1599.8628 | 2000.412 |
|  lactation4 | 1320.4705 | 2015.552 |

Zarówno biblioteka "statsmodel" w języku Python (formuła + macierze), jak i wszystkie rozpatrywane biblioteki języka R dają porównywalne wyniki, w których różnica tkwi w resztach, co jest związane z zaokrąglaniem liczb. Rozpatrując uzyskane wyniki pod kątem statystycznym, można dojść do wniosku, że wszystkie z analizowanych parametrów (efekty stałe + jeden efekt losowy) mają istotny wpływ na produkcję mleczną osobników, z wyłączeniem parametru "btn3a1", którego wartość p jest większa niż 0.05. Wariancja efektu losowego oceniona została na ok. 12404, natomiast dostarczona poprzez funkcję "bam()" biblioteki "mgcv" w R wartość p wskazuje na istotne różnice pomiędzy krowami w produkcji mlecznej w badanej populacji (p-value < 2e-16).


### 4.2 Czas wykonania
#### 4.2.1 Python

#### 4.2.2 R

### 4.3 RAM
#### 4.3.1 Python

#### 4.3.2 R
