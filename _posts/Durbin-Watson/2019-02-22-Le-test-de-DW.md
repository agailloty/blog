---
author: agailloty
layout: post
title: "Le test de Durbin-Watson"
comments: True
math : True
category : Econométrie
---



---------- --------- ----------


---------- --------- ----------
# C'est quoi le test de Durbin-Watson ?

## Autocorrelation des résidus
Pour garantir la fiabilité d'un modèle de régression linéaire il y a un certain nombre d'hypothèses que le modèle doit vérifier. Une de ces hypothèses concerne la statistique des termes d'erreur qu'on note souvent epsilon $\epsilon$ et stipule que chaque erreur doit être identique et indépendamment distribuée (IID) et doit suivre une loi normale:  $$ \epsilon \rightarrow N(0,\sigma)$$
Lorsque nous effectuons une régression linéaire sur une donnée à coupe transversale, les résidus sont bien indépendamment distribués car les individus présents dans la base de donnée sont eux aussi indépendants des autres. Par exemple si dans la base nous avons 100 pays et qu'on veut estimer un indicateur économique alors les erreurs dues à ce terme d'estimation seront indépendantes car les résultats d'un pays n'influenceront pas ceux d'un autre. Mais lorsque nous sommes dans le cas d'une série temporelle, dans le cas où on se focalise sur un seul pays par exemple pour observer l'évolution de ses variables, et qu'on veut faire une prévision sur le futur à partir des données du passé il y a un risque d'autocorrelation des erreurs car notre équation, pour prédire le futur, se servira des résultats des années précédentes.

## Intuition du test de Durbin-Watson

Supposons que le modèle qu'on veuille estimer est le suivant : $$Y_t = \beta_0 + \beta_1 X_{t-1} + \epsilon_t$$
L'intuition derrière le test de Durbin-Watson est que les termes d'erreur sont eux aussi le résultat d'une regression. Ainsi une erreur au temps t se présente comme ceci: $$\epsilon_t = \rho \epsilon_{t-1} + \omega_t$$
L'erreur au temps $t$ est égal à un coefficient $\rho$ multiplié par l'erreur de la période précédente plus un bruit blanc.
$$ avec :  $$
                    $$ |\rho| \leq 1  $$ 
                    $$\omega_t \rightarrow N(0,1)$$
$\rho\$ est le coefficient dont on va tester la significativité avec les hypothèses suivantes:
$$H_0 : \rho = 0 $$
$$H_1 : \rho \neq 0$$

Si donc $\rho$ est égal à 0 alors le coefficient est nul et il n'y a donc pas d'autocorrelation des résidus. Si l'hypothèse nulle est rejétée alors il y a autocorrelation, c'est-à-dire que l'erreur t est correlée avec l'erreur t-1.

## Exemple

### Bruits blancs

Des résidus non correlés sont dits **bruits blancs** et se présentent sous une forme sinusoïdale. Elle gravitent autour de l'espérance 0

![bruit_blanc.png](attachment:bruit_blanc.png)

### Des résidus autocorrelés et non bruits blancs

![autocorr_1.png](attachment:autocorr_1.png)

![autocorr_2.png](attachment:autocorr_2.png)

![autocorr_3.png](attachment:autocorr_3.png)


```python

```
