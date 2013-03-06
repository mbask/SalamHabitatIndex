---
title       : SalamHabIndex
subtitle    : Un framework per testare più indici di habitat di salamandre
author      : Marco Bascietto
job         : MANFOR
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
github:
  user: mbask
  repo: SalamHabIndex
---





# Introduzione

Per dare un senso a quello che dicevo in email, ho creato un *framework* per testare la bontà dei diversi indici di presenza di habitat di salamandre che si possono calcolare rimescolando le variae misure da prendere in campo (perimetro, diametro, numero delle concavità ecc.). Il *framework* è un semplice *script* R, lo stesso che ha prodotto questo breve *report*.

Per testare lo script ho disegnato il profilo di tre ceppaie afferenti a tre tipologie diverse per potenziale presenza di habitat (nessuno [`Cerchio`], qualche [`Carpino`], molti [`Carpinoso`] habitat):

![Ipotetica ceppaia senza habitat](data/Cerchio.jpg)
![Ipotetica ceppaia con habitat](data/Carpino.jpg)
![Ipotetica ceppaia con molti habitat](data/Carpinoso.jpg)

Il framework calcola tutti i potenziali indici per ciascuna tipologia, simula il conteggio delle salamandre trovate nelle 3 ceppaie e calcola la correlazione tra ciascuno degli indici e il conteggio di salamandre per ogni tipologia di ceppaia. Una migliore correlazione indica un indice più performante.
Per ora, gli indici considerati in questo test sono solo la dimensione frattale e l'indice proposto da Romano.


---

## Misure

Su ciascuno, con `ImageJ`, ho misurato la dimensione frattale (`Df`), perimetro (`B`), diametro massimo (`maxD`) ed ho imnposto che il diametro a 1.30m sia 100 (`dbh`), ossia il diametro della ceppaia a cerchio:


```
##       Forma    Df      B  maxD dbh
## 1 Carpinoso 1.279 2059.0 228.8 100
## 2   Carpino 1.185  761.8 206.8 100
## 3   Cerchio 1.111  330.7 100.0 100
```


---

## Simulazione
La simulazione siumula di trovare le salamandre in ciascuna tipologia di ceppaia. La simulazione del conteggio di salamandre avviene estraendo numeri da una distribuzione normale di media e deviazione standard prefissati, per ogni tipologia di ceppaia:


```
##       Forma sAvg sStd
## 1 Carpinoso    3  2.0
## 2   Carpino    2  1.0
## 3   Cerchio    0  0.5
```


Per esempio, il numero di salamandre trovate nella simulazione della ceppaia `Carpinoso` viene estratta da una $\mathcal{N}($3$,$2$)$ troncata a 0. Per 10 simulazioni di conteggio, potremmo avere i seguenti numeri per: 0, 4, 0, 3, 6, 4, 3, 2, 2, 5.

Per la ceppaia `Carpino`, invece, potremmo avere i seguenti conteggi: 0, 2, 3, 2, 2, 1, 1, 1, 2, 3

I parametri delle distribuzioni sono da definire secondo l'esperienza di Antonio.

---

# Valori degli indici

I valori dell'indice di A. Romano per le tre ceppaie, secondo l'equazione $\left(\frac{B}{C}/\frac{C}{T}\right)-1$ e la dimensione frattale sono:


```r
sIndexRaw <- within(sIndexRaw, {
    C <- maxD * pi
    T <- dbh * pi
    RIndex <- ((B/C)/(C/T)) - 1
})
print(sIndexRaw[, c("Forma", "RIndex", "Df")])
```

```
##       Forma   RIndex    Df
## 1 Carpinoso  0.25197 1.279
## 2   Carpino -0.43299 1.185
## 3   Cerchio  0.05265 1.111
```


Sembra strano il valore negativo per l'indice di Romano per il `Carpino`, se c'è un errore nel calcolo non riesco a trovarlo.

---

# Simulazione




Eseguo 10000 simulazioni, su 3 tipologie di ceppaie, ottengo 30000 dati di conteggi di salamandre (come se avessi campionato 10000 boschi di 3 alberi ciascuno). 

Per ogni simulazione (o bosco) ho a disposizione i 2 indici, per ciascuno dei quali calcolo la correlazione lineare (pearson) con il numero di salamandre.




La media e la deviazione standard sulle 10000 simulazioni della correlazione tra indice e numero salamandre, per ciascun indice sono le seguenti:

```
##       ind correlationAvg correlationStd
## 1:     Df       0.742965         0.3785
## 2: RIndex      -0.003177         0.5459
```


L'indice con `correlationAvg` maggiore è il migliore.

---
# Risultati

![plot of chunk Plot](figure/Plot.png) 


Grafico a violino della distribuzione delle 10000 correlazioni tra ciascun indice ed il numero di salamandre simulato. L'ampiezza del "violino" è proporzionale al numero di punti corrispondenti alla correlazione (sulle ordinate). Tanto più ampio è il violino verso valori di correlazione pari ad 1, tanto migliore è l'indice per descrivere il numero di salamandre nella ceppaia.


