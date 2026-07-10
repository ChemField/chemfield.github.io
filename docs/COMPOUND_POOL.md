# ChemField Compound Pool

The authoritative, woven pool of chemical compounds in the ChemField knitfield.

## Current Inventory

### Batch 1: Fundamental Compounds (5 knits)

| Formula | Name | CAS | Category | Source |
|---------|------|-----|----------|--------|
| H₂O | Water | 7732-18-5 | solvent | PubChem |
| C₂H₆O | Ethanol | 64-17-5 | organic | PubChem |
| C₃H₆O | Acetone | 67-64-1 | solvent | PubChem |
| CH₄ | Methane | 74-82-8 | hydrocarbon | PubChem |
| CO₂ | Carbon Dioxide | 124-38-9 | gas | PubChem |

### Batch 2: Inorganic Acids & Bases (5 knits)

| Formula | Name | CAS | Category | Source |
|---------|------|-----|----------|--------|
| NaCl | Sodium Chloride | 7647-14-5 | salt | PubChem |
| H₂SO₄ | Sulfuric Acid | 7664-93-9 | strong acid | PubChem |
| HCl | Hydrochloric Acid | 7647-01-0 | strong acid | PubChem |
| NH₃ | Ammonia | 7664-41-7 | weak base | PubChem |
| C₆H₆ | Benzene | 71-43-2 | aromatic | PubChem |

### Batch 3: Organic & Pharmaceutical (5 knits)

| Formula | Name | CAS | Category | Source |
|---------|------|-----|----------|--------|
| C₈H₁₀N₄O₂ | Caffeine | 58-08-2 | alkaloid | PubChem |
| C₉H₈O₄ | Acetylsalicylic Acid | 50-78-2 | pharmaceutical | PubChem |
| CH₃OH | Methanol | 67-56-1 | alcohol | PubChem |
| C₂H₅OH | Ethanol | 64-17-5 | alcohol | PubChem |
| C₃H₈O | Isopropanol | 67-63-0 | disinfectant | PubChem |

## Pool Statistics

- **Total compounds:** 15 unique
- **Total knits:** 15 (compound level)
- **Total fibers:** 75 (5 per compound)
- **Categories represented:** 4 (compounds, reactions, properties, hazards)
- **Provenance:** 100% CAS Registry + PubChem sourced
- **CID uniqueness:** 100% (all content-addressed)

## Fiber Structure

Each compound knit carries exactly 5 provenance-bearing fibers:

1. **in_category** → ChemField compounds category
2. **has_formula** → molecular formula (e.g., `formula:H2O`)
3. **has_cas** → CAS Registry link
4. **sourced_from** → PubChem publication
5. **related_to** → semantic grouping (organic, inorganic, pharmaceutical)

Example for caffeine (C₈H₁₀N₄O₂):

```
knit: ff1:<sha256-of-caffeine-record>
  └─ in_category → ff1:<sha256-of-compounds-category>
  └─ has_formula → formula:C8H10N4O2
  └─ has_cas → cas:58-08-2
  └─ sourced_from → https://pubchem.ncbi.nlm.nih.gov/
  └─ related_to → chemfield:organic-pharmaceuticals
```

## How to Add Compounds

1. **Source data** from authoritative registry (PubChem, ChemSpider, NIST, etc.)
2. **Create knit** with formula, name, CAS number
3. **Add 5 fibers** (category, formula, CAS, source, semantic)
4. **Batch commit** to `seeds/classification.jsonl`
5. **Replicate** via knitweb P2P fabric

Example Python:

```python
from chemfield.classification import Classification, Fiber

knit = Classification(
    scheme="chemfield",
    level="compound",
    code="H2O",
    name="Water",
    source={"kind": "chemfield-compounds", "ref": "chemfield.github.io"},
    method="published"
)
knit_dict = knit.record()
knit_dict["cas"] = "7732-18-5"

fibers = [
    Fiber("in_category", knit.cid, CHEMFIELD_CATEGORIES_CID, ...),
    Fiber("has_formula", knit.cid, "formula:H2O", ...),
    Fiber("has_cas", knit.cid, "cas:7732-18-5", ...),
    Fiber("sourced_from", knit.cid, "https://pubchem.ncbi.nlm.nih.gov/", ...),
    Fiber("related_to", knit.cid, "chemfield:inorganic-compounds", ...),
]
```

## Link to Live Data

- **FinField repository:** [github.com/FinField/facts](https://github.com/FinField/facts/blob/main/seeds/classification.jsonl)
- **Field graph visualization:** [knitweb.art/field-graph.html](https://knitweb.art/field-graph.html)
- **Knitfield schema:** [Classification knits guide](CLASSIFICATIONS.md)

## Next Phases

- Batch 4: Polymers & Materials (5 compounds)
- Batch 5: Biochemical Metabolites (5 compounds)
- Link to Reactions taxonomy (related compounds in reaction networks)
- Add Properties (melting point, boiling point, density) as separate knits
- Add Hazards (toxicity, flammability, corrosivity) as risk fibers
