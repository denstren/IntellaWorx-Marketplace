# IntellaWorx Marketplace Catalog

Solution catalog consumed by the IntellaWorx marketplace. The application reads this
repository's raw content; it is not meant to be browsed directly.

**Sample content only** — the solutions listed here exist to exercise the marketplace
feature and do not contain installable content yet.

## Layout

```
catalog.json                  # the index: one entry per published solution
solutions/<id>/
  manifest.json               # per-solution detail (description, credentials, counts,
                              #   compatibility, changelog)
  <icon file>                 # optional icon referenced by the catalog entry
  bundle.json                 # the installable export bundle (placeholder until
                              #   install support ships)
```

## Rules

- `catalog.json` `schemaVersion` is currently `1`. Consumers reject other versions.
- Fields may be added freely (consumers ignore unknown fields); removing or renaming
  fields is a breaking change.
- Never place credentials or secrets in a solution — solutions declare *required*
  credentials that each customer supplies on install.
