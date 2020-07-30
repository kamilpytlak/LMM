
# Zbiór danych "cows"

1. [Opis zbioru danych](#opis-zbioru-danych)
2. [Czas wykonania](#czas-wykonania)
3. [RAM](#ram)
4. [Rezultaty](#rezultaty)

<br />

## Opis zbioru danych
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

<br />

Następujący model można zatem przedstawić w formie równania:
<br />
<img src="https://render.githubusercontent.com/render/math?math=y_{milk} = X_{btn3a1} * \beta_{btn3a1} %2B X_{lactation} * \beta_{lactation} %2B Z_{cow.id} * u_{cow.id} %2B \epsilon">
<br />
, w którym:
- <img src="https://render.githubusercontent.com/render/math?math=y_{milk}"> jest wektorem obserwacji ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(y_{milk}) = X_{btn3a1} * \beta_{btn3a1} %2B X_{lactation} * \beta_{lactation}"> (zmienna zależna)
 - <img src="https://render.githubusercontent.com/render/math?math=X_{btn3a1}">, <img src="https://render.githubusercontent.com/render/math?math=X_{lactation}"> i <img src="https://render.githubusercontent.com/render/math?math=Z_{cow.id}"> znanymi macierzami projektowymi składającymi się z realizacji opisywanych czynników,
 -  <img src="https://render.githubusercontent.com/render/math?math=\beta_{btn3a1}"> i <img src="https://render.githubusercontent.com/render/math?math=\beta_{lactation}"> nieznanymi wektorami efektów stałych,
 - <img src="https://render.githubusercontent.com/render/math?math=u_{cow.id}"> nieznanym wektorem efektów losowych ze średnią <img src="https://render.githubusercontent.com/render/math?math=E(u) = 0">,
 - <img src="https://render.githubusercontent.com/render/math?math=\epsilon"> nieznanym wektorem zakłóceń losowych o średniej <img src="https://render.githubusercontent.com/render/math?math=E({\epsilon}) = 0">.

<br />

### Interpretacja
Model został poddany interpretacji w oparciu o porównanie wartości estymatorów uzyskanych metodą REML i ML w przypadku mieszanych modeli liniowych. Jak wyjaśniono w [https://github.com/kamilpytlak/LMM#mieszane-modele-liniowe-mlm---estymacja-parametr%C3%B3w-i-zastosowanie](https://github.com/kamilpytlak/LMM#mieszane-modele-liniowe-mlm---estymacja-parametr%C3%B3w-i-zastosowanie), estymacja metodą ograniczonej największej wiarygodności (REML - restricted maximum likelihood) zapewnia estymatory mniej obciążone niż w przypadku metody największej wiarygodności (ML - maximum likelihood), gdyż przekształcając dane nie dokonuje szacunków na pełnym zestawie parametrów, w związku z czym głównie przeznaczona jest do szacunków komponentów wariancyjnych. Uzyskane oceny efektów stałych (z wykorzystaniem zarówno metody REML, jak i ML) zestawiono z ocenami standardowego liniowego modelu.

<br />

#### Estymacja metodą REML
*Szczegółowe analizy dot. estymacji metodą REML:* 

<br />

**Efekty stałe**

<br />

**R**
Oceny estymatorów z wykorzystaniem metody ograniczonej największej wiarygodności przedstawiają się następująco:
|             |  model_lm (LM) | model_lme4 (REML) | model_nlme (REML) | model_mgcv (REML) |
|:-----------:|:---------:|:----------:|:----------:|:---------:|
| (Intercept) | 7770.0000 |  6699.4479 |  6699.4479 | 6699.4479 |
|   btn3a12   | -328.6617 |  -244.0844 |  -244.0844 | -244.0844 |
|  lactation2 | 1253.3234 |  1307.0367 |  1307.0367 | 1307.0367 |
|  lactation3 | 1856.3374 |  1800.5379 |  1800.5379 | 1800.5379 |
|  lactation4 | 1856.6869 |  1669.2723 |  1669.2723 | 1669.2723 |

W pierwszej kolumnie zebrane zostały efekty stałe - wyraz wolny, ortolog genu i poziomy laktacji, natomiast w następnych kolejno: klasyczny model liniowy, model mieszany z użyciem biblioteki lme4, model mieszany z użyciem biblioteki nlme, model mieszany z użyciem biblioteki mgcv.

&nbsp;

*Oceny efektów stałych z wykorzystaniem biblioteki "lme4":*
|             | Estimate | Std. Error | t value |
|-------------|----------|------------|---------|
| (Intercept) | 6699.45  | 81.08      | 82.632  |
| btn3a12     | -244.08  | 235.12     | -1.038  |
| lactation2  | 1307.04  | 84.63      | 15.443  |
| lactation3  | 1800.54  | 102.10     | 17.635  |
| lactation4  | 1669.27  | 176.45     | 9.460   |

&nbsp;

*Oceny efektów stałych z wykorzystaniem biblioteki "nlme":*
|             |   Value  | Std.Error |  DF |  t-value | p-value |
|:-----------:|:--------:|:---------:|:---:|:--------:|:-------:|
| (Intercept) | 6699.448 |  81.07595 | 588 | 82.63175 |  0.0000 |
|   btn3a12   | -244.084 | 235.11696 | 407 | -1.03814 |  0.2998 |
|  lactation2 | 1307.037 |  84.63442 | 588 | 15.44332 |  0.0000 |
|  lactation3 | 1800.538 | 102.10172 | 588 | 17.63475 |  0.0000 |
|  lactation4 | 1669.272 | 176.44866 | 588 |  9.46038 |  0.0000 |

&nbsp;

*Oceny efektów stałych z wykorzystaniem biblioteki "mgcv":*
|             | Estimate | Std.Error | t-value | Pr(>\|t\|) |     |
|:-----------:|:--------:|:---------:|:-------:|:----------:|:---:|
| (Intercept) |  6699.45 |   81.08   |  82.632 |   <2e-16   | *** |
|   btn3a12   |  -244.08 |   235.12  |  -1.038 |     0.3    |     |
|  lactation2 |  1307.04 |   84.63   |  15.443 |   <2e-16   | *** |
|  lactation3 |  1800.54 |   102.10  |  17.635 |   <2e-16   | *** |
|  lactation4 |  1669.27 |   176.45  |  9.460  |   <2e-16   | *** |

&nbsp;

**Python**
&nbsp;

_Oceny efektów stałych z wykorzystaniem deklaracji formuły:_
|                |   Coef.  | Std.Err. |    z   | P>\|z\| |  [0.025  |  0.975]  |
|:--------------:|:--------:|:--------:|:------:|:-------:|:--------:|:--------:|
|    Intercept   | 6699.448 |  81.078  | 82.630 |  0.000  | 6540.538 | 6858.358 |
|   btn3a1[T.2]  | -244.084 |  235.197 | -1.038 |  0.299  | -705.062 |  216.894 |
| lactation[T.2] | 1307.037 |  84.712  | 15.429 |  0.000  | 1141.005 | 1473.069 |
| lactation[T.3] | 1800.538 |  102.283 | 17.604 |  0.000  | 1600.067 | 2001.008 |
| lactation[T.4] | 1669.271 |  177.492 |  9.405 |  0.000  | 1321.394 | 2017.148 |

&nbsp;

_Oceny efektów stałych z wykorzystaniem deklaracji macierzy:_
|                |   Coef.  | Std.Err. |    z   | P>\|z\| |  [0.025  |  0.975]  |
|:--------------:|:--------:|:--------:|:------:|:-------:|:--------:|:--------:|
|    Intercept   | 6699.448 |  81.078  | 82.630 |  0.000  | 6540.538 | 6858.358 |
|   btn3a1[T.2]  | -244.084 |  235.197 | -1.038 |  0.299  | -705.062 |  216.894 |
| lactation[T.2] | 1307.037 |  84.712  | 15.429 |  0.000  | 1141.005 | 1473.069 |
| lactation[T.3] | 1800.538 |  102.283 | 17.604 |  0.000  | 1600.067 | 2001.008 |
| lactation[T.4] | 1669.271 |  177.492 |  9.405 |  0.000  | 1321.394 | 2017.148 |

&nbsp;


**Efekty losowe**
&nbsp;

**R**

&nbsp;

_Oceny efektów losowych z wykorzystaniem biblioteki “lme4”:_
|  Groups  |     Name    | Variance | Std.Dev. |
|:--------:|:-----------:|:--------:|:--------:|
|  cow.id  | (Intercept) |  1240403 |   1114   |
| Residual |             |  1252911 |   1119   |

&nbsp;

_Oceny efektów losowych z wykorzystaniem biblioteki “nlme”:_

Random effects:
 Formula: ~1 | cow.id
|         | (Intercept) | Residual |
|:-------:|:-----------:|:--------:|
| StdDev: |   1113.734  | 1119.335 |

&nbsp;

_Oceny efektów losowych z wykorzystaniem biblioteki “mgcv”:_
|           |  edf  | Ref.df |   F   | p-value |     |
|:---------:|:-----:|:------:|:-----:|:-------:|:---:|
| s(cow.id) | 277.4 |   407  | 2.351 |  <2e-16 | *** |

&nbsp;

**Python**
&nbsp;

_Oceny efektów losowych z wykorzystaniem deklaracji formuły:_
| Group Var | 1240396.526 | 147.330 |
|:---------:|:-----------:|:-------:|

&nbsp;

_Oceny efektów losowych z wykorzystaniem deklaracji macierzy:_
| Group Var | 1240396.526 | 147.330 |
|:---------:|:-----------:|:-------:|

&nbsp;

#### Estymacja metodą ML
*Szczegółowe analizy dot. estymacji metodą ML:*

&nbsp;

**R**
&nbsp;

Oceny estymatorów z wykorzystaniem metody największej wiarygodności przedstawiają się następująco: 
|             |  model_lm (LM) | model_lme4 (LM) | model_nlme (LM) | model_mgcv (LM) |
|:-----------:|:---------:|:----------:|:----------:|:---------:|
| (Intercept) | 7770.0000 |  6699.4450 |  6699.4450 | 6699.4450 |
|   btn3a12   | -328.6617 |  -244.0528 |  -244.0527 | -244.0528 |
|  lactation2 | 1253.3234 |  1307.0553 |  1307.0553 | 1307.0553 |
|  lactation3 | 1856.3374 |  1800.5067 |  1800.5067 | 1800.5067 |
|  lactation4 | 1856.6869 |  1669.1736 |  1669.1736 | 1669.1736 |

&nbsp;

## Czas wykonania

&nbsp;

### Python

&nbsp;

### R

&nbsp;


## RAM

### Python


### R



## Rezultaty

### Interpretacja modeli
Metoda REML zapewnia estymatory mniej obciążone niż metoda ML w przypadku mieszanych modeli liniowych - zestawiając wyniki efektów stałych dla mieszanych modeli liniowych z użyciem REML i ML, można zauważyć, że te uzyskane za pomocą ML mają większe obciążenie. Różnica w rezultatach dawanych przez funkcje bibliotek "lme4" i "nlme" tkwi sposobie przedstawienia istotności wyników - biblioteka "lme4" nie zapewnia p-value, a samą t-value z powodu trudności w oszacowaniu w obecności efektów losowych. W tym celu wartość p można oszacować, wykorzystując różne metody, jak np. test ilorazu wiarygodności, test permutacyjny czy test Walda, korzystając z dostarczanej przez rezultat modelu wartości t i aproksymacji do rozkładu normalnego:
```r
coeff <- summary(LMM("lme4"))$coefficients
p_value <- round(2 * (1 - pnorm(abs(coeff[, 3]))), 3)

p_value
```
Co daje następujące wartości:
| (Intercept) | btn3a12 | lactation2 | lactation3 | lactation4 |
|:-----------:|:-------:|:----------:|:----------:|:----------:|
|    0.000    |  0.299  |    0.000   |    0.000   |    0.000   |

Są one zgodne z tymi uzyskanymi z użyciem bibliotek "nlme" i "mgcv", a jedyna różnica między nimi polega na sposobie zaokrąglania liczb.

Język Python również zapewnia wartości p dla efektów stałych, a także (dodatkowo) przedziały ufności dla parametrów. Choć biblioteki w R nie zapewniają tej funkcjonalności, przedziały te również można łatwo wyprowadzić:
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


### Python

#### Czas wykonania
#### RAM

&nbsp;

### R

#### Czas wykonania
#### RAM
