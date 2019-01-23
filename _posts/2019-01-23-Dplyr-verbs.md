﻿---
author: agailloty
category : R
title : Les verbes dplyr (Tidyverse)
layout: post
---

J’aimerais dans cet article aborder la manipulation des données avec le
package **tidyverse**. Que l’on fasse de l’économétrie ou de la data
science, la plupart du temps les données ne viennent pas dans le format
qu’on souhaite et sans qu’on les transforme dans le format approprié il
est impossible de construire le modèle.

C’est quoi le tydiverse ?
=========================

``` r
options(warn = -1)
```

Le tydiverse est un ensemble de packages qui sont construits dans le but
de travailler ensemble. Dans le tydiverse il y a le mot anglais “tidy”
s’oppose au format “wide” dans lequel les données sont sous forme d’une
table de donnée. Dans une donnée au format tidy, les colonnes
représentent des variables et chaque ligne réprésente une observation de
la colonne. C’est le format classique des données, mais pas tojours.
Dans ce petit article je vais principalement utiliser le package dplyr
qu fait partie du tidyverse et je vais vous présenter un outil très
puissant qui est le *pipe*.

Allons y !

``` r
# Commençons par importer la librarie
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.8
    ## v tidyr   0.8.1     v stringr 1.3.1
    ## v readr   1.1.1     v forcats 0.3.0

    ## -- Conflicts ------------------------------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
fortune <- read.csv("fortune1000.csv")
```

J’ai choisi de travailler sur une base de donnée assez populaire dans la
communauté des **data scientists**, c’est lase *Fortune 1000* produite
par le magasine Forbes qui recense les 1000 plus grosses entreprises
américaines, en fournissant certaines informations sur elles comme le
profit, le revenue, la ville …

Regardons comment la base se présente.

``` r
head(fortune)
```

    ##   Rank            Company      Sector
    ## 1    1            Walmart   Retailing
    ## 2    2        Exxon Mobil      Energy
    ## 3    3              Apple  Technology
    ## 4    4 Berkshire Hathaway  Financials
    ## 5    5           McKesson Health Care
    ## 6    6 UnitedHealth Group Health Care
    ##                                   Industry          Location Revenue
    ## 1                    General Merchandisers   Bentonville, AR  482130
    ## 2                       Petroleum Refining        Irving, TX  246204
    ## 3              Computers, Office Equipment     Cupertino, CA  233715
    ## 4 Insurance: Property and Casualty (Stock)         Omaha, NE  210821
    ## 5                 Wholesalers: Health Care San Francisco, CA  181241
    ## 6  Health Care: Insurance and Managed Care    Minnetonka, MN  157107
    ##   Profits Employees
    ## 1   14694   2300000
    ## 2   16150     75600
    ## 3   53394    110000
    ## 4   24083    331000
    ## 5    1476     70400
    ## 6    5813    200000

Comme on peut le voir ici, Il y a une colonne Rank qui montre le rang de
l’entreprise dans le classement, une colonne Secteur et une autre
Industrie. Il y encore d’autres colonnes.

Le format dans lequel notre base se présente c’est ce qu’on appelle une
**tidy data**. Chaque ligne ici correspond à une observation
particulière.

Je vais explorer cette base de donnée avec le package **dplyr** qui est
déjà importée lorsque j’ai chargée la librairie **dplyr**.

C’est quoi dplyr ?
==================

On définit généralement le package dplyr par ses 4 verbes très connnus:
select, filter, group\_by et summarize.

Au fait ces 4 fonctions qui ne sont que 4 parmi une centaine nous
permettent de manipuler facilement notre base de donnée.

#### Un exemple

Si on voulait par exemple extraire une colonne de la base avec R, on
ferait cela:

``` r
head(fortune["Company"])
```

    ##              Company
    ## 1            Walmart
    ## 2        Exxon Mobil
    ## 3              Apple
    ## 4 Berkshire Hathaway
    ## 5           McKesson
    ## 6 UnitedHealth Group

Alors qu’avec select on ferait cela

``` r
 head(select(fortune, Company))
```

    ##              Company
    ## 1            Walmart
    ## 2        Exxon Mobil
    ## 3              Apple
    ## 4 Berkshire Hathaway
    ## 5           McKesson
    ## 6 UnitedHealth Group

Le gros avantage du tidyverse c’est qu’il accroit la lisibilité du code
et rend ainsi facile la compréhension de ce qu’on fait.

Je vais dans la suite de cet article vous présenter dans les détails ces
4 verbes en introduisant avant le concept du pipe.

C’est quoi le pipe
------------------

Le pipe est un élement qui permet de passer le résultat d’une action
précédente comme premier argument de la fonction suivante. Il est simple
à utiliser et son symbole est “%&gt;%”.

Prenons un exemple: supposons que je veux créer un vecteur de nombre
allant de 1:10 et trouver la moyenne. Dans R on peut le faire comme
cela:

``` r
mean(1:10)
```

    ## [1] 5.5

Ce cas est assez simple, supposons maintenant qu’on veuille lire notre
base de donnée sans l’enregistrer dans une variable et regarder
directement les six premières lignes, et choisir la colonne Industry.

``` r
select(head(read.csv("fortune1000.csv"),2), Industry)
```

    ##                Industry
    ## 1 General Merchandisers
    ## 2    Petroleum Refining

Vous avez sûrement remarqué comment on a combiné 3 fonctions pour
effectuer cette opération. ce code peut devenir davantage beaucoup plus
difficile à lire si on voulait effectuer encore beaucoup plus d’actions.
C’est alors qu’on va voir l’importance de l’opérateur pipe.

``` r
read.csv("fortune1000.csv") %>% head(2) %>% select(Industry)
```

    ##                Industry
    ## 1 General Merchandisers
    ## 2    Petroleum Refining

on voit que ce code est beaucoup plus lisible. Dans un premier temps on
a lu le fichier, ensuite on affiche les 6 premières lignes ensuite on
sélectionne la colonne Industry.

Le verbe *select*
-----------------

Select, comme son nom l’indique permet de sélectionner une ou des
colonnes de notre base de donnée.

Maintenant que vous avez compris le principe du pipe, vous allez voir
comment il s’enchaîne bien avec le tidyverse et les fonctions R.

Comme nous venons de le voir, Select nous a permis de ne choisir qu’une
seule colonne à utiliser.

Alternativement si vous voulez sélectionner toutes les colonnes sauf
une, vous pouvez le faire facilement en ajoutant **-** devant le nom de
la colonne.

``` r
fortune %>% select(-Rank) %>% head(2)
```

    ##       Company    Sector              Industry        Location Revenue
    ## 1     Walmart Retailing General Merchandisers Bentonville, AR  482130
    ## 2 Exxon Mobil    Energy    Petroleum Refining      Irving, TX  246204
    ##   Profits Employees
    ## 1   14694   2300000
    ## 2   16150     75600

Ici on a sélectionné toutes les colonnes sauf Rank qui était le premier
dans la base.

Il est ausi possible de sélectionner plusieurs les colonnes en même
temps, et aussi sélectionner des colonnes en fonctions des élements qui
apparaissent dans leurs noms.

Pour sélectionner plusieurs colonnes, rien de plus simple

``` r
fortune %>% select(c(Sector, Location, Profits)) %>% head(2)
```

    ##      Sector        Location Profits
    ## 1 Retailing Bentonville, AR   14694
    ## 2    Energy      Irving, TX   16150

Si on veut sélectionner des colonnes qui présentent des mots, lettres ou
expression dans le noms.

``` r
fortune %>% select(ends_with("y")) %>% head(2)
```

    ##       Company              Industry
    ## 1     Walmart General Merchandisers
    ## 2 Exxon Mobil    Petroleum Refining

Ici on vient de sélectionner les colonnes qui finissent par y. La
fonction complémentaires c’est stars\_with

``` r
fortune %>% select(starts_with("s")) %>% head(2)
```

    ##      Sector
    ## 1 Retailing
    ## 2    Energy

On peut aussi sélectionner les colonnes qui contiennent une lettre, un
mot …

``` r
fortune %>% select(contains("r")) %>% head(2)
```

    ##   Rank    Sector              Industry Revenue Profits
    ## 1    1 Retailing General Merchandisers  482130   14694
    ## 2    2    Energy    Petroleum Refining  246204   16150

Voilà donc une petite introduction àla fonction select.

Nous allons maintenant voir une fonction qui agit comme select mais qui
permet de filtrer la base.

### Filter

Filter nous permet de sélectionner des lignes qui respectent une
condition.

``` r
fortune %>% filter(Revenue>100000) %>% head()
```

    ##   Rank            Company      Sector
    ## 1    1            Walmart   Retailing
    ## 2    2        Exxon Mobil      Energy
    ## 3    3              Apple  Technology
    ## 4    4 Berkshire Hathaway  Financials
    ## 5    5           McKesson Health Care
    ## 6    6 UnitedHealth Group Health Care
    ##                                   Industry          Location Revenue
    ## 1                    General Merchandisers   Bentonville, AR  482130
    ## 2                       Petroleum Refining        Irving, TX  246204
    ## 3              Computers, Office Equipment     Cupertino, CA  233715
    ## 4 Insurance: Property and Casualty (Stock)         Omaha, NE  210821
    ## 5                 Wholesalers: Health Care San Francisco, CA  181241
    ## 6  Health Care: Insurance and Managed Care    Minnetonka, MN  157107
    ##   Profits Employees
    ## 1   14694   2300000
    ## 2   16150     75600
    ## 3   53394    110000
    ## 4   24083    331000
    ## 5    1476     70400
    ## 6    5813    200000

Ici on sélectionne les lignes qui ont un Revenue &gt;100000.

On peut encore sélectionner des entreprises qui appartiennent à un
secteur.

``` r
fortune %>% filter(Sector == "Retailing") %>% head()
```

    ##   Rank    Company    Sector                   Industry        Location
    ## 1    1    Walmart Retailing      General Merchandisers Bentonville, AR
    ## 2   15     Costco Retailing Specialty Retailers: Other    Issaquah, WA
    ## 3   28 Home Depot Retailing Specialty Retailers: Other     Atlanta, GA
    ## 4   38     Target Retailing      General Merchandisers Minneapolis, MN
    ## 5   47   Loweâ\200\231s Retailing Specialty Retailers: Other Mooresville, NC
    ## 6   71   Best Buy Retailing Specialty Retailers: Other   Richfield, MN
    ##   Revenue Profits Employees
    ## 1  482130   14694   2300000
    ## 2  116199    2377    161000
    ## 3   88519    7009    385000
    ## 4   73785    3363    341000
    ## 5   59074    2546    225000
    ## 6   39745     897    125000

Cette fonction facilite grandement la lisibilité du code.

Verbes mutate et transmute
--------------------------

Les verbes mutate et transmute nous permettent de créer facilement des
variables à partir de notre base de donnée initiale.

Supposons que dans notre cas, nous voulions créer une nouvelle colonne
qui s’appelle “Taux de profit” qui se calcule par le rapport du profit
et du revenu.

On peut le faire simplement en utilisant **mutate()**

``` r
fortune %>% mutate("Taux de Profit" = Profits/Revenue) %>% head()
```

    ##   Rank            Company      Sector
    ## 1    1            Walmart   Retailing
    ## 2    2        Exxon Mobil      Energy
    ## 3    3              Apple  Technology
    ## 4    4 Berkshire Hathaway  Financials
    ## 5    5           McKesson Health Care
    ## 6    6 UnitedHealth Group Health Care
    ##                                   Industry          Location Revenue
    ## 1                    General Merchandisers   Bentonville, AR  482130
    ## 2                       Petroleum Refining        Irving, TX  246204
    ## 3              Computers, Office Equipment     Cupertino, CA  233715
    ## 4 Insurance: Property and Casualty (Stock)         Omaha, NE  210821
    ## 5                 Wholesalers: Health Care San Francisco, CA  181241
    ## 6  Health Care: Insurance and Managed Care    Minnetonka, MN  157107
    ##   Profits Employees Taux de Profit
    ## 1   14694   2300000    0.030477257
    ## 2   16150     75600    0.065596010
    ## 3   53394    110000    0.228457737
    ## 4   24083    331000    0.114234350
    ## 5    1476     70400    0.008143853
    ## 6    5813    200000    0.037000261

La colonne “Taux de profit” vient de s’ajouter à notre base de donnée et
se situe à la fin.

Comme vous pouvez vous demander, que fait **transmute**, au fait elle
fait la même chose sauf qu’au lieu de retourner toute la base de donnée,
elle ne retourne que l’opération qu’on vient d’effectuer.

``` r
fortune %>% transmute("Taux de Profit" = Profits/Revenue) %>% head()
```

    ##   Taux de Profit
    ## 1    0.030477257
    ## 2    0.065596010
    ## 3    0.228457737
    ## 4    0.114234350
    ## 5    0.008143853
    ## 6    0.037000261

Comme on le voit la, on a maintenant une base avec une seule colonne.

Les verbes group\_by et summarize
---------------------------------

Le verbe group\_by permet de créer une sous-catégorie de notre base de
donnée sur laquelle on va effectuer des statistiques et souvent avec la
fonction summarize. Mais vous pouvez-vous demander quel est le réel
avantage d’utiliser summarize alors qu’on a déjà une très bonne fonction
native summary intégrée à R?

L’avantage c’est qu’on a plus de contrôle sur ce qu’on veut afficher et
que la combinaison de `group_by et summarize` nous donnent plus de
détails dans la comparaison.

Voyons ce que fait un summary normal

``` r
fortune %>% summary()
```

    ##       Rank                             Company                  Sector   
    ##  Min.   :   1.0   Host Hotels & Resorts    :  2   Financials       :139  
    ##  1st Qu.: 250.8   Noble Energy             :  2   Energy           :122  
    ##  Median : 500.5   Portland General Electric:  2   Technology       :102  
    ##  Mean   : 500.5   Regions Financial        :  2   Retailing        : 80  
    ##  3rd Qu.: 750.2   3M                       :  1   Health Care      : 75  
    ##  Max.   :1000.0   99 Cents Only Stores     :  1   Business Services: 51  
    ##                   (Other)                  :990   (Other)          :431  
    ##                                      Industry            Location  
    ##  Specialty Retailers: Other              : 42   New York, NY : 72  
    ##  Utilities: Gas and Electric             : 41   Houston, TX  : 44  
    ##  Chemicals                               : 30   Atlanta, GA  : 23  
    ##  Commercial Banks                        : 28   Chicago, IL  : 22  
    ##  Insurance: Property and Casualty (Stock): 28   Dallas, TX   : 16  
    ##  Mining, Crude-Oil Production            : 28   St. Louis, MO: 14  
    ##  (Other)                                 :803   (Other)      :809  
    ##     Revenue          Profits            Employees      
    ##  Min.   :  1895   Min.   :-23119.00   Min.   :     52  
    ##  1st Qu.:  2898   1st Qu.:    82.75   1st Qu.:   6405  
    ##  Median :  5113   Median :   282.50   Median :  12520  
    ##  Mean   : 13536   Mean   :   894.09   Mean   :  33028  
    ##  3rd Qu.: 11386   3rd Qu.:   784.50   3rd Qu.:  28125  
    ##  Max.   :482130   Max.   : 53394.00   Max.   :2300000  
    ## 

La fonction summary détecte automatiquement les types de données et
génère des statistiques descriptives globales pour chacune des
variables. Maintenant, supposons qu’on veuille comparer la moyenne et la
médiane des profits mais par secteur. C’est là qu’on verra l’intérêt de
nos deux fonctions.

``` r
fortune %>% group_by(Sector) %>% summarise(Moyenne = mean(Profits), Médiane = median(Profits)) %>%
  head()
```

    ## # A tibble: 6 x 3
    ##   Sector                     Moyenne Médiane
    ##   <fct>                        <dbl>   <dbl>
    ## 1 Aerospace & Defense          1437.    566.
    ## 2 Apparel                       549.    233 
    ## 3 Business Services             553.    296 
    ## 4 Chemicals                     754.    368.
    ## 5 Energy                       -602.    188.
    ## 6 Engineering & Construction    204     150.

Comme on peut le voir ici, on a groupé notre base de donnée par Secteur
et on a réalisé des statistiques sur une variable particulière qui est
le Profit. Bien entendu on peut combiner réaliser ces statistiques sur
mutliples variables en même temps. Nommer les opération (comme ici
écrire Moyenne et Médiane) n’est pas obligatoire mais pour plus de
clareté on peut décider de le faire.

Aussi autre aspect important, l’utilisation de ces fonctions nous créent
une nouvelle `dataframe`, on peut donc choisir de l’assigner à une
variable

``` r
statis <- fortune %>% group_by(Sector) %>% summarize("Moyenne Profit" = mean(Profits),
                                                     "Moyenne_Revenue" = mean(Revenue),
                                                     "Nombre Entreprise" = n())
```

On peut maintenant regarder ce qu’il y a dans la base statis

``` r
statis %>% head()
```

    ## # A tibble: 6 x 4
    ##   Sector               `Moyenne Profit` Moyenne_Revenue `Nombre Entrepris~
    ##   <fct>                           <dbl>           <dbl>              <int>
    ## 1 Aerospace & Defense             1437.          17897                  20
    ## 2 Apparel                          549.           6398.                 15
    ## 3 Business Services                553.           5337.                 51
    ## 4 Chemicals                        754.           8130.                 30
    ## 5 Energy                          -602.          12441.                122
    ## 6 Engineering & Const~             204            5922.                 26

Comme vous le voyez, cela nous donne plein d’autres informations qu’on
ne peut avoir en faisant le summary simple.