# Résumé exécutif – Projet final TfL (Équipe 15)

Ce projet analyse les **patterns de mobilité cycliste à Londres** à partir du jeu de données public **Transport for London (TfL)**.  
Les données proviennent de systèmes officiels de comptage automatisé et couvrent plus de **870 000 observations** (printemps 2024, intervalles de 15 minutes).  
Chaque ligne représente un nombre de cyclistes selon **l’heure**, **la direction** et **le mode de déplacement**.

---

## Contexte académique

Ce projet a été réalisé dans le cadre du cours  
**Science des données et intelligence d’affaires (8INF404)**  
à l’**Université du Québec à Chicoutimi (UQAC)** durant la session **Automne 2025**.

Description officielle du cours :  
https://programmes.uqac.ca/8inf404

Le projet a été réalisé en **équipe de quatre étudiants**.  
Dans l’équipe, j’ai contribué principalement à **l’analyse exploratoire des données, à la visualisation et à la modélisation statistique**.

---

## Question de recherche

**Comment les facteurs temporels (heure, jour), contextuels (météo) et opérationnels (mode, direction) influencent-ils l’évolution du trafic cycliste moyen à Londres ?**

### Objectif

- Identifier des tendances utiles pour la planification urbaine  
- Utiliser les outils du cours : visualisation, exploration, modèles de comptage (**Poisson / quasi-Poisson**)

---

## Données

- Fichier nettoyé final : `tfl_clean.rds`  
- Nombre total : **870 144 lignes**, **11 variables**

### Variables clés

- **count** : nombre de cyclistes  
- **time** : heure (64 niveaux de 15 minutes)  
- **day** : weekday  
- **weather** : dry / wet  
- **mode** : 6 types  
- **direction** : 4 directions  

Aucune donnée personnelle n’est présente dans ce jeu de données : il s’agit uniquement de **comptages anonymes de trafic cycliste**.

---

# Méthodologie

## Étape 1 – Nettoyage

- Normalisation des noms (`clean_names`)  
- Uniformisation des variables (`day`, `weather`, `mode`, `direction`)  
- Conversion des types (facteurs et entiers)  
- Suppression des doublons et des valeurs manquantes  
- Export des fichiers :  

- `tfl_clean.csv`  
- `tfl_clean.rds`

---

## Étape 2 – Analyse exploratoire

Trois graphiques principaux ont été produits pour explorer les données.

### Histogramme du nombre de cyclistes  
→ Distribution très asymétrique, avec une forte présence de valeurs faibles et de zéros.

### Count moyen selon la météo  
→ Le trafic cycliste est nettement plus élevé lorsque la météo est **dry**.

### Évolution du count moyen selon l’heure  
→ Deux pics très clairs apparaissent :  

- **7h–9h** (trajets domicile → travail)  
- **16h–19h** (trajets retour)

Ces visualisations respectent les bonnes pratiques de visualisation de données vues dans le cours.

---

## Étape 3 – Modèle de Poisson / quasi-Poisson

Modèle ajusté :
count ~ time + weather + mode + direction


Une **surdispersion élevée** a été observée (**≈ 17.6**), ce qui a conduit à utiliser un modèle **quasi-Poisson**.

### Résultats (IRR)

Les principaux résultats montrent que :

- Certains créneaux horaires ont des **IRR supérieurs à 6** (ex. **7h30–8h15**)  
- La météo **dry** est associée à une **hausse marquée du trafic cycliste**  
- Les modes comme **conventional cycles** présentent des volumes plus élevés  
- La direction **northbound** apparaît plus fréquentée

Les IRR sont interprétés de manière descriptive dans le cadre de l’analyse.

---

# Résultats principaux

- **Pattern bimodal** du trafic cycliste : pic le matin et pic le soir  
- Les conditions **dry** augmentent significativement la fréquentation  
- Les **modes de déplacement** et les **directions** influencent fortement les volumes observés  
- Le modèle **quasi-Poisson** confirme les tendances observées dans les visualisations

---

# Limites

Certaines limites doivent être mentionnées :

- Les données proviennent d’une seule vague (**W1 spring**)  
- L’analyse concerne principalement des **jours de semaine**  
- Absence de variables socio-démographiques  
- La variable `site_id` n’a pas été intégrée dans le modèle

---

# Conclusion

Les analyses exploratoires et le modèle quasi-Poisson montrent que les **facteurs temporels** (heures de pointe) et **contextuels** (météo) influencent significativement les volumes cyclistes à Londres.

Ce projet illustre l’application complète du **workflow de science des données** enseigné dans le cours, incluant :

- nettoyage des données  
- visualisation exploratoire  
- modélisation statistique avec **GLM**

Les résultats fournissent également des **indications utiles pour la planification des infrastructures cyclables**.

---

## Data Visualization

### Distribution of cyclist counts
![Histogram](docs/figures/histogram_cyclists.png)

### Average cyclists by weather
![Weather](docs/figures/cyclists_weather_barplot.png)

### Average cyclists by hour
![Hourly traffic](docs/figures/cyclists_by_hour.png)
