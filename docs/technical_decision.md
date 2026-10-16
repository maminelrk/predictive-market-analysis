# Technical decision / Décision technique

**Date:** 2026-10-16  
**Status:** chosen architecture for version 1; data coverage and outcome labels still require validation.

## Français

### Décision en une phrase

La version 1 utilisera **TF-IDF + NMF pour découvrir les sujets**, puis une **régression logistique régularisée pour prévoir leur progression** à un horizon défini. C'est un pipeline de deux modèles : NMF décrit *de quoi parlent les textes* ; la régression estime *quels sujets progresseront*. La période, le secteur et le seuil de « tendance importante » restent à fixer après le contrôle des données.

### Architecture retenue

| Étape | Outil / méthode retenu | Rôle et justification |
| --- | --- | --- |
| Acquisition | Python, `requests`, `BeautifulSoup` pour les pages autorisées ; fichiers de recherche et API officielles quand disponibles | Collecter les textes, URL et dates de manière reproductible ; consigner les conditions d'accès de chaque source. |
| Stockage | Parquet + DuckDB | Conserver des tables datées, filtrer les périodes et agréger sans charger tout le corpus en mémoire. |
| Préparation | Python + scikit-learn | Nettoyage, dédoublonnage, langue, séparation entreprise/consommateur, vocabulaire et expressions de 1–2 mots. |
| Découverte des sujets | `TfidfVectorizer` + `NMF` de scikit-learn | Sujets lisibles grâce aux termes les plus pondérés ; apprentissage uniquement sur les textes accessibles à la date simulée. |
| Signaux temporels | Agrégations mensuelles DuckDB/Python | Volume relatif, croissance récente, persistance, diversité des entreprises/sources et convergence entre annonces et voix des clients. Normaliser par le volume de chaque source. |
| Prévision | `LogisticRegression` régularisée de scikit-learn | Produire un classement et des scores probabilistes de progression à partir de signaux explicables ; vérifier leur calibration avant de les présenter comme des probabilités fiables. |
| Évaluation | Tests chronologiques glissants, `precision@k`, rappel des tendances, avance et fausses alertes | Comparer aux résultats futurs sans utiliser de documents ou d'étiquettes du futur ; confronter le modèle à une règle simple fondée sur la croissance récente. |
| Démonstration | Streamlit + graphiques Python | Choisir une date historique et montrer sujets, indices disponibles à cette date, prévisions et résultat ultérieur. |
| Collaboration | GitHub, environnement Python isolé et dépendances figées pour les expériences | Partager le code, les définitions et les résultats ; ne pas publier les corpus bruts sans vérifier les droits. |

### Règles expérimentales à figer avant les scores

1. **Unité prédite :** un sujet à une date limite. Définir l'horizon (par exemple 12 ou 24 mois) seulement après avoir vérifié la longueur des séries et la disponibilité des preuves de résultat.
2. **Découverte sans fuite :** ajuster TF-IDF et NMF sur les documents disponibles avant chaque date limite. Réutiliser ce modèle pour classer les documents de la période observée ; ne pas construire le vocabulaire ni nommer les sujets à partir de textes futurs. Documenter le rapprochement des sujets entre deux dates.
3. **Apprentissage supervisé :** chaque exemple est `(sujet, date limite, signaux connus, résultat futur)`. Un exemple ne peut entrer dans l'entraînement que lorsque tout son horizon futur est terminé. Les périodes finales servent aux tests, avec séparation par date limite plutôt que répartition aléatoire des lignes.
4. **Résultat indépendant :** définir à l'avance les règles de succès et d'échec, avec des indices de demande/adoption indépendants des annonces utilisées en entrée. Inclure les sujets qui n'ont pas émergé. Une hausse de mentions ou un lancement ne prouvent pas à eux seuls un succès commercial.
5. **Comparaison honnête :** rapporter la règle « croissance récente » et la régression sur les mêmes dates et sujets. Vérifier la calibration si suffisamment d'exemples existent. Ne présenter aucune précision chiffrée avant le premier test historique complet.

### Choix écartés pour la version 1

- **LLM entraîné ou ajusté localement :** non nécessaire pour les variables et les preuves visées ; coûterait davantage de mémoire, de temps et de contrôle expérimental.
- **BERTopic / embeddings récents :** réservés à une comparaison exploratoire ultérieure. Un modèle préentraîné après les événements historiques peut connaître leur vocabulaire ou leur importance ; ses résultats ne doivent pas être confondus avec le test historique strict.
- **LightGBM :** candidat ultérieur si le nombre d'exemples étiquetés est suffisant et si son gain face à la régression est démontré sur les périodes de test.

**Matériel :** le Ryzen 5 5500 et 16 Go de RAM conviennent au pilote si les gros fichiers sont lus par blocs et les analyses limitées à des sous-ensembles pertinents. La RTX 5060 8 Go n'est pas requise pour le pipeline retenu. La durée réelle dépendra du volume et devra être mesurée ; aucun entraînement sur le corpus Amazon complet n'est promis.

**Point bloquant actuel :** le [registre des sources](../data/source_audit.md) est documentaire. La couverture par mois, la qualité des dates et les étiquettes de résultats n'ont pas encore été mesurées. Si elles ne permettent pas assez d'exemples positifs et négatifs, produire un classement descriptif et le signaler comme tel plutôt que d'annoncer une précision prédictive.

### Prochaine séquence de travail

1. Prélever et auditer un petit corpus daté d'annonces et de voix des clients ; publier un tableau de couverture et un registre des droits.
2. Fixer la catégorie pilote, les dates, l'horizon et le protocole de résultat avant de regarder les performances.
3. Construire le pipeline NMF et la règle de croissance récente ; vérifier manuellement les sujets et les dates.
4. Former la régression sur les seules périodes dont l'horizon est connu ; réaliser le test chronologique final.
5. Présenter les résultats, les erreurs, les limites et une démonstration Streamlit dans le rapport d'avancement.

## English

### Decision

Version 1 will use **TF-IDF + NMF for topic discovery** and **regularized logistic regression for forecasting topic growth**. NMF answers what the documents discuss; logistic regression ranks which observed topics are likely to grow over a defined future horizon. Industry, historical window, horizon, and significance threshold will be set after the data coverage audit.

| Stage | Chosen tool / method | Purpose |
| --- | --- | --- |
| Collection | Python, `requests`, `BeautifulSoup`, official downloads/APIs | Reproducible text, dates, provenance, and access-term records. |
| Storage | Parquet + DuckDB | Date filtering and aggregation without loading the entire corpus into RAM. |
| Topics | scikit-learn `TfidfVectorizer` + `NMF` | Interpretable topic terms; fit only on text available at each simulated cutoff. |
| Signals | Monthly DuckDB/Python aggregates | Source-normalized mentions, growth, persistence, source/company diversity, and company–customer agreement. |
| Forecast | scikit-learn regularized `LogisticRegression` | Rank topics with an explainable feature set; check calibration before treating scores as reliable probabilities. |
| Evaluation | Rolling historical cutoffs; precision@k, recall, lead time, false alarms | Compare with a recent-growth rule on the same eligible topics and dates. |
| Demo | Streamlit | Show evidence known at a selected historical cutoff, prediction, and later outcome. |

**Historical protocol:** fit the vectorizer and NMF only on pre-cutoff text; define topic names without later evidence; train a `(topic, cutoff)` forecasting row only after its outcome horizon has elapsed; reserve later cutoffs for testing. Define the topic universe and independent success/failure labels before scoring. Include unsuccessful topics. The classifier is a selected architecture, **not yet a trained or validated model**.

**Other models:** recent embeddings/BERTopic and LightGBM are comparison experiments after the strict baseline. Fine-tuning a large language model is outside version 1. The selected pipeline runs on the Ryzen 5 5500/16 GB machine with chunked data handling and does not require the 8 GB GPU; full-corpus runtime has not been measured.

**Next milestone:** measure monthly source coverage and timestamp quality, lock the outcome protocol, then build and backtest this pipeline. If labels are too sparse, report descriptive rankings without a predictive accuracy claim.

## Method and tool references / Références

- [scikit-learn: TF-IDF + NMF topic extraction](https://scikit-learn.org/stable/auto_examples/applications/plot_topics_extraction_with_nmf_lda.html)
- [scikit-learn: logistic regression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)
- [scikit-learn: time-ordered validation](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html)
- [DuckDB: Parquet files](https://duckdb.org/docs/stable/data/parquet/overview)
- [Streamlit documentation](https://docs.streamlit.io/get-started)

