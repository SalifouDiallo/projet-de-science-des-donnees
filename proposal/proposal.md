Proposition de projet
================
Salifou Diallo, Mamadou Ciré Bah, Aboubacar Demba Bah, Mamadou Bah

``` r
library(tidyverse)
library(broom)
```

## 1. Introduction

Pour ce projet, notre équipe analyse un jeu de données de **comptages
cyclistes** publié par **Transport for London (TfL)**, accessible sur le
portail officiel **cycling.data.tfl.gov.uk**. Les données correspondent
à des comptages automatisés effectués à différents points de mesure dans
le centre de Londres durant le **printemps 2024**.

Contrairement aux données de locations individuelles, ce jeu capture le
**volume de passages** de cyclistes à des sites fixes, enregistrés à
**intervalles réguliers** au cours de la journée. Chaque observation
correspond à un **comptage par créneau temporel** (`Time`),
**direction** (`Direction`), **type de vélo** (`Mode`) et **conditions
météorologiques** (`Weather`).

**Question de recherche :**  
*Quels facteurs temporels et météorologiques influencent le volume de
cyclistes dans le centre de Londres, et observe-t-on des patterns
distincts selon les périodes de la journée et les jours de la semaine ?*

Ces données permettent d’explorer des habitudes de mobilité urbaine à
partir de variables observables (heure, jour, météo). L’enjeu est
pertinent pour la **planification des infrastructures cyclables** et la
**mobilité durable**. Le jeu comporte des **variables catégorielles**
(`Weather`, `Day`, `Direction`, `Path`, `Mode`), des **numériques**
(`Count`) et une **temporelle** (`Time`). Nous travaillerons sur
l’ensemble d’environ **870 144** enregistrements (printemps 2024), tout
en utilisant un **échantillon** lors du knit pour garder un rendu
fluide.

## 2. Données

**Source :** Transport for London (TfL) – *Cycling Open Data*  
**URL :** <https://cycling.data.tfl.gov.uk/>  
**Fichier :** `2024 W1 spring-Inner-Part1.csv` (Inner London – Wave 1,
printemps 2024)  
**Zone :** Inner London (centre)

**Aperçu du jeu de données (skim ou glimpse)**

    ## **Dimensions (aperçu knit) :** 10000 lignes × 11 colonnes

| Wave | SiteID | Date | Weather | Time | Day | Round | Direction | Path | Mode | Count |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|---:|
| 2024 W1 spring | ML0210 | 09/07/2024 | Dry | 06:00:00 | Weekday | A | Northbound | Carriageway | Cargo bikes | 0 |
| 2024 W1 spring | ML0210 | 09/07/2024 | Dry | 06:15:00 | Weekday | A | Northbound | Carriageway | Cargo bikes | 0 |
| 2024 W1 spring | ML0210 | 09/07/2024 | Dry | 06:30:00 | Weekday | A | Northbound | Carriageway | Cargo bikes | 0 |
| 2024 W1 spring | ML0210 | 09/07/2024 | Dry | 06:45:00 | Weekday | A | Northbound | Carriageway | Cargo bikes | 0 |
| 2024 W1 spring | ML0210 | 09/07/2024 | Dry | 07:00:00 | Weekday | A | Northbound | Carriageway | Cargo bikes | 0 |

Data summary

|                                                  |       |
|:-------------------------------------------------|:------|
| Name                                             | velos |
| Number of rows                                   | 10000 |
| Number of columns                                | 11    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |       |
| Column type frequency:                           |       |
| character                                        | 9     |
| difftime                                         | 1     |
| numeric                                          | 1     |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |       |
| Group variables                                  | None  |

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Wave          |         0 |             1 |  14 |  14 |     0 |        1 |          0 |
| SiteID        |         0 |             1 |   6 |   6 |     0 |        5 |          0 |
| Date          |         0 |             1 |  10 |  10 |     0 |        7 |          0 |
| Weather       |         0 |             1 |   3 |   3 |     0 |        2 |          0 |
| Day           |         0 |             1 |   7 |   7 |     0 |        1 |          0 |
| Round         |         0 |             1 |   1 |   1 |     0 |        2 |          0 |
| Direction     |         0 |             1 |   9 |  10 |     0 |        4 |          0 |
| Path          |         0 |             1 |   3 |  23 |     0 |       11 |          0 |
| Mode          |         0 |             1 |  10 |  19 |     0 |        6 |          0 |

**Variable type: difftime**

| skim_variable | n_missing | complete_rate | min | max | median | n_unique |
|:---|---:|---:|:---|:---|:---|---:|
| Time | 0 | 1 | 21600 secs | 78300 secs | 49500 secs | 64 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean |    sd |  p0 | p25 | p50 | p75 | p100 | hist  |
|:--------------|----------:|--------------:|-----:|------:|----:|----:|----:|----:|-----:|:------|
| Count         |         0 |             1 | 4.42 | 13.33 |   0 |   0 |   0 |   1 |  133 | ▇▁▁▁▁ |

    ## 
    ## **Noms de colonnes détectés :**

    ##  [1] "Wave"      "SiteID"    "Date"      "Weather"   "Time"      "Day"      
    ##  [7] "Round"     "Direction" "Path"      "Mode"      "Count"

**Dimensions du fichier complet :** environ 870 144 lignes × 11
colonnes  
**Date d’accès :** 21 octobre 2025

## 3. Plan d’analyse de données

### Variables principales

Notre variable de **résultat (Y)** est :  
- `Count` → le **nombre de cyclistes comptés** par créneau horaire et
par point de mesure.

Les variables **prédictives (X)** que nous considérons sont :  
- `Time` → créneau horaire (variable temporelle continue).  
- `Day` → type de jour (Weekday / Weekend).  
- `Weather` → conditions météorologiques (Dry / Wet).  
- `Mode` → type de vélo (Cargo, Classic, e-bike, etc.).  
- `Direction` → sens de déplacement (Northbound / Southbound).

Ces variables permettront de relier les volumes de circulation cycliste
aux **conditions temporelles et environnementales**.

------------------------------------------------------------------------

### Groupes de comparaison

Nous prévoyons plusieurs comparaisons descriptives :  
- **Weekday vs Weekend** → différence du volume moyen de cyclistes selon
le jour.  
- **Dry vs Wet** → effet des conditions météo sur la fréquentation.  
- **Morning vs Evening** → pics horaires typiques (analyse par créneau
`Time`).  
- **Type de vélo (`Mode`)** → identification des modes les plus
fréquents selon la météo.

Ces comparaisons nous aideront à visualiser les **patterns quotidiens**
et les **effets contextuels** sur la pratique cycliste.

------------------------------------------------------------------------

### Analyse exploratoire préliminaire

Nous commencerons par une **analyse descriptive** des données :

1.  **Statistiques de base**
    - Moyenne, médiane et écart-type de `Count`.  
    - Répartition des observations par `Day`, `Weather`, et `Direction`.
2.  **Visualisations prévues**
    - Histogramme ou densité du `Count` (distribution générale des
      volumes).  
    - Courbe du `Count` moyen par heure (`Time`) pour visualiser les
      heures de pointe.  
    - Boîtes à moustaches (`boxplot`) comparant `Count` entre conditions
      météo (`Weather`).  
    - Diagrammes en barres comparant la fréquentation moyenne selon
      `Day` (Weekday vs Weekend).  
    - Facettes par `Mode` pour observer les différences selon le type de
      vélo.

Ces visualisations aideront à **identifier les tendances globales** et
les **variables les plus influentes**.

------------------------------------------------------------------------

### Méthodes envisagées

Pour répondre à notre question de recherche, nous prévoyons plusieurs
approches :

1.  **Analyse descriptive et graphique** : pour observer visuellement
    les tendances et patterns horaires.
2.  **Modélisation statistique exploratoire** :
    - Un **modèle linéaire généralisé (GLM)** de type Poisson ou
      quasi-Poisson pour modéliser la variable `Count` (car il s’agit
      d’un comptage).
    - Variables explicatives : `Time`, `Day`, `Weather`, `Mode` et
      `Direction`.
3.  **Visualisation des effets** : estimation et représentation
    graphique des effets de `Weather` et `Day` sur le volume moyen de
    cyclistes.

------------------------------------------------------------------------

### Résultats attendus

Les résultats attendus incluent : - L’identification de **pics de
circulation** selon les heures de la journée.  
- Une **diminution du volume** lors de conditions météorologiques
défavorables.  
- Des différences marquées entre **jours de semaine** et **week-end**.  
- Une mise en évidence de certaines tendances selon le **type de vélo**
ou la **direction de déplacement**.

Ces résultats permettront de **mieux comprendre les facteurs influençant
la mobilité cycliste** à Londres et d’apporter des éléments utiles à la
planification urbaine.

------------------------------------------------------------------------

> *Remarque : Cette analyse exploratoire sera amenée à évoluer selon les
> observations et les résultats obtenus lors du traitement complet des
> données.*
