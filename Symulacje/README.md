
# Symulacje

1. [Opis zbiorów danych](#1-opis-zbiorow-danych)
2. [Czasy wykonania](#2-czasy-wykonania)
3. [RAM](#3-ram)
4. [Wnioski](#4-wnioski)<br />
<br />

## 1. Opis zbiorów danych
W języku R wygenerowano kilka zestawów zbiorów danych o następujących właściwościach:
 - od 10.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y", zmiennymi objaśniającymi "x_1", "x_2", "x_3" i dwiema zmiennymi grupującymi: "g_1" (25 grup) i g_2 (1.000 grup),
 -   od 10.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y",  zmienną objaśniającą "x_1" i jedną zmienną grupującą: "g_1" (1.000 grup),
 - od 20.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y", zmienną objaśniającą "x_1" i jedną zmienną grupującą: "g_1" (10.000 grup).
 - 1 zbiór danych z 1.000.000 obserwacji, ze zmienną objaśnianą "y", zmienną objaśniającą "x0" i jedną zmienną grupującą "fac" (4 grupy), używając funkcji "gamSim()" z biblioteki "mgcv".

**Tak stworzone zestawy posłużyły do określenia wpływu objętości zbioru (liczby obserwacji), liczby grup, liczby parametrów, a także interakcji pomiędzy poszczególnymi czynnikami na czas wykonania modelu i użytą pamięć operacyjną.**

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


## 2. Czasy wykonania
W przypadku jednego zbioru milionowego czasy zebrano w sposób analogiczny, co dla zbioru "cows", tzn. ze 100 powtórzeniami i oszacowaniem średniej arytmetycznej i odchylenia standardowego do 4 miejsc po przecinku. Zostały one zebrane do pliku "LMM_times_BIG_1e6_gam": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv)
Czasy dla wielu różnych kombinacji zestawów (z różnymi połączeniami liczby obserwacji i grup) zostały z kolei zebrane do pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv). Dla danej obserwacji złożonej z pary liczba obserwacji-liczba grup obliczono czas wykonania jako średnia z 3 niezależnych od siebie pomiarów cząstkowych.

### 2.1 Dla jednego zbioru milionowego

#### 2.1.1 Python
*Szczegółowe analizy dot. czasu wykonywania w języku Python*: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb)

Ogląd 6. pierwszych czasów wykonywania:
| statsmodel_formula | statsmodel_matrix |
|:------------------:|:-----------------:|
|       1.5731       |       1.5631      |
|       1.6571       |        1.83       |
|       1.9209       |       1.713       |
|        1.755       |       1.5611      |
|       1.9279       |       1.4991      |
|       1.8369       |       1.4582      |

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe:
 - **dla deklaracji modelu za pomocą formuły średnia wynosiła 1.8467 sek., odchylenie standardowe - 0.2006 sek**,
 - **dla macierzy - średnia: 1.6672 sek., a odchylenie standardowe - 0.2507 sek**.

![Wykres rozrzutu czasów wykonywania w zależności od deklaracji w języku Python](https://i.imgur.com/xMT8pNY.png)

Ze względu na zbliżone do siebie średnie czasy wykonywania obu deklaracji, postanowiono sprawdzić, czy różnią się one istotnie od siebie. Z racji tego, że rozkład czasów wykonywania obu metod nie jest normalny (na poziomie 0.05), wykonano test Wilcoxona. **Założono hipotezę, że czasy nie różnią się przeciwko hipotezie, że czas wykonywania konstrukcji modelu metodą "formula" jest większy niż "matrix"**.

```r
wilcox.test(csv_data$statsmodel_formula, csv_data$statsmodel_matrix, alternative="greater")

Wilcoxon rank sum test with continuity correction

data:  csv_data$statsmodel_formula and csv_data$statsmodel_matrix
W = 8265.5, p-value = 7.45e-16
alternative hypothesis: true location shift is greater than 0
```

**Zatem dla analizowanego zbioru danych, o wielkości 1.000.000 obserwacji z jednym efektem stałym i jednym losowym, sposób konstrukcji za pomocą macierzy jest szybszy niż za pomocą formuły, co odrzuca hipotezę o równości obu średnich.**

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

**Aż 71.5% czasu w przypadku formuły i 70.5% w przypadku macierzy składa się na ogólną konstrukcję modelu**, a 28.5% dla formuły i 29.5% dla macierzy na dopasowanie, czyli oszacowanie macierzy kowariancji i reszt. Występuje tu odwrotna proporcja niż w przypadku zbioru "cows" - ma to jednak swoje uzasadnienie: tam czasy wykonywania były rzędu dziesiątek sekund. Oznacza to, że czas dopasowania modelu rośnie nie szybciej niż przeznaczany czas na jego ogólną projekcję.

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

Bazując na pomiarach, oszacowano przeciętną wartość i odchylenie standardowe:
 - **"lme4" - średnia: 9.7396 sek., odchylenie standardowe - 0.5314 sek.**, 
 - **"nlme" - średnia: 11.4261 sek., odchylenie standardowe: 0.621 sek.**,
 - **"mgcv" - średnia: 3.9247 sek., odchylenie standardowe - 0.2585 sek.**.

![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/VhxUHNJ.png)

Wykres pudełkowy wyraźnie uwidacznia dużą rozbieżność pomiędzy czasami wykonywania bibliotek "lme4", "nlme" a "mgcv".

![Wykres rozrzutu czasów wykonywania modelu w zależności od jego konstrukcji dla języka R](https://i.imgur.com/Dqn13R4.png)

Z pomocą wbudowanego w program RStudio profilera czasu i przeznaczanej pamięci RAM dodatkowo sprawdzono stos wywołań każdej funkcji, w szczególności doszukiwano wywołań o przeważającym czasie wykonania. Rezultaty dostępne są w formie aplikacji w RPubs: [https://rpubs.com/kamilpytlak/LMM_1e6](https://rpubs.com/kamilpytlak/LMM_1e6)

*Dla funkcji "lmer()" z biblioteki "lme4"*

**Wywołaniem o najdłuższym czasie trwania (3.12 sek.) jest "mkReTrms", czyli aplikacja do modelu formuły, tworzenie macierzy i ich transpozycja. Stosunkowo dużo czasu (1.87 sek.) wykorzystuje też wywołanie "optimizeLmer", czyli optymalizator funkcji**. Jako parametr wykorzystuje on wygenerowaną w poprzednim stosie funkcję odchylenia ("devfun"). Właściwa optymalizacja jest przeprowadzona po wywołaniu funkcji "optwrap". Optymalizacja przeprowadzana jest na końcu działania funkcji konstruującej model i jej celem jest złożenie (a w końcowym etapie zwrócenie), czyli dopasowanie wcześniej oszacowanych macierzy parametrów, a także kontrola (jeśli zapewnia rezultat) pod względem istotności, tzn. sprawdzana jest kowariancja - jeśli wartość wariancji danego efektu losowego jest mniejsza od z góry ustalonej wartości domyślnej (1e-4), to takie dopasowanie jest nieistotne ("singular fit"), w związku z czym wówczas zwrócone zostanie ostrzeżenie. Weryfikowana jest także macierz Hessego, ze szczególnym naciskiem wielkość wartości własnych.

*Dla funkcji "lme()" z biblioteki "nlme"*

**Funkcja "lme()" większość czasu poświęca na tworzenie macierzy projektowej ("model.matrix"), na co zużywa 1.28 sek., a także na wykonanie określonej liczby iteracji algorytmu EM (Expectation–Maximization) dla efektów losowych modelu mieszanego (1.89 sek.)**. Algorytm ten służy do znajdowania maximum lokalnego funkcji (metodą największej wiarygodności) w celu oszacowania parametrów. Nazwa wywodzie się z naprzemiennego oczekiwania na utworzenie funkcji oczekiwania (E) oraz jej maksymalizacji w celu oszacowania parametrów (M).

*Dla funkcji "bam()" z biblioteki "mgcv"*

Podejście funkcji "bam()" do estymacji parametrów mieszanego modelu liniowego różni się diametralnie od stosowanych przez dwie pozostałe biblioteki metod. Przede wszystkim, biblioteka "mgcv" została stworzona z myślą o pracy nad dużymi zbiorami danych, stąd też domyślną metodą estymacji parametrów dla funkcji "bam()" jest nie REML, a fREML. **W przypadku analizowanego zbioru danych funkcja ta na całość wykonania zużyła najmniej czasu - 2.8 sek**.




### 2.2 Dla symulacji-kombinacji
Bazując na wielu różnych kombinacjach liczby obserwacji-grup sztucznie wygenerowanych zbiorów, sprawdzano wpływ liczby obserwacji, liczby grup, a także liczby obserwacji i liczby grup w interakcji na czas konstrukcji modelu w zależności od danej deklaracji/biblioteki. Same kombinacje zostały zaprojektowane w taki sposób, by każdy z zestawów uwzględniał od 10.000 do 1.000.000 obserwacji (z przeskokiem co 10.000) i zawierał po: 25, 1000 i 10000 grup, przykładowo:
| Liczba_obserwacji | Liczba_grup | Czas_wykonania | Biblioteka |   RAM   |
|:-----------------:|:-----------:|:--------------:|:----------:|:-------:|
|       10000       |      25     |     0.0959     |    lme4    | 14.3831 |
|       20000       |      25     |     0.1580     |    lme4    | 28.6927 |
|       30000       |      25     |     0.2302     |    lme4    | 41.8227 |
|       40000       |      25     |     0.3035     |    lme4    | 57.3128 |
|       50000       |      25     |     0.3971     |    lme4    | 70.4420 |
|       60000       |      25     |     0.4710     |    lme4    | 83.5720 |

W tej części analizy skupione były wokół modelu liniowego. Rozpatrywany model liniowy z interakcjami można przedstawić następującym równaniem:<br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas} = B_{L. obserwacji} * X_{L. obserwacji} %2B B_{L. grup} * X_{L. grup} %2B B_{L.obserwacji:L.grup} * X_{L. obserwacji} * X_{L. grup}">

#### 2.2.1 Python
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i deklaracji formułą:**
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

Wszystkie badane czynniki mają dodatni wpływ na czas wykonywania modelu - zarówno liczba obserwacji, liczba grup, jak i przyrost liczby obserwacji i grup łącznie (jako interakcja). Odnosząc się do zamieszczonego wyżej ogólnego modelu liniowego, w tym przypadku zależność przedstawia się następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (formula)} = 0.0000101 * X_{L. obserwacji} %2B  0.006286 * X_{L. grup} %2B 0.0000000009173 * X_{L. obserwacji} * X_{L. grup}">

Dla minimalnego wzrostu liczby obserwacji czy grup (rzędów jedności) różnice wydają się być minimalne. Jednak już dla wielkoskalowych danych, różnice są już znaczące, np. dla 100.000 obserwacji (bez uwzględniania grup) jest to przyrost o 1.01 sekundy. **Uwzględniając grupy, czas wzrasta intensywniej, bowiem już nawet dla 1.000 grup (bez uwzględniania obserwacji) następuje przyrost o 6.286 sekund.**

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

![Wykres zależności czasu wykonania od liczby obserwacji, grup i deklaracji dla języka Python](https://i.imgur.com/cxAguA6.png)


![Wykres 3D zależności liczby obserwacji i liczby grup na czas wykonania dla języka Python](https://i.imgur.com/DJGvzuQ.png)

Tę specyfikę potwierdza także skonstruowany trójwymiarowy wykres trzech zmiennych. Posuwając się wzdłuż osi x ("liczba obserwacji") i rozważając wyniki osi y ("czas wykonania"), zauważa się nieznaczący wzrost. Gwałtownie wznoszący się "płaszcz" zaczyna się natomiast tworzyć po uwzględnieniu dodatkowej osi z ("liczba grup"). Ważnej uwadze zasługuje również kant "płaszcza" - przy obserwacjach wynoszących do ok. 500.000 czasy wykonywania są dość jednostajne, a gwałtowny ich wzrost odnotowuje się przy liczbie obserwacji wynoszącej od ok. 800.000 (co jest widoczne na wykresie w postaci zawiniętego ku górze prawego brzegu). **Jednorodny, fioletowy kolor płaszczyzny wskazuje na minimalnie mniejszy czas wykonywania w przypadku deklaracji modelu za pomocą macierzy** - niebieska płaszczyzna (dla deklaracji formułą) nie jest w ogóle widoczna, ponieważ jest ona "zamaskowana" przez różową.

#### 2.2.2 R
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "lme4":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "lme4"))

Residuals:
    Min      1Q  Median      3Q     Max 
-4.9053 -1.3867 -0.1070  0.9754  6.3093 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -1.199e+00  3.354e-01  -3.574  0.00041 ***
Liczba_obserwacji              1.520e-05  5.766e-07  26.370  < 2e-16 ***
Liczba_grup                    5.126e-05  5.857e-05   0.875  0.38220    
Liczba_obserwacji:Liczba_grup  4.824e-10  1.004e-10   4.807 2.45e-06 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 2.231 on 295 degrees of freedom
Multiple R-squared:  0.8413,	Adjusted R-squared:  0.8397 
F-statistic: 521.4 on 3 and 295 DF,  p-value: < 2.2e-16
```

**Dla funkcji "lme()" z biblioteki "lme4" zarówno liczba obserwacji i liczba obserwacji w interakcji z liczbą grup dodatnio korelują z czasem wykonywania**. Nie stwierdzono jednak zależności pomiędzy samymi grupami a czasem wykonywania. Powołując się na wcześniej zamieszczony ogólny model liniowy, w tym przypadku przedstawia się on następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (lme4)} = 0.00001520 * X_{L. obserwacji} %2B 0.0000000004824 * X_{L. obserwacji} * X_{L. grup} - 1.199">

Dla małej liczby obserwacji wzrost czasu wykonania jest niemal analogiczny do tego z języka Python.  Różnica jednak pojawia się z napływem coraz to większej liczby obserwacji. ***Dla języka Python konstrukcja modelu dla sztucznego zbioru danych z 1.000.000 obserwacji i 10.000 grup wyniosła ok. 100 sekund, a dla tego samego zbioru, ale dla biblioteki "lme4" - ok. 20 sekund.**


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

W przypadku funkcji "lme()" z biblioteki "nlme" również zauważa się dodatnią korelację między liczbą obserwacji, a czasem wykonania, a dodatkowo również liczbą grup. Można to przedstawić następującym równaniem:<br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (nlme)} = 0.00001213 * X_{L. obserwacji} %2B 0.0001632 * X_{L. grup}">

**Przyrównując do biblioteki "lme4", czas wykonania modelu przy stałej liczbie obserwacji dla obu bibliotek różni się o ok. 25%**. Z racji tego, że obserwacji w zbiorze jest zazwyczaj o wiele więcej niż grup, to właśnie biblioteka "nlme" jest szybsza od "lme4", nawet przy złożonośći w wysokości ok. 10.000 grup.


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

Funkcja "bam()" z biblioteki "mgcv" funkcjonuje względnie wolno dla małych zbiorów danych (tu wynikł istotny wyraz wolny), aczkolwiek dla dużych zbiorów danych jest ona o wiele efektywniejsza od wszystkich analizowanych tu deklaracji/bibliotek. Można to zapisać następującym równaniem:<br /> 
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (bam)} = 0.000002334 * X_{L. obserwacji} %2B 0.00007872 * X_{L. grup} %2B -0.00000000005103 * X_{L. obserwacji} * X_{L. grup} %2B 1.478">

Dla porównania, **przy tej samej liczbie obserwacji (nie uwzględniając grup), czas wykonania jest ponad 5-krotnie mniejszy od bibliotek "lme4" i "nlme".** Uwzględniając grupy (bez uwzględniania liczby obserwacji), czas konstrukcji z użyciem tej biblioteki w porównaniu z biblioteką "lme4" jest ponad 6-krotnie mniejszy, natomiast w porównaniu z "nlme" - 2-krotnie.

![enter image description here](https://i.imgur.com/2qdbof7.png)

**Dla danych wielkoskalowych efektywna wydaje się być biblioteka "mgcv"** - wzrost czasu wykonywania spowodowany wzrostem liczby obserwacji i/lub grup jest wprawdzie istotny, ale małoznaczący dla obliczeń.

![Wykres 3D zależności liczby obserwacji i liczby grup na czas wykonania dla języka R](https://i.imgur.com/C8Jo9RR.png)

**Nad dużymi zbiorami danych "bam()" faktycznie ma przewagę (różowa płaszczyzna) - już przy ok. 300.000 obserwacji (przy niewielkiej liczbie grup) czas wykonywania jest znacznie niższy niż w przypadku funkcji "lmer()" czy "lme()"**. Po uwzględnieniu grup (przy stałej liczbie obserwacji dla pozostałych bibliotek) czas wykonywania także nie wzrasta szybciej w porównaniu z "lme4" czy "nlme".


## 3. RAM
Dla jednego zbioru milionowego użyty w celu wykonania konstrukcji RAM został oszacowany jednokrotnie - zarówno dla języka Python, jak i R - z użyciem odpowiednich profilerów.
Dla wielu różnych kombinacji-zestawów, podobnie jak z czasami wykonywania, wartości użytego RAMu zebrane zostały do pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv). W R zsumowane zostały wartości przeznaczane na każdy proces, a wynikła suma (w bajtach) została zamieniona na megabajty. W języku Python wartości te pierwotnie są podawane w mebibajtach, dlatego w celu ujednolicenia również zostały zamienione na megabajty.

### 3.1 Dla zbioru milionowego
#### 3.1.1 Python
*Szczegółowe analizy dot. użytego RAMu w języku Python*: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb)

Na początku RAM oszacowano z wykorzystaniem jednej z "IPython Magic Commands" w Jupyter Notebook:
```python
%memit function
```

**Dla deklaracji za pomocą formuły użyta została pamięć w wysokości 137.9 MB (131.51 MiB), a dla macierzy - 113.5 MB (108.24 MiB).**

Z użyciem biblioteki "memory_profiler" w programie PyCharm szczegółowo sprawdzono również, na jaką dokładnie instrukcję procesor wymaga najwięcej pamięci. Dla formuły wynik przedstawia się następująco:
```python
22    150.7 MiB    150.7 MiB   @profile
23                             def LMM(model):
24    150.7 MiB      0.0 MiB       if model == "formula":
25    267.0 MiB    116.3 MiB           LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
26    293.9 MiB     26.9 MiB           LMMF_formula = LMM_formula.fit()
27    293.9 MiB      0.0 MiB           return LMMF_formula
28                             
29                                 elif model == "matrix":
30                                     LMM_matrix = sm.MixedLM(y, X, groups=groups)
31                                     LMMF_matrix = LMM_matrix.fit()
32                                     return LMMF_matrix
```

![Wykres zależności użytego RAMu od czasu w języku Python - "formula"](https://i.imgur.com/2NSlowT.png)

Natomiast dla macierzy:
```python
22    150.7 MiB    150.7 MiB   @profile
23                             def LMM(model):
24    150.7 MiB      0.0 MiB       if model == "formula":
25                                     LMM_formula = smf.mixedlm(formula, data_set, groups=groups)
26                                     LMMF_formula = LMM_formula.fit()
27                                     return LMMF_formula
28                             
29    150.7 MiB      0.0 MiB       elif model == "matrix":
30    229.0 MiB     78.2 MiB           LMM_matrix = sm.MixedLM(y, X, groups=groups)
31    255.8 MiB     26.9 MiB           LMMF_matrix = LMM_matrix.fit()
32    255.8 MiB      0.0 MiB           return LMMF_matrix
```

![Wykres zależności użytego RAMu od czasu w języku Python - "matrix"](https://i.imgur.com/4Rp1fCy.png)

Choć w tym przypadku deklaracją z użyciem macierzy zużywa mniej pamięci, to w **obu metodach najwięcej RAMu przeznaczanego jest na ogólną konstrukcję modelu, natomiast pozostała część (ok. 20%) na oszacowanie macierzy kowariancji, czyli jego dopasowanie.**


#### 3.1.2 R
*Szczegółowe analizy dot. użytego RAMu w języku R:* [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb)

Dla każdej z trzech bibliotek RAM oszacowany został z użyciem biblioteki "profmem". Wyniki przedstawiają się następująco:

| lme4_RAM | nlme_RAM |  bam_RAM |
|:--------:|:--------:|:--------:|
| 1356.555 |  1425.31 | 1006.423 |


### 3.2 Dla symulacji-kombinacji
Podobnie jak z czasem wykonywania, aby oszacować zależność pomiędzy liczbą obserwacji, grup, a także liczbą obserwacji i grup w charakterze interakcji, zebrane zostały wartości (w MB) użytego RAMu na podstawie wielu różnych kombinacji. Dane zostały zgromadzone w pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv].

Rozpatrywany model liniowy z interakcjami można przedstawić następującym równaniem:<br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec} = B_{L. obserwacji} * X_{L. obserwacji} %2B B_{L. grup} * X_{L. grup} %2B B_{L.obserwacji:L.grup} * X_{L. obserwacji} * X_{L. grup}">

#### 3.2.1 Python
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i deklaracji formułą:**
```python
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "statsmodel_formula"))

Residuals:
    Min      1Q  Median      3Q     Max 
-35.287  -5.634   0.068   5.354  36.619 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -3.222e+00  1.154e+00  -2.791  0.00559 ** 
Liczba_obserwacji              1.344e-04  1.984e-06  67.723  < 2e-16 ***
Liczba_grup                    1.377e-03  2.016e-04   6.833 4.78e-11 ***
Liczba_obserwacji:Liczba_grup -6.673e-09  3.454e-10 -19.319  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 7.677 on 295 degrees of freedom
Multiple R-squared:  0.9522,	Adjusted R-squared:  0.9517 
F-statistic:  1957 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać:
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (formula)} = 0.0001344 * X_{L. obserwacji} %2B 0.001377 * X_{L. grup} %2B -0.000000006673 * X_{L. obserwacji} * X_{L. grup} - 3.222">

**Dla deklaracji formułą wszystkie badane czynniki mają istotny wpływ na użytą pamięć operacyjną**. **Największy wpływ ma liczba grup**: już z 1.000 obserwacji (bez uwzględniania obserwacji) czas wykonywania wzrasta o 1.377 sekundy. Z kolei wzrost liczby obserwacji o 100.000 (bez uwzględniania grup) powoduje przyrost czasu wykonywania o 13.44 sekundy.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i deklaracji macierzą:**
```python
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "statsmodel_matrix"))

Residuals:
    Min      1Q  Median      3Q     Max 
-29.758  -6.739   2.411   9.095  21.387 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -8.093e+00  1.762e+00  -4.593 6.49e-06 ***
Liczba_obserwacji              9.860e-05  3.045e-06  32.376  < 2e-16 ***
Liczba_grup                    2.446e-04  3.102e-04   0.788    0.431    
Liczba_obserwacji:Liczba_grup -3.583e-09  5.308e-10  -6.751 7.89e-11 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 11.68 on 293 degrees of freedom
Multiple R-squared:  0.8319,	Adjusted R-squared:  0.8301 
F-statistic: 483.2 on 3 and 293 DF,  p-value: < 2.2e-16
```

Co można zapisać:
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (macierz)} = 0.0000986 * X_{L. obserwacji} %2B -0.000000003583 * X_{L. obserwacji} * X_{L. grup} - 8.093">

Dla deklaracji macierzą liczba grup nie ma istotnego wpływu na przyrost czasu wykonywania modelu. Sam przyrost czasu wykonywania w zależności od liczby obserwacji jest o ok. 33% mniejszy niż w przypadku deklaracji formułą. 

![Wykres zależności zużytego RAMu od liczby obserwacji i grup dla języka Python](https://i.imgur.com/X1gvrRE.png)

**Na uwagę zasługuje fakt, że zużyty RAM w przypadku formuły nie przekracza 140 MB, natomiast w przypadku macierzy - 100 MB**.  Jest to ok. 10-krotnie mniejsza ilość w porównaniu z bibliotekami języka R, uwzględniając przy tym, że Python, oprócz zapewnienia wartości p, szacuje również przedziały ufności.

![Wykres 3D zależności zużytego RAMu od liczby obserwacji, grup i deklaracji dla języka Python](https://i.imgur.com/yKmYGXW.png)


#### 3.2.2 R
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "lme4":**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "lme4"))

Residuals:
    Min      1Q  Median      3Q     Max 
-36.100  -6.667  -2.347   6.049  24.632 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   5.631e+00  1.519e+00   3.706 0.000252 ***
Liczba_obserwacji             1.399e-03  2.612e-06 535.681  < 2e-16 ***
Liczba_grup                   1.176e-03  2.654e-04   4.432 1.32e-05 ***
Liczba_obserwacji:Liczba_grup 3.528e-10  4.547e-10   0.776 0.438395    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 10.11 on 295 degrees of freedom
Multiple R-squared:  0.9994,	Adjusted R-squared:  0.9994 
F-statistic: 1.585e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (lme4)} = 0.001399 * X_{L. obserwacji} %2B 0.001176 * X_{L. grup} %2B 5.631">

Wraz ze wzrostem liczby obserwacji o 100.000, zużycie RAMu wzrasta o 139.9 MB - w przypadku takiej samej ilości, ale dla grup, byłoby to 117.6 MB.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki “nlme”:**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "nlme"))

Residuals:
    Min      1Q  Median      3Q     Max 
-38.646  -8.824  -3.175   8.263  47.076 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   6.709e+00  1.934e+00   3.469 0.000601 ***
Liczba_obserwacji             1.539e-03  3.325e-06 462.726  < 2e-16 ***
Liczba_grup                   4.843e-03  3.378e-04  14.338  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 4.231e-11  5.788e-10   0.073 0.941767    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 12.86 on 295 degrees of freedom
Multiple R-squared:  0.9992,	Adjusted R-squared:  0.9992 
F-statistic: 1.184e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (nlme)} = 0.001539 * X_{L. obserwacji} %2B 0.004843 * X_{L. grup} %2B 6.709">

Co daje ok. 10%-owe większe zużycie dla takiej samej liczby obserwacji w zestawieniu z "lme4" i ponad 4-krotne większe zużycie dla takiej samej liczby grup.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki “mgcv”:**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "mgcv"))

Residuals:
    Min      1Q  Median      3Q     Max 
-32.343  -1.393  -0.419   1.404   5.588 

Coefficients:
                                Estimate Std. Error  t value Pr(>|t|)    
(Intercept)                    4.540e+00  4.387e-01   10.347   <2e-16 ***
Liczba_obserwacji              9.895e-04  7.542e-07 1311.937   <2e-16 ***
Liczba_grup                    3.275e-05  7.662e-05    0.427    0.669    
Liczba_obserwacji:Liczba_grup -2.226e-11  1.313e-10   -0.170    0.865    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 2.918 on 295 degrees of freedom
Multiple R-squared:  0.9999,	Adjusted R-squared:  0.9999 
F-statistic: 9.486e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (mgcv)} = 0.0009895 * X_{L. obserwacji} %2B 4.54">

**Tylko liczba obserwacji ma wpływ na zużycie RAMu przez funkcję "bam()"**. Ponadto, zużycie to jest mniejsze o ok. 40% dla takiej samej liczby obserwacji w porównaniu z biblioteką "lme4".

![Zależność użytego RAMu od liczby obserwacji, grup i biblioteki dla języka R](https://i.imgur.com/dIUHVSm.png)


## 4. Wnioski
Analizy przeprowadzone nad dużym (milionowym), a także wieloma innymi kombinacjami zbiorów danych dostarczają istotnych spostrzeżeń w zakresie podobieństwa i różnic dla określania tego samego modelu mieszanego, ale z użyciem różnych języków (tu: Python i R) i metod (tu: deklaracja z użyciem formuły i macierzy dla Python i biblioteki "lme4", "nlme" i "mgcv" w R). Trzeba podkreślić, że w dużej mierze nie są ważne liczby, ponieważ zależą one od mocy obliczeniowej komputera, w związku z czym na dwóch różnych maszynach mogą się one całkowicie różnić - z użyciem nawet tego samego bloku instrukcji i działań - **ale na największą uwagę zasługują proporcje między różnymi czynnikami, bo to one właśnie stanowią o możliwościach danej metody/biblioteki.** Ponadto, w niniejszej pracy nacisk położono na nierozbudowany model, tzn. z jednym efektem stałym i jednym losowym, ale z dokładnym zbadaniem wpływu liczby obserwacji i grup (jak i interakcji) na czas konstrukcji modelu i zużyty RAM. Choć nie zostało to szerzej opisane, to wykryto także istotny i duży wpływ liczby parametrów modelu (przede wszystkim efektów losowych) na czas wykonywania - nie zostało to dokładniej sprawdzone ze względu na długi czas oczekiwania konstrukcji jednego zbioru danych o kilkudziesięciu obserwacjach, wynoszącego kilkadziesiąt minut. Skrótowo:

 - **dla języka Python istotne znaczenie na czas wykonywania modelu ma liczba grup** - wraz ze stopniem złożoności grupowej zbioru, spodziewać się można długiego czasu oczekiwania (rzędu kilkudziesięciu, a nawet kilkunastu sekund - jeśli rozpatrywany byłby duży zbiór danych). **Deklaracja modelu za pomocą macierzy wydaje się mieć lepszy wpływ, jeśli chodzi o czas wykonania i przydzielaną pamięć operacyjną** - w obu przypadkach stwierdzono różnice dla obu metod, gdzie dla macierzy wartości te były mniejsze. **Python dostarcza także wartości p dla współczynników i przedziały ufności.**
 - dla języka R, podczas analizy względnie małych zbiorów danych (do 200.000) i niskiej złożoności grupowej (do 1.000) warto rozważyć wybór pomiędzy bibliotekami "lme4" a "nlme", ponieważ "mgcv", pomimo użycia fREML, stabilizuje swój czas wykonywania dla obserwacji wynoszących ok. powyżej 300.000.
