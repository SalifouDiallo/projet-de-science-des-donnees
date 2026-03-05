# Résumé exécutif – Projet final TfL (Équipe 15)

Ce projet analyse les **patterns de mobilité cycliste à Londres** à partir du jeu de données public **Transport for London (TfL)**.  
Les données proviennent de systèmes officiels de comptage automatisé et couvrent plus de **870 000 observations** (printemps 2024, intervalles de 15 minutes).  
Chaque ligne représente un nombre de cyclistes selon **l’heure**, **la direction** et **le mode de déplacement**.

---

## Question de recherche

**Comment les facteurs temporels (heure, jour), contextuels (météo) et opérationnels (mode, direction) influencent-ils l’évolution du trafic cycliste moyen à Londres ?**

### Objectif

- Identifier des tendances utiles pour la planification urbaine  
- Utilisation des outils du cours : visualisation, exploration, modèles de comptage (**Poisson / quasi-Poisson**)

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

Aucune donnée personnelle → uniquement des comptages anonymes.

---

# Méthodologie

## Étape 1 – Nettoyage

- Normalisation des noms (`clean_names`)  
- Uniformisation (`day`, `weather`, `mode`, `direction`)  
- Conversion des types (facteurs et entiers)  
- Suppression des doublons et NA  
- Export des fichiers :  
  - `tfl_clean.csv`  
  - `tfl_clean.rds`

---

## Étape 2 – Analyse exploratoire

Trois graphiques principaux :

### Histogramme du nombre de cyclistes  
→ Distribution très asymétrique, beaucoup de zéros.

### Count moyen selon la météo  
→ Trafic nettement plus élevé en *dry*.

### Évolution du count moyen selon l’heure  
→ Deux pics clairs : **7h–9h** et **16h–19h**.

Ces visualisations respectent les bonnes pratiques vues en cours.

---

## Étape 3 – Modèle de Poisson / quasi-Poisson

Modèle ajusté :

count ~ time + weather + mode + direction


- Surdispersion élevée (**≈ 17.6**)  
→ utilisation du **quasi-Poisson**

### Résultats (IRR)

- Créneaux horaires > 6 (ex. **7h30–8h15**)  
- Météo *dry* → hausse marquée du trafic  
- Modes comme *conventional cycles* → volumes plus élevés  
- Direction *northbound* → plus fréquentée  

Les IRR sont interprétés de manière descriptive.

---

# Résultats principaux

- **Pattern bimodal** : pic matin + pic soir  
- **Dry** = trafic cycliste beaucoup plus élevé  
- **Modes & directions** influencent fortement le volume  
- Le **quasi-Poisson** confirme les observations visuelles

---

# Limites

- Une seule vague (W1 spring) → aucune saisonnalité  
- Données surtout *weekday*  
- Pas de données socio-démographiques  
- `site_id` non modélisé

---

# Conclusion

Les analyses exploratoires et le modèle quasi-Poisson montrent que les **facteurs temporels** (heures de pointe) et **contextuels** (météo) influencent significativement les volumes cyclistes.

Ce projet fournit des **insights importants** pour la planification des infrastructures cyclables et illustre l’application du workflow **tidyverse + GLM** enseigné dans le cours.

## Data Visualization

### Distribution of cyclist counts
![Histogram](histogram_cyclists.png)

### Average cyclists by weather
![Weather](cyclists_weather_barplot.png)

### Average cyclists by hour
![Hourly traffic](cyclists_by_hour.png)
