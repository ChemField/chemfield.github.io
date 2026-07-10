# ChemField

**Open-source chemical knowledge commons, woven into the knitweb peer-to-peer fabric.**

ChemField is an ecosystem for publishing, discovering, and verifying chemical data without central authority. Every fact is content-addressed, provenance-bearing, and votable by peers.

## Quick Links

- **[Field Graph](https://knitweb.art/field-graph.html)** — Interactive visualization of woven knits across all knitweb fields
- **[Quick Start](docs/QUICK_START.md)** — Begin publishing compounds and reactions
- **[Classification Guide](docs/CLASSIFICATIONS.md)** — Understand knits, fibers, and provenance
- **[Integration Guide](docs/INTEGRATION_GUIDE.md)** — Wire up your chemistry data
- **[Dataset Schema](docs/DATASET_SCHEMA.md)** — Exact schema for chemical facts
- **[FAQ](docs/FAQ.md)** — Common questions
- **[Contributing](docs/CONTRIBUTING.md)** — Join the effort

## What is ChemField?

A distributed ledger for chemistry. Instead of:
- Publishing papers (delayed peer review)
- Uploading to closed databases (licensing walls)
- Centralizing on GitHub (single point of failure)

ChemField lets you:
- Mint chemical facts as immutable, content-addressed records
- Sign them with your identity (Radicle NID, Ethereum address, etc.)
- Replicate them peer-to-peer across the knitweb
- Have peers vote on whether to accept them into their local fabric

## The Weave

Every chemical compound, reaction, property, and hazard is a **knit** — a signed record. Every relation between knits is a **fiber** — a typed, attributed edge carrying source provenance.

**Current knits:** 68 total across all fields
- **ChemField:** 9 knits (4 categories + 5 compounds)
- **FinField:** 49 knits (10 NAICS sectors + 10 SIC divisions + 39 SIC major-groups)
- **NAICS:** 10 knits (modern industry taxonomy)

**Current fibers:** 283 total (all provenance-bearing, all CID-unique)

## P2P Architecture

No server. No central database. Every node runs:

1. **Knitweb P2P relay** — peer discovery, heartbeat, anti-entropy
2. **Local fabric** — content-addressed knits woven from trusted sources
3. **Consensus layer** — vote on new facts using vank (Byzantine-fault-tolerant weighted median)

## Getting Started

```bash
# Install
pip install chemfield knitweb-knitfield

# Publish a compound
from chemfield import publish_compound
cid = publish_compound(
    formula="C6H12O6",
    name="Glucose",
    cas="50-99-7",
    source="https://pubchem.ncbi.nlm.nih.gov/compound/5793"
)
print(f"Minted: {cid}")

# Replicate across peers
knitweb_node.weave(cid)
knitweb_node.announce()
```

## License

Public domain (CC0). Chemical data belongs to no one and everyone.

---

**Built on knitweb.** [Join the network.](https://knitweb.art)
