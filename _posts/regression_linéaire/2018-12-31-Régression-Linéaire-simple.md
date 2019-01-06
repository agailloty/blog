---
layout : post
category : R

---

<img src="{{site.url}}/_posts/regression_linéaire/linear_reg.png">


### Hello,

Je suis content de vous présenter mon tout premier article sur ce blog portant sur la **regression linéaire simple**. 

La régression linéaire simple est l'une des premières techniques qu'on apprend en économétrie et en machine learning. Appelé également la **méthode des moindres carrés ordinaires (MCO)**, cette technique permet d'expliquer une variable *y* à l'aide d'une autre variable explicative notée *X*. 


Dans cet exemple nous allons pas à pas utiliser R pour réaliser une régression linéaire simple. Le but de ce tutoriel est de vous donner tous les outils à la main afin de réaliser une regression linéaire simple.

Une attention particulière sera donnée aux méthodes économétriques de nettoyage de la base de données : tests sur les valeurs atypiques, test de linéarité, vérification ex ante et ex post.

Nous allons réaliser la regression linéaire sur sur base de donnée des présentant les caractéristiques des maisons ainsi que leurs prix. Nous voulons prédire le prix à partir de la superficie de la maison.


```R
# La base étant au format Excel, nous utilisons le package xlsx
library(xlsx)
```


```R
# Nous lisons le fichier et indiquons que la base se trouve sur la feuille 1.
maison <- read.xlsx('Maison.xlsx', sheetIndex = 1)
```


```R
# Voyons comment la base se présente.
head(maison)
```


<table>
<thead><tr><th scope=col>PRIX</th><th scope=col>SUPERFICIE</th><th scope=col>CHAMBRES</th><th scope=col>SDB</th><th scope=col>ETAGES</th><th scope=col>ALLEE</th><th scope=col>SALLEJEU</th><th scope=col>CAVE</th><th scope=col>GAZ</th><th scope=col>AIR</th><th scope=col>GARAGES</th><th scope=col>SITUATION</th></tr></thead>
<tbody>
	<tr><td>42000</td><td>5850 </td><td>3    </td><td>1    </td><td>2    </td><td>1    </td><td>0    </td><td>1    </td><td>0    </td><td>0    </td><td>1    </td><td>0    </td></tr>
	<tr><td>38500</td><td>4000 </td><td>2    </td><td>1    </td><td>1    </td><td>1    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td></tr>
	<tr><td>49500</td><td>3060 </td><td>3    </td><td>1    </td><td>1    </td><td>1    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td></tr>
	<tr><td>60500</td><td>6650 </td><td>3    </td><td>1    </td><td>2    </td><td>1    </td><td>1    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td></tr>
	<tr><td>61000</td><td>6360 </td><td>2    </td><td>1    </td><td>1    </td><td>1    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td><td>0    </td></tr>
	<tr><td>66000</td><td>4160 </td><td>3    </td><td>1    </td><td>1    </td><td>1    </td><td>1    </td><td>1    </td><td>0    </td><td>1    </td><td>0    </td><td>0    </td></tr>
</tbody>
</table>



# Analyse Exploratoire des données

Dans cette partie, nous voulons explorer la base de donnée, pour voir s'il y a des relations entre les variables.


```R
dim(maison)
```


<ol class=list-inline>
	<li>546</li>
	<li>12</li>
</ol>



Nous avons une base contenant 546 observations et 12 variables. 

**La description des variable est la suivante**

Prix: le prix de la maison en dollar canadien

Superficie en mètre carré

Nombre de salles de bains

Etage: la maison a combien d'étages

Allée: variabe binaire prenant 0 si la maison n'a pas d'allée, 1 si elle en a.

Les variables, SITUATION, GAZ, GARAGE, SALLEJEU sont aussi binaires et prennent les mêmes modalités que la variable Allée.

### Statistiques descriptives

Nous allons uniquement prendre en compte la variable SUPERFICIE pour notre regression linéaire simple. 

Dans un autre tuto, nous verrons la regression multiple dans laquelle nous prendront en compte toutes les variables.


```R
library(dplyr)
```

Nous allons pour la suite du tuto ne travailler que sur le prix et la superficie. Nous allons donc créer une nouvelle base avec ces deux variables.


```R
base <- maison %>% select(c(PRIX, SUPERFICIE))
```


```R
head(base)
```


<table>
<thead><tr><th scope=col>PRIX</th><th scope=col>SUPERFICIE</th></tr></thead>
<tbody>
	<tr><td>42000</td><td>5850 </td></tr>
	<tr><td>38500</td><td>4000 </td></tr>
	<tr><td>49500</td><td>3060 </td></tr>
	<tr><td>60500</td><td>6650 </td></tr>
	<tr><td>61000</td><td>6360 </td></tr>
	<tr><td>66000</td><td>4160 </td></tr>
</tbody>
</table>




```R
summary(base)
```


          PRIX          SUPERFICIE   
     Min.   : 25000   Min.   : 1650  
     1st Qu.: 49125   1st Qu.: 3600  
     Median : 62000   Median : 4600  
     Mean   : 68122   Mean   : 5150  
     3rd Qu.: 82000   3rd Qu.: 6360  
     Max.   :190000   Max.   :16200  


Il y a de forte disparité dans les prix et superificie des maisons. Le prix minimum de la maison est de 25000 $ alors que le maximum est de près de 200 mille dollars.

Pour nous faire une meilleure idée de la distribution de ces deux variables, représentons les graphiquement.


```R
library(ggplot2)
```


```R
options(repr.plot.width=4, repr.plot.height=3)
```

Nous allons commencer par construire des histogrammes des prix et des superifice afin de voir la tendance.


```R
ggplot(base, aes(x= PRIX)) + geom_histogram(fill = 'lightblue', col = 'black', bins= 30) +
ggtitle("Histogramme des prix en dollar") + xlab('Prix de la maison $')
```




![png](/_posts/regression_linéaire/output_22_1.png)


Nous observons que pour la plupart des maisons, les prix se concentrent entre 50000 et 70000, la répartition des prix semble unimodale. Il semble bien avoir des valeurs atypiques car certains prix s'isolent du reste


```R
ggplot(base, aes(x= SUPERFICIE)) + geom_histogram(fill = 'lightblue', col = 'black', bins= 30) +
ggtitle("Histogramme des superficies") + xlab('Superificie en m²')
```




![png](/_posts/regression_linéaire/output_24_1.png)


La répartition de la superifice semble plutôt bimodale. Une grande partie des maison a une superificie comprise entre 3000 et 5000.

**Nous voyons globalement que les deux variables ont une distribution similaire.** 

Essayons de voir s'il y a de potentielles valeurs atypiques.


```R
par(mfrow = c(1,2))
boxplot(base$PRIX, main= "Prix")
boxplot(base$SUPERFICIE, main = "Superficie")
```


![png](/_posts/regression_linéaire/output_26_0.png)


Nous observons à partir de cette boîte à moustache que plusieurs maisons s'écartent des autres tant par leurs prix que par leurs superificie

## Valeurs atypiques

Nous allons utiliser certains tests statistiques pour déterminer s'il y a des observations atypiques


```R
library(EnvStats)
```

Nous allons utiliser le test de Ronser pour détecter des valeurs atypiques dans la base de donnée


```R
rosnerTest(x = base$PRIX, k = 7)
```


    
    Results of Outlier Test
    -------------------------
    
    Test Method:                     Rosner's Test for Outliers
    
    Hypothesized Distribution:       Normal
    
    Data:                            base$PRIX
    
    Sample Size:                     546
    
    Test Statistics:                 R.1 = 4.564278
                                     R.2 = 4.086235
                                     R.3 = 4.154354
                                     R.4 = 4.206341
                                     R.5 = 3.821542
                                     R.6 = 3.554273
                                     R.7 = 3.191059
    
    Test Statistic Parameter:        k = 7
    
    Alternative Hypothesis:          Up to 7 observations are not
                                     from the same Distribution.
    
    Type I Error:                    5%
    
    Number of Outliers Detected:     4
    
      i   Mean.i     SD.i  Value Obs.Num    R.i+1 lambda.i+1 Outlier
    1 0 68121.60 26702.67 190000     378 4.564278   3.886307    TRUE
    2 1 67897.97 26210.45 175000     332 4.086235   3.885826    TRUE
    3 2 67701.09 25828.06 175000     363 4.154354   3.885345    TRUE
    4 3 67503.48 25436.96 174500     419 4.206341   3.884863    TRUE
    5 4 67306.07 25040.66 163000      93 3.821542   3.884379   FALSE
    6 5 67129.19 24722.59 155000     338 3.554273   3.883895   FALSE
    7 6 66966.47 24453.80 145000     362 3.191059   3.883409   FALSE
    
    


Le test de Rosner conclut que dans la distribution des prix, 4 observations sont atypiques. Nous allons les enlever de la base des données afin de permettre une meilleure estimation, les maisons qui ont un prix supérieur ou égal à 174500 sont considérées comme atypiques


```R
subset(base, subset = base$PRIX >= 174500)
```


<table>
<thead><tr><th></th><th scope=col>PRIX</th><th scope=col>SUPERFICIE</th></tr></thead>
<tbody>
	<tr><th scope=row>332</th><td>175000</td><td>8960  </td></tr>
	<tr><th scope=row>363</th><td>175000</td><td>9960  </td></tr>
	<tr><th scope=row>378</th><td>190000</td><td>7420  </td></tr>
	<tr><th scope=row>419</th><td>174500</td><td>7500  </td></tr>
</tbody>
</table>




```R
#Nous enlevons donc ces observation de la base
base <- subset(base, subset = base$PRIX <174500)
```


```R
dim(base)
# Nous passons de 546 observations à 542
```


<ol class=list-inline>
	<li>542</li>
	<li>2</li>
</ol>



Faisons maintenant la même chose pour la variable superifice


```R
rosnerTest(base$SUPERFICIE, k= 7)
```


    
    Results of Outlier Test
    -------------------------
    
    Test Method:                     Rosner's Test for Outliers
    
    Hypothesized Distribution:       Normal
    
    Data:                            base$SUPERFICIE
    
    Sample Size:                     542
    
    Test Statistics:                 R.1 = 5.137865
                                     R.2 = 4.987933
                                     R.3 = 3.945039
                                     R.4 = 4.007074
                                     R.5 = 3.944130
                                     R.6 = 3.984054
                                     R.7 = 3.631799
    
    Test Statistic Parameter:        k = 7
    
    Alternative Hypothesis:          Up to 7 observations are not
                                     from the same Distribution.
    
    Type I Error:                    5%
    
    Number of Outliers Detected:     6
    
      i   Mean.i     SD.i Value Obs.Num    R.i+1 lambda.i+1 Outlier
    1 0 5125.839 2155.401 16200     367 5.137865   3.884379    TRUE
    2 1 5105.370 2104.004 15600     363 4.987933   3.883895    TRUE
    3 2 5085.935 2056.777 13200     364 3.945039   3.883409    TRUE
    4 3 5070.881 2028.692 13200     380 4.007074   3.882923    TRUE
    5 4 5055.771 1999.992 12944     487 3.944130   3.882435    TRUE
    6 5 5041.082 1972.593 12900     529 3.984054   3.881947    TRUE
    7 6 5026.420 1944.926 12090     358 3.631799   3.881458   FALSE
    
    


Pour les superificies, le test nous indique qu'il y a 6 valeurs atypiques. A partir de 12900 mètre carrés, nous considérons que la superficie est atypique.


```R
subset(base, subset = base$SUPERFICIE >=12900)
```


<table>
<thead><tr><th></th><th scope=col>PRIX</th><th scope=col>SUPERFICIE</th></tr></thead>
<tbody>
	<tr><th scope=row>365</th><td> 84900</td><td>15600 </td></tr>
	<tr><th scope=row>366</th><td> 99000</td><td>13200 </td></tr>
	<tr><th scope=row>369</th><td>145000</td><td>16200 </td></tr>
	<tr><th scope=row>383</th><td>140000</td><td>13200 </td></tr>
	<tr><th scope=row>491</th><td> 50000</td><td>12944 </td></tr>
	<tr><th scope=row>533</th><td> 70000</td><td>12900 </td></tr>
</tbody>
</table>




```R
base <- subset(base, subset = base$SUPERFICIE <12900)
```


```R
dim(base)
```


<ol class=list-inline>
	<li>536</li>
	<li>2</li>
</ol>



# Linéarité du modèle

### Relation entre les deux variables


```R
ggplot(base, aes(x= PRIX, y = SUPERFICIE)) + geom_point(col = 'lightblue')
```




![png](/_posts/regression_linéaire/output_44_1.png)


Il semble y avoir une relation entre les deux variables mais cette relation n'est pas complètement linéaire comme nous pouvons le voir à partir de ce graphique. Il nous faut linéariser la relation entre le prix et la superifice.

D'abord, nous allons utiliser un test statistique, c'est le test RESET.

Ce test a pour hypothèse nulle : le modèle n'est pas linéaire et hypothèse alternative le modèle est linéaire.


```R
resettest(PRIX~SUPERFICIE, data = base)
```


    
    	RESET test
    
    data:  PRIX ~ SUPERFICIE
    RESET = 18.066, df1 = 2, df2 = 532, p-value = 2.564e-08
    


Comme nous pouvons nous attendre, le test montre que le modèle n'est pas linéaire au seuil de 1%. Il nous faut linéariser la relation entre le prix de la maison et sa superficie. 

Un des moyens est de transformer le prix en logarithme des prix, cela a pour effet de reduire les variations. Nous allons ajouter une nouvelle variable dans la base qui est le logarithme du prix


```R
base$log_PRIX <- log(base$PRIX)
```


```R
head(base)
```


<table>
<thead><tr><th scope=col>PRIX</th><th scope=col>SUPERFICIE</th><th scope=col>log_PRIX</th></tr></thead>
<tbody>
	<tr><td>42000   </td><td>5850    </td><td>10.64542</td></tr>
	<tr><td>38500   </td><td>4000    </td><td>10.55841</td></tr>
	<tr><td>49500   </td><td>3060    </td><td>10.80973</td></tr>
	<tr><td>60500   </td><td>6650    </td><td>11.01040</td></tr>
	<tr><td>61000   </td><td>6360    </td><td>11.01863</td></tr>
	<tr><td>66000   </td><td>4160    </td><td>11.09741</td></tr>
</tbody>
</table>



Représentons maintenant le logarithme du prix et la superficie


```R
ggplot(base, aes(x= SUPERFICIE, y = log_PRIX)) + geom_point(col = 'lightblue')
```




![png](/_posts/regression_linéaire/output_51_1.png)



```R
resettest(log_PRIX~SUPERFICIE, data = base)
```


    
    	RESET test
    
    data:  log_PRIX ~ SUPERFICIE
    RESET = 19.749, df1 = 2, df2 = 532, p-value = 5.327e-09
    


Malgré les transformations, nous n'arrivons pas à satisfaire le test Reset. Nous allons continuer la regression linéaire malgré cela !


```R
model <- lm(PRIX~SUPERFICIE, data = base)
summary(model)
```


    
    Call:
    lm(formula = PRIX ~ SUPERFICIE, data = base)
    
    Residuals:
       Min     1Q Median     3Q    Max 
    -50001 -14168  -2608   9772  79888 
    
    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept) 3.304e+04  2.508e+03   13.18   <2e-16 ***
    SUPERFICIE  6.748e+00  4.654e-01   14.50   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 20940 on 534 degrees of freedom
    Multiple R-squared:  0.2825,	Adjusted R-squared:  0.2811 
    F-statistic: 210.2 on 1 and 534 DF,  p-value: < 2.2e-16
    


### Interprétation du résulat des tests 

Le premier model que j'ai effectué est le model sans transformation, on explique le prix par la superficie.

Le test de Fisher nous indique une p-value très inférieure au seuil de 5%, donc l'hypothèse de nullité du coefficient que prend la variable superficie pour expliquer le prix est rejetté. Le modèle a donc intérêt à être interprêté.

La variable superifice est statistiquement significative au seuil de 1% pour expliquer le prix. 

Le R² qui est 0.2825 signifie que le modèle explique environ 28,25% de la variance du prix. 

### Interpretation du coefficient

Le coefficient de la superficie est de 6.74. Cela signifie qu'en moyenne et toute chose égale par ailleurs, une augmentation de la superficie de la maison de 1 mètre carré entraîne une augmentation du prix de la maison de 6.74 dollar. 

Nous pouvons choisir de faire un modèle à partir de la variable transformée pour voir si on a un gain d'information


```R
model <-lm(log_PRIX~SUPERFICIE, data = base)
summary(model)
```


    
    Call:
    lm(formula = log_PRIX ~ SUPERFICIE, data = base)
    
    Residuals:
         Min       1Q   Median       3Q      Max 
    -0.85257 -0.19274  0.01088  0.19574  0.89675 
    
    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept) 1.054e+01  3.628e-02  290.56   <2e-16 ***
    SUPERFICIE  1.004e-04  6.733e-06   14.92   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 0.3029 on 534 degrees of freedom
    Multiple R-squared:  0.2942,	Adjusted R-squared:  0.2928 
    F-statistic: 222.6 on 1 and 534 DF,  p-value: < 2.2e-16
    


Les résultats des test statistiques restent toujours aussi pertinents que le premier modèle.

Ici, vu que nous avons transformé la variable, l'interprétation des coefficients sera différente. 

Le modèle semi-log s'interprète comme cela : en moyenne et toute chose égale par ailleurs, une augmentation de la superficie de 1% entraîne une augmentation du prix de 0.04%. 

### Etude du modèle

On peut essayer de comprendre davantage le modèle en réalisant des tests. Est-ce qu'il y a des observations qui ont influencé les résultats ou non ? 

Les hypothèses d'un modèle linéaire sont-ils respectés (homoscédasticité, normalité des résidus ...)

**Normalité des résidus** 

On va utiliser le test de Shapiro sur les résidus du modèle


```R
shapiro.test(model$residuals)
```


    
    	Shapiro-Wilk normality test
    
    data:  model$residuals
    W = 0.99794, p-value = 0.7697
    


Le test de Shapiro confirme que les résidus suivent une loi normale au seuil de 5% (p-value).


```R
bptest(model)
```


    
    	studentized Breusch-Pagan test
    
    data:  model
    BP = 7.8731, df = 1, p-value = 0.005018
    


Le test de Breusch-Pagan test l'homoscédasticité des résidus, c'est-à-dire la constance de la variance des résidus. Car non seulement les résidus du modèle doivent être normalement distribués mais ils doivent aussi rester constants.


```R
plot(dffits(model))
```


![png](/_posts/regression_linéaire/output_66_0.png)


Maintenant j'aimerais représenter les valeurs prédites contres les valeurs observées pour voir la performance du modèle


```R
base$predictions <- model$fitted.values
```


```R
ggplot(base, aes(x = PRIX, y = predictions)) +
geom_point(col = 'lightblue') + xlab("Prix observé") + ylab("Prix prédits") +
ggtitle("Prix observé vs. Prédits")
```




![png](/_posts/regression_linéaire/output_69_1.png)

