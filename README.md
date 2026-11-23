
# Prédiction du nombre de chômeurs en Tunisie (R)

Projet R de machine learning visant à prédire le **nombre total de chômeurs en Tunisie** (données trimestrielles INS) à partir d'indicateurs macroéconomiques annuels (PIB, inflation, dette publique, paiements d'intérêts).

> ⚠️ **Projet exploratoire.** La variable cible n'est disponible que sur 6 trimestres (T1 2022 – T2 2023). Les résultats ne permettent pas de tirer de conclusion économique ni de faire de la prévision réelle (voir *Limites*).

## Problématique

Comment les facteurs macroéconomiques influencent-ils le nombre de chômeurs en Tunisie, et dans quelle mesure permettent-ils de le prédire ?

## Données

| Fichier | Source | Contenu |
|---|---|---|
| `data/data.csv` | INS (Institut National de la Statistique, Tunisie) | Indicateurs trimestriels emploi, chômage, démographie (72 lignes, 2022–2024) |
| `data/donnees_economiques_tunisie.pdf` | Banque mondiale, *Données économiques Tunisie* (Tableau 1) | PIB réel, inflation, dette publique, paiements d'intérêts (2020–2025) |

- Les valeurs **2024 et 2025** du rapport sont des **projections** de la Banque mondiale.
- Certaines lignes de `data.csv` (ex. nombre de retraités 2024) sont des **estimations ou projections** (colonne `Remarques supplémentaires`).

## Méthodologie

1. **Importation** : lecture de `data.csv` et extraction des indicateurs macro depuis le PDF (`pdftools` + expressions régulières, avec contrôles automatiques de cohérence).
2. **Prétraitement** : nettoyage des libellés, variables `Annee` et `Date_trim`, construction de la cible (nombre de chômeurs, total tous sexes), fusion avec les variables macro annuelles.
3. **Variable dérivée** : `Ratio_interets_dette = paiements d'intérêts / dette publique × 100` (ce n'est **pas** un taux d'intérêt réel).
4. **Analyse exploratoire** : statistiques descriptives, corrélations, graphiques.
5. **Choix des prédicteurs** : suppression automatique des variables sans variance et des variables redondantes (|r| ≥ 0,9).
6. **Modélisation** (`caret`) : régression linéaire, Random Forest, kNN, comparés à une prévision de référence (moyenne).
7. **Validation** : leave-one-out (LOOCV), adaptée au très faible nombre d'observations. Métriques : RMSE et MAE.

## Structure du dépôt

```
.
├── README.md
├── .gitignore
├── projet.Rproj
├── analyse_chomage_tunisie.Rmd   # script principal
├── data/
│   ├── data.csv
│   └── donnees_economiques_tunisie.pdf
└── docs/
    └── Problematique.docx
```

## Installation et exécution

**Prérequis** : R ≥ 4.2, RStudio (recommandé) et une distribution LaTeX pour la sortie PDF (par ex. TinyTeX).

```r
install.packages(c("readr", "dplyr", "stringr", "ggplot2", "pdftools",
                   "caret", "randomForest", "kableExtra",
                   "knitr", "rmarkdown", "tinytex"))
tinytex::install_tinytex()   # une seule fois, si LaTeX n'est pas installé
```

Ouvrir `projet.Rproj` dans RStudio, puis :

```r
rmarkdown::render("analyse_chomage_tunisie.Rmd")
```

Le rapport PDF `analyse_chomage_tunisie.pdf` est généré à la racine. Les chemins de données sont relatifs à la racine du dépôt.

## Limites

- **6 observations** seulement pour la cible (2022 à T2 2023).
- Les variables macro sont **annuelles** : seulement 2 valeurs distinctes (2022 et 2023), donc parfaitement redondantes entre elles (corrélation = ±1). Un seul prédicteur est retenu par la sélection automatique.
- La validation leave-one-out ne respecte pas l'ordre chronologique : elle ne mesure pas une capacité de prévision future.
- Corrélation ≠ causalité.

## Pistes d'amélioration

- Allonger la série (publications trimestrielles de l'INS sur une longue période) : le code s'adapte automatiquement.
- Prédire le **taux de chômage** plutôt que le nombre brut (la problématique initiale, `docs/Problematique.docx`, visait le taux).
- Ajouter des variables trimestrielles et un vrai taux d'intérêt réel (taux nominal − inflation).
- Validation temporelle (`timeslice`), modèles régularisés (Ridge/Lasso), ARIMA/ARIMAX.

## Auteur

**Malak Saidani** — [github.com/MalakSaidani26](https://github.com/MalakSaidani26)
