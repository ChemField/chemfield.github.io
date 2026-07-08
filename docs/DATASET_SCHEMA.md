# ChemField Dataset Schema

## Overview

The ChemField dataset is a peer-to-peer, content-addressed collection of chemical compositions, material properties, and related facts woven into the Knitweb fabric.

**Live data:** `github.com/ChemField/openchem` — 21 chemical compositions, 108 semantic relations

**Live viewer:** `knitweb.github.io/knitweb/chemfield/` — integrated with 3D knowledge graph at `/graph.html`

---

## Data Model

### Knits (Facts)

Each knit is a signed, content-addressed claim:

```json
{
  "id": "knit:slag-composition-001",
  "claim": "BOF steel slag contains 30-40% FeO by weight",
  "tags": ["composition", "slag", "FeO"],
  "sources": ["BOF-process-spec-2024", "laboratory-assay-XRF"],
  "weaved_by": "EdwinHauwert",
  "timestamp": 1720310400
}
```

**Canonical ID:** SHA-256 of `{claim, tags}` → `knit:` prefix

**Prefixes by domain:**
- `knit:` — fact (verifiable claim)
- `material:` — substance or compound
- `source:` — data source or measurement method
- `var:` — variable or parameter (FeO, mass%, temperature)
- `record:` — raw lab record or dataset entry
- `comparison:` — comparative property (vs. reference)
- `massbalance:` — mass balance calculation
- `alias:` — synonymous name or ID

---

## Fibers (Relations)

Typed, weighted edges linking knits and materials:

```json
{
  "from": "knit:slag-composition-001",
  "to": "material:FeO",
  "rel": "contains",
  "weight": 0.35
}
```

**Common relations:**
- `contains` — material composition
- `measured_by` — knit source method
- `verified_by` — external confirmation
- `contradicts` — conflicting claim
- `derives_from` — ancestry (raw → processed)
- `improves` — optimization relation
- `reference_value` — comparison baseline

---

## Weaving Process (P2P Sync)

1. **Author** creates knit + sources on local machine
2. **Weave** into Knitweb fabric (deterministic CID, signed)
3. **Announce** to peers via P2P relay (InventoryRelay)
4. **Reconcile** via anti-entropy (ReconcileSession)
5. **Verify** independently (re-execute origin sources)
6. **Accept** into local fabric once verified

All operations are **deterministic** — same input always yields same CID.

---

## Content-Addressing (CIDs)

**Canonical encoding:** dag-cbor (deterministic CBOR with sorted keys)

**SHA-256 digest → CID `z<base32>`**

Example: `zDaA97G9VQ7bYXSRqPe8PJ5xN8M2xQ...`

**Immutability guarantee:** changing any fact field re-hashes to a different CID, so all references break unless updated.

---

## Example: Slag Composition Dataset

### Materials
- `material:slag-bof` (Basic Oxygen Furnace slag)
- `material:FeO`, `material:Fe2O3`, `material:CaO`, `material:SiO2` (oxides)
- `material:vanadium-oxide`, `material:titanium-oxide` (recovery targets)

### Facts
- `knit:slag-composition-001` — Major oxides (30-40% FeO, 5-15% CaO, ...)
- `knit:slag-recovery-potential` — Vanadium recovery yield (1-3% V₂O₅ by weight)
- `knit:leaching-acid-requirement` — H₂SO₄ consumption rates

### Relations
- slag-composition → contains → FeO (weight: 0.35)
- slag-composition → verified_by → XRF-measurement-2024
- leaching-requirement → measured_by → lab-protocol-SOP-42

---

## Contributing

To add facts to ChemField:

1. **Clone** `github.com/ChemField/openchem`
2. **Author** new knit records in `data/chemfield-knits.json`
3. **Source** each claim (cite lab data, published specs, or equipment manuals)
4. **Test** locally: `npm run validate` (schema + signature checks)
5. **Pull Request** to `main` with detailed source descriptions
6. **Merge** once verified by domain expert

All facts are cryptographically signed and P2P-distributed.

---

## Integration Points

### Knitweb Hub
- **Field page:** `knitweb.github.io/knitweb/chemfield/`
- **3D graph:** `/graph.html?field=chemfield` (WebGL visualization)
- **Explorer:** Full-text search across all knits + materialized relations

### Reactive Lab
- **Mission chain:** slag → vanadium → leach → precipitate
- **Weaving engine:** Interactive molecule builder using Knitweb fabric
- **Live scoring:** SynapticCompileJob verification of completed missions

### FinField Scrapers
- **Market data:** Commodity prices (vanadium, steel, slag)
- **Cost modeling:** Energy + acid + equipment ROI
- **Integration:** Cross-field relations (ChemField ↔ FinField)

---

## Schema Validation

**JSON Schema:** `field.schema.json` § A3

**Validation rules:**
- Required fields: `id`, `claim`, `tags`, `sources`
- Tags: lowercase, hyphen-separated, max 3 words
- Sources: non-empty list, each ≥10 chars
- CID: deterministic (bit-identical for same data)

**Validation command:**
```bash
npm run validate -- --field chemfield
```

---

## Future Extensions

- [ ] **Quantitative constraints**: min/max ranges, uncertainty bands
- [ ] **Temporal tracking**: fact evolution over time (versioned knits)
- [ ] **Cross-field relations**: ChemField ↔ FinField ↔ other domains
- [ ] **Dispute resolution**: challenge protocol for contradicting knits
- [ ] **Privacy modes**: local-only facts vs. P2P-published

---

**Last updated:** 2026-07-08  
**Maintainer:** ChemField/chemfield.github.io  
**Contact:** github.com/ChemField
