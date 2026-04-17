## Contexte

Une chaîne de distribution alimentaire québécoise cherche à anticiper les **ruptures de stock sur ses 500 SKUs les plus critiques**, avec un horizon de prévision de **3 jours**. L'équipe data interne est peu mature : les solutions proposées doivent donc être robustes, documentées et maintenables.

Tu es mandaté pour poser les bases d'un pipeline ML bout-en-bout, de l'exploration des données jusqu'à une architecture de déploiement cloud.

Un dataset CSV fictif est fourni avec les colonnes suivantes :

| Colonne | Description |
|---|---|
| `date` | Date de la observation |
| `sku_id` | Identifiant du produit |
| `store_id` | Identifiant du magasin |
| `sales_qty` | Quantité vendue |
| `stock_level` | Niveau de stock |
| `promotion_flag` | Indicateur de promotion |
| `temperature` | Température extérieure |
| `day_of_week` | Jour de la semaine |
| `stockout_next_3d` | Variable cible qui indique si on est hors stock |
| `stock_risk_score` | Risque d'etre hors stock |

> Le dataset (~5 000 lignes) contient des **anomalies volontaires**

---

## Partie 1 — Modélisation

### 1. Analyse exploratoire (EDA)
Identifie les **2 à 3 problèmes principaux** dans les données et décris précisément comment tu les traites (imputation, suppression, correction…).

### 2. Modèle de prévision
Entraîne le modèle de ton choix. Dans le `README.md`, **justifie ce choix** en tenant compte du contexte (horizon de 3 jours, volumétrie, maturité de l'équipe).

### 3. Évaluation
Évalue ton modèle avec une métrique adaptée au **contexte business** (coût d'une rupture vs. coût d'un surstock). **Justifie le choix de la métrique.**

---

## Partie 2 — MLOps & mise en production

### 1. Structure du repo

Le code doit être organisé comme s'il partait en production :

```bash
├── src/
│   ├── preprocessing.py
│   ├── training.py
│   └── evaluation.py
├── Makefile (ou run.sh)
├── requirements.txt (ou pyproject.toml)
└── README.md
```

### 2. Architecture de déploiement

Dans le `README.md`, décris en **5 bullets maximum** comment tu déploierais ce modèle sur une plateforme cloud de ton choix. Pas de code attendu — une description de l'architecture envisagée suffit.

---

## Modalités de soumission

- **Délai : 48h** à compter de la réception du sujet
- Librairies libres : scikit-learn, XGBoost, LightGBM, etc.
- Le `README.md` est une **livrable à part entière** — soigne-le