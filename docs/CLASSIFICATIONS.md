# ChemField Classification Knits

The ChemField domain in the knitweb is modeled as **provenance-bearing classification knits and fibers**. Each chemical compound, reaction, property, and hazard is a content-addressed record with explicit source attribution.

## Structure

ChemField classifications follow the finfield pattern:

- **Knits** — domain entities: compounds, reactions, properties, hazards
- **Fibers** — typed, attributed relations between knits and reference data

Every knit and fiber hashes to a deterministic CID (content ID), so nodes can be replicated peer-to-peer without coordination. The fiber carries the relationship type and source provenance — who said what, where it came from.

## Compound Knits

Each chemical compound is a knit with:

```json
{
  "kind": "finfield-classification",
  "scheme": "chemfield",
  "level": "compound",
  "code": "H2O",
  "name": "Water",
  "method": "published",
  "source": {"kind": "chemfield-compounds", "ref": "chemfield.github.io"},
  "cas": "7732-18-5"
}
```

The `code` is the molecular formula; `cas` is the authoritative Chemical Abstracts Service number.

## Provenance Fibers

Each compound links to:

1. **in_category**: ChemField category (compounds, reactions, properties, hazards)
2. **has_formula**: molecular formula reference
3. **has_cas**: CAS Registry link
4. **sourced_from**: data source (PubChem, chemistry textbooks, etc.)
5. **related_to**: semantic grouping (e.g., organic-compounds)

### Fiber Record

```json
{
  "kind": "finfield-fiber",
  "rel": "has_cas",
  "subject": "ff1:...",
  "object": "cas:7732-18-5",
  "method": "published",
  "financial_fact": false,
  "news_fact": false,
  "source": {"kind": "cas-registry", "ref": "https://www.cas.org/"}
}
```

Fibers are unidirectional typed edges. The `method` field marks whether the relation is `published` (sourced from authoritative data) or `derived` (our own estimate).

## Building the Pool

ChemField compounds are woven in batches of 5 knits + 25 fibers (5 relations per compound). Each batch is committed to the finfield classification seed file and replicated across nodes.

Current pool:

- H₂O (Water) — solvent, essential compound
- C₂H₆O (Ethanol) — organic compound, alcohol
- C₃H₆O (Acetone) — solvent, ketone
- CH₄ (Methane) — hydrocarbon, greenhouse gas
- CO₂ (Carbon Dioxide) — gas, climate-relevant

## P2P Verification

Each node independently hashes the compound knit and verifies its CID. No central authority is needed — the content is the source of truth. Peer nodes can vote on whether to accept new compounds into their local fabric, using the finfield consensus model.

## Extending the Pool

To add more compounds:

1. Source authoritative data (NIST, PubChem, ChemSpider, etc.)
2. Create compound knit with formula and CAS number
3. Add 5 provenance fibers (category, formula, CAS, source, semantic)
4. Batch into seeds and weave
5. Replicate across the P2P network

## See Also

- [finfield/facts](https://github.com/FinField/facts) — the classification repository
- [Knitweb Field Graph](https://knitweb.art/field-graph.html) — interactive visualization of woven knits
