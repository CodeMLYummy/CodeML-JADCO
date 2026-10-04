# Hausse de loyer 2026 – Collection Équinoxe

CodeML 2026, défi JADCO « Clés en main ».

**Estimation finale : 2,7 %** (scénario central, fourchette de 0,7 % à 4,8 % selon l'évolution des concessions).

**Définition :** croissance médiane du **loyer effectif** (`sRentEffective`, concessions accessoires comprises), à **unité constante**. Chaque bail débutant en 2026 est comparé au bail précédent de 12 mois de la même unité (`sPropCode` + `sUnitCode`).

La hausse contractuelle prévue est de 4,8 %, et la variante « loyer seulement » de 4,0 %. Les justifications, le backtest 2023-2025 et les hypothèses sont détaillés dans le notebook.

## Structure du projet

```
.
├── starter.ipynb                     notebook principal (livrable évalué)
├── README.md
├── requirements.txt
├── equinoxe_listings.csv             ┐
├── equinoxe_lease_history.csv        │ données du CRM : à placer à la racine,
├── equinoxe_concessions.csv          │ NON incluses dans les livrables (voir Confidentialité)
├── equinoxe_asking_history.csv       ┘
└── data/                             données publiques
    ├── IPC_1810000401.csv
    ├── rmr-montreal-2019-en.xlsx … rmr-montreal-2025-en.xlsx
    ├── rmr-ottawa-2019-en.xlsx … rmr-ottawa-2025-en.xlsx
    ├── external_schl_rental_market.csv       produit par le notebook (section 13.1)
    └── external_regulatory_parameters.csv    produit par le notebook (section 13.3)
```

Les fichiers sources ne sont jamais modifiés. Tout renommage et toute transformation se font dans le notebook. Les deux fichiers `external_*.csv` sont régénérés à chaque exécution.

## Exécuter le notebook

1. Installer **Python 3.10 ou plus récent**.
2. (Recommandé) Créer un environnement virtuel :
   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows : .venv\Scripts\activate
   ```
3. Installer les dépendances :
   ```bash
   pip install -r requirements.txt
   ```
4. Placer les quatre fichiers `equinoxe_*.csv` à la racine du projet, et les fichiers publics dans `data/`.
5. Lancer Jupyter et exécuter tout le notebook dans l'ordre :
   ```bash
   jupyter notebook starter.ipynb
   ```
   Puis utiliser **Kernel > Restart & Run All**.

**En cas d'erreur « openpyxl est requis »** (section 13.1) : exécuter `%pip install -r requirements.txt` dans une cellule, puis **redémarrer le noyau**. Un paquet installé pendant qu'un noyau tourne n'est visible qu'après redémarrage. Utiliser `%pip` plutôt que `pip` garantit que l'installation se fait dans l'environnement du noyau.

## Versions des librairies

| Librairie | Version minimale | Version testée |
|---|---|---|
| Python | 3.10 | 3.12.3 |
| pandas | 2.2 | 3.0.2 |
| numpy | 1.26 | 2.4.4 |
| matplotlib | 3.8 | 3.10.8 |
| openpyxl | 3.1 | 3.1.5 |

Aucun modèle entraîné n'est livré. La prévision repose sur des ancrages publics et des règles explicites, sans régression ni modèle d'apprentissage automatique : avec 7 années utiles, une régression ou un modèle de type XGBoost ne serait pas justifiable (section 13.4).

## Organisation du notebook

| Section | Contenu |
|---|---|
| 1 à 7 | Notebook de départ fourni (chargement, effet de composition, méthode à unité constante, concessions, renouvellements) |
| 8 | Définition de la hausse et règle d'usage des mesures |
| 9 | Dictionnaire des données : signification réelle des colonnes, renommage, colonnes retirées |
| 10 | Tables de travail : baux enrichis des concessions, paires de baux comparables |
| 11 | Effet de composition : graphiques, décomposition prix / composition, vérification |
| 12 | Croissance à unité constante : contractuel vs effectif, renouvellements vs relocations, The Met (Ontario), robustesse |
| 13 | Données publiques (SCHL, IPC, TAL, Ontario) et réconciliation avec la tendance interne |
| 14 | Prévision 2026, `estimate_2026()`, `backtest()` sur 2023-2025, scénarios, prévision par immeuble et par nombre de chambres |
| Fin | Estimation finale et références |

## Sources publiques

| Source | Fichier(s) | Lien |
|---|---|---|
| SCHL, *Enquête sur les logements locatifs*, tableaux de données des rapports sur le marché locatif, RMR de Montréal et d'Ottawa, enquêtes 2019 à 2025 | `data/rmr-*.xlsx` | https://www.cmhc-schl.gc.ca |
| Statistique Canada, tableau 18-10-0004-01, *Indice des prix à la consommation mensuel, non désaisonnalisé* (IPC d'ensemble et composante loyers, Québec et Ontario) | `data/IPC_1810000401.csv` | https://www150.statcan.gc.ca/t1/tbl1/fr/tv.action?pid=1810000401 |
| Tribunal administratif du logement, paramètres annuels de fixation de loyer (2020 à 2026) | `data/external_regulatory_parameters.csv` | https://www.tal.gouv.qc.ca |
| Gouvernement de l'Ontario, ligne directrice sur l'augmentation des loyers (2019 à 2026) | `data/external_regulatory_parameters.csv` | https://www.ontario.ca/page/rent-increase-guideline |

La source précise de chaque taux du TAL et de chaque ligne directrice ontarienne figure dans `data/external_regulatory_parameters.csv` : URL, communiqué officiel ou source secondaire. Le taux du TAL de 2019 n'est publié qu'en image sur le site officiel ; il est laissé manquant plutôt que deviné.

Le jeu de données Kaggle suggéré dans les consignes n'a pas été utilisé : c'est un instantané unique d'annonces (juin 2024), sans historique comparable.

## Outils d'IA utilisés

- **Claude (Anthropic)**, modèle Claude Opus 5.5, via claude.ai. Utilisé comme assistant tout au long du projet : exploration des données, rédaction et vérification du code, recherche et citation des paramètres réglementaires, rédaction des cellules Markdown et de cette documentation.
- Les choix de méthode ont été discutés et validés par l'équipe, et chaque résultat a été vérifié par l'exécution du notebook.

Ces outils sont aussi cités dans la section **Références** du notebook, comme l'exigent les consignes.

## Confidentialité

Les données du CRM (`equinoxe_*.csv`) sont confidentielles. Elles **ne doivent pas être partagées, publiées ni déposées** dans un dépôt public (GitHub, Kaggle, etc.), et ne font **pas partie des livrables**. Si le projet est versionné avec Git, ajouter cette ligne au fichier `.gitignore` :

```
equinoxe_*.csv
```

Les fichiers publics de `data/` et les deux fichiers produits par le notebook peuvent être livrés.
