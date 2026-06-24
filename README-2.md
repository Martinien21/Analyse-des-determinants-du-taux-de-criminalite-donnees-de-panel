# Analyse des déterminants du taux de criminalité — Données de panel

> Projet académique réalisé à l'ENSGMM (Bénin) dans le cadre du cours d'économétrie des données de panel.

---

## Aperçu

Ce projet analyse les facteurs socio-économiques, démographiques et institutionnels qui influencent le **taux de criminalité** dans les comtés de Caroline du Nord (États-Unis) sur la période **1981–1987**, à partir du dataset `Crime` du package R `plm`.

L'analyse mobilise plusieurs estimateurs de panel (Pooled OLS, Between, Premières différences, Effets fixes, Effets aléatoires) et conclut, après une batterie de tests statistiques, que le **modèle à effets fixes avec effets temporels** est le plus approprié.

---

## Objectifs

1. Analyser les variations du taux de criminalité entre comtés et dans le temps
2. Évaluer l'impact des facteurs socio-économiques, démographiques et judiciaires
3. Comparer les spécifications de panel et sélectionner la plus pertinente

---

## Structure du projet

```
.
├── Devoir_Martinien.Rmd     # Document R Markdown principal
├── Devoir_Martinien.html    # Rapport rendu en HTML
├── Devoir_Martinien.pdf     # Rapport rendu en PDF
└── README.md
```

---

## Données

**Source :** package R `plm`, dataset `Crime`  
**Unité d'observation :** Comté (county), État de Caroline du Nord  
**Période :** 1981 à 1987  
**Structure :** Panel équilibré court (*short panel*, T < N)  
**Valeurs manquantes :** Aucune

### Variables principales retenues (après sélection par `step()`)

| Variable       | Description                              |
|----------------|------------------------------------------|
| `taux_crime`   | Crimes commis par personne (cible)       |
| `density`      | Densité de population (hab./mile²)       |
| `prob_arr`     | Probabilité d'arrestation                |
| `prob_cond`    | Probabilité de condamnation              |
| `police`       | Effectifs policiers par habitant         |
| `pct_jeune_h`  | % jeunes hommes dans la population       |
| `pct_minor`    | % minorités (1980)                       |
| `region`       | Région (autre / ouest / centre)          |

---

## Méthodologie

### 1. Préparation des données
- Sélection et renommage des variables
- Vérification de la structure de panel (`pdim`)
- Analyse exploratoire : hétérogénéité individuelle et temporelle
- Matrice de corrélation
- Sélection de variables par critère AIC (`step()`)
- Vérification de la multicolinéarité (VIF < 3)

### 2. Test de stationnarité
- Test de **Levin-Lin-Chu** (LLC)
- Test de **Im-Pesaran-Shin** (IPS)
- Résultat : toutes les variables sont stationnaires → recours au panel classique

### 3. Estimateurs comparés

| Estimateur              | Commande `plm`          |
|-------------------------|-------------------------|
| Pooled OLS              | `model = "pooling"`     |
| Between                 | `model = "between"`     |
| Premières différences   | `model = "fd"`          |
| Effets fixes (Within)   | `model = "within"`      |
| Effets aléatoires (RE)  | `model = "random"`      |
| Effets fixes + temporels| `within` + `factor(year)`|

### 4. Sélection du modèle

| Test                        | Résultat                                     |
|-----------------------------|----------------------------------------------|
| Breusch-Pagan LM (`plmtest`)| Effets individuels significatifs (p < 2.2e-16)|
| F-test (`pFtest`)           | FE préféré au Pooled OLS (p < 2.2e-16)       |
| Test de Hausman (`phtest`)  | FE préféré au RE (p < 0.05)                  |
| F-test effets temporels     | Effets temporels significatifs (p < 0.05)    |

→ **Modèle retenu : Effets fixes individuels + effets temporels**

### 5. Diagnostic et correction

| Problème détecté             | Test utilisé                     |
|------------------------------|----------------------------------|
| Hétéroscédasticité           | Breusch-Pagan (`bptest`)         |
| Dépendance transversale      | Pesaran CD + LM de Breusch-Pagan |
| Autocorrélation sérielle     | Breusch-Godfrey (`pbgtest`)      |

**Correction :** Erreurs standards robustes de Driscoll-Kraay (`vcovSCC`)

---

## Principaux résultats

Après correction robuste, deux variables restent significatives au seuil de 5 % :

- **`prob_cond`** : une hausse de la probabilité de condamnation réduit le taux de criminalité de **0,09 %**
- **`police`** : une hausse du nombre de policiers par habitant est associée à une **augmentation** du taux de criminalité (+2,13) — interprétée comme un déploiement *réactif* dans les zones à fort taux de crime (endogénéité potentielle)

---

## Prérequis

**Langage :** R (≥ 4.0)

**Packages requis :**

```r
install.packages(c(
  "plm", "stargazer", "gplots", "ggplot2", "plotly",
  "lmtest", "tseries", "lattice", "dplyr", "corrplot",
  "car", "sandwich"
))
```

---

## Exécution

Ouvrir `Devoir_Martinien.Rmd` dans RStudio et cliquer sur **Knit** pour générer le rapport HTML ou PDF.

```r
rmarkdown::render("Devoir_Martinien.Rmd", output_format = "html_document")
```

---

## Auteur

**Martinien GBEMAHLOUE**  
Étudiant en Modélisation Aléatoire, Statistique et Finance (MASF)  
École Nationale Supérieure de Génie Mathématique et Modélisation — ENSGMM/UNSTIM, Bénin  
Date : Novembre 2025
