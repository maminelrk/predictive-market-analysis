# Data source audit / Audit des sources de données

**Status (2026-10-09):** documentation reviewed; no dataset has been downloaded or checked for year-by-year coverage. The pilot below tests feasibility and does not set the final industries, dates, or success criteria.

**État (09/10/2026) :** documentation examinée ; aucun corpus n'a encore été téléchargé ni vérifié année par année. Le pilote ci-dessous sert à tester la faisabilité, sans fixer les secteurs, les dates ou les critères de réussite définitifs.

## English

### What data is needed

The model needs two **input streams**: dated company communications (what firms announced or planned) and dated customer language (needs, complaints, expectations, reactions). A third, **independent outcome stream** must establish whether a topic later became significant. A product launch alone is evidence of supply, not proof of adoption. Each historical forecast must use only records available before its cutoff date.

### Source register

| Priority | Source and access | Role | What to collect | Main limitation / check |
| --- | --- | --- | --- | --- |
| A | [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/main.html), research downloads by category | Customer voice | Review text, title, rating, product ID, review timestamp; begin with `Cell_Phones_and_Accessories` (20.8M ratings) and a bounded `Electronics` sample (43.9M) | Reviews mainly follow a purchase. The overall 1996–2023 span does **not** establish usable early coverage for either category. Audit counts by year. Item metadata were collected later; do not use their current price, description, or aggregate rating in historical features. Stream files rather than loading a full category into 16 GB RAM. |
| A | [Apple Newsroom archive](https://www.apple.com/newsroom/archive/), [Sony press archive](https://www.sony.com/en/SonyInfo/News/Press/archive.html), [Samsung Global Newsroom](https://news.samsung.com/global/) | Company activity | Dated product announcements: title, body, product/category, company, source URL, publication date | Archives mix product announcements with unrelated corporate news. Check accessible historical years, missing pages, terms of use, and whether a date belongs to the original release. More than one company is needed to avoid learning a single firm's publicity cycle. |
| B | [Hacker News Search API](https://hn.algolia.com/api) | Early customer/enthusiast discussion | Story and comment text, creation time, URL, thread ID | Technology enthusiasts are not representative consumers. Date-filtered collection should include broad category discussions, not only hindsight-selected trend keywords. |
| B | [Stack Exchange public data dumps](https://stackoverflow.blog/2014/01/23/stack-exchange-cc-data-now-hosted-by-the-internet-archive/) | Supplemental user questions | Dated posts/comments from relevant sites | Technical-user and question-format bias; inspect site-specific coverage and dump license before inclusion. |
| B | [Wayback CDX index](https://github.com/internetarchive/wayback/blob/master/wayback-cdx-server/README.md) | Historical verification / gap filling | Captured company product pages, capture timestamp, original URL, status, digest | Capture time is a **latest possible availability check**, not necessarily the original publication date. Captures are incomplete; inspect text and deduplicate versions. |
| C | [SEC EDGAR](https://www.sec.gov/search-filings/edgar-search-assistance/accessing-edgar-data) | Supplemental company strategy | Dated filings and selected passages about products/markets | Low frequency and investor-oriented; useful for context, unlikely to be the main source of product-topic signals. |
| C | [Google Trends data and export](https://support.google.com/trends/answer/4365533?hl=en) | Supplemental outcome signal | Interest series for a *predeclared* list of topics and locations | Values are sampled and normalized, not absolute demand. Choosing queries after seeing the successful trends creates hindsight bias. Do not use as the only success label. |

**Automotive caution:** the Amazon `Automotive` category contains reviews of automotive products/accessories; it should not be treated as a dataset of car buyers or electric-vehicle adoption without checking the actual items. Car-specific sources require a separate audit.

### First feasibility pilot

Use **smartphones and connected accessories** as a provisional access test because both the review category and several company archives are available. The proposed window is 2014–2022, subject to measured coverage. It is an access and timestamp pilot, not a retrospective claim that selected famous products were predictable.

1. Record source URLs, access method, terms, extraction date, and exact fields available. Download or query a small, reproducible sample. Do not commit raw review text to GitHub.
2. Count records by **month, source, company, and product category**. Count usable text and missing/ambiguous timestamps separately. Check the earliest reliable month for each source.
3. Randomly inspect records from early, middle, and late periods. Estimate extraction errors, duplicates, irrelevant products, and whether customer text describes a need, a purchase reaction, or neither.
4. Store `published_at`, `first_available_at` (if known), and `collected_at` separately. For historical features, use the later defensible availability date when publication timing is uncertain. Never mix later item metadata, later votes, or revised text into an earlier cutoff.
5. Draft an **outcome protocol before selecting trends**: define the topic universe, forecast horizon, and at least two independent indicators of later significance (for example, sustained multi-source customer discussion and adoption evidence). Include topics that did not take off. Historical announcements may corroborate diffusion but cannot alone define market success.
6. Decide whether the measured coverage supports a chronological backtest. If it does, freeze the source list and extraction rules before modeling; if it does not, test the next candidate category or add a source.

**Deliverable from this pilot:** a coverage table, 20–50 inspected examples with error notes, a source/rights log, and a short go/no-go decision. There is no defensible accuracy estimate until the independent labels and time-separated backtest exist.

## Français

### Données nécessaires

Le modèle a besoin de deux **flux d'entrée** : des communications d'entreprises datées (ce que les sociétés annoncent ou préparent) et des textes de consommateurs datés (besoins, plaintes, attentes, réactions). Un troisième flux, **indépendant des entrées**, doit établir si un sujet est ensuite devenu important. Un lancement prouve une offre, mais pas son adoption. Chaque prévision historique doit se limiter aux informations disponibles avant la date simulée.

### Registre des sources

| Priorité | Source et accès | Rôle | Données à relever | Limite / vérification essentielle |
| --- | --- | --- | --- | --- |
| A | [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/main.html), téléchargement par catégorie | Voix des consommateurs | Texte, titre, note, identifiant de produit et date de l'avis ; commencer par `Cell_Phones_and_Accessories` (20,8 M de notes) et un échantillon limité d'`Electronics` (43,9 M) | Avis généralement postérieurs à l'achat. La période globale 1996–2023 ne garantit pas une bonne couverture ancienne par catégorie : compter les avis par année. Les métadonnées de produit collectées plus tard (prix, description, note globale) introduiraient une fuite d'information. Lire les fichiers par blocs sur un PC de 16 Go de RAM. |
| A | [Archives Apple](https://www.apple.com/newsroom/archive/), [Sony](https://www.sony.com/en/SonyInfo/News/Press/archive.html) et [Samsung](https://news.samsung.com/global/) | Activité des entreprises | Annonces de produits datées : titre, corps, produit/catégorie, société, URL, date | Séparer les annonces de produits des actualités institutionnelles. Vérifier les années accessibles, les pages manquantes, les conditions d'utilisation et l'origine des dates. Plusieurs entreprises évitent de mesurer seulement la communication d'une marque. |
| B | [API de recherche Hacker News](https://hn.algolia.com/api) | Discussions précoces | Texte des publications/commentaires, date de création, URL, fil | Public technophile non représentatif. Relever des discussions générales sur la catégorie, sans se limiter aux mots clés de tendances connues après coup. |
| B | [Archives publiques Stack Exchange](https://stackoverflow.blog/2014/01/23/stack-exchange-cc-data-now-hosted-by-the-internet-archive/) | Questions complémentaires | Publications/commentaires datés de sites pertinents | Biais lié aux utilisateurs techniques et au format des questions ; vérifier la couverture de chaque site et la licence. |
| B | [Index Wayback CDX](https://github.com/internetarchive/wayback/blob/master/wayback-cdx-server/README.md) | Vérification historique / lacunes | Anciennes pages de produits, date de capture, URL, état, empreinte | La capture ne donne pas nécessairement la date de publication. Archives incomplètes ; examiner le texte et supprimer les doublons. |
| C | [SEC EDGAR](https://www.sec.gov/search-filings/edgar-search-assistance/accessing-edgar-data) | Stratégie complémentaire | Dépôts réglementaires datés et passages pertinents | Peu fréquent et destiné aux investisseurs ; surtout utile pour le contexte. |
| C | [Google Trends](https://support.google.com/trends/answer/4365533?hl=en) | Indice complémentaire de résultat | Séries d'intérêt pour une liste de sujets et de régions définie à l'avance | Données échantillonnées et normalisées, pas une mesure absolue de la demande. Éviter les requêtes choisies après avoir vu les tendances gagnantes. |

**Attention à l'automobile :** la catégorie Amazon `Automotive` regroupe des produits et accessoires automobiles. Elle ne représente pas automatiquement les acheteurs de voitures ou l'adoption des véhicules électriques. Il faudra auditer des sources adaptées à ce secteur séparément.

### Premier pilote de faisabilité

Les **smartphones et accessoires connectés** sont un point de départ provisoire, car une catégorie d'avis et plusieurs archives d'entreprises existent. La fenêtre proposée, 2014–2022, dépendra de la couverture réelle. Ce pilote vérifie l'accès et la datation ; il ne prétend pas encore prédire des produits célèbres.

1. Consigner URL, accès, conditions d'utilisation, date de collecte et champs disponibles. Prélever un petit échantillon reproductible. Ne pas publier les avis bruts dans GitHub.
2. Compter les documents par **mois, source, entreprise et catégorie**, ainsi que les textes utilisables et les dates absentes ou ambiguës. Déterminer le premier mois fiable de chaque source.
3. Examiner au hasard des documents du début, du milieu et de la fin de la période : erreurs d'extraction, doublons, produits hors sujet, besoins exprimés ou simple réaction après achat.
4. Conserver séparément `published_at`, `first_available_at` (si connue) et `collected_at`. En cas d'incertitude, utiliser la date de disponibilité la plus prudente. Exclure les métadonnées, votes ou modifications postérieurs à la date simulée.
5. Définir **avant de choisir les tendances** l'univers des sujets, l'horizon de prévision et au moins deux indices indépendants d'importance future (par exemple, discussion durable dans plusieurs sources et preuve d'adoption). Inclure les sujets qui n'ont pas réussi. Une annonce peut confirmer la diffusion d'un sujet, mais ne suffit pas à prouver son succès commercial.
6. Décider si la couverture permet un test chronologique. Si oui, figer les sources et les règles d'extraction avant la modélisation ; sinon, tester une autre catégorie ou ajouter une source.

**Livrable du pilote :** tableau de couverture, 20 à 50 exemples examinés avec notes d'erreur, registre des sources et droits d'utilisation, puis décision motivée de poursuivre ou d'ajuster le périmètre. Aucune précision prédictive ne peut être estimée sérieusement avant de disposer d'étiquettes indépendantes et d'un test séparé dans le temps.

