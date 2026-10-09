# Predictive Market Analysis / Analyse prédictive des marchés

[English](#english) · [Français](#français)

> **Status / État :** project setup and research design. No predictive performance has been established yet. / Mise en place et conception de la recherche. Aucune performance prédictive n'a encore été établie.

## English

### Goal

Build a reproducible system that uses **company communications** and **customer voice** to forecast which consumer-technology topics will become significant. The intended output is a ranked list of emerging topics, each supported by dated evidence and an estimated probability of growth.

The team will decide the industries, historical period, forecast horizon, and definition of a “significant trend” after auditing the available data. Well-known shifts, such as touchscreen phones or wireless headphones, can be historical case studies when enough earlier evidence exists.

### How the system will work

1. **Collect dated text** from company announcements, product pages, reports, reviews, and discussions. Store each document's source, URL, publication date, and archive date where applicable.
2. **Discover and track topics** with a transparent TF-IDF + NMF baseline. Compare text-embedding models where they improve the grouping of different expressions of the same need.
3. **Build time-based signals** for each topic: changes in discussion volume, recurring customer needs, sentiment or complaints, company activity, and the diversity of sources.
4. **Forecast future growth** with a logistic-regression baseline. Test a more flexible model, such as LightGBM, only if it improves held-out results.
5. **Replay history** at several cutoff dates. At each cutoff, use only information available then, rank the topics, and compare the predictions with what happened during the chosen forecast horizon.

### Evidence of success

The evaluation will include **all eligible topics**, including ones that did not become trends. We plan to report precision among the top predictions, recall of major shifts, lead time, false alarms, and performance against a simple growth-based baseline. The team will define outcome criteria before scoring the final historical tests.

**Historical validity matters:** a document, label, or feature created after a simulated cutoff cannot enter that forecast. Modern pretrained embedding models may contain knowledge of past outcomes; results using them will be reported separately from the strict, time-limited baseline.

### Likely tools

Python; DuckDB and Parquet for dated data; scikit-learn for TF-IDF, NMF, logistic regression, and evaluation; Sentence Transformers and BERTopic for embedding experiments; LightGBM for forecasting comparisons; and Streamlit for a demonstration. This stack is provisional and will be refined after the data audit.

### First milestones

- Audit historical coverage, access conditions, and timestamps for candidate data sources.
- Define the topic unit, forecast horizon, and independent evidence of trend significance.
- Build a small dated corpus and a reproducible TF-IDF + NMF baseline.
- Run the first chronological backtest before expanding the corpus or model complexity.

## Français

### Objectif

Construire un système reproductible qui utilise **les communications des entreprises** et **la voix des clients** pour prévoir les sujets qui deviendront importants dans la technologie grand public. Le résultat visé est un classement de sujets émergents, accompagné de preuves datées et d'une probabilité estimée de progression.

L'équipe choisira les secteurs, la période historique, l'horizon de prévision et la définition d'une « tendance majeure » après avoir vérifié les données disponibles. Des évolutions connues, comme les téléphones tactiles ou les écouteurs sans fil, pourront servir d'études de cas si suffisamment d'informations antérieures existent.

### Fonctionnement prévu

1. **Collecter des textes datés** : annonces, pages de produits et rapports d'entreprises, avis et discussions de clients. Conserver la source, l'URL, la date de publication et, si nécessaire, la date d'archivage.
2. **Découvrir et suivre les sujets** avec une méthode de référence TF-IDF + NMF. Comparer des modèles d'embeddings lorsque ceux-ci regroupent mieux les différentes formulations d'un même besoin.
3. **Construire des signaux dans le temps** pour chaque sujet : évolution du nombre de mentions, besoins récurrents, avis ou plaintes, activité des entreprises et diversité des sources.
4. **Prévoir la progression** avec une régression logistique de référence. Tester un modèle plus souple, comme LightGBM, s'il améliore les résultats sur des données réservées à l'évaluation.
5. **Rejouer l'histoire** à plusieurs dates limites. À chaque date, n'utiliser que les informations alors disponibles, classer les sujets et comparer les prévisions à la période suivante.

### Mesurer la réussite

L'évaluation inclura **tous les sujets admissibles**, y compris ceux qui ne sont jamais devenus des tendances. Nous prévoyons de mesurer la précision des premiers sujets annoncés, le rappel des changements majeurs, l'avance obtenue, les fausses alertes et le gain face à une méthode simple fondée sur la croissance récente. Les critères de réussite seront définis avant le test historique final.

**La validité historique est essentielle :** aucun document, libellé ou signal postérieur à la date simulée ne peut entrer dans la prévision. Les modèles d'embeddings préentraînés récemment peuvent déjà connaître des événements passés ; leurs résultats seront présentés séparément de la méthode de référence strictement limitée aux données de l'époque.

### Outils envisagés

Python ; DuckDB et Parquet pour les données datées ; scikit-learn pour TF-IDF, NMF, la régression logistique et l'évaluation ; Sentence Transformers et BERTopic pour les essais d'embeddings ; LightGBM pour comparer les prévisions ; et Streamlit pour la démonstration. Cette sélection sera affinée après l'audit des données.

### Premières étapes

- Vérifier la couverture historique, les conditions d'accès et la qualité des dates des sources envisagées.
- Définir ce qu'est un sujet, l'horizon de prévision et les preuves indépendantes d'une tendance majeure.
- Constituer un petit corpus daté et une méthode de référence TF-IDF + NMF reproductible.
- Réaliser un premier test chronologique avant d'élargir le corpus ou de complexifier les modèles.

## Repository layout / Organisation du dépôt

```text
data/       Data provenance notes; datasets are not committed by default.
notebooks/  Exploratory analysis and historical backtest demonstrations.
src/        Data preparation, topic extraction, and forecasting code.
reports/    Figures and evaluation summaries.
```

See [`data/README.md`](data/README.md) for the data-handling conventions. Dataset licensing and access terms must be checked before adding any source.

Voir [`data/README.md`](data/README.md) pour les conventions relatives aux données. Les droits d'utilisation et les conditions d'accès doivent être vérifiés pour chaque source.

