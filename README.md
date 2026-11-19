# my-bnm-rmit-oscal

Machine-readable OSCAL 1.1.2 catalog for **Bank Negara Malaysia's
Risk Management in Technology (RMiT)** policy document
(issued 25 September 2026 as `BNM/RH/PD 028-98`).

> **Unofficial.** Source content remains © Bank Negara Malaysia.
> This repository is a community conversion published for
> interoperability with [OSCAL](https://pages.nist.gov/OSCAL/) tooling.
> The upstream BNM PDF is the authoritative text. See [`NOTICE`](NOTICE).

## Contents

| File | Purpose |
|---|---|
| `catalog.json` | OSCAL 1.1.2 catalog (canonical) |
| `catalog.yaml` | Same catalog, YAML serialisation |
| `profile-full.json` | OSCAL 1.1.2 profile importing every control (`include-all`) |
| `resolved-profile-full.json` | Flattened catalog from `oscal-cli profile resolve` |
| `provenance.json` | Builder commit SHA, source URL, PDF SHA256, build time |
| `CHANGELOG.md` | Per-release change log |
| `NOTICE` | Copyright + attribution |

RMIT defines no maturity tiers or role-scoped baselines, so only a single
`profile-full` is published. The **S / G distinction** (standard vs guidance)
is preserved per-control via the `class` field, so consumers can author
overlay profiles that scope to S-only or G-only by filtering on class.

## Stats

- 3 Parts (A: Overview · B: Policy Requirements · C: Regulatory Process)
- 18 numbered sections
- 134 controls (= RMIT numbered paragraphs)
  - 111 standards (`S`, must-comply)
  - 10 guidance (`G`, encouraged)
  - 13 provisions (Part A: definitional/procedural)
- 12 appendices (back-matter resources)

## Validation

Catalog, profile, and resolved catalog all validate clean against:
- OSCAL 1.1.2 JSON Schema
- [`oscal-cli`](https://github.com/metaschema-framework/oscal-cli) 3.2.0
  (metaschema + Schematron)

## Identifiers

- Deterministic UUIDv5 under namespace `rmit.bnm.gov.my`. Re-generating from
  the same inputs produces identical UUIDs — stable across builds for diff
  and reference purposes.
- Control IDs mirror RMIT numbering: `rmit-8.1`, `rmit-10.52`, etc.
- Group IDs: `rmit-g-part-a`, `rmit-g-8`, `rmit-g-10`, etc.
- Each control carries a `marker` prop (`S`, `G`, or absent for Part A) in
  the `https://baden.id/ns/rmit` namespace, plus a `source-page` prop
  pointing back to the upstream PDF.

## Source

- Document: [BNM RMIT, 25 September 2026](https://www.bnm.gov.my/documents/20124/938039/pd-rmit-sep26.pdf)
- Publisher: Bank Negara Malaysia
- Copyright: © Bank Negara Malaysia
- Policy reference: `BNM/RH/PD 028-98`
- Effective date: 28 November 2025 (except where otherwise stated)

## Converter

- Author: Baden Hughes
- License: Apache-2.0 (conversion artifacts in this repository only)
- Builder: [`my-bnm-rmit-oscal-builder`](https://github.com/badenh/my-bnm-rmit-oscal-builder) (private)

Each release's `provenance.json` records the exact builder commit used to
produce the artifacts. Builds are deterministic: the same builder commit and
the same source PDF SHA256 produce byte-for-byte identical OSCAL output.

## Using the catalog

```python
import json, httpx
cat = httpx.get(
    "https://raw.githubusercontent.com/badenh/my-bnm-rmit-oscal/main/catalog.json"
).json()["catalog"]
print(cat["metadata"]["title"], cat["metadata"]["version"])
# Risk Management in Technology (RMiT) 2026-09
```

Overlay profile selecting only the S-marked standards:

```yaml
profile:
  imports:
    - href: "https://raw.githubusercontent.com/badenh/my-bnm-rmit-oscal/main/catalog.json"
      include-controls:
        - matching:
            class: standard
```

## Reporting issues

Conversion bugs (misparsed text, wrong IDs, missing content): open an issue
here.

Questions about the source text itself should go to Bank Negara Malaysia.
