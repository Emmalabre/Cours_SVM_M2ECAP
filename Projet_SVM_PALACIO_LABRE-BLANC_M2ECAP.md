
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
  - [A. Présentation des données](#a-présentation-des-données)
  - [B. Analyse univariée](#a-analyse-univariée)
  - [C. Analyse bivariée](#b-analyse-bivariée)
- [II. Modélisation](#ii-modélisation)
  - [A. Préparation des variables pour la modélisation](#a-préparation-des-données)
  - [B. Construction des modèles](#b-construction-des-modèles)
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
<p align="justify"> Nous commencerons par une analyse exploratoire des données pour en comprendre la structure, détecter les éventuelles anomalies et identifier les variables les plus discriminantes. Ensuite, nous testerons plusieurs modèles de classification (SVM, Random Forest, XGBoost…) afin de sélectionner le plus performant. Une phase d’optimisation des hyperparamètres permettra d’affiner les performances des modèles retenus. Enfin, nous analyserons les résultats à l’aide de techniques d’explicabilité telles que Permutation Feature Importance ou SHAP, dans le but de mieux comprendre l’influence de chaque variable biologique sur la prédiction du statut tabagique. L’objectif final est d’élaborer un modèle robuste, interprétable, et capable de prédire avec fiabilité si un individu est fumeur ou non, à partir de ses données médicales. </p>

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

<p align="justify">Le jeu de données contient 27 variables et 55 692 observations. Le tableau ci-dessous permet d'en avoir un aperçu : </p>


<p align="center"> <u>Table N°1 : Tableau présentant les variables </u></p>

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


## B. Statistiques descriptives

<p align="justify"> Après avoir effectué le prétraitement de nos données, nous avons procédé à une analyse des statistiques descriptives de l’ensemble des variables du jeu de données afin d'explorer les caractéristiques globales de la population étudiée, détecter d’éventuelles valeurs extrêmes, déséquilibres ou anomalies.


### 1. Variable d'intérêt

<p align="justify">
Nous débutons par l’analyse de la variable cible : le statut tabagique des individus. La distribution des classes est représentée par un histogramme (<strong>Figure 1</strong>). Ce graphique met en évidence un déséquilibre modéré : sur les 44 552 individus de l’échantillon, 16 352 sont fumeurs (soit 37 %), contre 28 200 non-fumeurs. Ainsi, environ un tiers des observations sont associées à la classe « fumeur ».
</p>

<p align="justify">
Un taux de 37 % pour la classe minoritaire n’est pas considéré comme fortement déséquilibré en apprentissage automatique. En général, le recours à des techniques de rééchantillonnage devient crucial lorsque la proportion descend en dessous de 20 %. Dans notre cas, la classe « fumeur » est suffisamment représentée pour permettre à plusieurs algorithmes, notamment ceux basés sur les arbres de décision, d’apprendre efficacement sans correction initiale. 
</p>

<p align="justify">
Nous procéderons donc, dans un premier temps, à l’entraînement des modèles sur les données telles quelles. Si les performances montrent par la suite un déséquilibre significatif (par exemple à travers les matrices de confusion), nous envisagerons alors l’utilisation de méthodes de rééchantillonnage telles que le sur-échantillonnage, le sous-échantillonnage ou encore le SMOTE.

### 2. Prédicteurs quantitatifs

<p align="justify"> L’analyse des variables quantitatives est résumée dans le tableau des statistiques descriptives (<strong>Tableau 1</strong>) ainsi que dans les histogrammes et boxplots présentés en <strong>Figures 2 et 3</strong>.</p>

<p align="center"> <u>Tableau N°1 : Statistiques descriptives des variables quantitatives </u></p>

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

<p align="justify">Voici les principales observations : </p>

<strong>Caractéristiques anthropométriques</strong>
<p align="justify"> L’âge moyen des individus est de 44 ans, avec une population allant de 20 à plus de 85 ans, ce qui constitue un échantillon adulte assez large. La taille moyenne est d’environ 1m65, pour un poids moyen de 66 kg. Le tour de taille moyen s’élève à 82 cm. </p>
<strong>Systèmes cardiovasculaire et visuel</strong>
<p align="justify"> La tension artérielle moyenne est de 121,5 mmHg en systolique et 76 mmHg en diastolique. Toutefois, quelques valeurs aberrantes ont été relevées, comme une systolique à 240 mmHg ou une diastolique à 146 mmHg, probablement issues d’erreurs de saisie. L’acuité visuelle moyenne est proche de 1.0 pour chaque œil, mais certaines valeurs très élevées (jusqu’à 9.9) indiquent une cécité. </p>
<strong>Variables biologiques (métaboliques)</strong>
<p align="justify"> La glycémie à jeun est en moyenne de 99 mg/dL, suggérant une population globalement saine. Toutefois, certaines valeurs très élevées (jusqu’à 505 mg/dL) sont préoccupantes et pourraient signaler des cas sévères de diabète non contrôlé. Le cholestérol total est en moyenne de 197 mg/dL, tandis que les triglycérides atteignent une moyenne de 127 mg/dL, avec une distribution très asymétrique (jusqu’à 999 mg/dL). </p> <p align="justify"> Le HDL (bon cholestérol) affiche une moyenne de 57 mg/dL, mais une valeur maximale incohérente à 618 mg/dL a été relevée. Le LDL (mauvais cholestérol), quant à lui, est en moyenne à 115 mg/dL, mais présente aussi une valeur extrême à 1860 mg/dL, vraisemblablement due à une erreur d’unité ou de saisie. </p>
<strong>Marqueurs cliniques (sang, foie, reins)</strong>
<p align="justify"> L’hémoglobine a une moyenne de 14,6 g/dL, avec des extrêmes allant de 4,9 à 21,1 g/dL. La créatinine sérique, indicateur de la fonction rénale, est en moyenne à 0,89 mg/dL, mais atteint un maximum de 11,6 mg/dL, ce qui est fortement suspect sans contexte clinique particulier. </p> <p align="justify"> Les enzymes hépatiques AST, ALT et GTP présentent des moyennes situées dans les plages normales (entre 26 et 40 UI/L). Cependant, certaines valeurs sont exceptionnellement élevées : 2914 UI/L pour l’ALT, 999 UI/L pour le GTP, et 1311 UI/L pour l’AST. Bien que rares, ces cas pourraient s’expliquer par des pathologies sévères (hépatites, intoxications, etc.)</p>

<p align="justify">
Ainsi, comme nous l'avons constater, plusieurs variables présentent des valeurs aberrantes ou extrêmes, parfois très éloignées des plages physiologiques normales. Ce qui a été confirmé par le test des valeurs atypiques IQR (Un point est considéré comme un outlier s’il se situe en dehors de l’intervalle [Q1 - 1.5×IQR ; Q3 + 1.5×IQR]). Un traitement spécifique sera donc appliqué lors de la phase de modélisation afin d’atténuer leur impact sans altérer la structure globale des données.
</p>

### 3. Prédicteurs qualitatifs

<p align="justify"> La distribution des variables qualitatives a été visualisée à l’aide de diagrammes en barres (voir <strong>Figures 4 à 6</strong>). Cette analyse met en évidence plusieurs déséquilibres : </p> <ul> <li>64 % des individus de la base de données sont des hommes, indiquant une surreprésentation masculine.</li> <li>Il y a 37 % de fumeurs, comme vu précédemment.</li> <li>21 % de la population présentent des caries dentaires, tandis que 55 % a du tartre.</li> <li>Les troubles auditifs sont rares : environ 2,6 % pour chaque oreille.</li> <li>La variable <strong>Urine_protein</strong> est très déséquilibrée : plus de 94 % des individus sont au niveau 1.0, indiquant l’absence de protéinurie. Les niveaux supérieurs (jusqu’à 6.0) sont rares mais peuvent avoir une forte signification clinique.</li> </ul> <p align="justify">

<p align="justify">Cette analyse descriptive nous a permis de mieux comprendre la structure et les caractéristiques de notre jeu de données.
Elle révèle une population adulte majoritairement masculine, une proportion significative de fumeurs, et quelques variables présentant des valeurs extrêmes ou déséquilibrées.</p>


## C. Analyse bivariée

<p align="justify">Nous passons maintenant à l'analyse bivarié. Nous présenterons successivement les analyses bivariées entre la variable cible et les prédicteurs quantitatifs et qualitatifs puis l'étude des corrélations entre les variables quantitatives et enfin les liens entre les variables qualitatives.</p>

### 1. Variable cible et prédicteurs qualitatifs et quantitatifs

#### a) cible et prédicteurs quantitatifs

<p align="justify"> Nous avons comparé les moyennes des variables numériques entre les individus fumeurs et non-fumeurs, ce qui a permis de dégager plusieurs différences significatives. Les fumeurs sont, en moyenne, plus jeunes, mais aussi plus grands, plus lourds et présentent un tour de taille plus élevé — des caractéristiques probablement liées à une surreprésentation masculine dans ce groupe. Leur tension artérielle (systolique et diastolique) est légèrement plus élevée, en lien avec les effets vasoconstricteurs de la nicotine, et leur glycémie à jeun tend à être supérieure, ce qui peut indiquer un risque accru de troubles métaboliques. Paradoxalement, leur cholestérol total est légèrement inférieur à celui des non-fumeurs. Toutefois, cette différence masque une baisse du HDL (le bon cholestérol), ce qui n’indique pas nécessairement un meilleur profil lipidique. En effet, un HDL plus bas est associé à un risque cardiovasculaire accru, et peut déséquilibrer le rapport LDL/HDL. Enfin, l'acuité visuelle moyenne des fumeurs apparaît légèrement meilleure. Ce résultat, contre-intuitif, pourrait s’expliquer par une différence d’âge : les fumeurs de l’échantillon étant en moyenne plus jeunes, ils sont naturellement moins touchés par la presbytie ou d'autres troubles visuels liés à l’âge.</p>
<p align="justify">Ces écarts suggèrent une association entre le statut tabagique et certaines caractéristiques physiologiques, cohérente avec les effets connus du tabac sur la santé métabolique et cardiovasculaire. </p> 
<p align="justify"> Une attention particulière a été portée à la variable <strong>taille</strong>, dont la distribution diffère fortement selon le statut tabagique. À première vue, cette variable semble utile pour discriminer les classes. Toutefois, nous avons suspecté un biais de confusion, en particulier avec la variable <strong>sexe</strong>, les hommes étant statistiquement plus grands et également plus susceptibles de fumer dans certains contextes. L'analyse de la corrélation (Pearson) a révélé une forte association entre la taille et le poids (r = 0.68), ce qui est logique sur le plan physiologique. Par ailleurs, le VIF (Variance Inflation Factor) élevé de la variable taille a confirmé un risque de multicolinéarité dans nos modèles. En conséquence, nous avons décidé de <strong>supprimer la variable taille</strong> pour éviter à la fois la redondance et les biais de confusion. </p> 
<p align="justify"> Par ailleurs, une analyse de variance (ANOVA) a été conduite pour tester les différences moyennes entre fumeurs et non-fumeurs pour chaque variable continue. Les résultats indiquent que l’ensemble des variables analysées présentent des <strong>p-values significatives</strong>, suggérant des différences statistiquement robustes entre les deux groupes. </p>

#### b) cible et prédicteurs qualitatifs

<p align="justify"> Nous avons également exploré les associations entre la variable cible et les prédicteurs qualitatifs. Les diagrammes en barres (<strong>Figure X</strong>) mettent en évidence des tendances marquées : les fumeurs sont plus nombreux à présenter du tartre et des caries dentaires, et la majorité des fumeurs sont des hommes. Ces éléments pourraient refléter des comportements ou des facteurs sociaux liés au tabagisme. </p> <p align="justify"> Ces observations visuelles ont été confirmées par un <strong>test du chi²</strong>, réalisé pour chacune des variables catégorielles. Les résultats révèlent que les variables <em>sex, dental_caries, tartar, pb_hearing (gauche et droite), urine_protein</em> présentent toutes des <strong>p-values significatives</strong>, confirmant l’existence de dépendances entre ces variables et le statut tabagique. Elles pourront donc être considérées comme prédicteurs pertinents dans la phase de modélisation. </p>


### 2. Etude des corrélations

<p align="justify"> L’analyse des corrélations entre variables continues nous a permis d’identifier des redondances potentielles. À partir de la <strong>matrice de corrélation de Spearman</strong>, qui tient compte des relations monotones, nous avons mis en évidence plusieurs associations fortes (ρ ≥ 0.70), illustrées en <strong>Figure Y</strong>. </p> <p align="justify"> Les principales corrélations observées sont les suivantes : <ul> <li><strong>Poids et tour de taille</strong> : ρ = 0.81 — forte corrélation attendue</li> <li><strong>Acuité visuelle gauche/droite</strong> : ρ = 0.70 — logique biométrique</li> <li><strong>Tension systolique/diastolique</strong> : ρ = 0.74 — pression artérielle intrinsèquement liée</li> <li><strong>Cholestérol total et LDL</strong> : ρ = 0.89 — le LDL étant une composante du cholestérol total</li> <li><strong>AST et ALT</strong> : ρ = 0.73 — enzymes hépatiques souvent corrélées</li> </ul> </p> <p align="justify"> L’analyse de la matrice de <strong>corrélation de Pearson</strong>, axée sur les relations linéaires, a confirmé ces dépendances : <ul> <li>Poids et tour de taille : r = 0.82</li> <li>Tension systolique et diastolique : r = 0.76</li> <li>LDL et cholestérol total : r = 0.74</li> <li>AST et ALT : r = 0.74</li> <li>Taille et poids : r = 0.68</li> </ul> </p> <p align="justify"> Ces résultats nous permettent d’identifier des variables fortement liées entre elles et nous questionne sur le besoin ou non de sélectionner des variables pour nos modélisations. Nous aborderons ce point dans la partie concernant l'optimisation des modèles.


### 3. Lien entre les variables qualitatives

<p align="justify"> Enfin, nous avons étudié les liens entre variables qualitatives à l’aide de tests du chi² sur toutes les paires de variables. Plusieurs associations significatives ont été détectées, indiquant que certaines variables partagent une information redondante. </p> <p align="justify"> Un exemple particulier concerne les variables <strong>pb_hearing (left)</strong> et <strong>pb_hearing (right)</strong>, qui sont fortement liées. Toutefois, une analyse plus fine a montré que les modalités diffèrent pour 1 107 individus, ce qui justifie la <strong>conservation des deux variables</strong> dans l’analyse, du moins dans un premier temps. </p>

<p align="justify"> Finalement, de manière générale, nous avons choisi de <strong>conserver l’ensemble des variables</strong> à ce stade, afin de ne pas écarter prématurément des informations potentiellement utiles. Une <strong>sélection de variables</strong> (via des méthodes automatiques ou empiriques) sera envisagée ultérieurement dans la phase de modélisation, pour optimiser la performance et la robustesse de nos modèles. </p>


# II. Phase préparatoire et modélisations

<p align="justify"> Après avoir exploré les caractéristiques individuelles et les relations entre variables, nous passons désormais à la phase de modélisation. Celle-ci se déroulera en deux temps : dans un premier temps, nous appliquerons un ensemble de traitements aux variables afin de les préparer correctement à l’apprentissage automatique (encodage, mise à l’échelle, traitement des valeurs extrêmes, etc.). Ensuite, nous testerons plusieurs modèles de classification afin de comparer leurs performances et de sélectionnder le meilleur modèle. </p>

## A. Préparation des données

### 1) Séparation du jeu de données en jeux train et test

<p align="justify"> Afin de pouvoir évaluer la capacité de généralisation de nos modèles, nous avons divisé notre jeu de données en deux ensembles distincts en utilisant la fonction <code>train_test_split</code> de <code>sklearn.model_selection</code> avec un ratio de 80 % des données pour l’entraînement et 20 % pour le test. Cette séparation permet d'éviter les biais d'optimisme et de s’assurer que les modèles ne sont pas uniquement adaptés aux données vues pendant l'entraînement. </p>

<p align="justify">De plus, nous avons effectué cette séparation avant les autres opérations de transformation (standardisation, winsorization) pour éviter tout <strong>data leakage</strong>, c’est-à-dire que des informations statistiques issues du test influencent l’entraînement. Tous les prétraitements ultérieurs sont ainsi appris uniquement à partir des données d'entraînement, puis appliqués aux données de test. </p>

### 2) Traitement des valeurs atypiques : winsorization

<p align="justify"> Comme nous avons pu précedemment le constater, plusieurs variables numériques présentent des valeurs atypiques, parfois cliniquement peu plausibles, susceptibles de perturber l'entraînement des modèles. Pour limiter leur influence sans exclure d'observations, nous avons appliqué une technique de <strong>winsorization</strong> à l'ensemble des variables suivantes : <em>'ALT', 'AST', 'Gtp', 'serum_creatinine', 'LDL', 'HDL', 'triglyceride', 'Cholesterol', 'fasting_blood_sugar', 'systolic', 'relaxation', 'hemoglobin'</em>. Cette méthode consiste à ramener les valeurs situées en dehors des 1er et 99e percentiles à ces bornes, de manière à réduire l'effet des outliers tout en conservant la distribution générale. </p> 
<p align="justify"> Les variables <em>'age', 'weight', 'height', 'waist(cm)'</em> ainsi que <em>'eyesight(left)'</em> et <em>'eyesight(right)'</em> n'ont pas été winsorisées : bien que certaines valeurs aient été identifiées comme atypiques par des tests statistiques (comme le test ESD), elles restent cohérentes. </p> 
<p align="justify"> Les seuils de winsorization ont été calculés exclusivement sur le jeu d’entraînement, puis appliqués tels quels au jeu de test, afin de respecter les bonnes pratiques d'apprentissage automatique et d’éviter toute fuite d’information (<em>data leakage</em>). Cette approche contribue à stabiliser les performances des algorithmes sensibles aux valeurs extrêmes. </p>

### 3) Standardisation et encodage

<p align="justify"> Pour que chaque variable quantitative contribue de manière équivalente aux algorithmes, et notamment aux modèles sensibles à l’échelle (comme les SVM ou la régression logistique), nous avons procédé à une standardisation. Celle-ci consiste à recentrer chaque variable (moyenne nulle) et à la réduire (écart-type unitaire). Pour ce faire, nous avons utilisé la fonction <code>StandardScaler</code> de <code>sklearn.preprocessing</code>. Comme pour les étapes précédentes, les paramètres de standardisation sont appris uniquement sur l'ensemble d’entraînement, puis appliqués au jeu de test.</p>

<p align="justify"> Concernant les variables qualitatives, la seule variable catégorielle nécessitant un encodage est <strong>urine_protein</strong>, considérée comme ordinale. En effet, les autres variables qualitatives présentes dans notre jeu de données sont déjà représentées sous forme booléenne (0/1), ce qui les rend directement exploitables par les algorithmes. Pour cette variable ordinale, nous avons appliqué un encodage à l’aide de la classe <code>OrdinalEncoder</code> de <code>sklearn</code>, qui transforme chaque modalité selon l’ordre naturel de la sévérité.</p>

<p align="justify">Cette étape à été réalisé grâce à la pipeline ColumnTransformer qui permet d’appliquer des transformations spécifiques à chaque type de variable.</p>

## B. Modélisations

<p align="justify">Nous passons à présent à la partie dédiée aux modélisations. Nous commencerons par présenter les modèles testés ainsi que les métriques utilisées pour les comparer. Nous procéderons ensuite à une première série de modélisations en utilisant les paramètres par défaut des algorithmes, avant de chercher à optimiser les performances des modèles les plus prometteurs, puis de sélectionner le modèle retenu.</p> 

### 1) Présentation des modèles et métriques pour la comparaison

<p align="justify"> Dans le but de prédire le <strong>statut tabagique</strong> des individus (variable cible : <strong>smoking</strong>), nous avons entraîné et comparé plusieurs <strong>modèles de classification binaire</strong> : </p> <ul> <li><strong>Régression logistique</strong> : modèle linéaire paramétrique, simple, rapide à entraîner et aisément interprétable. Il constitue un excellent <em>modèle de référence</em> pour évaluer la linéarité entre les variables prédictives et la variable cible.</li> <li><strong>Random Forest</strong> : modèle non paramétrique basé sur un ensemble d’arbres de décision construits aléatoirement. Il est capable de capturer des interactions non linéaires, de gérer des variables hétérogènes (numériques, catégorielles, ordinales) et se montre robuste face aux valeurs aberrantes.</li> <li><strong>Gradient Boosting / XGBoost</strong> : modèles puissants d’<em>ensemble learning</em> qui construisent séquentiellement des arbres pour corriger les erreurs des précédents. Très performants sur des relations complexes et adaptées à des effets faibles répartis sur de nombreuses variables. XGBoost, en particulier, est reconnu pour sa rapidité et son efficacité.</li> <li><strong>SVM (Support Vector Machines)</strong> avec différents noyaux (<em>linéaire</em>, <em>RBF</em>, <em>polynomial</em>) : ces modèles cherchent à maximiser la marge entre les classes. Le noyau linéaire permet de tester la séparabilité linéaire des données, tandis que les noyaux RBF et polynomial permettent de modéliser des frontières de décision plus complexes en projetant les données dans des espaces de dimension supérieure.</li> <li><strong>Classifieurs linéaires</strong> : <ul> <li><strong>LinearSVC</strong> : une implémentation optimisée du SVM linéaire, adaptée aux grands jeux de données.</li> <li><strong>SGDClassifier</strong> : algorithme basé sur la descente de gradient stochastique, très rapide, particulièrement efficace pour les jeux de données volumineux ou en streaming.</li> </ul> </li> </ul> 
<p align="justify"> Pour évaluer la performance de ces modèles, nous avons utilisé différentes <strong>métriques d’évaluation</strong>, mesurées à la fois : </p> <ul> <li>Sur les jeux <strong>d'entraînement</strong> et de <strong>test</strong> pour évaluer leur capacité de généralisation.</li> <li>En <strong>validation croisée (5-fold)</strong> pour estimer la robustesse et la stabilité des performances.</li> </ul> 
<p align="justify">Les résultats ont été synthétisés sous forme de tableaux et de graphiques comparatifs.</p> <h4><u>Métriques utilisées</u> :</h4> <ul> <li><strong>Accuracy</strong> : proportion globale de bonnes prédictions (vrais positifs + vrais négatifs). Elle répond à la question : <em>« À quelle fréquence le modèle est-il correct ? »</em></li> <li><strong>Erreur (1 - accuracy)</strong> : proportion d’observations mal classées. Elle permet de mesurer l'inefficacité du modèle. Un bon modèle aura une <strong>accuracy proche de 1</strong> et une <strong>erreur proche de 0</strong>.</li> <li><strong>Précision</strong> : parmi les individus prédits comme fumeurs, quelle proportion l’est réellement ? Cette métrique est sensible aux <em>faux positifs</em>.</li> <li><strong>Rappel (Recall)</strong> : parmi les fumeurs réels, combien ont été correctement identifiés ? Cette métrique est sensible aux <em>faux négatifs</em>.</li> <li><strong>F1-score</strong> : moyenne harmonique entre précision et rappel. Elle est particulièrement utile lorsque les classes sont déséquilibrées (ce qui est modérément le cas ici). Un bon modèle affiche un <strong>F1-score élevé</strong>, proche de 1.</li> </ul> 
<p align="justify"> L’objectif de cette évaluation multiple est de ne pas se limiter à l’accuracy, qui peut être trompeuse en cas de déséquilibre de classes, Une forte accuracy peut masquer des performances médiocres sur la classe minoritaire, pourtant essentielle à détecter dans notre étude. C’est pourquoi d’autres métriques sont mobilisées afin de mieux comprendre les compromis entre faux positifs et faux négatifs. </p>
<p align="justify"> Ainsi, une attention particulière est portée au <strong>f1-score</strong> et au <strong>recall</strong>, car il est crucial d’<strong>identifier correctement les individus fumeurs</strong>, même au prix de quelques faux positifs. En effet, dans un contexte de prévention ou de dépistage médical, il est souvent préférable de <strong>ne pas rater un cas à risque</strong> (fumeur non détecté), quitte à déclencher des vérifications supplémentaires. C’est pourquoi le rappel sera la métrique principale utilisée pour comparer les performances des modèles. </p>

### 2) Modélisations avec les paramètres par défaut

<p align="justify">Nous avons commencé par tester les modèles sans optimisation, avec leurs paramètres par défaut. Cela nous a permis d’identifier ceux qui étaient les plus prometteurs dans notre contexte.</p> <p>

<p align="justify">
Les résultats initiaux ont été obtenus sans validation croisée, en comparant directement les performances sur les jeux d'entraînement et de test. Cela permettait de détecter des cas de surapprentissage évident, notamment pour le modèle <strong>Random Forest</strong>, dont le F1-score chute de 0.999 sur le jeu d’entraînement à 0.677 sur le jeu de test, traduisant une très mauvaise généralisation. À l’inverse, les modèles comme le <strong>Gradient Boosting</strong> ou le <strong>LinearSVC</strong> présentent des scores cohérents entre entraînement et test, ce qui témoigne d’une meilleure robustesse (cf. Tableau 1).
</p>

### Tableau 1 – Résultats des modèles sans validation croisée

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

<p align="justify">
Pour affiner cette analyse, une <strong>validation croisée à 5 plis</strong> a été réalisée afin d’estimer la stabilité des modèles et leur capacité de généralisation sur des sous-ensembles différents du jeu de données. Les résultats par pli sont présentés dans le <strong>Tableau 2</strong>, et les statistiques globales (moyenne et écart-type du F1-score) dans le <strong>Tableau 3</strong>.
</p>

### Tableau 2 – Résultats F1-score par pli (Cross-validation à 5 plis)

|                    | Logistic Regression | Random Forest | Gradient Boosting | SVC (RBF) | SVC (Linear) | SVC (Poly) | LinearSVC | SGDClassifier |
|--------------------|---------------------|----------------|-------------------|-----------|--------------|-------------|-----------|----------------|
| Fold 1             | 0.579668            | 0.667040       | 0.684962          | 0.516760  | 0.660084     | 0.536328    | 0.664850  | 0.000000       |
| Fold 2             | 0.580901            | 0.669417       | 0.681290          | 0.524376  | 0.673123     | 0.553153    | 0.678689  | 0.052476       |
| Fold 3             | 0.582193            | 0.668545       | 0.682384          | 0.509638  | 0.672170     | 0.538160    | 0.671065  | 0.037244       |
| Fold 4             | 0.570887            | 0.668159       | 0.689352          | 0.518260  | 0.672737     | 0.549705    | 0.671965  | 0.299416       |
| Fold 5             | 0.581766            | 0.667290       | 0.681262          | 0.522839  | 0.665223     | 0.545287    | 0.662784  | 0.584317       |

### Tableau 3 – Moyenne et écart-type des F1-score (Cross-validation)

| Modèle              | Moyenne F1-score | Écart-type  |
|---------------------|------------------|-------------|
| Logistic Regression | 0.579083         | 0.004682    |
| Random Forest       | 0.668090         | 0.000964    |
| Gradient Boosting   | 0.683850         | 0.003425    |
| SVC (RBF Kernel)    | 0.518375         | 0.005807    |
| SVC (Linear Kernel) | 0.668667         | 0.005793    |
| SVC (Poly Kernel)   | 0.544527         | 0.007238    |
| LinearSVC           | 0.669871         | 0.006305    |
| SGDClassifier       | 0.194691         | 0.247838    |

<p align="justify">
Le <strong>Gradient Boosting</strong> est le modèle le plus performant en validation croisée, avec un F1-score moyen de <strong>0.684</strong> et une variance très faible, ce qui témoigne de sa stabilité. Il surpasse même le Random Forest et LinearSVC sur cette base. Le <strong>SGDClassifier</strong>, en revanche, affiche un F1-score moyen de seulement 0.195 avec un écart-type extrêmement élevé de 0.25, montrant une <strong>grande instabilité</strong> et des performances peu fiables.
</p>

<p align="justify">
Compte tenu de ces résultats, nous avons décidé de retenir uniquement les modèles <strong>Gradient Boosting</strong> et <strong>LinearSVC</strong> pour la suite de l’étude et les étapes d’optimisation. Ce choix repose à la fois sur leurs bonnes performances sur les jeux de test, leur stabilité démontrée par la validation croisée, ainsi que sur des considérations pratiques : limiter à deux modèles permet de <strong>réduire le temps de calcul</strong> tout en concentrant les efforts sur les méthodes les plus prometteuses.
</p>


### 3) Modélisations optimisées : Présentation des stratégies d'optimisation

<p align="justify">Afin d’optimiser les performances des deux modèles retenus, nous avons plusieurs stratégies :</p> <ul> <li><strong>Recherche d’hyperparamètres seule</strong> (GridSearchCV)</li> <li><strong>Resampling seul</strong></li> <li><strong>Resampling seul + GridSearchCV</strong></li> <li><strong>Sélection de variables + GridSearchCV</strong></li><li><strong>Transformations log</strong></li> </ul> 
<p align="justify">Cette approche nous permet d’évaluer si une simple recherche d’hyperparamètres suffit ou si l’ajout de techniques comme le resampling ou la sélection de variables améliore significativement les résultats, notamment en termes de <strong>rappel</strong>.</p>

<strong>Pourquoi envisager une méthode de resampling : </strong>
<p align="justify">Le jeu de données présente un déséquilibre de classes : environ <strong>37 % de fumeurs</strong> contre <strong>63 % de non-fumeurs</strong>. Ce déséquilibre peut biaiser les modèles, qui auront tendance à prédire la classe majoritaire pour maximiser l’<em>accuracy</em>, au détriment de la détection des fumeurs.</p> 
<p align="justify">Pour corriger cela, deux options ont été envisagées :</p> <ul> <li><strong>Oversampling</strong> : duplication d’exemples de la classe minoritaire (fumeurs). Risque : surapprentissage.</li> <li><strong>Undersampling</strong> : réduction du nombre d’exemples de la classe majoritaire (non-fumeurs). Risque : perte d’information.</li> </ul> 
<p align="justify">Nous avons retenu l’<strong>undersampling</strong>, notre dataset étant suffisamment grand pour rendre cette perte acceptable. Le resampling a été appliqué uniquement sur l’échantillon d’entraînement, avant la validation croisée, en utilisant le <strong>recall</strong> comme critère d’optimisation.</p> 

<strong>Pourquoi envisager une méthode de sélection de variable :</strong>
<p align="justify">Certaines variables montraient des corrélations fortes ou des redondances, ce qui soulevait la question de leur utilité dans le modèle. Afin de simplifier les modèles, d’en améliorer l’interprétabilité et de limiter les effets de multicolinéarité, nous avons appliqué une <strong>sélection automatique de variables</strong>.</p> 
<p align="justify">Cette sélection a été intégrée dans un pipeline combiné avec GridSearchCV, permettant d’optimiser simultanément les hyperparamètres et le sous-ensemble de variables retenu.</p>

### 4) Choix du meilleur modèle

<p align="justify">
Après avoir testé un large éventail de modèles de classification, incluant des versions non optimisées, des modèles avec recherche d’hyperparamètres (GridSearchCV), des variantes intégrant un rééquilibrage de classes (<em>undersampling</em>) et des approches combinées à une sélection automatique de variables, le modèle présentant les meilleures performances globales est le <strong>Gradient Boosting avec undersampling</strong> (sans transformation logarithmique). Il atteint un <strong>recall de 0.8919</strong> et un <strong>F1-score de 0.7201</strong>, surpassant ainsi les autres modèles, qu’il s’agisse de la régression logistique (F1-score ≈ 0.611), de LinearSVC (≈ 0.682), du Gradient Boosting non optimisé (≈ 0.689) ou même des modèles combinant undersampling, grid search ou sélection de variables.
</p>

<p align="justify">
L’intégration de l’<strong>undersampling</strong> a joué un rôle important : elle a permis de réduire le déséquilibre entre classes et d’améliorer considérablement le rappel, en diminuant les cas de fumeurs non détectés. À l’inverse, la <strong>sélection de variables</strong> n’a pas permis de dépasser les scores obtenus avec l’undersampling seul, ce qui confirme l’importance du rééquilibrage des données.
</p>

<p align="justify">
Une version alternative avec transformation logarithmique des variables numériques (<strong>GB + undersampling + log</strong>) a également été envisagée et a permis d’améliorer légèrement le <strong>recall</strong> (91,4 % vs 89,2 %) et de réduire les faux négatifs. Toutefois, cette amélioration se fait au prix d’une <strong>précision plus faible</strong> (≈ 0.588 vs 0.604) et d’un <strong>nombre accru de faux positifs</strong> (2 092 contre 1 910). Ce compromis n’a pas été jugé acceptable ici, car il dégrade l’équilibre général du modèle, en particulier si l’on considère les coûts potentiels d’un surdépistage.
</p>

<p align="justify">
Dans le cadre de cette étude, où l’objectif est de détecter efficacement les fumeurs pour mieux orienter les actions de santé publique, le <strong>modèle Gradient Boosting avec undersampling</strong> sans transformation log, sans gridsearch et sans features selection représente le meilleur compromis entre <strong>rappel élevé</strong>, <strong>précision acceptable</strong>, et <strong>taux de faux positifs maîtrisé</strong>. Il permet d’atteindre une couverture satisfaisante sans générer un nombre excessif d’erreurs coûteuses.
</p>

<p align="justify">
Ce choix reste toutefois contextuel : en dépistage ou prévention précoce, ce type de modèle reste justifié. Mais dans des cas plus sensibles — décisions médicales lourdes, stigmatisation, ou applications externes comme l’<strong>évaluation de risque pour des assurances</strong> —, il serait préférable d’envisager des modèles à plus forte précision, ou d’ajuster les seuils de classification pour limiter les faux positifs.
</p>

<p align="justify">
Les performances des modèles LinearSVC et Gradient Boosting,  optimisés et non optimisés sont présentées dans le tableau ci-dessous.
</p>

| Nom                                             | Recall   | F1-score | Accuracy | Precision | Best Params                                                                 |
|--------------------------------------------------|----------|----------|----------|-----------|------------------------------------------------------------------------------|
| LinearSVC - Sans optimisation                      | 0.720674 | 0.681930 | 0.753675 | 0.647140  | /                                                              |
| LinearSVC - GridSearch seul                      | 0.722818 | 0.682278 | 0.753339 | 0.646044  | {'model__C': 1}                                                              |
| LinearSVC - Undersampling seul                   | 0.937519 | 0.707991 | 0.716642 | 0.568748  | C=1 (défaut)                                                                 |
| LinearSVC - GridSearch + Undersampling           | 0.937519 | 0.708237 | 0.716979 | 0.569065  | {'model__C': 0.01}                                                           |
| LinearSVC - Sélection de variables               | 0.719755 | 0.681653 | 0.753675 | 0.647383  | C=0.01, vars: ['systolic', 'respiratory', 'alcohol', 'age', 'cholesterol']  |
| Gradient Boosting - Sans optimisation              | 0.715467 | 0.689187 | 0.763551 | 0.664769  | / |
| Gradient Boosting - GridSearch seul              | 0.714548 | 0.688404 | 0.762990 | 0.664105  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
| Gradient Boosting - Undersampling seul           | 0.891884 | 0.720168 | 0.746044 | 0.603899  | default                                                                      |
| Gradient Boosting - GridSearch + Undersampling   | 0.913936 | 0.715931 | 0.734261 | 0.588444  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |
| Gradient Boosting - Sélection de variables       | 0.715161 | 0.685354 | 0.759398 | 0.657932  | {'learning_rate': 0.05, 'max_depth': 3, 'n_estimators': 100}                |
| Gradient Boosting - Résultats avec log transform | 0.914242 | 0.715656 | 0.733812 | 0.587946  | {'model__learning_rate': 0.05, 'model__max_depth': 3, 'model__n_estimators': 100} |


# III. Interprétation du meilleur modèle

<p align="justify">
Dans cette troisième et dernière partie, nous nous attachons à interpréter le modèle sélectionné, à savoir le Gradient Boosting avec resampling (sans GridSearch ni sélection de variables).  
Nous proposons tout d'abord une analyse globale de son fonctionnement afin d’identifier les variables les plus influentes dans les prédictions, avant de passer à une interprétation locale visant à mieux comprendre certains cas spécifiques.
</p>

## A. Interprétation globale

Partial dependance plot : Effet marginal d'une variable (qualitative ou quantitvative) sur la target

Accumulated Local Effect : Effet marginal d'une variable par interval sur la target (quanti)

Feature Interaction (H-statistic) : Quantifie les effets joints des variables

Feature Importance : Mesure l'effet d'une feature sur la fonction de perte

### 1) Importance des variables

### 2) Partial Dependence Plots - PDP

### 3) Permutation features importance

## B. Interprétation locale

Individual conditional curves (ICE) basée sur les Partial Depence Plot(PDP) mais par instance cette fois

Local surrogate models (LIME) expliquer une prédiction en remplacant un modèle black box avec un modèle white box localement

Shapley values : méthode d'attribution des prédictions basées sur les caractéristiques individuelles

SHAP : Dérivée des shapley values avec également avec des global model agnostic (méthode la plus populaire)

### 1) ICE

### 2) LIME

# Conclusion




# Glossaire

<div style="text-align: justify;">

  <p><strong>Pression artérielle systolique (systolic)</strong> : Le cœur se contracte avec une grande force à la pression artérielle la plus élevée de l'examiné. Il s'agit de la pression intravasculaire lorsque le cœur pousse le sang dans les artères. (mmHg)</p>

  <p><strong>Pression artérielle diastolique (relaxation)</strong> : La pression artérielle enregistrée lorsque le cœur est en phase de relâchement (diastole), c’est-à-dire la pression minimale dans les artères entre deux battements. (mmHg)</p>

  <p><strong>Glycémie préprandiale (fasting blood sugar)</strong> : Mesure de la concentration de glucose dans 100 ml de sang chez l'individu à jeun. Indicateur clé du métabolisme du glucose. (mg/dL)</p>

  <p><strong>Cholestérol total (cholesterol)</strong> : Somme du cholestérol estérifié et non estérifié (libre) dans le sérum. Le cholestérol libre représente environ un tiers du total, le reste étant des esters. Normale : 150–250 mg/dL.</p>

  <p><strong>Triglycérides (triglyceride)</strong> : Lipides simples formés par l’estérification du glycérol avec trois acides gras. Ce sont les lipides les plus communs dans le corps. Valeur normale : 30–135 mg/dL.</p>

  <p><strong>HDL Cholestérol (HDL)</strong> : Particules de cholestérol de haute densité impliquées dans le transport inverse du cholestérol. Elles aident à nettoyer les vaisseaux sanguins. Valeur normale : 30–65 mg/dL.</p>

  <p><strong>LDL Cholestérol (LDL)</strong> : Cholestérol contenu dans les lipoprotéines de basse densité. Un excès peut s'accumuler sur les parois artérielles, provoquant de l'athérosclérose. Hyperlipidémie au-delà de 170 mg/dL. (mg/dL)</p>

  <p><strong>Pigments sanguins / Hémoglobine (hemoglobin)</strong> : Protéine pigmentée du sang, composée de globine et d’un groupe hème, servant au transport de l’oxygène. (g/dL)</p>

  <p><strong>Protéinurie (Urine_protein)</strong> : Présence de protéines dans les urines, codée de 1 à 6 :
    <br>1 (-) : Absence (normal),
    <br>2 (±) : Trace,
    <br>3 (+1) : Faible,
    <br>4 (+2) : Modérée,
    <br>5 (+3) : Importante,
    <br>6 (+4) : Sévère.
  </p>

  <p><strong>Créatinine (creatinin)</strong> : Résidu métabolique issu de la créatine musculaire, excrété par les reins. Taux élevé en cas d'insuffisance rénale. Valeur normale : 0,8–1,7 mg/dL.</p>

  <p><strong>AST (Aspartate Aminotransférase)</strong> : Enzyme hépatique également présente dans le cœur, les reins, les muscles, etc. Sa concentration augmente lors de lésions cellulaires. Valeur normale : 0–40 UI/L.</p>

  <p><strong>ALT (Alanine Aminotransférase)</strong> : Enzyme principalement localisée dans le foie. Sa concentration augmente en cas de dommages hépatiques. Valeur normale : 0–40 UI/L.</p>

  <p><strong>GTP (Gamma-Glutamyl Transférase)</strong> : Enzyme biliaire mesurant la fonction hépatique. Taux élevé en cas de dysfonctionnement des canaux biliaires ou du foie. Valeurs normales : jusqu’à 1163 UI/L (homme), 835 UI/L (femme).</p>

</div>

# Table des matières

- [Introduction](#introduction)
- [I. Analyse exploratoire](#i-analyse-exploratoire)
  - [A. Présentation des données](#a-présentation-des-données)
  - [B. Analyse univariée](#a-analyse-univariée)
    - [1) Variable d'intérêt](#1-variable-dintérêt)
    - [2) Prédicteurs quantitatifs](#2-prédicteurs-quantitatifs)
    - [3) Prédicteurs qualitatifs](#3-prédicteurs-qualitatifs)
  - [C. Analyse bivariée](#b-analyse-bivariée)
    - [1) Etude des corrélations](#1-variable-cible-et-prédicteurs-qualitatifs)
    - [2) Liens entre la variable à prédire et les prédicteurs qualitatifs](#2-etude-des-corrélations)
    - [3) Lien entre les variables qualitatives](#3-lien-entre-les-variables-qualitatives)
- [II. Modélisation](#ii-modélisation)
  - [A. Préparation des variables pour la modélisation](#a-Préparation-des-données)
    - [1) Encodage des variables categorielles](#1-Encodage-des-variables-catégorielles)
    - [2) Séparation du jeu de données en jeux train et test](#2-Séparation-du-jeu-de-données-en-jeu-train-et-test)
    - [3) Traitement des valeurs atypiques : winsorization](#3-Traitement-des-valeurs-atypiques-:-Winsorization)
    - [4) Standardisation](#4-Standardisation)
  - [B. Construction des modèles](#b-construction-des-modèles)
    - [1) Modélisations avec les paramètres par défaut](#1-modélisation-avec-les-paramètres-par-défaut)
    - [2) Évaluation des modèles](#2-évaluation-des-modèles-accuracy-et-f1-score-et-choix)
      - [a) Définitions des métriques](#a-définitions-des-métriques)
      - [b) Présentation du raisonnement pour le choix du modèle final](#b-présentation-du-raisonnement-pour-le-choix-du-modèle-final)
      - [c) Etat des lieux sans la validation croisée](#c-etat-des-lieux-sans-la-validation-croisée)
      - [d) Etat des lieux avec la validation croisée](#d-etat-des-lieux-avec-la-validation-croisée)
      - [e) Optimisation des modèles](#e-optimisation-des-modèles)
      - [f) Importance des variables](#f-importance-des-variables)
- [III. Interprétation du meilleur modèle](#iii-interprétation-du-meilleur-modèle)
  - [A. Interprétation globale](#a-interprétation-globale)
    - [1) Partial Dependence Plots - PDP](#1-partial-dependence-plots---pdp)
    - [2) Permutation features importance](#2-permutation-features-importance)
  - [B. Interprétation locale](#b-interprétation-locale)
    - [1) ICE](#1-ice)
    - [2) LIME](#2-lime)
- [Conclusion](#conclusion)
- [Glossaire](#Glossaire)
</p>

