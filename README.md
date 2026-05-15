#  Projet Machine Learning Immobilier – Avito Maroc

##  Présentation du projet

Ce projet a pour objectif de développer un pipeline complet de **Machine Learning appliqué à l’immobilier au Maroc**, à partir des données d’annonces immobilières collectées depuis Avito et stockées dans un **Data Warehouse PostgreSQL**.

Le projet s’inscrit dans une architecture **Data Engineering + Machine Learning**, où les données sont d’abord centralisées, nettoyées et consolidées dans un entrepôt de données avant d’être exploitées pour l’apprentissage automatique.

Après l’intégration des données dans le Data Warehouse, une table spécifique dédiée au Machine Learning est créée :

```sql
ml_schema.obt_avito
```

Cette table suit une logique **OBT (One Big Table)** contenant toutes les informations immobilières sous une forme plate, prête pour les traitements avancés.

---

##  Objectifs du projet

Le projet vise à répondre à deux problématiques principales :

### 1. Modèle de Régression

L’objectif est de **prédire le prix des biens immobiliers** à partir des caractéristiques du logement.

Le modèle apprend les relations entre plusieurs variables immobilières afin d’estimer un prix cohérent.

Exemples de variables utilisées :

* Surface du bien
* Nombre de chambres
* Nombre de salles de bain
* Ville
* Type de bien
* Équipements disponibles
* Informations géographiques

---

###  2. Modèle de Classification

L’objectif est de **classifier les biens immobiliers selon leur niveau de prix**.

Une variable cible est créée à partir d’une transformation logarithmique du prix :

* **Bas**
* **Moyen**
* **Haut**

Plusieurs modèles sont testés afin de comparer leurs performances et sélectionner le meilleur algorithme.

---

##  Architecture du pipeline Machine Learning

Le pipeline suit une architecture standardisée et reproductible :

```text
Data Warehouse (PostgreSQL)
            ↓
Extraction OBT (ml_schema)
            ↓
Train / Test Split
            ↓
Feature Engineering
            ↓
Préprocessing
(Imputation + Scaling + Encoding)
            ↓
Gestion du déséquilibre (SMOTE)
            ↓
Training des modèles
            ↓
Évaluation des performances
            ↓
Analyse des résultats
            ↓
Export des modèles
```

Toutes les transformations Machine Learning sont réalisées **après extraction depuis PostgreSQL** afin de conserver un Data Warehouse propre et indépendant des traitements analytiques.

---

##  Source des données

Les données utilisées proviennent d’une table consolidée du Data Warehouse :

### Schéma :

```sql
ml_schema
```

### Table :

```sql
obt_avito
```

Cette table contient :

* les informations descriptives des biens immobiliers ;
* les caractéristiques techniques ;
* les données géographiques ;
* les informations de prix ;
* les variables enrichies nécessaires au Machine Learning.

---

##  Préparation des données

Après extraction de l’OBT, plusieurs étapes de préparation sont appliquées.

### 1. Séparation des données

Les données sont divisées en :

* **80 % pour l’entraînement**
* **20 % pour les tests**

Cette séparation permet d’éviter le **data leakage** et d’évaluer le modèle sur des données jamais vues.

---

### 2. Gestion des valeurs manquantes

Pour garantir la robustesse des modèles :

#### Variables numériques

Les valeurs manquantes sont remplacées par :

```text
Médiane
```

#### Variables catégorielles

Les valeurs manquantes sont remplacées par :

```text
Valeur la plus fréquente
```

---

### 3. Encodage des variables catégorielles

Les variables textuelles sont converties en données numériques grâce à :

```text
One Hot Encoding
```

Exemple :

```text
Casablanca → [1,0,0]
Rabat → [0,1,0]
Marrakech → [0,0,1]
```

---

### 4. Standardisation des variables numériques

Les variables numériques sont normalisées avec :

```text
StandardScaler
```

Cette étape améliore les performances et évite qu’une variable domine les autres.

---

### 5. Gestion du déséquilibre des classes

Dans le modèle de classification, certaines classes peuvent être sous-représentées.

Pour équilibrer les données, la technique suivante est utilisée :

```text
SMOTE
```

Cette méthode génère artificiellement de nouveaux exemples pour les classes minoritaires.

---

##  Feature Engineering

Des transformations avancées sont réalisées après extraction des données.

### Transformation logarithmique du prix

Le prix est transformé afin de réduire l’asymétrie des données :

```text
prix_log = log(prix)
```

---

### Création de ratios

Exemple :

```text
prix_par_m2 = prix / surface
```

Ces ratios permettent de mieux représenter la valeur immobilière.

---

### Interactions entre variables

Création de variables dérivées :

```text
surface × nombre_de_chambres
```

afin de capturer des relations plus complexes.

---

### Variables enrichies

Le projet exploite également :

* des indicateurs géographiques ;
* des variables temporelles ;
* des interactions immobilières.

---

##  Modèle de Régression

Le modèle de régression est utilisé pour :

> **Prédire le prix d’un bien immobilier**

### Algorithme utilisé

```text
Random Forest Regressor
```

### Métriques d’évaluation

#### MAE (Mean Absolute Error)

Mesure l’erreur moyenne entre le prix réel et le prix prédit.

#### MSE (Mean Squared Error)

Pénalise davantage les grandes erreurs de prédiction.

#### R² Score

Mesure la capacité du modèle à expliquer la variance du prix immobilier.

---

##  Modèle de Classification

Le modèle de classification est utilisé pour :

> **Prédire le niveau de prix d’un bien immobilier**

### Modèles testés

* Random Forest Classifier
* Decision Tree Classifier
* Logistic Regression

---

### Métriques utilisées

#### Accuracy

Pourcentage global de bonnes prédictions.

#### Precision

Qualité des prédictions réalisées.

#### Recall

Capacité à retrouver correctement chaque classe.

#### F1-score

Équilibre entre précision et rappel.

#### ROC-AUC

Mesure globale de la performance multiclasses.

---

##  Analyse des résultats

Une analyse détaillée est réalisée afin de :

### Comprendre les variables importantes

Les features les plus influentes sont analysées grâce à :

```text
Feature Importance
```

Variables souvent importantes :

* surface ;
* nombre de chambres ;
* localisation ;
* équipements.

---

### Étudier les erreurs de prédiction

Le projet identifie :

* les prédictions incorrectes ;
* les cas difficiles ;
* les anomalies potentielles.

---

### Comparer les modèles

Les différents modèles sont comparés selon leurs performances afin de sélectionner la meilleure approche.

---

##  Optimisation des modèles

Le projet inclut également :

* ajustement des hyperparamètres ;
* validation croisée ;
* amélioration des features ;
* réduction du surapprentissage.

---

##  Automatisation du pipeline

Le pipeline est entièrement reproductible :

```text
Extraction OBT
      ↓
Préprocessing
      ↓
Training
      ↓
Evaluation
      ↓
Export modèle
```

L’objectif est de permettre une réexécution automatique à partir du Data Warehouse.

---

##  Technologies utilisées

* Python
* PostgreSQL
* SQLAlchemy
* Pandas
* NumPy
* Scikit-Learn
* Imbalanced-Learn (SMOTE)
* Joblib



##  Auteur

Projet réalisé dans le cadre d’un projet de **Data Engineering & Machine Learning Immobilier – Avito Maroc**.
