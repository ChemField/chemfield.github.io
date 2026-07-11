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

### Batch 4: Industrial Gases & Monomers (5 knits)

| Formula | Name | CAS | Category | Source |
|---------|------|-----|----------|--------|
| C₂H₄ | Ethylene | 74-85-1 | monomer | PubChem |
| C₃H₆ | Propylene | 115-07-1 | monomer | PubChem |
| C₆H₅OH | Phenol | 108-95-2 | building block | PubChem |
| CH₂O | Formaldehyde | 50-00-0 | industrial | PubChem |
| N₂ | Nitrogen Gas | 7727-37-9 | industrial gas | PubChem |

### Batch 5: Biochemical & Metabolites (5 knits)

| Formula | Name | CAS | Category | Source |
|---------|------|-----|----------|--------|
| C₆H₁₂O₆ | Glucose | 50-99-7 | monosaccharide | PubChem |
| C₁₂H₂₂O₁₁ | Sucrose | 57-50-1 | disaccharide | PubChem |
| C₁₆H₃₂O₂ | Palmitic Acid | 57-10-3 | fatty acid | PubChem |
| C₁₈H₃₆O₂ | Stearic Acid | 57-11-4 | fatty acid | PubChem |
| C₅H₁₀N₂O₃ | Glutamine | 56-85-9 | amino acid | PubChem |

## Pool Statistics

- **Total compounds:** 25 unique (5 batches)
- **Total knits:** 25 (compound level) + 4 (categories) = 29 ChemField knits
- **Total fibers:** 125 (5 per compound) + 4 (category links) = 129 ChemField fibers
- **Overall knitfield:** 88 total knits (29 ChemField + 49 SIC + 10 NAICS)
- **Overall fibers:** 383 total (129 ChemField + rest in SIC/NAICS)
- **Total records:** 471 (all unique by CID)
- **Provenance:** 100% CAS Registry + PubChem sourced
- **CID uniqueness:** 100% (all content-addressed)

## Semantic Tags

- Batch 1: fundamental solvents & gases
- Batch 2: inorganic-compounds (acids, bases, salts)
- Batch 3: organic-pharmaceuticals (drugs, alcohols)
- Batch 4: industrial-gases-monomers (feedstocks, polymerization precursors)
- Batch 5: biochemical-metabolites (sugars, amino acids, lipids)

## Live Data

- **FinField repository:** [github.com/FinField/facts](https://github.com/FinField/facts/blob/main/seeds/classification.jsonl)
- **Field graph visualization:** [knitweb.art/field-graph.html](https://knitweb.art/field-graph.html)
- **Classification schema:** [CLASSIFICATIONS.md](CLASSIFICATIONS.md)

## Next Phases

- Batch 6+: Continue expanding (minerals, polymers, metals)
- Reactions taxonomy (link compounds in reaction networks)
- Properties knits (melting point, boiling point, density, solubility)
- Hazards knits (toxicity, flammability, environmental impact)
