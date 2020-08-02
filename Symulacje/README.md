
# Symulacje

1. [Opis zbiorów danych](#1-opis-zbiorów-danych)
2. [Czasy wykonania](#2-czasy-wykonania)
3. [RAM](#3-ram)
4. [Wnioski](#4-wnioski)<br />
<br />

## 1. Opis zbiorów danych
W języku R wygenerowano kilka zestawów zbiorów danych o następujących właściwościach:
 - od 10.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y", zmiennymi objaśniającymi "x_1", "x_2", "x_3" i dwiema zmiennymi grupującymi: "g_1" (100 grup) i g_2 (1.000 grup),
 -   od 10.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y",  zmienną objaśniającą "x_1" i jedną zmienną grupującą: "g_1" (1.000 grup),
 - od 20.000 do 1.000.000 obserwacji (co 10.000) ze zmienną objaśnianą "y", zmienną objaśniającą "x_1" i jedną zmienną grupującą: "g_1" (10.000 grup).
 - 1 zbiór danych z 1.000.000 obserwacji, ze zmienną objaśnianą "y", zmienną objaśniającą "x0" i jedną zmienną grupującą "fac" (4 grupy) z użyciem funkcji "gamSim()" z biblioteki "mgcv".

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
W przypadku jednego zbioru milionowego czasy zebrano w sposób analogiczny, co dla zbioru "cows", tzn. ze 100 powtórzeniami i oszacowaniem średniej arytmetycznej i odchylenia standardowego do 4 miejsc po przecinku. Zostały one zebrane do pliku "LMM_times_BIG_1e6_gam": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/LMM_times_BIG_1e6_gam.csv)<br />
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

Z pomocą wbudowanego w program RStudio profilera czasu i przeznaczanej pamięci RAM dodatkowo sprawdzono stos wywołań każdej funkcji, w szczególności doszukiwano się wywołań o przeważającym czasie wykonania. Rezultaty dostępne są w formie aplikacji w RPubs: [https://rpubs.com/kamilpytlak/LMM_1e6](https://rpubs.com/kamilpytlak/LMM_1e6)<br />

*Dla funkcji "lmer()" z biblioteki "lme4"*

**Wywołaniem o najdłuższym czasie trwania (3.12 sek.) jest "mkReTrms", czyli aplikacja do modelu formuły, tworzenie macierzy i ich transpozycja. Stosunkowo dużo czasu (1.87 sek.) wykorzystuje też wywołanie "optimizeLmer", czyli optymalizator funkcji**. Jako parametr wykorzystuje on wygenerowaną w poprzednim stosie funkcję odchylenia ("devfun"). Właściwa optymalizacja jest przeprowadzona po wywołaniu funkcji "optwrap". Optymalizacja przeprowadzana jest na końcu działania funkcji konstruującej model i jej celem jest złożenie (a w końcowym etapie zwrócenie), czyli dopasowanie wcześniej oszacowanych macierzy parametrów, a także kontrola (jeśli zapewnia rezultat) pod względem istotności, tzn. sprawdzana jest kowariancja - jeśli wartość wariancji danego efektu losowego jest mniejsza od z góry ustalonej wartości domyślnej (1e-4), to takie dopasowanie jest nieistotne ("singular fit"), w związku z czym wówczas zwrócone zostanie ostrzeżenie. Weryfikowana jest także macierz Hessego, ze szczególnym naciskiem wielkość wartości własnych.<br />

*Dla funkcji "lme()" z biblioteki "nlme"*

**Funkcja "lme()" większość czasu poświęca na tworzenie macierzy projektowej ("model.matrix"), na co zużywa 1.28 sek., a także na wykonanie określonej liczby iteracji algorytmu EM (Expectation–Maximization) dla efektów losowych modelu mieszanego (1.89 sek.)**. Algorytm ten służy do znajdowania maximum lokalnego funkcji (metodą największej wiarygodności) w celu oszacowania parametrów. Nazwa wywodzie się z naprzemiennego oczekiwania na utworzenie funkcji oczekiwania (E) oraz jej maksymalizacji w celu oszacowania parametrów (M).<br />

*Dla funkcji "bam()" z biblioteki "mgcv"*

Podejście funkcji "bam()" do estymacji parametrów mieszanego modelu liniowego różni się diametralnie od stosowanych przez dwie pozostałe biblioteki metod. Przede wszystkim, biblioteka "mgcv" została stworzona z myślą o pracy nad dużymi zbiorami danych, stąd też domyślną metodą estymacji parametrów dla funkcji "bam()" jest nie REML, a fREML. **W przypadku analizowanego zbioru danych funkcja ta na całość wykonania zużyła najmniej czasu - 2.8 sek.**




### 2.2 Dla symulacji-kombinacji
Bazując na wielu różnych kombinacjach liczby obserwacji-grup sztucznie wygenerowanych zbiorów, sprawdzano wpływ liczby obserwacji, liczby grup, a także liczby obserwacji i liczby grup w interakcji na czas konstrukcji modelu w zależności od danej deklaracji/biblioteki. Same kombinacje zostały zaprojektowane w taki sposób, by każdy z zestawów uwzględniał od 10.000 do 1.000.000 obserwacji (z przeskokiem co 10.000) i zawierał po: 100, 1000 i 10000 grup, przykładowo:
| Liczba_obserwacji | Liczba_grup | Czas_wykonania | Biblioteka |   RAM   |
|:-----------------:|:-----------:|:--------------:|:----------:|:-------:|
|       10000       |      100     |     0.0959     |    lme4    | 14.3831 |
|       20000       |      100     |     0.1580     |    lme4    | 28.6927 |
|       30000       |      100     |     0.2302     |    lme4    | 41.8227 |
|       40000       |      100     |     0.3035     |    lme4    | 57.3128 |
|       50000       |      100     |     0.3971     |    lme4    | 70.4420 |
|       60000       |      100     |     0.4710     |    lme4    | 83.5720 |

W tej części analizy skupione były wokół modelu liniowego. Rozpatrywany model liniowy z interakcjami można przedstawić następującym równaniem:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas} = B_{L. obserwacji} * X_{L. obserwacji} %2B B_{L. grup} * X_{L. grup} %2B B_{L.obserwacji:L.grup} * X_{L. obserwacji} * X_{L. grup}">

#### 2.2.1 Python
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i deklaracji formułą:**
```python
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "statsmodel_formula"))

Residuals:
    Min      1Q  Median      3Q     Max 
-16.536  -2.353  -0.865   2.843  23.364 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   1.091e+00  6.355e-01   1.716   0.0872 .  
Liczba_obserwacji             1.134e-05  1.093e-06  10.380  < 2e-16 ***
Liczba_grup                   6.288e-03  1.110e-04  56.659  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 7.805e-10  1.902e-10   4.104 5.25e-05 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 4.207 on 295 degrees of freedom
Multiple R-squared:  0.9812,	Adjusted R-squared:  0.981 
F-statistic:  5121 on 3 and 295 DF,  p-value: < 2.2e-16
```

Wszystkie badane czynniki mają dodatni wpływ na czas wykonywania modelu - zarówno liczba obserwacji, liczba grup, jak i przyrost liczby obserwacji i grup łącznie (jako interakcja). Odnosząc się do zamieszczonego wyżej ogólnego modelu liniowego, w tym przypadku zależność przedstawia się następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (formula)} = 0.00001134 * X_{L. obserwacji} %2B  0.006288 * X_{L. grup} %2B 0.0000000007805 * X_{L. obserwacji} * X_{L. grup}">

Dla minimalnego wzrostu liczby obserwacji czy grup (rzędów jedności) różnice wydają się minimalne. Jednak już dla wielkoskalowych danych, różnice są już znaczące, np. dla 100.000 obserwacji (bez uwzględniania grup) jest to przyrost o 1.134 sekundy. **Uwzględniając grupy, czas wzrasta intensywniej -  już nawet dla 1.000 grup (bez uwzględniania obserwacji) następuje przyrost o 6.288 sekund.**

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i metody "matrix":**
```python
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "statsmodel_matrix"))

Residuals:
     Min       1Q   Median       3Q      Max 
-16.9052  -2.3877  -0.9878   2.8087  21.0182 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   1.210e+00  5.931e-01   2.041 0.042164 *  
Liczba_obserwacji             1.108e-05  1.020e-06  10.870  < 2e-16 ***
Liczba_grup                   6.309e-03  1.046e-04  60.318  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 6.653e-10  1.786e-10   3.725 0.000234 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 3.927 on 294 degrees of freedom
Multiple R-squared:  0.9833,	Adjusted R-squared:  0.9831 
F-statistic:  5775 on 3 and 294 DF,  p-value: < 2.2e-16
```

W przypadku macierzy również wszystkie badane komponenty mają dodatni wpływ na czas wykonywania modelu - zarówno liczba obserwacji, liczba grup, jak i przyrost liczby obserwacji i grup łącznie (jako interakcja). Odnosząc się do zamieszczonego wyżej ogólnego modelu liniowego tu rozpatrywanego, w tym przypadku zależność przedstawia się następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (matrix)} = 0.00001108 * X_{L. obserwacji} %2B  0.006309 * X_{L. grup} %2B 0.0000000006653 * X_{L. obserwacji} * X_{L. grup} %2B 1.21">

Średni czas konstrukcji modeli z wykorzystaniem macierzy jest mniejszy od średniego czasu z wykorzystaniem formuły o ok. 2.3%. Oznacza to, że dla zbioru danych z 1.000.000 obserwacjami szacunkowo w przypadku formuły operacja zajmie 10.1 sekundy, a dla macierzy - 10.33 sekund, więc ta różnica wydaje się minimalna i nieznacząca. Inaczej jednak jest, mając na uwadze liczbę grup - tu wraz ze wzrostem liczby grup o 1000, czas wykonywania wzrasta o 6.309 sekund, natomiast dla formuły byłoby to 6.288 sekund. **Dlatego w analizowanym przypadku jednego efektu stałego i losowego dla dwóch możliwych deklaracji modelu w języku Python, to nie liczba obserwacji, a liczba grup ma znaczący wpływ na rezultat.**

![Wykres zależności czasu wykonania od liczby obserwacji, grup i deklaracji dla języka Python](https://i.imgur.com/jxYjlqC.png)


![Wykres 3D zależności liczby obserwacji i liczby grup na czas wykonania dla języka Python](https://i.imgur.com/KQ8VaDU.png)

Tę specyfikę potwierdza także skonstruowany trójwymiarowy wykres trzech zmiennych. Posuwając się wzdłuż osi x ("liczba obserwacji") i rozważając wyniki osi y ("czas wykonania"), zauważa się nieznaczący wzrost. Gwałtownie wznoszący się "płaszcz" zaczyna się natomiast tworzyć po uwzględnieniu dodatkowej osi z ("liczba grup"). Ważnej uwadze zasługuje również kant "płaszcza" - przy obserwacjach wynoszących do ok. 500.000 czasy wykonywania są dość jednostajne, a gwałtowny ich wzrost odnotowuje się przy liczbie obserwacji wynoszącej od ok. 800.000 (co jest widoczne na wykresie w postaci zawiniętego ku górze prawego brzegu). **Jednorodny, fioletowy kolor płaszczyzny wskazuje na minimalnie mniejszy czas wykonywania w przypadku deklaracji modelu za pomocą macierzy** - niebieska płaszczyzna (dla deklaracji formułą) nie jest w ogóle widoczna, ponieważ jest ona "zamaskowana" przez różową od dołu.

#### 2.2.2 R
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "lme4":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "lme4"))

Residuals:
    Min      1Q  Median      3Q     Max 
-3.2180 -1.3020 -0.1934  1.0040  5.0440 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -1.562e+00  2.668e-01  -5.855 1.27e-08 ***
Liczba_obserwacji              1.730e-05  4.587e-07  37.722  < 2e-16 ***
Liczba_grup                    9.146e-05  4.660e-05   1.963  0.05062 .  
Liczba_obserwacji:Liczba_grup  2.512e-10  7.985e-11   3.145  0.00183 ** 
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 1.767 on 295 degrees of freedom
Multiple R-squared:  0.9029,	Adjusted R-squared:  0.9019 
F-statistic: 913.9 on 3 and 295 DF,  p-value: < 2.2e-16
```

**Dla funkcji "lme()" z biblioteki "lme4" zarówno liczba obserwacji, grup i liczba obserwacji w interakcji z liczbą grup dodatnio korelują z czasem wykonywania**. Powołując się na wcześniej zamieszczony ogólny model liniowy, w tym przypadku przedstawia się on następująco:

<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (lme4)} = 0.00001730 * X_{L. obserwacji} %2B 0.00009146 * X_{L. grup} %2B 0.0000000002512 * X_{L. obserwacji} * X_{L. grup} - 1.562">

Dla małej liczby obserwacji wzrost czasu wykonania jest niemal analogiczny do tego z języka Python.  Różnica jednak pojawia się z napływem coraz to większej liczby obserwacji. ***Dla języka Python konstrukcja modelu dla sztucznego zbioru danych z 1.000.000 obserwacji i 10.000 grup wyniosła ok. 100 sekund, a dla tego samego zbioru, ale dla biblioteki "lme4" - ok. 20 sekund.**


**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "nlme":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "nlme"))

Residuals:
    Min      1Q  Median      3Q     Max 
-2.2727 -0.5543 -0.0667  0.2279  4.7246 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -3.618e-01  1.465e-01  -2.470   0.0141 *  
Liczba_obserwacji              1.236e-05  2.518e-07  49.068  < 2e-16 ***
Liczba_grup                    1.799e-04  2.558e-05   7.033 1.41e-11 ***
Liczba_obserwacji:Liczba_grup  2.179e-11  4.383e-11   0.497   0.6194    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.9698 on 295 degrees of freedom
Multiple R-squared:  0.936,	Adjusted R-squared:  0.9354 
F-statistic:  1439 on 3 and 295 DF,  p-value: < 2.2e-16
```

W przypadku funkcji "lme()" z biblioteki "nlme" również zauważa się dodatnią korelację między liczbą obserwacji a czasem wykonania, a dodatkowo również liczbą grup. Można to przedstawić następującym równaniem:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (nlme)} = 0.00001236 * X_{L. obserwacji} %2B 0.0001799 * X_{L. grup} - 3.618">

**Przyrównując do biblioteki "lme4", czas wykonania modelu przy stałej liczbie obserwacji dla obu bibliotek różni się o ok. 40%**. Przewaga biblioteki "lme4" wydaje się widoczna dla małych zbiorów danych - ze względnie niskim stosunkiem liczby grup do liczby obserwacji. **Niemniej, z racji tego, że obserwacji w zbiorze jest na ogół o wiele więcej niż grup, różnice pomiędzy współczynnikami parametru liczby grup każą przypuszczać, że czas wykonywania funkcji z biblioteki "nlme" jest niższy niż "lme4".**


**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "mgcv":**
```r
Call:
lm(formula = Czas_wykonania ~ Liczba_obserwacji * Liczba_grup, 
    data = interakcje %>% filter(Biblioteka == "mgcv"))

Residuals:
    Min      1Q  Median      3Q     Max 
-0.5535 -0.2597  0.0067  0.2071  3.4227 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                    1.658e+00  5.497e-02  30.154  < 2e-16 ***
Liczba_obserwacji              2.590e-06  9.450e-08  27.413  < 2e-16 ***
Liczba_grup                    5.936e-05  9.600e-06   6.183 2.09e-09 ***
Liczba_obserwacji:Liczba_grup -7.902e-11  1.645e-11  -4.804 2.48e-06 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.3639 on 295 degrees of freedom
Multiple R-squared:  0.7779,	Adjusted R-squared:  0.7756 
F-statistic: 344.3 on 3 and 295 DF,  p-value: < 2.2e-16
```

Funkcja "bam()" z biblioteki "mgcv" funkcjonuje względnie wolno dla małych zbiorów danych (tu wynikł istotny wyraz wolny), aczkolwiek dla dużych zbiorów danych jest ona o wiele efektywniejsza od wszystkich analizowanych tu deklaracji/bibliotek. Można to zapisać następującym równaniem:<br /><br /> 
<img src="https://render.githubusercontent.com/render/math?math=y_{Czas (bam)} = 0.000002590 * X_{L. obserwacji} %2B 0.00005936 * X_{L. grup} %2B -0.00000000007902 * X_{L. obserwacji:L. grup} %2B 1.658">

Dla porównania, **przy tej samej liczbie obserwacji (nie uwzględniając grup), czas wykonania jest ok. 5-krotnie mniejszy od bibliotek "lme4" i "nlme".** Uwzględniając grupy (bez uwzględniania liczby obserwacji), czas konstrukcji z użyciem tej biblioteki w porównaniu z biblioteką "nlme" jest ponad 3-krotnie mniejszy.

![enter image description here](https://i.imgur.com/QBGza5P.png)

**Dla danych wielkoskalowych efektywna wydaje się biblioteka "mgcv"** - wzrost czasu wykonywania spowodowany wzrostem liczby obserwacji lub grup jest wprawdzie istotny, ale małoznaczący dla obliczeń.

![Wykres 3D zależności liczby obserwacji i liczby grup na czas wykonania dla języka R](https://i.imgur.com/Kwh1fHL.png)

**Czas konstrukcji modelu z użyciem funkcji "bam()" z biblioteki "mgcv" już od ok. 300.000 obserwacji jest znacznie niższy niż w przypadku funkcji "lmer()" czy "lme()"**. Po uwzględnieniu grup (przy stałej liczbie obserwacji dla pozostałych bibliotek) czas wykonywania także nie wzrasta szybciej w porównaniu z "lme4" czy "nlme". Dla niewielkich zbiorów danych (mniejszych od 200.000) czas działania jest większy w porównaniu z dwiema pozostałymi bibliotekami.


## 3. RAM
Dla jednego zbioru milionowego użyty w celu wykonania konstrukcji RAM został oszacowany jednokrotnie - zarówno dla języka Python, jak i R - z użyciem odpowiednich profilerów.
Dla wielu różnych kombinacji-zestawów, podobnie jak z czasami wykonywania, wartości użytego RAM-u zebrane zostały do pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv). W R zsumowane zostały wartości przeznaczane na każdy proces, a wynikła suma (w bajtach) została zamieniona na megabajty. W języku Python wartości te pierwotnie są podawane w mebibajtach, dlatego w celu ujednolicenia również zostały zamienione na megabajty.

### 3.1 Dla zbioru milionowego
#### 3.1.1 Python
*Szczegółowe analizy dot. użytego RAM-u w języku Python*: [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/Python.ipynb)

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

![Wykres zależności użytego RAM-u od czasu w języku Python - "formula"](https://i.imgur.com/2NSlowT.png)

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

![Wykres zależności użytego RAM-u od czasu w języku Python - "matrix"](https://i.imgur.com/4Rp1fCy.png)

Choć w tym przypadku deklaracją z użyciem macierzy zużywa mniej pamięci, to w **obu metodach najwięcej RAM-u przeznaczanego jest na ogólną konstrukcję modelu, natomiast pozostała część (ok. 20%) na oszacowanie macierzy kowariancji, czyli jego dopasowanie.**


#### 3.1.2 R
*Szczegółowe analizy dot. użytego RAM-u w języku R:* [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb](https://github.com/kamilpytlak/LMM/blob/master/Symulacje/1.000.000%20obserwacji/R.ipynb)

Dla każdej z trzech bibliotek RAM oszacowany został z użyciem biblioteki "profmem". Wyniki przedstawiają się następująco:

| lme4_RAM | nlme_RAM |  bam_RAM |
|:--------:|:--------:|:--------:|
| 1356.555 |  1425.31 | 1006.423 |


### 3.2 Dla symulacji-kombinacji
Podobnie jak z czasem wykonywania, aby oszacować zależność pomiędzy liczbą obserwacji, grup, a także liczbą obserwacji i grup w charakterze interakcji, zebrane zostały wartości (w MB) użytego RAM-u na podstawie wielu różnych kombinacji. Dane zostały zgromadzone w pliku "Interakcje": [https://github.com/kamilpytlak/LMM/blob/master/Symulacje/Kombinacje/Interakcje.csv].

Rozpatrywany model liniowy z interakcjami można przedstawić następującym równaniem:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec} = B_{L. obserwacji} * X_{L. obserwacji} %2B B_{L. grup} * X_{L. grup} %2B B_{L.obserwacji:L.grup} * X_{L. obserwacji} * X_{L. grup}">

#### 3.2.1 Python
**Model liniowy zależności użytego RAM-u od liczby obserwacji, liczby grup i deklaracji formułą:**
```python
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "statsmodel_formula"))

Residuals:
    Min      1Q  Median      3Q     Max 
-63.580  -1.795   0.352   2.737  36.188 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -5.454e+00  1.295e+00  -4.213 3.35e-05 ***
Liczba_obserwacji              1.263e-04  2.226e-06  56.740  < 2e-16 ***
Liczba_grup                    1.652e-03  2.261e-04   7.305 2.61e-12 ***
Liczba_obserwacji:Liczba_grup -5.871e-09  3.874e-10 -15.154  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 8.571 on 295 degrees of freedom
Multiple R-squared:  0.9324,	Adjusted R-squared:  0.9317 
F-statistic:  1357 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (formula)} = 0.0001263 * X_{L. obserwacji} %2B 0.001652 * X_{L. grup} - 0.000000005871 * X_{L. obserwacji} * X_{L. grup} - 5.454">

**Dla deklaracji formułą wszystkie badane czynniki miały istotny wpływ na użytą pamięć operacyjną**. **Największy wpływ ma liczba grup**: już z 1.000 obserwacji (bez uwzględniania obserwacji) alokacja RAM-u wzrasta o 1.652 sekundy. Z kolei wzrost liczby obserwacji o 100.000 (bez uwzględniania grup) powoduje przyrost zużytego RAM-u o 12.63 sekundy.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i deklaracji macierzą:**
```python
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "statsmodel_matrix"))

Residuals:
    Min      1Q  Median      3Q     Max 
-27.914  -4.801   1.694   7.132  21.044 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   -1.263e+01  1.522e+00  -8.296 3.93e-15 ***
Liczba_obserwacji              9.995e-05  2.617e-06  38.197  < 2e-16 ***
Liczba_grup                    7.433e-04  2.684e-04   2.769  0.00597 ** 
Liczba_obserwacji:Liczba_grup -3.730e-09  4.584e-10  -8.138 1.14e-14 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 10.08 on 294 degrees of freedom
Multiple R-squared:  0.8689,	Adjusted R-squared:  0.8675 
F-statistic: 649.4 on 3 and 294 DF,  p-value: < 2.2e-16
```

Co można zapisać:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (macierz)} = 0.00009995 * X_{L. obserwacji} %2B 0.0007433 * X_{L. grup} -0.00000000373 * X_{L. obserwacji} * X_{L. grup} - 0.1263">

Dla deklaracji macierzą również wszystkie badane czynniki mają istotny wpływ na zużycie RAM-u. **W zależności od liczby obserwacji, zużycie jest o ok. 30% mniejsze niż w przypadku deklaracji formułą.** 

![Wykres zależności zużytego RAM-u od liczby obserwacji i grup dla języka Python](https://i.imgur.com/X1gvrRE.png)

**Na uwagę zasługuje fakt, że zużyty RAM w przypadku formuły nie przekracza 140 MB, natomiast w przypadku macierzy - 100 MB**.  Jest to ok. 10-krotnie mniejsza ilość w porównaniu z bibliotekami języka R, uwzględniając przy tym, że Python, oprócz zapewnienia wartości p, szacuje również przedziały ufności. Także trzeba zaznaczyć, że dla obu modeli wynikła istotna, ujemna zależność pomiędzy zużyciem RAM-u a liczbą obserwacji i grup w charakterze interakcji. 

![Wykres 3D zależności zużytego RAM-u od liczby obserwacji, grup i deklaracji dla języka Python](https://i.imgur.com/lzU2SJV.png)


#### 3.2.2 R
**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki "lme4":**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "lme4"))

Residuals:
    Min      1Q  Median      3Q     Max 
-36.102  -6.676  -2.375   6.055  24.631 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   5.703e+00  1.526e+00   3.738 0.000223 ***
Liczba_obserwacji             1.399e-03  2.623e-06 533.384  < 2e-16 ***
Liczba_grup                   1.168e-03  2.665e-04   4.384 1.62e-05 ***
Liczba_obserwacji:Liczba_grup 3.645e-10  4.566e-10   0.798 0.425347    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 10.1 on 295 degrees of freedom
Multiple R-squared:  0.9994,	Adjusted R-squared:  0.9994 
F-statistic: 1.586e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (lme4)} = 0.001399 * X_{L. obserwacji} %2B 0.00168 * X_{L. grup} %2B 5.703">

Wraz ze wzrostem liczby obserwacji o 100.000, zużycie RAM-u wzrasta o 139.9 MB - w przypadku takiej samej ilości, ale dla grup, jest to 168 MB.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki “nlme”:**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "nlme"))

Residuals:
    Min      1Q  Median      3Q     Max 
-38.766  -8.801  -3.332   8.231  47.085 

Coefficients:
                               Estimate Std. Error t value Pr(>|t|)    
(Intercept)                   6.905e+00  1.956e+00   3.529 0.000483 ***
Liczba_obserwacji             1.538e-03  3.363e-06 457.412  < 2e-16 ***
Liczba_grup                   4.822e-03  3.417e-04  14.112  < 2e-16 ***
Liczba_obserwacji:Liczba_grup 5.259e-11  5.854e-10   0.090 0.928489    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 12.95 on 295 degrees of freedom
Multiple R-squared:  0.9992,	Adjusted R-squared:  0.9992 
F-statistic: 1.168e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (nlme)} = 0.001538 * X_{L. obserwacji} %2B 0.004822 * X_{L. grup} %2B 6.905">

Co daje ok. 10%-owe większe zużycie dla takiej samej liczby obserwacji w zestawieniu z "lme4" i ok. 3-krotne większe zużycie dla takiej samej liczby grup.

**Model liniowy zależności czasu wykonania od liczby obserwacji, liczby grup i biblioteki “mgcv”:**
```r
Call:
lm(formula = RAM ~ Liczba_obserwacji * Liczba_grup, data = interakcje %>% 
    filter(Biblioteka == "mgcv"))

Residuals:
    Min      1Q  Median      3Q     Max 
-32.296  -1.396  -0.428   1.436   5.554 

Coefficients:
                                Estimate Std. Error  t value Pr(>|t|)    
(Intercept)                    4.622e+00  4.390e-01   10.528   <2e-16 ***
Liczba_obserwacji              9.893e-04  7.547e-07 1310.813   <2e-16 ***
Liczba_grup                    2.377e-05  7.667e-05    0.310    0.757    
Liczba_obserwacji:Liczba_grup -3.598e-13  1.314e-10   -0.003    0.998    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 2.907 on 295 degrees of freedom
Multiple R-squared:  0.9999,	Adjusted R-squared:  0.9999 
F-statistic: 9.559e+05 on 3 and 295 DF,  p-value: < 2.2e-16
```

Co można zapisać jako:<br /><br />
<img src="https://render.githubusercontent.com/render/math?math=y_{Pamiec (mgcv)} = 0.0009893 * X_{L. obserwacji} %2B 4.622">

**Tylko liczba obserwacji ma wpływ na zużycie RAM-u przez funkcję "bam()"**. Ponadto, zużycie to jest mniejsze o ok. 40% dla takiej samej liczby obserwacji w porównaniu z biblioteką "lme4" i ok. 60% mniejsze w porównaniu z "nlme".

![Zależność użytego RAM-u od liczby obserwacji, grup i biblioteki dla języka R](https://i.imgur.com/8hyjClv.png)



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
