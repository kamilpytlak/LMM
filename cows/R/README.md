   # <center>R: Użyte biblioteki i zastosowane metody</center>
   ------------
   
   
   ## Biblioteki
   W R główne funkcje do konstrukcji mieszanych modeli liniowych zostały zaimportowane z bibliotek lme4 i nlme - lmer() i lme(). Z biblioteki profmem użyto funkcję profmem() do sprawdzenia ilości zużytej na okreslony blok kodu pamięci RAM. Ostatnie dwie biblioteki (FSA i ggplot2) posłużyły do wykonania testów statystycznych (test Dunneta) i końcowej wizualizacji wyników.


```R
library(lme4)
library(nlme)
library(profmem)
library(FSA)
library(ggplot2)
```


## Funkcje
### > Funkcja tworząca modele MLM(model)
Konstrukcję modeli zaprojektowano w funkcjach, tak aby z danym argumentem tyczącym się zastosowanej biblioteki ("lme4" i "nlme") lub zastosowanej biblioteki i obliczeń istotności współczynników ("lme4\_effects") mogły dawać natychmiastowy rezultat i zostać użyte w ramach innych bloków programu. Każda z nich zawiera użytą funkcję do matematycznej konstrukcji modelu (lmer()/lme()) zaczerpniętą z analizowanych bibliotek, a także dodatkowe informacje, jak np. tabela współczynników zawierająca wartości t, które z kolei użyte zostały do "ręcznego" obliczenia wartości p niedostarczane przez bibliotekę, a niezbędne do oszacowania istotności. Te zostały obliczone w oparciu o asymptotyczne dążenie do rozkładu normalnego.


```R
MLM <- function(model) {
  if(model == "lme4") {
    MLM_lme4 <- lmer(data = data_set, milk~btn3a1+lactation+(1|cow.id))
    return(MLM_lme4)
  }
  else if(model == "lme4_effects") {
    MLM("lme4")
    coeff <- summary(MLM("lme4"))
    p_value <- round(2 * (1 - pnorm(abs(coeff$coefficients[, 3]))), 3)
    conf_int <- confint(MLM("lme4"))
    return(list("p_value" = p_value, "conf_int" = conf_int))
  }
  else if(model == "nlme") {
    MLM_nlme <- lme(data = data_set, milk~btn3a1+lactation, random =~1|cow.id)
    return(MLM_nlme)
  }
}
```

### > Funkcja sprawdzająca czas check_time(model, n)
Funkcja sprawdzająca czas wykonania określonego bloku (tu: konstrukcjimodeli lub konstrukcji modelu i istotności modelu) funkcjonuje w oparciu o po-miar czasu systemowego przed wywołaniem funkcji MLM() i po niej, co dajedwie różne wartości, które są kolejno od siebie odejmowane, zaś powstała róż-nica dodawana do zbiorczego wektora przechowującego czasy. Operacja ta jestpowtarzana n razy i również działa w oparciu o model przekazany w postacitekstowej do argumentu.


```R
check_time <- function(model, n) {
  times <- c()

  for(i in 1:n) {
    start_time <- Sys.time()
    MLM(model)
    end_time <- Sys.time()
    times <- c(times, round(end_time - start_time, 3))
    }
    return(times)
  }
```

### > Funkcja mierząca ilość zużytego RAMu check_RAM(model)
Funkcja profmem() z biblioteki memprof posłużyła do sprawdzenia ilości zużytej pamięci RAM przez określony blok programu (tu: konstrukcję modelu).Z racji tego, że funkcja ta weryfikuje ilość przydzielanej pamięci RAM dla każdejoperacji, wartości cząstkowe zostały zsumowane i przemnożone, tak by uzyskać wynik w jednostce MB (pierwotnym wynikiem jest ilość w bajtach).


```R
check_RAM <- function(model) {
  prof_mem <- profmem({
    MLM(model)
      })
  sum_in_MB <- round(sum(prof_mem$bytes[!is.na(prof_mem$bytes)]) * 1e-6, 3)
  return(sum_in_MB)
}
```
