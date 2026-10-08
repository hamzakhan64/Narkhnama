# Narkhnama

**Digital Daily Market Rate Platform**

Narkhnama is a web platform for publishing verified daily market prices by city and market. Its core goal is to answer:

> **“What is today’s rate of this product in my market?”**

## Project Status

**Release target:** 1.0 MVP  
**Current documentation:** SRS baseline 1.0  
**Initial coverage:** Abbottabad  
**Primary data:** Vegetables and selected grocery items

## Core Scope

- Public product price search by product, city, market and category
- Latest verified price with unit, date, source and verification status
- Price history and price-change indicators
- Shopkeeper accounts and saved product lists
- PDF and print-ready rate lists
- Admin/Data Operator price entry and verification workflow
- Master-data management for cities, markets, categories and products

## Repository Structure

```text
Narkhnama/
├── .github/              # GitHub workflows and issue/PR templates
├── docs/
│   ├── srs/              # Software Requirements Specification
│   ├── architecture/     # Architecture and technical design
│   ├── diagrams/         # System diagrams and models
│   └── api/              # API contracts and documentation
├── frontend/             # Public web app + Shopkeeper portal
├── backend/              # REST API and business logic
├── database/             # Schema, migrations and seed data
├── tests/                # Integration / end-to-end test assets
├── scripts/              # Development and maintenance scripts
├── .env.example          # Environment variable template
├── .gitignore
└── README.md
```

## Release Roadmap

| Release | Scope |
|---|---|
| 1.0 MVP | Search, locations, current verified prices, history table, shopkeeper lists, manual price entry, verification, admin, PDF/print |
| 1.1 | Charts, comparison, bulk import, Urdu UI, reports, audit-log polish, notifications |
| 1.2 | Production collector for approved sources, more markets |
| 2.0 | More cities/categories, mobile app, alerts, analytics |
| Future | Prediction, user-submitted prices, WhatsApp |

## Documentation

The authoritative requirements document is located at:

`docs/srs/Narkhnama_SRS_Industry_Structure_v1.0.docx`

The original source material is preserved at:

`docs/srs/Narkhnama_SRS_Source.md`

## Development Rule

Keep implementation aligned with the SRS requirement IDs (`FR-*`, `NFR-*`, `BR-*`, `UC-*`). Changes that affect requirements should update the SRS and traceability documentation in the same change.
