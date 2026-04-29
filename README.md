# Test technique : ML / MLOps Engineer

> **Pratique IA - SBI NORAM**
> Délai : **48h** à compter de la réception du sujet.
> Ce `README.md` est une **livrable à part entière** : soigne-le.
> Une partie des sujets MLOps (CI/CD, monitoring, drift, retraining, sécurité, IA responsable) **n'est pas demandée à l'écrit** : elle est abordée en entretien technique à l'oral.

---

## 1. Contexte

Une chaîne de distribution alimentaire québécoise cherche à anticiper les **ruptures de stock sur ses SKUs critiques**, avec un horizon de prévision de **3 jours**. L'équipe data interne est **peu mature** : les solutions livrées doivent donc être **robustes, documentées et maintenables**, par quelqu'un d'autre que toi.

Tu es mandaté·e pour poser les bases d'un pipeline ML bout-en-bout, de l'exploration des données jusqu'à l'architecture de déploiement cloud, en passant par le packaging et la mise en production du modèle.

---

## 2. Dataset

Un dataset CSV fictif (`stocks.csv`) est fourni :

- **~36 500 lignes** couvrant **365 jours** (année 2024)
- **20 SKUs × 5 magasins**

### Colonnes :

| Colonne | Description |
|---|---|
| `date` | Date d'observation |
| `sku_id` | Identifiant du produit |
| `store_id` | Identifiant du magasin |
| `sales_qty` | Quantité vendue ce jour-là |
| `stock_level` | Niveau de stock en fin de journée |
| `promotion_flag` | Indicateur de promotion (0/1) |
| `temperature` | Température extérieure (°C) |
| `day_of_week` | Jour de la semaine (0 = lundi) |
| `stockout_next_3d` | **Variable cible** : 1 si rupture dans les 3 prochains jours, 0 sinon |
| `stock_risk_score` | Score de risque de rupture |

> Le dataset contient **plusieurs anomalies volontaires**. À toi de les identifier et de les traiter.

---

## Partie 1 - Modélisation

### 1.1 Analyse exploratoire (EDA)

Identifie les **principales anomalies / problèmes** dans les données et décris précisément comment tu les traites (imputation, suppression, correction, exclusion de variable…). Justifie chaque décision.

### 1.2 Modèle de prévision

Entraîne le modèle de ton choix. Dans le `README.md`, **justifie ton choix** en tenant compte du contexte :

- horizon de prévision de 3 jours,
- volumétrie disponible,
- maturité technique faible de l'équipe cliente (qui devra maintenir le modèle).

### 1.3 Évaluation

Évalue ton modèle avec une **métrique adaptée au contexte business** (coût d'une rupture vs. coût d'un surstock). **Justifie le choix de la métrique**.

---

## Partie 2 - MLOps & mise en production

### 2.1 Structure du repo

Le code doit être organisé comme s'il partait en production :

```bash
.
├── src/
│   ├── preprocessing.py
│   ├── training.py
│   ├── evaluation.py
│   └── api.py
├── tests/
│   └── ...
├── Dockerfile
├── Makefile (ou run.sh)
├── requirements.txt (ou pyproject.toml)
└── README.md
```

Tu peux ajouter d'autres fichiers/dossiers selon ton organisation, mais cette structure minimale est attendue.

### 2.2 Packaging du modèle

Le modèle doit être **exposé via une API HTTP** (FastAPI, Flask ou équivalent) avec au minimum :

- un endpoint `POST /predict` qui prend en entrée une ou plusieurs observation(s) et renvoie la prédiction de rupture sur 3 jours (et idéalement la probabilité associée),
- un endpoint `GET /health` qui renvoie un statut `200 OK` quand l'API est prête.

L'API doit être **containerisée** via le `Dockerfile` fourni, et démarrable en une commande (`make run`, `docker run …`, etc.).

#### Contrat d'API minimal

Pour assurer la reproductibilité de la pré-évaluation automatique (cf. section *Pré-évaluation automatique* plus bas), respecte ce contrat minimal :

- **Port exposé** : `8000`
- **Tag d'image Docker** buildable via `docker build -t stockout-api:test .` (depuis la racine du repo)
- **Payload `POST /predict`** :

```json
{
  "instances": [
    {
      "date": "2024-12-15",
      "sku_id": "SKU_001",
      "store_id": "STORE_1",
      "sales_qty": 25,
      "stock_level": 10,
      "promotion_flag": 0,
      "temperature": 5.0,
      "day_of_week": 6
    }
  ]
}
```

- **Réponse attendue** : un JSON contenant les prédictions (format laissé à ton appréciation, mais documenté dans ton README).

Tu peux enrichir l'API avec d'autres endpoints / champs si tu le juges utile.

### 2.3 Tests unitaires

Au minimum **2 tests unitaires** pertinents (préprocessing, fonctions critiques, ou endpoint de l'API). Le choix de ce qui est testé est lui-même un signal, justifie-le brièvement.

### 2.4 Architecture de déploiement cloud

Décris dans le `README.md` comment tu déploierais ce modèle sur la plateforme cloud de ton choix. **Format au choix** :

- une liste de bullets,
- un schéma Mermaid intégré dans le README,
- une image (PNG/SVG) référencée dans le README.

---

## Modalités de soumission

- **Délai** : 48h à compter de la réception du sujet.
- **Stack** : Python 3.10+, librairies libres (scikit-learn, XGBoost, LightGBM, PyTorch, etc.).
- **Process Git attendu** :
  - Travail sur une **branche** dédiée (pas de commits directs sur `main`),
  - Commits atomiques avec messages clairs (Conventional Commits apprécié),
  - Livraison via **Pull Request** sur le repo, avec une description de PR soignée (résumé, choix techniques, points d'attention).
- **Format de livraison** : la livraison se fait par **Pull Request ouverte sur `main`**. La PR n'a pas besoin d'être mergée, elle sera revue en l'état. La structure de repo de la section 2.1 est obligatoire ; tu es libre d'ajouter ce que tu juges utile.
- **Reproductibilité** : ton code doit pouvoir être exécuté de bout en bout par une personne tierce avec les seules instructions du `README.md`.
- **Checks rouges** : la branche `main` est protégée et les checks de pré-évaluation sont obligatoires pour merger. Si certains checks restent rouges à la deadline, livre quand même ta PR : nous l'examinerons en l'état et les échecs ne sont pas éliminatoires en soi.

---

## Grille d'évaluation

Le test est noté sur 5 axes. Chaque axe est évalué indépendamment.

| Axe | Pondération | Ce qui est évalué |
|---|---|---|
| **1. Data Science** | 20 % | Qualité de l'EDA, détection des anomalies, choix du modèle, validation, métrique business, lucidité sur les pièges du dataset |
| **2. Qualité du code** | 20 % | Lisibilité, modularité, typage, gestion des erreurs, tests unitaires, respect de la structure de repo |
| **3. MLOps & industrialisation** | 30 % | Packaging API, Dockerfile, reproductibilité, gestion des dépendances, automatisation (Makefile/CI), versioning |
| **4. Architecture cloud** | 15 % | Pertinence et clarté du schéma de déploiement (ingestion, training, registry, serving, retraining) |
| **5. Communication** | 15 % | Qualité du `README.md`, justifications des choix, documentation, qualité de la PR (description, commits) |


**Posture attendue** : on ne cherche pas la solution parfaite, on cherche un·e ingénieur·e capable de **faire des choix justifiés, de prioriser sous contrainte de temps, et de livrer un travail propre, traçable et maintenable**.

---

> **À propos de l'utilisation d'outils d'IA**
>
> L'usage d'assistants IA (Copilot, Cursor, ChatGPT, Claude, etc.) est **autorisé** sur ce test, on ne va pas se cacher, c'est notre quotidien à tous aujourd'hui. Ce qui nous intéresse, ce n'est pas *si* tu en as utilisé, mais **comment** : qu'est-ce que tu lui as délégué, ce que tu as relu/corrigé, et ce que tu as choisi de faire toi-même.

Bonne chance.
