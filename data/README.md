# Data / Données

## English

The project needs a dated record of what could be known at each historical cutoff. Keep raw downloads in `data/raw/` and cleaned tables in `data/processed/`; both folders are ignored by Git. Record each source's name, URL, access method, usage terms, collection date, and available historical range before using it.

Minimum document fields: `document_id`, `source`, `source_type` (company/customer/other), `url`, `published_at`, `first_available_at` when known, `collected_at`, `language`, `text`, and `content_hash`. If dates conflict, document the resolution rather than silently choosing one.

Candidate sources include company archives and [Wayback CDX](https://github.com/internetarchive/wayback/blob/master/wayback-cdx-server/README.md), [SEC EDGAR](https://www.sec.gov/search-filings/edgar-application-programming-interfaces), and the research [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/main.html) dataset. Their suitability depends on coverage and access conditions for the period selected.

## Français

Le projet doit conserver une trace datée de ce qui pouvait être connu à chaque date simulée. Placer les téléchargements dans `data/raw/` et les tables nettoyées dans `data/processed/` ; ces dossiers sont ignorés par Git. Avant d'utiliser une source, noter son nom, son URL, son mode d'accès, ses conditions d'utilisation, la date de collecte et la période historique couverte.

Champs minimaux par document : `document_id`, `source`, `source_type` (entreprise/client/autre), `url`, `published_at`, `first_available_at` si connue, `collected_at`, `language`, `text` et `content_hash`. Documenter toute incohérence entre les dates.

Parmi les sources possibles figurent les archives d'entreprises et [Wayback CDX](https://github.com/internetarchive/wayback/blob/master/wayback-cdx-server/README.md), [SEC EDGAR](https://www.sec.gov/search-filings/edgar-application-programming-interfaces) et le corpus de recherche [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/main.html). Leur pertinence dépend de la couverture et des conditions d'accès pour la période retenue.

