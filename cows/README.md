# Zbiór danych "cows"

1. [Opis zbioru danych](#opis-zbioru-danych)
2. [Czas wykonania](#czas-wykonania)
3. [RAM](#ram)
4. [Rezultaty](#rezultaty)

&nbsp;

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

Następujący model można zatem przedstawić w formie równania:

<center><img src="https://render.githubusercontent.com/render/math?math=y_{milk} = X_{btn3a1} * \beta_{btn3a1} %2B X_{lactation} * \beta_{lactation} %2B (1|cow.id) %2B \epsilon"></center>

, w którym:

- <img src="https://render.githubusercontent.com/render/math?math=y_{milk}"> jest zmienną zależną,
 - <img src="https://render.githubusercontent.com/render/math?math=X_{btn3a1}"> i <img src="https://render.githubusercontent.com/render/math?math=X_{lactation}"> zmiennymi niezależnymi,
 -  <img src="https://render.githubusercontent.com/render/math?math=\beta_{btn3a1}"> i <img src="https://render.githubusercontent.com/render/math?math=\beta_{lactation}"> estymowanymi współczynnikami kierunkowymi,
 -  <img src="https://render.githubusercontent.com/render/math?math=(1|cow.id)"> efektem losowym, 
 - <img src="https://render.githubusercontent.com/render/math?math=\epsilon"> zakłóceniem losowym.

&nbsp;

## Czas wykonania

&nbsp;

### Python

&nbsp;

### R

&nbsp;


## RAM

&nbsp;

### Python

&nbsp;

### R

&nbsp;


## Rezultaty

&nbsp;

### Python

&nbsp;

### R

&nbsp;

### Ogólne
