# Prévision du trafic aérien commercial mensuel en France

Projet réalisé dans le cadre du cours de **Techniques de Prévision et Conjoncture**, encadré par Olivier Darné, en Master 1 Économétrie Appliquée à l’IAE Nantes.

L’étude porte sur l’évolution du trafic aérien commercial en France de 2007 à 2024 et compare plusieurs modèles de prévision saisonnière sous R.

## Objectif

Identifier un modèle adapté à la prévision du trafic aérien mensuel en tenant compte de sa tendance, de sa saisonnalité annuelle et des perturbations liées à la crise sanitaire de 2020–2021.

## Données

Les données proviennent de la **Direction générale de l’aviation civile (DGAC)**. Le projet utilise les fichiers de la série `ASP_APT`, qui décrivent le trafic aérien commercial mensuel des aérodromes français. [Consulter le jeu de données sur data.gouv.fr](https://www.data.gouv.fr/datasets/trafic-aerien-commercial-mensuel-francais-par-paire-daeroports-par-sens-depuis-1990)

- **216 observations mensuelles**, de janvier 2007 à décembre 2024.
- **Variable étudiée** — nombre de passagers commerciaux, obtenu en additionnant les départs et les arrivées, puis en agrégeant les données par mois.
- **Données sources** — 18 fichiers CSV annuels dans `data/asp-apt-2007-2024/`.
- **Série utilisée dans le code** — `data/df.csv`, qui contient la date et le total mensuel de passagers.

## Démarche

### 1. Analyse exploratoire et traitement des points atypiques

L’analyse décrit l’évolution de la série, ses fluctuations saisonnières et la rupture liée à la crise sanitaire. Les points atypiques sont détectés avec `tsoutliers`, puis une série corrigée est construite pour étudier la dynamique régulière du trafic.

La saisonnalité est étudiée à l’aide des autocorrélations, du périodogramme et de tests statistiques. La méthode **X13-SARIMA-SEATS** permet de décomposer la série et d’en extraire une version désaisonnalisée.

### 2. Estimation des modèles de prévision

L’étude compare onze méthodes appartenant à trois groupes.

- **Méthodes de référence et de décomposition** — naïve saisonnière, X13-SARIMA-SEATS, STL et STS.
- **Modèles de lissage exponentiel** — Holt-Winters, ETS, TBATS et ADAM.
- **Modèles SARIMA** — sélection automatique, spécification manuelle et estimation sur la série désaisonnalisée avec réintroduction de la saisonnalité.

### 3. Comparaison des performances

Les modèles sont réestimés sur la période d’apprentissage, puis comparés sur **les douze mois de 2024 de la série corrigée**. L’évaluation utilise l’erreur quadratique moyenne (**MSE**), le **R² hors échantillon** par rapport à la méthode naïve saisonnière et le cumul des erreurs quadratiques de prévision (**CSPE**).

La correction des points atypiques est effectuée sur l’ensemble de la série avant la séparation entre apprentissage et test. Les observations de 2024 contribuent donc au prétraitement de la série sur laquelle les prévisions sont évaluées.

Le **test de Diebold-Mariano** complète cette comparaison en évaluant les pertes de prévision des modèles par rapport à un modèle **AR(1)**.

## Quelques résultats

### Évolution et saisonnalité

Le trafic augmente globalement jusqu’en 2019, chute fortement en 2020, puis reprend progressivement. La série présente une saisonnalité annuelle marquée, avec des pics de trafic en été. Les points atypiques se concentrent principalement autour de 2020–2021.

### Performances sur la période de test

| Modèle | MSE | R² hors échantillon |
|---|---:|---:|
| **SARIMA manuel** | **178 891 211 416** | **0,609** |
| STS | 186 939 111 927 | 0,591 |
| X13-SARIMA-SEATS | 187 939 518 281 | 0,589 |
| SARIMA automatique | 350 777 289 015 | 0,233 |
| Naïve saisonnière | 457 212 516 653 | Référence |
| STL | 596 266 870 822 | −0,304 |

Le **SARIMA manuel**, de spécification **(1,1,1)(0,1,1)₁₂**, obtient le MSE le plus faible. Sur la période de test, son erreur quadratique moyenne est inférieure d’environ **61 %** à celle de la méthode naïve saisonnière. STS et X13-SARIMA-SEATS obtiennent des résultats proches.

Le SARIMA automatique présente un meilleur AIC en échantillon, tandis que le SARIMA manuel obtient les meilleures performances sur la période de test. Le test de Diebold-Mariano indique également des pertes de prévision significativement plus faibles pour le SARIMA manuel que pour l’AR(1), avec une **p-value inférieure à 0,001**.

## Technologies

**Langage** — R

**Manipulation et visualisation**

- `dplyr`
- `readr`
- `tidyr`
- `tibble`
- `ggplot2`

**Séries temporelles et prévision**

- `forecast`
- `tsoutliers`
- `moments`
- `RJDemetra`
- `smooth`
- `seastests`

**Document**

- Quarto

## Structure du dépôt

```text
prevision-trafic-aerien-france/
├── README.md
├── .gitattributes
├── code/
│   └── code_TPC_Amelie_PIRES.qmd
├── data/
│   ├── df.csv
│   └── asp-apt-2007-2024/
│       ├── ASP_APT_2007.csv
│       ├── …
│       └── ASP_APT_2024.csv
└── rapport/
    └── DOSSIER.pdf
```

Pour reproduire l’analyse, télécharger le dépôt complet, ouvrir `code/code_TPC_Amelie_PIRES.qmd` dans RStudio et exécuter les blocs dans l’ordre depuis la racine du dépôt, avec les bibliothèques R nécessaires installées.

## Rapport

Le rapport présente la démarche, les graphiques, les résultats détaillés et les annexes. [Consulter le rapport](rapport/DOSSIER.pdf)

## Auteur

**Amélie Pires**

Master 1 Économétrie Appliquée — Techniques de Prévision et Conjoncture — IAE Nantes, 2025–2026.

## Contact

**Amélie Pires**

Mail [amelie.pires@hotmail.com](mailto:amelie.pires@hotmail.com) · [LinkedIn](https://www.linkedin.com/in/amelie-pires) · [GitHub](https://github.com/aps-18)
