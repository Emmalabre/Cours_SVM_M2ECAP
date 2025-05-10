
<h1 align="center">
  <b>Prédiction du statut tabagique à partir de données biologiques issues d’examens de santé en Corée du Sud (2002–2015)</b>
</h1>

<p align="center">
  PALACIO Isabel 
</p>
<p align="center">
  LABRE-BLANC Emma
</p>
<p align="center">
  Master 2 Econométrie et Statistiques Appliquées
</p>

<br><br>

# Sommaire

- [Introduction](#introduction)
- [I. Analyse exploratoire](#i-analyse-exploratoire)
  - [A. Présentation des données](#a-Présentation-des-données)
  - [B. Analyse univariée](#b-Analyse-univariée)
  - [C. Analyse bivariée](#c-Analyse-bivariée)
- [II. Phase préparatoire et modélisations](#ii-Phase-préparatoire-et-modélisations)
  - [A. Préparation des variables pour la modélisation](#a-Préparation-des-variables-pour-la-modélisation)
  - [B. Modélisations](#b-Modélisations)
- [III. Interprétation du meilleur modèle](#iii-interprétation-du-meilleur-modèle)
  - [A. Interprétation globale](#a-interprétation-globale)
  - [B. Interprétation locale](#b-interprétation-locale)
- [Conclusion](#conclusion)
- [Glossaire](#Glossaire)
- [Table des matières](#Table-des-matières)
</p>

<br> <br>

# Introduction

<p align="justify"> Le tabagisme est l’un des principaux facteurs de risque pour de nombreuses maladies chroniques, notamment les pathologies cardiovasculaires, les cancers et les maladies respiratoires. Identifier les individus fumeurs à partir de données médicales pourrait permettre de mieux cibler les efforts de prévention, en particulier lorsque les données déclaratives ne sont pas disponibles ou peu fiables. </p> 
<p align="justify"> Ce projet a pour objectif de développer un modèle prédictif capable de déterminer si une personne fume ou non à partir de variables biologiques et cliniques issues d’examens de santé. Les données utilisées proviennent d’un échantillon de la base de données nationale coréenne d’assurance maladie, couvrant les années 2002 à 2015. Elles incluent des informations variées telles que le cholestérol, la glycémie, la pression artérielle, le taux de créatinine ou encore l'acuité visuelle. </p> 
<p align="justify"> Nous commencerons par une analyse exploratoire des données pour en comprendre la structure, détecter les éventuelles anomalies et identifier les variables les plus discriminantes. Ensuite, nous testerons plusieurs modèles de classification (SVM, Random Forest…) afin de sélectionner le plus performant. Une phase d’optimisation des hyperparamètres permettra d’affiner les performances des modèles retenus. Enfin, nous analyserons les résultats à l’aide de techniques d’explicabilité telles que Permutation Feature Importance ou SHAP, dans le but de mieux comprendre l’influence de chaque variable biologique sur la prédiction du statut tabagique. L’objectif final est d’élaborer un modèle robuste, interprétable, et capable de prédire avec fiabilité si un individu est fumeur ou non, à partir de ses données médicales. </p>

# I. Analyse exploratoire

<p align="justify"> La première étape de notre étude consiste à prendre connaissance des données disponibles. Cela nous permettra de comprendre la structure du jeu de données, d’identifier d’éventuelles anomalies ou valeurs manquantes, et de repérer les tendances générales ou les relations significatives entre les variables. Cette exploration est essentielle pour guider et optimiser les modélisations à venir. </p> 
<p align="justify"> Cette section est structurée en trois sous-parties. Nous commencerons par une présentation générale du jeu de données, incluant le type et la description des variables, ainsi que les traitements préliminaires effectués. Ensuite, une analyse univariée permettra d’étudier individuellement chaque variable à travers des visualisations et des statistiques descriptives. Enfin, une analyse bivariée visera à examiner les relations entre les variables explicatives et la variable cible (le statut tabagique), à l’aide de tests statistiques et de graphiques croisés. </p>


## A. Présentation des données

<p align="justify">Les données utilisées pour cette analyse proviennent du portail national coréen des données ouvertes : 
<a href="https://www.data.go.kr/data/15007122/fileData.do">data.go.kr</a>. 
Elles ont été publiées par le Service national d'assurance maladie (KHIC) et couvrent la période de 2002 a 2015. 
Ces données incluent des informations issues des examens de santé, des prescriptions médicales et de l'historique des soins pour un million d'assurés. 
Afin de garantir la confidentialité, les données personnelles sensibles ont été retirées ou anonymisées a l'aide de techniques telles que la discrétisation de l'âge ou le masquage des identifiants.</p>

<p align="justify">Nous utilisons ici un echantillon publié sur Kaggle, accessible via ce lien : 
<a href="https://www.kaggle.com/datasets/kukuroo3/body-signal-of-smoking/data">Kaggle - Body Signal of Smoking</a>. 
Cet échantillon permet de poser un problème de classification binaire : prédire si une personne fume (1 = non fumeur, 2 = fumeur) à partir de données biologiques et cliniques.</p>

<p align="justify">Le jeu de données contient 27 variables et 55 692 observations. Le tableau ci-dessous (<strong>Tableau 6</strong>) permet d'en avoir un aperçu : </p>


<p align="center"> <u>Tableau 1 : Présentation des variables </u></p>

<div align="center">

| Variable            | Description                                                                                     | Type / Unité                      |
|---------------------|-------------------------------------------------------------------------------------------------|-----------------------------------|
| `ID`                | Identifiant unique attribué à chaque individu                                                   | Identifiant (non informatif)      |
| `oral`              | Indique si l’individu a accepté de passer un examen buccodentaire                               | Catégorielle (N : non, Y : oui)        |
| `gender`            | Sexe biologique de l’individu                                                                   | Catégorielle (M : homme, F : femme) |
| `age`               | Tranche d’âge de l’individu, par intervalles de 5 ans ( si plus de 85 ans noté 85)                                          | Numérique (années)             |
| `height`            | Taille de l’individu par tranche de 5cm, exprimée en centimètres                                                   | Numérique (cm)                    |
| `weight`            | Poids de l’individu par tranche de 5kg, exprimé en kilogrammes                                                     | Numérique (kg)                    |
| `waist`             | Tour de taille, mesuré en centimètres                                                           | Numérique (cm)                    |
| `systolic`           | Pression artérielle systolique (pression maximale)                                              | Numérique (mmHg)                  |
| `relaxation`           | Pression artérielle diastolique (pression minimale)                                             | Numérique (mmHg)                  |
| `fasting blood sugar`              | Glycémie à jeun, taux de sucre dans le sang                                                     | Numérique (mg/dL)                 |
| `Cholesterol`         | Taux de cholestérol total                                                                       | Numérique (mg/dL)                 |
| `HDL`         | Taux de cholestérol HDL (le « bon » cholestérol)                                                | Numérique (mg/dL)                 |
| `LDL`         | Taux de cholestérol LDL (le « mauvais » cholestérol)                                            | Numérique (mg/dL)                 |
| `triglyceride`      | Taux de triglycérides dans le sang                                                              | Numérique (mg/dL)                 |
| `hemoglobin`               | Taux d’hémoglobine (pigments sanguins transportant l’oxygène)                                   | Numérique (g/dL)                  |
| `Urine protein`     | Niveau de protéines dans les urines (1 = normal, 6 = très élevé)                                | Catégorielle ordonnée (1 à 6)     |
| `serum creatinine`        | Taux de créatinine, indicateur de la fonction rénale                                            | Numérique (mg/dL)                 |
| `AST`               | Enzyme hépatique (Aspartate Aminotransférase), indicateur de santé hépatique                   | Numérique (UI/L)                  |
| `ALT`               | Enzyme hépatique (Alanine Aminotransférase), indicateur de santé hépatique                     | Numérique (UI/L)                  |
| `GTP`               | Gamma-GT, enzyme hépatique                              | Numérique (UI/L)                  |
| `hearing(left)`         | Résultat du test auditif pour l’oreille gauche (1 = normal, 2 = anormal)                        | Catégorielle binaire              |
| `hearing(right)`        | Résultat du test auditif pour l’oreille droite (1 = normal, 2 = anormal)                        | Catégorielle binaire              |
| `eyesight(left)`        | Acuité visuelle de l’œil gauche (exprimée entre 0.1 et 2.5 ; 9.9 = cécité)                      | Numérique                         |
| `eyesight(right)`       | Acuité visuelle de l’œil droit (exprimée entre 0.1 et 2.5 ; 9.9 = cécité)                       | Numérique                         |
| `dental caries`     | Présence de caries dentaires (0 = non, 1 = oui)                                                 | Binaire                           |
| `tartar`            | Présence de tartre (0 = non, 1 = oui)                                                           | Binaire                           |
| `smoking`           | Variable cible : statut tabagique (1 = non-fumeur, 2 = fumeur)   | Binaire     

</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">Afin de garantir la qualité et la cohérence des données avant toute analyse, plusieurs traitements préliminaires ont été effectués. Ces étapes ont permis de préparer le jeu de données pour l’analyse exploratoire et la modélisation.</p>

<p align="justify">Tout d’abord, les noms des colonnes ont été uniformisés. Les espaces ont été remplacés par des underscores (<code>_</code>) afin de faciliter leur manipulation dans l’environnement Python, notamment avec la bibliothèque pandas.</p>

<p align="justify">Ensuite, certaines variables ont été reclassées pour mieux refléter leur nature :</p>

<ul>
  <li align="justify"> <strong>gender</strong> a été recodée en une variable binaire indiquant si l’individu est un homme, puis convertie en type booléen.</li>
  <li align="justify">Les colonnes <strong>hearing_left</strong> et <strong>hearing_right</strong> ont été transformées en deux variables indicatrices distinctes : <strong>pb_hearing_left</strong> et <strong>pb_hearing_right</strong>, elles aussi converties en booléen.</li>
  <li align="justify">Les variables <strong>tartar</strong>, <strong>dental_caries</strong> et <strong>smoking</strong> ont également été converties en type booléen.</li>
  <li align="justify">La variable <strong>Urine_protein</strong>, qui présente des valeurs ordinales de 1 à 6, a été explicitement définie comme une variable catégorielle ordonnée.</li>
  <li align="justify">À la suite de ces transformations, les colonnes devenues redondantes (comme <strong>gender</strong> ou <strong>hearing_left</strong>) ont été supprimées.</li>
</ul>

<p align="justify">Une re-détection propre des types de variables a ensuite été réalisée, permettant de classer clairement chaque colonne en type numérique, catégoriel ou booléen. Cette étape facilite les traitements à venir et garantit une interprétation correcte des analyses statistiques.</p>

<p align="justify">Nous avons ensuite vérifié la présence de valeurs manquantes ou de doublons dans les données. Aucune valeur manquante n’a été détectée sur l’ensemble des colonnes, ce qui évite tout traitement d’imputation.</p>

<p align="justify">Concernant les doublons, bien que l’exécution de la commande <code>df.duplicated().sum()</code> retourne zéro doublon, une vérification plus approfondie — en excluant la colonne <strong>ID</strong> — a mis en évidence 11 140 enregistrements strictement identiques sur toutes les autres variables, peut être en raison d'un échantillonage aléatoire avec remise. Ces doublons ont été supprimés afin d’éviter tout biais dans l’analyse.</p>

<p align="justify">Enfin, deux colonnes ont été retirées du jeu de données : la colonne <strong>ID</strong>, qui ne constitue qu’un identifiant unique sans valeur informative pour l’analyse, et la colonne <strong>oral</strong>, qui ne contenait qu’une seule modalité partagée par tous les individus.</p>


## B. Analyse univariée

<p align="justify"> Après avoir effectué le prétraitement de nos données, nous avons procédé à une analyse univariée de l’ensemble des variables du jeu de données afin d'explorer les caractéristiques globales de la population étudiée, détecter d’éventuelles valeurs extrêmes, déséquilibres ou anomalies.


### 1. Variable d'intérêt

<p align="justify">
Nous débutons par l’analyse de la variable cible : le statut tabagique des individus. La distribution des classes est représentée par un histogramme (<strong>Figure 1</strong>).</p>

<p align="center"><u>Figure 1 : Distribution de la variable cible</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/graph_smoking.png" alt="Graphique de la variable cible">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>
 
<p align="justify">
Ce graphique met en évidence un déséquilibre modéré : sur les 44 552 individus de l’échantillon, 16 352 sont fumeurs (soit 37 %), contre 28 200 non-fumeurs. Ainsi, environ un tiers des observations sont associées à la classe « fumeur ».
</p>

<p align="justify">
Un taux de 37 % pour la classe minoritaire n'est pas considéré comme fortement déséquilibré en apprentissage automatique. Ainsi, dans un premier temps, nous procéderons à des modélisations sans rééchantillonnage. Cependant, nous envisagerons l'utilisation de méthodes de rééchantillonnage pour déterminer si cela peut améliorer les performances des modèles.</p>

### 2. Prédicteurs quantitatifs

<p align="justify"> L’analyse des variables quantitatives est résumée dans le tableau des statistiques descriptives (<strong>Tableau 2</strong>) ainsi que dans les histogrammes présentés en <strong>Figures 2</strong>.</p>

<p align="center"> <u>Tableau 2 : Statistiques descriptives des variables quantitatives </u></p>

<div align="center">

| Variable               | Count   | Mean       | Std        | Min   | 25%   | 50%   | 75%   | Max    |
|------------------------|---------|------------|------------|-------|-------|-------|-------|--------|
| age                    | 44552.0 | 44.210698  | 12.089196  | 20.0  | 40.0  | 40.0  | 55.0  | 85.0   |
| height(cm)             | 44552.0 | 164.657030 | 9.198674   | 130.0 | 160.0 | 165.0 | 170.0 | 190.0  |
| weight(kg)             | 44552.0 | 65.883462  | 12.823819  | 30.0  | 55.0  | 65.0  | 75.0  | 135.0  |
| waist(cm)              | 44552.0 | 82.077186  | 9.278384   | 51.0  | 76.0  | 82.0  | 88.0  | 129.0  |
| eyesight(left)         | 44552.0 | 1.011730   | 0.488136   | 0.1   | 0.8   | 1.0   | 1.2   | 9.9    |
| eyesight(right)        | 44552.0 | 1.008130   | 0.488767   | 0.1   | 0.8   | 1.0   | 1.2   | 9.9    |
| systolic               | 44552.0 | 121.529179 | 13.688876  | 71.0  | 112.0 | 120.0 | 130.0 | 240.0  |
| relaxation             | 44552.0 | 76.043320  | 9.695356   | 40.0  | 70.0  | 76.0  | 82.0  | 146.0  |
| fasting_blood_sugar    | 44552.0 | 99.320210  | 20.845547  | 46.0  | 89.0  | 96.0  | 104.0 | 505.0  |
| Cholesterol            | 44552.0 | 196.996005 | 36.423237  | 55.0  | 172.0 | 195.0 | 220.0 | 445.0  |
| triglyceride           | 44552.0 | 126.722257 | 71.612721  | 8.0   | 74.0  | 108.0 | 160.0 | 999.0  |
| HDL                    | 44552.0 | 57.288382  | 14.795399  | 4.0   | 47.0  | 55.0  | 66.0  | 618.0  |
| LDL                    | 44552.0 | 115.037978 | 40.938284  | 1.0   | 92.0  | 113.0 | 136.0 | 1860.0 |
| hemoglobin             | 44552.0 | 14.622235  | 1.564866   | 4.9   | 13.6  | 14.8  | 15.7  | 21.1   |
| serum_creatinine       | 44552.0 | 0.886104   | 0.226090   | 0.1   | 0.8   | 0.9   | 1.0   | 11.6   |
| AST                    | 44552.0 | 26.213795  | 19.087304  | 6.0   | 19.0  | 23.0  | 29.0  | 1311.0 |
| ALT                    | 44552.0 | 27.085002  | 31.755110  | 1.0   | 15.0  | 21.0  | 31.0  | 2914.0 |
| Gtp                    | 44552.0 | 40.066103  | 50.723940  | 1.0   | 17.0  | 26.0  | 44.0  | 999.0  |
</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

 <p align="center"><u>Figure 2 : Distribution des variables quantitatives</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/image2.png" alt="Graphique des variables quantitatives">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>


<p align="justify">Voici les principales observations : </p>

<p align="justify"><strong>Caractéristiques anthropométriques</strong></p>
<p align="justify"> L’âge moyen des individus est de 44 ans, avec une population allant de 20 à plus de 85 ans, ce qui constitue un échantillon adulte assez large. La taille moyenne est d’environ 1m65, pour un poids moyen de 66 kg. Le tour de taille moyen s’élève à 82 cm. </p>
<p align="justify"><strong>Systèmes cardiovasculaire et visuel</strong></p>
<p align="justify"> La tension artérielle moyenne est de 121,5 mmHg en systolique et 76 mmHg en diastolique. Toutefois, quelques valeurs aberrantes ont été relevées, comme une systolique à 240 mmHg ou une diastolique à 146 mmHg, probablement issues d’erreurs de saisie. L’acuité visuelle moyenne est proche de 1.0 pour chaque œil, mais certaines valeurs très élevées (jusqu’à 9.9) indiquent une cécité. </p>
<p align="justify"><strong>Variables biologiques (métaboliques)</strong></p>
<p align="justify"> La glycémie à jeun est en moyenne de 99 mg/dL, suggérant une population globalement saine. Toutefois, certaines valeurs très élevées (jusqu’à 505 mg/dL) sont préoccupantes et pourraient signaler des cas sévères de diabète non contrôlé. Le cholestérol total est en moyenne de 197 mg/dL, tandis que les triglycérides atteignent une moyenne de 127 mg/dL, avec une distribution très asymétrique (jusqu’à 999 mg/dL). </p> <p align="justify"> Le HDL (bon cholestérol) affiche une moyenne de 57 mg/dL, mais une valeur maximale incohérente à 618 mg/dL a été relevée. Le LDL (mauvais cholestérol), quant à lui, est en moyenne à 115 mg/dL, mais présente aussi une valeur extrême à 1860 mg/dL, vraisemblablement due à une erreur d’unité ou de saisie. </p>
<p align="justify"><strong>Marqueurs cliniques (sang, foie, reins)</strong></p>
<p align="justify"> L’hémoglobine a une moyenne de 14,6 g/dL, avec des extrêmes allant de 4,9 à 21,1 g/dL. La créatinine sérique, indicateur de la fonction rénale, est en moyenne à 0,89 mg/dL, mais atteint un maximum de 11,6 mg/dL, ce qui est fortement suspect sans contexte clinique particulier. </p> 
<p align="justify"> Les enzymes hépatiques AST, ALT et GTP présentent des moyennes situées dans les plages normales (entre 26 et 40 UI/L). Cependant, certaines valeurs sont exceptionnellement élevées : 2914 UI/L pour l’ALT, 999 UI/L pour le GTP, et 1311 UI/L pour l’AST. Bien que rares, ces cas pourraient s’expliquer par des pathologies sévères (hépatites, intoxications, etc.)</p>

<p align="justify">
Ainsi, comme nous l'avons constater, plusieurs variables présentent des valeurs aberrantes ou extrêmes, parfois très éloignées des plages physiologiques normales. Cela a été confirmé par le test des valeurs atypiques IQR (Un point est considéré comme un outlier s’il se situe en dehors de l’intervalle [Q1 - 1.5×IQR ; Q3 + 1.5×IQR])(<strong>Tableau 3</strong>).</p>

<p align="center"> <u>Tableau 3 : Outliers selon la méthode IQR </u></p>

<div align="center">


| Variable        | Outliers | Variable      | Outliers |
|--------------------------|----------|--------------------------|----------|
| age                      | 245      |Cholesterol               | 487      |
| height(cm)               | 203      | triglyceride              | 1802
| weight(kg)               | 160      |       HDL                       | 802   
| waist(cm)                | 414      |   LDL                       | 515
| eyesight(left)           | 1085     |       hemoglobin                | 616 
| eyesight(right)          | 1084     |      serum_creatinine          | 2535
| systolic                 | 555      |      AST                       | 2385 
| relaxation               | 565      |     ALT                       | 2979   
| fasting_blood_sugar      | 2657     |   Gtp                       | 3838 
</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>
  
<p align="justify">
  Un traitement spécifique sera donc appliqué lors de la phase de modélisation afin d’atténuer leur impact sans altérer la structure globale des données.
</p>

### 3. Prédicteurs qualitatifs

<p align="justify"> La distribution des variables qualitatives a été visualisée à l’aide de diagrammes en barres (<strong>Figure 3</strong>). </p>

<p align="center"><u>Figure 3 : Distribution des variables qualitatives</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/graph_quali.png" alt="Graphique des variables qualitatives">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>
  
  
  
<p align="justify">Cette analyse met en évidence plusieurs déséquilibres : </p> <ul> <li>64 % des individus de la base de données sont des hommes, indiquant une surreprésentation masculine.</li> <li>Il y a 37 % de fumeurs, comme vu précédemment.</li> <li>21 % de la population présentent des caries dentaires, tandis que 55 % a du tartre.</li> <li>Les troubles auditifs sont rares : environ 2,6 % pour chaque oreille.</li> <li>La variable Urine_protein est très déséquilibrée : plus de 94 % des individus sont au niveau 1.0, indiquant l’absence de protéinurie. Les niveaux supérieurs (jusqu’à 6.0) sont rares mais peuvent avoir une forte signification clinique.</li> </ul> </p>

<p align="justify">Cette analyse descriptive nous a permis de mieux comprendre la structure et les caractéristiques de notre jeu de données.
Elle révèle une population adulte majoritairement masculine, une proportion significative de fumeurs, et quelques variables présentant des valeurs extrêmes ou déséquilibrées.</p>


## C. Analyse bivariée

<p align="justify">Nous passons maintenant à l'analyse bivarié. Nous présenterons successivement les analyses bivariées entre la variable cible et les prédicteurs quantitatifs et qualitatifs puis l'étude des corrélations entre les variables quantitatives et enfin les liens entre les variables qualitatives.</p>

### 1. Liens entre la variable cible et les prédicteurs qualitatifs et quantitatifs

#### a) Liens entre la variable cible et les prédicteurs quantitatifs

<p align="justify"> Nous avons comparé les moyennes des variables numériques entre les individus fumeurs et non-fumeurs, ce qui a permis de dégager plusieurs différences significatives (<strong>Tableau 4</strong>) .</p>

<p align="center"><u>Tableau 4 : Moyenne des variables quantitatives en fonction de la variable cible</u></p>

<div align="center">

| Variable              | Non-fumeur (False) | Fumeur (True) |
|-----------------------|--------------------|---------------|
| age                   | 45.693972          | 41.652703     |
| height(cm)            | 161.899291         | 169.412916    |
| weight(kg)            | 62.959397          | 70.926186     |
| waist(cm)             | 80.490858          | 84.812904     |
| eyesight(left)        | 0.989656           | 1.049798      |
| eyesight(right)       | 0.984894           | 1.048202      |
| systolic              | 120.761809         | 122.852556    |
| relaxation            | 75.249149          | 77.412916     |
| fasting_blood_sugar   | 97.742376          | 102.041279    |
| Cholesterol           | 197.833262         | 195.552104    |
| triglyceride          | 113.040567         | 150.317148    |
| HDL                   | 59.270071          | 53.870841     |
| LDL                   | 116.441277         | 112.617906    |
| hemoglobin            | 14.148823          | 15.438662     |
| serum_creatinine      | 0.850078           | 0.948233      |
| AST                   | 25.343404          | 27.714836     |
| ALT                   | 24.802730          | 31.020915     |
| Gtp                   | 30.991773          | 55.715325     |
</div> 

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">Les fumeurs sont, en moyenne, plus jeunes, mais aussi plus grands, plus lourds et présentent un tour de taille plus élevé — des caractéristiques probablement liées à une surreprésentation masculine dans ce groupe. Leur tension artérielle (systolique et diastolique) est légèrement plus élevée, en lien avec les effets vasoconstricteurs de la nicotine, et leur glycémie à jeun tend à être supérieure, ce qui peut indiquer un risque accru de troubles métaboliques. Paradoxalement, leur cholestérol total est légèrement inférieur à celui des non-fumeurs. Toutefois, cette différence masque une baisse du HDL (le bon cholestérol), ce qui n’indique pas nécessairement un meilleur profil lipidique. En effet, un HDL plus bas est associé à un risque cardiovasculaire accru, et peut déséquilibrer le rapport LDL/HDL. Enfin, l'acuité visuelle moyenne des fumeurs apparaît légèrement meilleure. Ce résultat, contre-intuitif, pourrait s’expliquer par une différence d’âge : les fumeurs de l’échantillon étant en moyenne plus jeunes, ils sont naturellement moins touchés par la presbytie ou d'autres troubles visuels liés à l’âge.</p>
<p align="justify">Ces écarts suggèrent une association entre le statut tabagique et certaines caractéristiques physiologiques, cohérente avec les effets connus du tabac sur la santé métabolique et cardiovasculaire. </p> 
<p align="justify"> De plus, une analyse de variance (ANOVA) a été conduite pour tester les différences moyennes entre fumeurs et non-fumeurs pour chaque variable continue. Les résultats indiquent que l’ensemble des variables analysées présentent des p-values significatives, suggérant des différences statistiquement robustes entre les deux groupes. </p>
<p align="justify">
Par ailleurs, la variable taille (height(cm)) présente une forte association avec le statut de fumeur. Toutefois, cette relation doit être interprétee avec prudence, car d'un point de vue biologique, la taille n'a pas de lien direct ou plausible avec le tabagisme. Il s'agit donc très probablement d'un effet de confusion, en particulier lié au sexe : les hommes sont en moyenne plus grands que les femmes et ils sont également plus souvent fumeurs. La taille sert alors de proxy implicite pour le genre. Ce type de biais est important à identifier car il peut fausser l'interprétation des modèles. Nous décidons alors de ne pas conserver la variable height(cm) pour la modélisation, d'autant plus que sa présence induit de la multicolinéarité.</p>


#### b) Liens entre la variable cible et les prédicteurs qualitatifs

<p align="justify"> Nous avons également exploré les associations entre la variable cible et les prédicteurs qualitatifs (<strong>Figure 4</strong>) : </p>
  
<p align="center"><u>Figure 4 : Distribution des variables qualitatives en fonction de la variable cible</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/graph_ciblequali.png" alt="Graphique des variables qualitatives avec cible">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>
  
  
 <p align="justify"> Les diagrammes en barres mettent en évidence des tendances marquées : les fumeurs sont plus nombreux à présenter du tartre et des caries dentaires, et la majorité des fumeurs sont des hommes. Ces éléments pourraient refléter des comportements ou des facteurs sociaux liés au tabagisme. </p> <p align="justify"> Ces observations visuelles ont été confirmées par un test du chi², réalisé pour chacune des variables catégorielles. Les résultats révèlent que les variables <em>sex, dental_caries, tartar, pb_hearing (gauche et droite), urine_protein</em> présentent toutes des p-values significatives, confirmant l’existence de dépendances entre ces variables et le statut tabagique. Elles pourront donc être considérées comme prédicteurs pertinents dans la phase de modélisation. </p>

### 2. Etude des corrélations

<p align="justify"> L’analyse des corrélations entre variables continues nous a permis d’identifier des redondances potentielles (<strong>Figure 5</strong>) : </p>

<p align="center"> <u>Figure 5 : Corrélations (Spearman) entre les variables quantitatives</u> </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/corr.png" alt="Corrélations">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>
  
 <p align="justify"> À partir de la matrice de corrélation de Spearman, qui tient compte des relations monotones, nous avons mis en évidence plusieurs associations fortes (ρ ≥ 0.70), illustrées en <strong>Figure 5</strong>. </p> <p align="justify"> Les principales corrélations observées sont les suivantes : <ul> <li><strong>Poids et tour de taille</strong> : ρ = 0.81 — forte corrélation attendue</li> <li><strong>Acuité visuelle gauche/droite</strong> : ρ = 0.70 — logique biométrique</li> <li><strong>Tension systolique/diastolique</strong> : ρ = 0.74 — pression artérielle intrinsèquement liée</li> <li><strong>Cholestérol total et LDL</strong> : ρ = 0.89 — le LDL étant une composante du cholestérol total</li> <li><strong>AST et ALT</strong> : ρ = 0.73 — enzymes hépatiques souvent corrélées</li> </ul> </p> <p align="justify"> L’analyse de la matrice de corrélation de Pearson, axée sur les relations linéaires, a également confirmé ces dépendances. Ces résultats nous permettent d’identifier des variables fortement liées entre elles et nous questionnent sur le besoin ou non de sélectionner des variables pour nos modélisations. Nous aborderons ce point dans la partie concernant l'optimisation des modèles.

### 3. Liens entre les variables qualitatives

<p align="justify"> Enfin, nous avons étudié les liens entre variables qualitatives à l’aide de tests du chi² sur toutes les paires de variables. Plusieurs associations significatives ont été détectées, indiquant que certaines variables partagent une information redondante. </p> 
<p align="justify"> Un exemple particulier concerne les variables <strong>pb_hearing (left)</strong> et <strong>pb_hearing (right)</strong>, qui sont fortement liées. Toutefois, une analyse plus fine a montré que les modalités diffèrent pour 1 107 individus, ce qui justifie la conservation des deux variables dans l’analyse, du moins dans un premier temps. </p>

<p align="justify"> Finalement, de manière générale, nous avons choisi de conserver l’ensemble des variables à ce stade, afin de ne pas écarter prématurément des informations potentiellement utiles. Une sélection de variables (via des méthodes automatiques ou empiriques) sera envisagée ultérieurement dans la phase de modélisation, pour optimiser la performance et la robustesse de nos modèles. </p>


# II. Phase préparatoire et modélisations

<p align="justify"> Après avoir exploré les caractéristiques individuelles et les relations entre variables, nous passons désormais à la phase de modélisation. Celle-ci se déroulera en deux temps : dans un premier temps, nous appliquerons un ensemble de traitements aux variables afin de les préparer correctement à l’apprentissage automatique (encodage, mise à l’échelle, traitement des valeurs extrêmes, etc.). Ensuite, nous testerons plusieurs modèles de classification afin de comparer leurs performances et de sélectionnder le meilleur modèle. </p>

## A. Préparation des variables pour la modélisation

### 1) Séparation du jeu de données en jeux train et test

<p align="justify"> Afin de pouvoir évaluer la capacité de généralisation de nos modèles, nous avons divisé notre jeu de données en deux ensembles distincts en utilisant la fonction <code>train_test_split</code> de <code>sklearn.model_selection</code> avec un ratio de 80 % des données pour l’entraînement et 20 % pour le test. Cette séparation permet d'éviter les biais d'optimisme et de s’assurer que les modèles ne sont pas uniquement adaptés aux données vues pendant l'entraînement. </p>

<p align="justify">De plus, nous avons effectué cette séparation avant les autres opérations de transformation (standardisation, winsorization) pour éviter tout data leakage, c’est-à-dire que des informations statistiques issues du test influencent l’entraînement. Tous les prétraitements ultérieurs sont ainsi appris uniquement à partir des données d'entraînement, puis appliqués aux données de test. </p>

### 2) Traitement des valeurs atypiques - winsorization

<p align="justify"> Comme nous avons pu précedemment le constater, plusieurs variables numériques présentent des valeurs atypiques, parfois cliniquement peu plausibles, susceptibles de perturber l'entraînement des modèles. Pour limiter leur influence sans exclure d'observations, nous avons appliqué une technique de <strong>winsorization</strong> à l'ensemble des variables suivantes : <em>'ALT', 'AST', 'Gtp', 'serum_creatinine', 'LDL', 'HDL', 'triglyceride', 'Cholesterol', 'fasting_blood_sugar', 'systolic', 'relaxation', 'hemoglobin'</em>. Cette méthode consiste à ramener les valeurs situées en dehors des 1er et 99e percentiles à ces bornes, de manière à réduire l'effet des outliers tout en conservant la distribution générale. </p> 
<p align="justify"> Les variables <em>'age', 'weight', 'height', 'waist(cm)'</em> ainsi que <em>'eyesight(left)'</em> et <em>'eyesight(right)'</em> n'ont pas été winsorisées : bien que certaines valeurs aient été identifiées comme atypiques par des tests statistiques (comme le test ESD), elles restent cohérentes. </p> 
<p align="justify"> Les seuils de winsorization ont été calculés exclusivement sur le jeu d’entraînement, puis appliqués tels quels au jeu de test, afin de respecter les bonnes pratiques d'apprentissage automatique et d’éviter toute fuite d’information (<em>data leakage</em>). Cette approche contribue à stabiliser les performances des algorithmes sensibles aux valeurs extrêmes. </p>

### 3) Standardisation et encodage

<p align="justify"> Pour que chaque variable quantitative contribue de manière équivalente aux algorithmes, et notamment aux modèles sensibles à l’échelle (comme les SVM ou la régression logistique), nous avons procédé à une standardisation. Celle-ci consiste à recentrer chaque variable (moyenne nulle) et à la réduire (écart-type unitaire). Pour ce faire, nous avons utilisé la fonction <code>StandardScaler</code> de <code>sklearn.preprocessing</code>. Comme pour les étapes précédentes, les paramètres de standardisation sont appris uniquement sur l'ensemble d’entraînement, puis appliqués au jeu de test.</p>

<p align="justify"> Concernant les variables qualitatives, la seule variable catégorielle nécessitant un encodage est urine_protein, considérée comme ordinale. En effet, les autres variables qualitatives présentes dans notre jeu de données sont déjà représentées sous forme booléenne (0/1), ce qui les rend directement exploitables par les algorithmes. Pour cette variable ordinale, nous avons appliqué un encodage à l’aide de la classe <code>OrdinalEncoder</code> de <code>sklearn</code>, qui transforme chaque modalité selon l’ordre naturel de la sévérité.</p>

<p align="justify">Cette étape à été réalisé grâce à la pipeline ColumnTransformer qui permet d’appliquer des transformations spécifiques à chaque type de variable.</p>

## B. Modélisations

<p align="justify">Nous passons à présent à la partie dédiée aux modélisations. Nous commencerons par présenter les modèles testés ainsi que les métriques utilisées pour les comparer. Nous procéderons ensuite à une première série de modélisations en utilisant les paramètres par défaut des algorithmes, avant de chercher à optimiser les performances des modèles les plus prometteurs, puis de sélectionner le modèle retenu.</p> 

### 1) Présentation des modèles et métriques pour la comparaison

<p align="justify"> Dans le but de prédire le <strong>statut tabagique</strong> des individus (variable cible : smoking), nous avons entraîné et comparé plusieurs modèles de classification binaire : </p> <ul align="justify"> <li><strong>Régression logistique</strong> : modèle linéaire paramétrique, simple, rapide à entraîner et aisément interprétable. Il constitue un excellent <em>modèle de référence</em> pour évaluer la linéarité entre les variables prédictives et la variable cible.</li> <li><strong>Random Forest</strong> : modèle non paramétrique basé sur un ensemble d’arbres de décision construits aléatoirement. Il est capable de capturer des interactions non linéaires, de gérer des variables hétérogènes (numériques, catégorielles, ordinales) et se montre robuste face aux valeurs aberrantes.</li> <li><strong>Gradient Boosting</strong> : modèles puissants d’<em>ensemble learning</em> qui construisent séquentiellement des arbres pour corriger les erreurs des précédents. Très performants sur des relations complexes et adaptées à des effets faibles répartis sur de nombreuses variables.</li> <li><strong>SVM (Support Vector Machines)</strong> avec différents noyaux (<em>linéaire</em>, <em>RBF</em>, <em>polynomial</em>) : ces modèles cherchent à maximiser la marge entre les classes. Le noyau linéaire permet de tester la séparabilité linéaire des données, tandis que les noyaux RBF et polynomial permettent de modéliser des frontières de décision plus complexes en projetant les données dans des espaces de dimension supérieure.</li> <li><strong>Classifieurs linéaires</strong> : <ul> <li><strong>LinearSVC</strong> : une implémentation optimisée du SVM linéaire, adaptée aux grands jeux de données.</li> <li><strong>SGDClassifier</strong> : algorithme basé sur la descente de gradient stochastique, très rapide, particulièrement efficace pour les jeux de données volumineux ou en streaming.</li> </ul> </li> </ul> 
<p align="justify"> Pour évaluer la performance de ces modèles, nous avons utilisé différentes métriques d’évaluation, mesurées à la fois : </p> <ul align="justify"> <li>Sur les jeux d'entraînement et de test pour évaluer leur capacité de généralisation.</li> <li>En validation croisée (5-fold) pour estimer la robustesse et la stabilité des performances.</li> </ul> 
<p align="justify">Les résultats ont été synthétisés sous forme de tableaux et de graphiques comparatifs.</p> <h4><u align="justify">Métriques utilisées</u> :</h4> <ul align="justify"> <li><strong>Accuracy</strong> : proportion globale de bonnes prédictions (vrais positifs + vrais négatifs). Elle répond à la question : <em>« À quelle fréquence le modèle est-il correct ? »</em></li> <li><strong>Erreur (1 - accuracy)</strong> : proportion d’observations mal classées. Elle permet de mesurer l'inefficacité du modèle. Un bon modèle aura une <strong>accuracy proche de 1</strong> et une erreur proche de 0.</li> <li><strong>Précision</strong> : parmi les individus prédits comme fumeurs, quelle proportion l’est réellement ? Cette métrique est sensible aux <em>faux positifs</em>.</li> <li><strong>Rappel (Recall)</strong> : parmi les fumeurs réels, combien ont été correctement identifiés ? Cette métrique est sensible aux <em>faux négatifs</em>.</li> <li><strong>F1-score</strong> : moyenne harmonique entre précision et rappel. Elle est particulièrement utile lorsque les classes sont déséquilibrées (ce qui est modérément le cas ici). Un bon modèle affiche un F1-score élevé, proche de 1.</li> </ul> 
<p align="justify"> L’objectif de cette évaluation multiple est de ne pas se limiter à l’accuracy, qui peut être trompeuse en cas de déséquilibre de classes, Une forte accuracy peut masquer des performances médiocres sur la classe minoritaire, pourtant essentielle à détecter dans notre étude. C’est pourquoi d’autres métriques sont mobilisées afin de mieux comprendre les compromis entre faux positifs et faux négatifs. </p>
<p align="justify"> Ainsi, une attention particulière est portée au <strong>f1-score</strong> et au <strong>recall</strong>, car il est crucial d’<strong>identifier correctement les individus fumeurs</strong>, même au prix de quelques faux positifs. En effet, dans un contexte de prévention ou de dépistage médical, il est souvent préférable de <strong>ne pas rater un cas à risque</strong> (fumeur non détecté), quitte à déclencher des vérifications supplémentaires. </p>

### 2) Modélisations avec les paramètres par défaut

<p align="justify">Nous avons commencé par tester les modèles sans optimisation, avec leurs paramètres par défaut. Cela nous a permis d’identifier ceux qui étaient les plus prometteurs dans notre contexte.</p> <p>

<p align="justify">
Les résultats initiaux ont été obtenus sans validation croisée, en comparant directement les performances sur les jeux d'entraînement et de test. Cela permettait de détecter des cas de surapprentissage évident, notamment pour le modèle <strong>Random Forest</strong>, dont le F1-score chute de 0.999 sur le jeu d’entraînement à 0.677 sur le jeu de test, traduisant une très mauvaise généralisation. À l’inverse, les modèles comme le <strong>Gradient Boosting</strong> ou le <strong>LinearSVC</strong> présentent des scores cohérents entre entraînement et test, ce qui témoigne d’une meilleure robustesse (<strong>Tableau 5</strong>).
</p>

<p align="center"> <u>Tableau 5 : Performances des modèles non optimisés </u></p>

<div align="center">


| Modèle               | Données | Accuracy  | Erreur    | Précision | Recall   | F1-score |
|----------------------|---------|-----------|-----------|-----------|----------|----------|
| Logistic Regression  | Train   | 0.722062  | 0.277938  | 0.633442  | 0.576908 | 0.603855 |
| Logistic Regression  | Test    | 0.727079  | 0.272921  | 0.638880  | 0.586830 | 0.611750 |
| Random Forest        | Train   | 0.999972  | 0.000028  | 1.000000  | 0.999924 | 0.999962 |
| Random Forest        | Test    | 0.758276  | 0.241724  | 0.662760  | 0.692802 | 0.677448 |
| Gradient Boosting    | Train   | 0.769619  | 0.230381  | 0.673080  | 0.724459 | 0.697825 |
| Gradient Boosting    | Test    | 0.763551  | 0.236449  | 0.664769  | 0.715467 | 0.689187 |
| SVC (RBF Kernel)     | Train   | 0.713532  | 0.286468  | 0.669495  | 0.434171 | 0.526745 |
| SVC (RBF Kernel)     | Test    | 0.714959  | 0.285041  | 0.668526  | 0.440429 | 0.531019 |
| SVC (Linear Kernel)  | Train   | 0.745602  | 0.254398  | 0.640894  | 0.698632 | 0.668519 |
| SVC (Linear Kernel)  | Test    | 0.753226  | 0.246774  | 0.648056  | 0.714548 | 0.679680 |
| SVC (Poly Kernel)    | Train   | 0.724250  | 0.275750  | 0.679678  | 0.471002 | 0.556418 |
| SVC (Poly Kernel)    | Test    | 0.729884  | 0.270116  | 0.691347  | 0.474732 | 0.562920 |
| LinearSVC            | Train   | 0.745378  | 0.254622  | 0.639752  | 0.701689 | 0.669290 |
| LinearSVC            | Test    | 0.753675  | 0.246325  | 0.647140  | 0.720674 | 0.681930 |
| SGDClassifier        | Train   | 0.636177  | 0.363823  | 0.889610  | 0.010468 | 0.020693 |
| SGDClassifier        | Test    | 0.636853  | 0.363147  | 0.784314  | 0.012251 | 0.024125 |

</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">
Pour affiner cette analyse, une validation croisée à 5 plis a été réalisée afin d’estimer la stabilité des modèles et leur capacité de généralisation sur des sous-ensembles différents du jeu de données. Les résultats par pli et les statistiques sont présentés dans le <strong>Tableau 6</strong> : 
</p>

<p align="center"> <u>Tableau 6 : Performances en cross-validation des modèles non optimisés </u></p>

<div align="center">

| Graphique F1-score | Moyenne et écart-type des F1-score |
|-----------------------------------------------|---------------------------------------------|
| <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/raw/main/Screenshots/graph_cv.png" width="1500"/> | <table><thead><tr><th>Modèle</th><th>Moyenne</th><th>Écart-type</th></tr></thead><tbody><tr><td>Logistic Regression</td><td>0.5791</td><td>0.0047</td></tr><tr><td>Random Forest</td><td>0.6681</td><td>0.0010</td></tr><tr><td>Gradient Boosting</td><td>0.6839</td><td>0.0034</td></tr><tr><td>SVC (RBF)</td><td>0.5184</td><td>0.0058</td></tr><tr><td>SVC (Linear)</td><td>0.6687</td><td>0.0058</td></tr><tr><td>SVC (Poly)</td><td>0.5445</td><td>0.0072</td></tr><tr><td>LinearSVC</td><td>0.6699</td><td>0.0063</td></tr><tr><td>SGDClassifier</td><td>0.1947</td><td>0.2478</td></tr></tbody></table> |
</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>


<p align="justify">
Le Gradient Boosting est le modèle le plus performant en validation croisée, avec un F1-score moyen de 0.684 et une variance très faible, ce qui témoigne de sa stabilité. Il surpasse le Random Forest et LinearSVC sur cette base. Le SGDClassifier, en revanche, affiche un F1-score moyen de seulement 0.195 avec un écart-type extrêmement élevé de 0.25, montrant une grande instabilité et des performances peu fiables.
</p>

<p align="justify">
Compte tenu de ces résultats, nous avons décidé de retenir uniquement les modèles <strong>Gradient Boosting</strong> et <strong>LinearSVC</strong> pour la suite de l’étude et les étapes d’optimisation. Ce choix repose à la fois sur leurs bonnes performances sur les jeux de test, leur stabilité démontrée par la validation croisée, ainsi que sur des considérations pratiques : limiter à deux modèles permet de réduire le temps de calcul tout en concentrant les efforts sur les méthodes les plus prometteuses.
</p>


### 3) Modélisations optimisées - Présentation des stratégies d optimisation

<p align="justify">Afin d’optimiser les performances des deux modèles retenus, nous avons plusieurs stratégies :</p> <ul align="justify"> <li><strong>Recherche d’hyperparamètres</strong> (GridSearchCV)</li> <li><strong>Resampling</strong></li> <li><strong>Sélection de variables</strong></li><li><strong>Transformations log</strong></li><li><strong>Combinaisons de ces stratégies</strong></li></ul>
<p align="justify">Cette approche nous permet d’évaluer si un modèle sans optimisation suffit ou si la recherche d’hyperparamètres, le resampling, la sélection de variables ou les transformations log améliorent significativement les résultats, notamment en termes de rappel.</p>

<strong>Pourquoi envisager une méthode de resampling : </strong>
<p align="justify">Le jeu de données présente un déséquilibre de classes : environ 37 % de fumeurs contre 63 % de non-fumeurs. Ce déséquilibre peut biaiser les modèles, qui auront tendance à prédire la classe majoritaire pour maximiser l’accuracy, au détriment de la détection des fumeurs.</p> 
<p align="justify">Pour corriger cela, deux options ont été envisagées :</p> <ul> <li><strong>Oversampling</strong> : duplication d’exemples de la classe minoritaire (fumeurs). Risque : surapprentissage.</li> <li><strong>Undersampling</strong> : réduction du nombre d’exemples de la classe majoritaire (non-fumeurs). Risque : perte d’information.</li> </ul> 
<p align="justify">Nous avons retenu l’<strong>undersampling</strong>, notre dataset étant suffisamment grand pour rendre cette perte acceptable. Le resampling a été appliqué uniquement sur l’échantillon d’entraînement, avant la validation croisée, en utilisant le recall comme critère d’optimisation.</p> 

<strong>Pourquoi envisager une méthode de sélection de variable :</strong>
<p align="justify">Certaines variables montraient des corrélations fortes ou des redondances, ce qui soulevait la question de leur utilité dans le modèle. Afin de simplifier les modèles, d’en améliorer l’interprétabilité et de limiter les effets de multicolinéarité, nous avons appliqué une sélection automatique de variables.</p> 
<p align="justify">Cette sélection a été intégrée dans un pipeline combiné avec GridSearchCV, permettant d’optimiser simultanément les hyperparamètres et le sous-ensemble de variables retenu.</p>

### 4) Choix du meilleur modèle

<p align="justify">
Après avoir testé un large éventail de modèles de classification, incluant des versions non optimisées, des modèles avec recherche d’hyperparamètres (GridSearchCV), des variantes intégrant un rééquilibrage de classes (<em>undersampling</em>) et des approches combinées à une sélection automatique de variables, le modèle présentant les meilleures performances globales est le <strong>Gradient Boosting avec undersampling</strong> (sans gridsearch et sans transformation logarithmique). Il atteint un recall de 0.8919= et un F1-score de 0.7201, surpassant ainsi les autres modèles, qu’il s’agisse de la régression logistique (F1-score ≈ 0.611), de LinearSVC (≈ 0.682), du Gradient Boosting non optimisé (≈ 0.689) ou même des modèles combinant undersampling, grid search ou sélection de variables.
</p>

<p align="justify">
L’intégration de l’undersampling</strong> a joué un rôle important : elle a permis de réduire le déséquilibre entre classes et d’améliorer considérablement le rappel, en diminuant les cas de fumeurs non détectés. À l’inverse, la sélection de variables ou l'utilisation du gridsearch n’ont pas permis de dépasser les scores obtenus avec l’undersampling seul, ce qui confirme l’importance du rééquilibrage des données.
</p>

<p align="justify">
Une version alternative avec transformation logarithmique des variables numériques (<strong>GradientBoostong + undersampling + log</strong>) a également été envisagée et a permis d’améliorer légèrement le <strong>recall</strong> (91,4 % vs 89,2 %) et de réduire les faux négatifs. Toutefois, cette amélioration se fait au prix d’une précision plus faible</strong> (≈ 0.588 vs 0.604) et d’un nombre accru de faux positifs</strong> (2 092 contre 1 910). Ce compromis n’a pas été jugé acceptable ici, car il dégrade l’équilibre général du modèle, en particulier si l’on considère les coûts potentiels d’un surdépistage.
</p>

<p align="justify">
Dans le cadre de cette étude, où l’objectif est de détecter efficacement les fumeurs pour mieux orienter les actions de santé publique, le modèle Gradient Boosting avec undersampling sans transformation log, sans gridsearch et sans features selection représente le meilleur compromis entre rappel élevé, précision acceptable, et taux de faux positifs maîtrisé. Il permet d’atteindre une couverture satisfaisante sans générer un nombre excessif d’erreurs coûteuses.
</p>

<p align="justify">
Ce choix reste toutefois contextuel : en dépistage ou prévention précoce, ce type de modèle reste justifié. Mais dans des cas plus sensibles — décisions médicales lourdes, stigmatisation, ou applications externes comme l’évaluation de risque pour des assurances —, il serait préférable d’envisager des modèles à plus forte précision, ou d’ajuster les seuils de classification pour limiter les faux positifs.
</p>

<p align="justify">
Les performances des modèles LinearSVC et Gradient Boosting,  optimisés et non optimisés sont présentées dans le tableau ci-dessous (<strong>Tableau 7</strong>).
</p>

<p align="center"> <u>Tableau 7 : Performances des différents modèles </u></p>

<div align="center">

| Nom                                             | Recall   | F1-score | Accuracy | Precision | Best Params                                                                 |
|--------------------------------------------------|----------|----------|----------|-----------|------------------------------------------------------------------------------|
| LinearSVC - Sans optimisation                      | 0.720674 | 0.681930 | 0.753675 | 0.647140  | /                                                              |
| LinearSVC - GridSearch seul                      | 0.722818 | 0.682278 | 0.753339 | 0.646044  | {'model__C': 1}                                                              |
| LinearSVC - Undersampling seul                   | 0.937519 | 0.707991 | 0.716642 | 0.568748  | C=1 (défaut)                                                                 |
| LinearSVC - GridSearch + Undersampling           | 0.937519 | 0.708237 | 0.716979 | 0.569065  | {'model__C': 0.01}                                                           |
| LinearSVC - Gridsearch + Sélection de variables               | 0.719755 | 0.681653 | 0.753675 | 0.647383  | C=0.01, vars: ['systolic', 'respiratory', 'alcohol', 'age', 'cholesterol']  |
| Gradient Boosting - Sans optimisation              | 0.715467 | 0.689187 | 0.763551 | 0.664769  | / |
| Gradient Boosting - GridSearch seul              | 0.714548 | 0.688404 | 0.762990 | 0.664105  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
| Gradient Boosting - GridSearch seul + sélection   | 0.713016 |0.686017 | 0.760857 | 0.660988  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
| <strong>Gradient Boosting - Undersampling seul </strong>          | <strong>0.891884</strong> | <strong>0.720168</strong> | <strong>0.746044</strong> | <strong>0.603899</strong>  | <strong>default                                                          </strong>            |
| Gradient Boosting - Undersampling seul + sélection  | 0.715773 |0.689584 | 0.763887 | 0.665243  | default                                                                      |
| Gradient Boosting - GridSearch + Undersampling   | 0.913936 | 0.715931 | 0.734261 | 0.588444  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
| Gradient Boosting -  GridSearch + Undersampling + sélection      | 0.715161 | 0.685354 | 0.759398 | 0.657932  | {'learning_rate': 0.05, 'max_depth': 3, 'n_estimators': 100}                |
| Gradient Boosting - GridSearch + Undersampling + log | 0.914242 | 0.715656 | 0.733812 | 0.587946  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
</div> 

 <p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

# III. Interprétation du meilleur modèle

<p align="justify">
Dans cette troisième et dernière partie, nous nous attachons à interpréter le modèle sélectionné, à savoir le Gradient Boosting avec resampling (sans GridSearch ni sélection de variables).  
Nous proposons tout d'abord une analyse globale de son fonctionnement afin d’identifier les variables les plus influentes dans les prédictions, avant de passer à une interprétation locale visant à mieux comprendre certains cas spécifiques.
</p>

## A. Interprétation globale

### 1) Importance des variables

<p align="justify">
Le graphique représentant l’importance des variables issu du modèle Gradient Boosting avec undersampling permet de visualiser quelles caractéristiques ont le plus contribué à la prédiction du statut de fumeur. Cette importance ne reflète pas la direction de l'effet (positive ou négative), mais indique dans quelle mesure chaque variable a été utilisée par le modèle pour améliorer sa capacité de prédiction. Donc, plus une variable a une importance élevée, plus elle a été jugée utile pour différencier les fumeurs des non-fumeurs dans les arbres de décision constituant le modèle (<strong>Figure 6</strong>).</p>

<p align="center"> <u>Figure 6 : Importance des variables dans la modélisation Gradient Boosting avec resample </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/features_importance.png" alt="Importance">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">
Les résultats montrent que la variable la plus influente est le sexe, de très loin, ce qui souligne un lien fort entre genre et comportement tabagique dans l’échantillon étudié. D’autres variables biologiques importantes émergent, comme la gamma-glutamyl transpeptidase (Gtp), les triglycérides, les enzymes hépatiques (ALT, AST), ou encore l’hémoglobine, suggérant que le tabagisme pourrait être associé à des altérations métaboliques ou hépatiques détectables dans les données cliniques. Des facteurs liés à la santé bucco-dentaire (dental_caries, tartar) apparaissent également comme discriminants. À l’inverse, certaines variables comme les troubles de l’audition, la vision, ou la protéinurie (Urine_protein) semblent avoir une importance négligeable.</p>

### 2) Partial Dependence Plots - PDP

<p align="justify">
Nous allons à présent nous intéresser aux Partial Dependence Plots (PDP), en focalisant notre attention sur les variables identifiées comme les plus importantes par le modèle de Gradient Boosting avec undersampling. Le but de cette visualisation est de mieux comprendre comment chaque variable influence, en moyenne, la prédiction du modèle, toutes choses égales par ailleurs.</p>

<p align="justify">
Les PDP permettent ainsi de représenter la relation marginale entre une variable et la probabilité prédite d’être fumeur. Chaque courbe montre alors comment le modèle ajuste sa prédiction moyenne en fonction d'une seule variable, en conservant les autres constantes . </p>

#### a) PDP des variables quantitatives

<p align="center"> <u>Figure 7 : Partial dépendence plot (PDP) des variables quantitatives de la modélisation Gradient Boosting avec resample </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/pdp.png" alt="Importance">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">
L’analyse des PDP (<strong>Figure 7</strong>) révèle que une augmentation de la variable GTP est fortement associée à une augmentation de la probabilité d’être fumeur. Le GTP est une enzyme hépatique dont le taux augmente fréquemment en cas de consommation chronique d’alcool, une habitude souvent corrélée au tabagisme. De nombreuses études mettent en évidence que les personnes fumeuses ont aussi une consommation d’alcool plus élevée en moyenne, ce qui peut expliquer cette relation indirecte mais robuste entre GTP et statut tabagique.</p>

<p align="justify">
Concernant l’âge, le modèle identifie une probabilité plus élevée d’être fumeur dans les tranches d’âge jeunes à moyennes, avec une baisse progressive au-delà d’un certain seuil. Ce comportement est cohérent avec les tendances épidémiologiques observées, où les jeunes adultes et les personnes d’âge moyen affichent une prévalence plus élevée du tabagisme, tandis qu’une partie des individus plus âgés a tendance à arrêter ou réduire leur consommation, notamment pour des raisons de santé.</p>

<p align="justify">
Les triglycérides montrent également une relation croissante avec la probabilité de fumer. Cela est biologiquement plausible, car le tabagisme est reconnu pour favoriser un profil lipidique perturbé, en augmentant notamment les triglycérides et en réduisant le HDL.</p>

<p align="justify">
Enfin, l'augmentation des enzymes hépatiques ALT et AST est associée à une légère baisse de la probabilité prédite d'être fumeur. Bien que cette relation puisse sembler contre-intuitive, elle pourrait refléter des effets indirects : ces enzymes peuvent être élevées dans certaines pathologies pour lesquelles les individus sont médicalement suivis, incités à adopter un mode de vie plus sain ou à arrêter de fumer.</p>

<p align="justify">
Nous avons également généré des PDP à partir des données non standardisées afin d’identifier les niveaux de rupture dans les relations (<strong>Figure 8</strong>). </p>

<p align="center"> <u>Figure 8 : Partial dépendence plot (PDP) des variables quantitatives de la modélisation Gradient Boosting avec resample </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/pdpreel.png" alt="Importance">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

<p align="justify">
Pour GTP, la probabilité de fumer augmente nettement jusqu’à environ 80 U/L, puis se stabilise. Pour l’âge, la probabilité augmente progressivement jusqu’à 40 ans, puis diminue au-delà. L’augmentation des triglycérides entraîne une hausse continue de la probabilité de fumer. Pour ALT, plus les valeurs augmentent, plus la probabilité diminue, de manière régulière. Enfin, pour AST, une hausse entre 10 et 20 U/L correspond à une diminution de la probabilité, qui se stabilise entre 20 et 40, avant d’augmenter légèrement au-delà de 40 U/L, avec une stabilisation vers 45.</p>

<p align="justify">
Par ailleurs, nous avons calculé les effets locaux accumulés (ALE) (<strong>Figure 9</strong>) pour vérifier la robustesse des résultats. Les courbes obtenues sont très proches de celles des PDP, ce qui confirme que les relations identifiées par le modèle ne semblent pas trop affectées par des corrélations entre variables.</p>

<p align="center"> <u>Figure 9 : Accumulated Loccal Effect (ALE) des variables quantitatives de la modélisation Gradient Boosting avec resample </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/ale.png" alt="ALE">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>


#### b) PDP de la variable qualitative 

<p align="justify">
Concernant la variable qualitative identifiée comme la plus importante, selon ce PDP (<strong>Figure 10</strong>), on constate que le fait d’être un homme augmente significativement la probabilité prédite de fumer, tandis qu’être une femme la diminue. Autrement dit, toutes choses égales par ailleurs, le modèle estime qu’un homme a plus de chances d’être fumeur qu’une femme. Cette observation est cohérente avec de nombreuses données épidémiologiques : dans plusieurs pays, et notamment en population adulte, les taux de tabagisme sont plus élevés chez les hommes que chez les femmes. Cela peut s’expliquer par des facteurs sociaux et culturels, comme des différences dans les normes comportementales, une exposition plus fréquente à certains environnements propices au tabagisme (par exemple, certains milieux professionnels), ou encore des stratégies de gestion du stress qui varient selon le genre. </p>

<p align="center"> <u>Figure 10 : Partial dépendence plot (PDP) de la variable homme de la modélisation Gradient Boosting avec resample </p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/pdp2.png" alt="Importance">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em> </p>

### 3) Permutation features importance


<p align="justify">
Par la suite, nous avons évalué l’importance des variables via la méthode de permutation (<strong>Figure 11</strong>). Cette approche permet de quantifier la contribution réelle de chaque variable à la performance du modèle en mesurant l’impact de sa permutation aléatoire sur le score de rappel. L’axe des abscisses représente ici la variation moyenne du rappel induite par la perturbation de chaque variable : plus cette variation est élevée, plus la variable est jugée importante pour détecter les individus fumeurs.</p>

<p align="center"><u>Figure 11 : Permutation Features Importance de la modélisation Gradient Boosting avec resample</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/permutation.png" alt="Importance">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em></p>

<p align="justify">
La variable homme se distingue nettement, avec une augmentation moyenne de l’erreur de rappel d’environ 0,28 lorsqu’elle est permutée, confirmant ainsi son rôle central dans la prédiction du statut tabagique. Elle est suivie par GTP (~0,10), enzyme déjà identifiée comme fortement liée au comportement tabagique dans les PDP. D’autres variables telles que l’hémoglobine, ALT et les triglycérides présentent également une importance modérée (~0,02), suggérant qu’elles contribuent à la prédiction sans être centrale.</p>

<p align="justify">
Certaines variables, telles que pb_hearing, eyesight ou dental_caries, n’induisent aucune variation notable du score de rappel lorsqu’elles sont permutées. Elles sont donc considérées comme peu informatives dans ce contexte. À l’inverse, des variables comme serum_creatinine, waist ou fasting blood sugar présentent une importance négative, ce qui signifie que leur permutation améliore légèrement le rappel. Cela pourrait s’expliquer par la présence de bruit, une corrélation trompeuse ou une certaine redondance nuisible dans la modélisation.</p>

## B. Interprétation locale

<p align="justify">
Finalement, nous nous intéressons à l’interprétation des prédictions individuelles, aussi appelée explicabilité locale. L’objectif est d'expliquer pourquoi le modèle a prédit qu’un individu était fumeur (ou non), en identifiant les variables qui ont le plus influencé cette prédiction.</p>

### 1) ICE

<p align="justify">
Les courbes ICE permettent d’analyser l’effet d’une variable spécifique sur la prédiction pour chaque individu. Contrairement aux PDP qui affichent la moyenne des effets, l’ICE trace une courbe par individu, révélant les variations individuelles. Cela permet d’aller plus loin en explorant la variabilité interindividuelle (<strong>Figure 12</strong>).</p>

<p align="center"><u>Figure 12 : ICE de la modélisation Gradient Boosting avec resample</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/pdpindiv.png" alt="ICE">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em></p>

<p align="justify">
Nous constatons que, pour GTP, les courbes ICE confirment une relation croissante pour la majorité des individus. Toutefois, on observe que l’intensité de l’effet varie selon les personnes, certaines courbes étant plus plates ou plus abruptes que d’autres. Cela montre que l’impact de GTP n’est pas homogène dans toute la population.</p>

<p align="justify">
Pour l’âge, les PDP suggéraient une relation non linéaire décroissante. Avec les courbes ICE on observe que de nombreuses courbes chutent fortement à partir d’un certain âge, confirmant que la majorité des individus âgés ont une probabilité prédite plus faible d’être fumeur. Mais d'autres courbes sont plus stables ou montrent un effet atténué, traduisant des profils atypiques : par exemple des fumeurs persistants parmi les personnes âgées.</p>

<p align="justify">
Concernant les triglycérides, les ICE confirment une tendance ascendante relativement homogène, avec cependant quelques variations dans la pente, suggérant un effet plus marqué chez certains individus.</p>

<p align="justify">
Et enfin les ICE des variables ALT et AST montrent quant à elles une baisse globale des prédictions avec l’augmentation de ces enzymes, mais avec une dispersion faible. Cela pourrait indiquer que l’effet est plus stable ou moins influent, et pourrait refléter un phénomène indirect.</p>

### 2) LIME

<p align="justify">
Afin de mieux comprendre les décisions de notre modèle Gradient Boosting pour un individu donné, nous avons utilisé la méthode LIME (Local Interpretable Model-agnostic Explanations).
Cette approche consiste à approximer localement le modèle complexe par un modèle linéaire plus simple, permettant d’identifier quelles variables influencent une prédiction spécifique.</p>

<p align="justify">
Dans notre cas, nous avons utilisé un explainer en mode classification, en passant les données transformées (X_test_prepared), les noms des variables (feature_names) et les noms de classes (["non-fumeur", "fumeur"]). Nous avons sélectionné l’individu d’index i = 10 de notre jeu de test, et généré une explication à l’aide de la fonction explain_instance (<strong>Figure 13</strong>). </p>

<p align="center"><u>Figure 13 : LIME de la modélisation Gradient Boosting avec resample</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/lime.png" alt="LIME">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em></p>

<p align="justify">
Parmi les résultats obtenus :

- Intercept : 0.0665 — c’est la probabilité moyenne d’être fumeur dans le voisinage généré par LIME
- Prediction_local : 0.7746 — c’est la prédiction du modèle simplifié (local)
- Right : 0.7855 — c’est la prédiction du modèle complexe (Gradient Boosting) pour cet individu
</p>

<p align="justify">
Ce résultat indique une prédiction de 79 % de probabilité d’être fumeur. Les variables qui ont le plus contribué à cette prédiction sont :
- homme = 1 (poids : +0.49) : Être un homme a fortement poussé la prédiction vers "fumeur".
- triglyceride > 0.40, hemoglobin > 0.61, tartar = 1, et une valeur intermédiaire de GTP ont également influencé le modèle dans ce sens, mais de manière plus modérée.
</p>

### 3) SHAP

<p align="justify">
Après avoir exploré LIME, nous utilisons ici la méthode SHAP, qui décompose chaque prédiction en une somme de contributions individuelles attribuées aux variables, selon une logique d’équité. Chaque variable "participe" à la prédiction et se voit attribuer une valeur SHAP indiquant dans quelle mesure elle augmente ou diminue la probabilité prédite.</p>

<p align="justify">
Le <strong>graphique Waterfall</strong> (<strong>Figure 14</strong>) représente l’explication locale pour un individu spécifique. La valeur moyenne des prédictions du modèle (appelée *base value*, ici -0.191 en log-odds) constitue le point de départ. À partir de là, chaque variable vient ajouter ou soustraire un effet pour aboutir à la prédiction finale, ici -0.293. Par exemple, le fait d’être un homme contribue fortement à augmenter la probabilité d’être fumeur (+0.88), tout comme le fait d'avoir des carries. Tandis qu’un faible taux de GTP ou l'absence de tartre contribuent à réduire cette probabilité. On visualise ainsi clairement les forces "en présence" dans la décision du modèle.</p>

<p align="center"><u>Figure 14 : Waterfall de la modélisation Gradient Boosting avec resample</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/waterfall.png" alt="Waterfall">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em></p>

<p align="justify">
Le <strong>graphique SHAP beeswarm</strong> <strong>Figure 15</strong>, quant à lui, offre une vue globale sur l’influence des variables pour l’ensemble des prédictions. 

<p align="center"><u>Figure 15 : Beeswarm de la modélisation Gradient Boosting avec resample</u></p>

<p align="center">
  <img src="https://github.com/Emmalabre/Cours_SVM_M2ECAP/blob/main/Screenshots/beeswarm.png" alt="Beeswarm">
</p>

<p align="center"><em>Source : Dossier SVM, Isabel Palacio et Emma Labre-Blanc</em></p>

<p align="justify">
On observe que la variable `homme` est la plus influente, suivie par `Gtp`, `triglyceride`, `age` ou encore `ALT`. Les couleurs représentent la valeur de la variable : en rose pour les valeurs élevées, en bleu pour les faibles. Par exemple, un GTP ou un taux de triglycérides élevé pousse le modèle à prédire un statut de fumeur, tandis qu’un âge élevé contribue au contraire à diminuer cette probabilité.</p>

<p align="justify">
On observe également une nette séparation des points colorés pour la majorité des variables dans le graphique beeswarm. Les valeurs en rouge sont associées à une augmentation de la probabilité d’être fumeur, tandis que les valeurs en bleu tendent à la diminuer. Par exemple, des taux élevés de triglycérides et de GTP augmentent la probabilité d’être fumeur, alors qu’un âge élevé la réduit. Pour les variables binaires comme homme, tartar et dental carries, la séparation est encore plus marquée, ce qui suggère qu’elles permettent de bien distinguer les individus fumeurs des non-fumeurs. Cette répartition claire reflète un comportement cohérent du modèle et confirme l’influence importante de ces variables dans ses décisions. </p>


<p align="justify">
Ces visualisations, nous permettent de mieux comprendre pourquoi un individu est classé comme fumeur (ou non) par le modèle, et d’identifier les facteurs personnels qui influencent le plus les prédictions.</p>

# Conclusion

<p align="justify">
Cette étude visait à prédire le statut tabagique (fumeur ou non-fumeur) à partir de données biologiques et de santé issues d'examens médicaux réalisés en Corée du Sud entre 2002 et 2015. Il s’agissait d’un problème de classification binaire, mené à partir d’un échantillon extrait d’une base de données disponible sur Kaggle.</p>
<p align="justify">
Nous avons débuté par une analyse exploratoire rigoureuse des données : suppression des doublons, vérification de l'absence de valeurs manquantes, corrections de types et description statistique des variables. Cette phase nous a permis de mieux comprendre les distributions et les éventuelles corrélations.</p>
<p align="justify">
Un prétraitement a ensuite été appliqué pour préparer les données à la modélisation : séparation en jeu d’entraînement et de test, standardisation des variables numériques, winsorisation pour limiter l’effet des valeurs extrêmes, et encodage des variables catégorielles.</p>
<p align="justify">
Nous avons testé plusieurs approches de modélisation supervisée, en explorant différentes stratégies : utilisation des hyperparamètres par défaut, techniques de sous-échantillonnage (undersampling), sélection de variables, optimisation des hyperparamètres, ou encore transformation logarithmique de certaines variables. Les modèles ont été comparés à l’aide de plusieurs métriques, notamment le F1-score et le recall, afin de mieux prendre en compte le déséquilibre des classes.</p>
<p align="justify">
Au terme de cette analyse, le modèle de Gradient Boosting avec undersampling s’est avéré le plus performant et le plus adapté à notre problématique. En effet, il présentait le meilleur F1-score, ainsi qu’un des meilleurs recall, ce qui est particulièrement important dans notre contexte. Dans le domaine de la santé, il est essentiel de minimiser les faux négatifs, c’est-à-dire de bien identifier les individus fumeurs, afin de pouvoir cibler les actions de prévention ou de suivi médical. Ce modèle offrait ainsi un compromis optimal entre précision et sensibilité, rendant ses prédictions à la fois fiables et utiles pour un objectif d’identification.</p>
<p align="justify">
Grâce aux outils d’interprétabilité, nous avons pu identifier les variables ayant le plus influencé les décisions du modèle. En particulier, le fait d’être un homme s’est révélé comme le facteur le plus discriminant, contribuant fortement à augmenter la probabilité d’être fumeur. Les variables GTP, triglycérides, âge, ALT et AST ont également joué un rôle significatif. Une augmentation de GTP ou des triglycérides est associée à une probabilité plus élevée d’être fumeur, tandis qu’un âge avancé, ou des taux plus élevés de ALT/AST, tendent à réduire cette probabilité — même si cet effet reste plus modéré.</p>
<p align="justify">
Enfin, nos résultats apparaissent cohérents avec la littérature médicale : les biomarqueurs identifiés et les tendances observées s’alignent avec ce que l’on sait des profils biologiques associés au tabagisme. Cette étude illustre ainsi l’intérêt de combiner des approches de machine learning robustes avec des outils d’interprétabilité, pour à la fois prédire efficacement et mieux comprendre les facteurs de risque comportementaux à partir de données cliniques.</p>



# Glossaire

<p align="justify">

<strong>Pression artérielle systolique (systolic)</strong> : Le cœur se contracte avec une grande force à la pression artérielle la plus élevée de l'examiné. Il s'agit de la pression intravasculaire lorsque le cœur pousse le sang dans les artères. (mmHg)</p>

<p align="justify"><strong>Pression artérielle diastolique (relaxation)</strong> : La pression artérielle enregistrée lorsque le cœur est en phase de relâchement (diastole), c’est-à-dire la pression minimale dans les artères entre deux battements. (mmHg)</p>

  <p align="justify"><strong>Glycémie préprandiale (fasting blood sugar)</strong> : Mesure de la concentration de glucose dans 100 ml de sang chez l'individu à jeun. Indicateur clé du métabolisme du glucose. (mg/dL)</p>

  <p align="justify"><strong>Cholestérol total (cholesterol)</strong> : Somme du cholestérol estérifié et non estérifié (libre) dans le sérum. Le cholestérol libre représente environ un tiers du total, le reste étant des esters. Normale : 150–250 mg/dL.</p>

  <p align="justify"><strong>Triglycérides (triglyceride)</strong> : Lipides simples formés par l’estérification du glycérol avec trois acides gras. Ce sont les lipides les plus communs dans le corps. Valeur normale : 30–135 mg/dL.</p>

  <p align="justify"><strong>HDL Cholestérol (HDL)</strong> : Particules de cholestérol de haute densité impliquées dans le transport inverse du cholestérol. Elles aident à nettoyer les vaisseaux sanguins. Valeur normale : 30–65 mg/dL.</p>

  <p align="justify"><strong>LDL Cholestérol (LDL)</strong> : Cholestérol contenu dans les lipoprotéines de basse densité. Un excès peut s'accumuler sur les parois artérielles, provoquant de l'athérosclérose. Hyperlipidémie au-delà de 170 mg/dL. (mg/dL)</p>

  <p align="justify"><strong>Pigments sanguins / Hémoglobine (hemoglobin)</strong> : Protéine pigmentée du sang, composée de globine et d’un groupe hème, servant au transport de l’oxygène. (g/dL)</p>

  <p align="justify"><strong>Protéinurie (Urine_protein)</strong> : Présence de protéines dans les urines, codée de 1 à 6 :
    <br>1 (-) : Absence (normal),
    <br>2 (±) : Trace,
    <br>3 (+1) : Faible,
    <br>4 (+2) : Modérée,
    <br>5 (+3) : Importante,
    <br>6 (+4) : Sévère.
  </p>

  <p align="justify"><strong>Créatinine (creatinin)</strong> : Résidu métabolique issu de la créatine musculaire, excrété par les reins. Taux élevé en cas d'insuffisance rénale. Valeur normale : 0,8–1,7 mg/dL.</p>

  <p align="justify"><strong>AST (Aspartate Aminotransférase)</strong> : Enzyme hépatique également présente dans le cœur, les reins, les muscles, etc. Sa concentration augmente lors de lésions cellulaires. Valeur normale : 0–40 UI/L.</p>

  <p align="justify"><strong>ALT (Alanine Aminotransférase)</strong> : Enzyme principalement localisée dans le foie. Sa concentration augmente en cas de dommages hépatiques. Valeur normale : 0–40 UI/L.</p>

  <p align="justify"><strong>GTP (Gamma-Glutamyl Transférase)</strong> : Enzyme biliaire mesurant la fonction hépatique. Taux élevé en cas de dysfonctionnement des canaux biliaires ou du foie. Valeurs normales : jusqu’à 1163 UI/L (homme), 835 UI/L (femme).</p>

</p>

# Table des matières

- [Introduction](#introduction)
- [I. Analyse exploratoire](#i-analyse-exploratoire)
  - [A. Présentation des données](#a-présentation-des-données)
  - [B. Analyse univariée](#b-analyse-univariée)
    - [1) Variable d'intérêt](#1-variable-dintérêt)
    - [2) Prédicteurs quantitatifs](#2-prédicteurs-quantitatifs)
    - [3) Prédicteurs qualitatifs](#3-prédicteurs-qualitatifs)
  - [C. Analyse bivariée](#c-analyse-bivariée)
    - [1) Liens entre la variable cible et les prédicteurs qualitatifs et quantitatifs](#1-Liens-entre-la-variable-cible-et-les-prédicteurs-qualitatifs-et-quantitatifs)
        - [a) Liens entre la variable cible et les prédicteurs quantitatifs](#a-Liens-entre-la-variable-cible-et-les-prédicteurs-quantitatifs)
        - [b) Liens entre la variable cible et les prédicteurs qualitatifs](#b-Liens-entre-la-variable-cible-et-les-prédicteurs-qualitatifs)
    - [2) Etude des corrélations](#2-etude-des-corrélations)
    - [3) Liens entre les variables qualitatives](#3-liens-entre-les-variables-qualitatives)
- [II. Phase préparatoire et modélisations](#ii-Phase-préparatoire-et-modélisations)
  - [A. Préparation des variables pour la modélisation](#a-Préparation-des-variables-pour-la-modélisation)
    - [1) Séparation du jeu de données en jeux train et test](#1-Séparation-du-jeu-de-données-en-jeux-train-et-test)
    - [2) Traitement des valeurs atypiques : winsorization](#2-Traitement-des-valeurs-atypiques---winsorization)
    - [3) Standardisation et encodage](#3-Standardisation-et-encodage)
  - [B. Modélisations](#b-Modélisations)
    - [1) Présentation des modèles et métriques pour la comparaison](#1-Présentation-des-modèles-et-métriques-pour-la-comparaison)
    - [2) Modélisations avec les paramètres par défaut](#2-Modélisations-avec-les-paramètres-par-défaut)
    - [3) Modélisations optimisées : Présentation des stratégies d'optimisation](#3-Modélisations-optimisées---Présentation-des-stratégies-d-optimisation)
    - [4) Choix du meilleur modèle](#4-Choix-du-meilleur-modèle)
- [III. Interprétation du meilleur modèle](#iii-interprétation-du-meilleur-modèle)
  - [A. Interprétation globale](#a-interprétation-globale)
    - [1) Importance des variables](#1-Importance-des-variables)
    - [2) Partial Dependence Plots - PDP](#2-partial-dependence-plots---pdp)
    - [3) Permutation features importance](#3-permutation-features-importance)
  - [B. Interprétation locale](#b-interprétation-locale)
    - [1) ICE](#1-ice)
    - [2) LIME](#2-lime)
    - [3) SHAP](#3-shap)
- [Conclusion](#conclusion)
- [Glossaire](#Glossaire)
</p>

