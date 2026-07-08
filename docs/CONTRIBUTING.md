# Contributing to ChemField

ChemField is a **peer-to-peer, open** knowledge fabric for chemistry, materials science, and chemical engineering. Every contributor signs their work cryptographically and peers independently verify — no central authority, no gatekeeping.

---

## Core Principle

**"The fabric ís the database."** We don't store data on a server; we weave it into a distributed, content-addressed P2P network. Your contribution is:
1. **Local first** — author facts on your machine
2. **Cryptographically signed** — you own your claims
3. **P2P-synchronized** — peers pull and verify
4. **Permanently immutable** — all changes tracked via content addresses (CIDs)

---

## Getting Started

### Prerequisites

- Node.js ≥18.0
- `git` and a GitHub account
- Familiarity with JSON schema (we validate all claims)
- Basic understanding of chemistry (you're contributing factual claims)

### 1. Clone the repository

```bash
git clone https://github.com/ChemField/openchem.git
cd openchem
npm install
```

### 2. Set up your identity

Each fact you weave is signed with your public key. Create a keypair:

```bash
npm run keygen
# Output: public_key.txt, private_key.txt (keep private_key.txt safe)
export CHEMFIELD_PRIVATE_KEY=$(cat private_key.txt)
```

Store your private key securely:
```bash
# Option A: Keychain (recommended)
security add-generic-password -a chemfield -s chemfield-private -w "$(cat private_key.txt)"

# Option B: Environment file (add to .gitignore)
echo "export CHEMFIELD_PRIVATE_KEY=$(cat private_key.txt)" >> .env.local
```

---

## Authoring a Fact (Knit)

### Fact Structure

Each fact is a **claim** with verifiable **sources**:

```json
{
  "id": "knit:slag-composition-xrf-assay-2024",
  "claim": "BOF steel slag contains 35.2% FeO by XRF assay (June 2024)",
  "tags": ["composition", "slag", "FeO", "assay-2024"],
  "sources": ["lab-report-xrf-2024-06-15", "bof-process-spec-tata-steel"],
  "uncertainty": "±2.5% (instrument resolution)"
}
```

**Rules:**
- `claim`: factual statement (≤280 chars, no hedging like "probably", "may")
- `tags`: 2–4 lowercase tags (hyphens, no spaces), max 3 words each
- `sources`: ≥1 reference to lab record, published paper, equipment manual, or process spec
- `uncertainty`: optional quantitative bounds (e.g., "±2.5%", "15–25% by mass")
- No claims about human health, toxicity, or regulatory compliance (refer to MSDS/regulations instead)

### 2. Create a fact file

```bash
# Edit data/chemfield-knits.json
cat >> data/chemfield-knits.json <<'EOF'
{
  "id": "knit:vanadium-recovery-slag-leach",
  "claim": "Steel slag leached in 2M H2SO4 at 65°C yields 1.8% V2O5 recovery by weight",
  "tags": ["recovery", "leaching", "vanadium"],
  "sources": ["lab-protocol-sop-42", "literature-patel-2023"],
  "uncertainty": "±0.3%"
}
EOF
```

### 3. Validate

```bash
npm run validate -- --field chemfield
# Output: ✓ 42 knits, 108 fibers — all signatures valid
```

### 4. Sign and weave

```bash
npm run weave
# Output: wove knit (CID: zDaA97G9VQ..., signed by $your_public_key)
```

This generates:
- **Local Fabric record** (`~/.chemfield/fabric/knits/`) — immutable, content-addressed
- **P2P announcement** — peers notified via Knitweb relay
- **Signature proof** — cryptographic proof you authored this claim

### 5. Create a pull request

Add your knit(s) to the PR with source documentation:

```bash
git add data/chemfield-knits.json
git commit -m "Add slug-leaching recovery data from lab-protocol-sop-42"
git push origin my-fact-branch
gh pr create --title "Add: vanadium recovery yield from SOP-42" \
  --body "**Source:** Lab protocol SOP-42, dated 2024-06-15
**Claim:** Slag leached in 2M H2SO4 yields 1.8% V2O5 recovery
**Verification:** CID zDaA97G9VQ... · Signature valid · Peers replicated"
```

### 6. Peer review and merge

Once a domain expert (materials scientist, chemist, process engineer) reviews your sources and verifies the claim:
1. They re-weave on their own node to confirm CID matches
2. They vote in the Knitweb quorum
3. PR merges to `main`

---

## Authoring a Fiber (Relation)

Fibers connect facts, creating the knowledge graph:

```json
{
  "from": "knit:slag-composition-xrf-2024",
  "to": "material:FeO",
  "rel": "contains",
  "weight": 0.352
}
```

Add to `data/chemfield-fibers.json`:

```bash
echo '{
  "from": "knit:slag-composition-xrf-2024",
  "to": "knit:slag-recovery-potential",
  "rel": "measured_by",
  "weight": 1.0
}' >> data/chemfield-fibers.json

npm run validate
```

**Common relation types:**
- `contains` — compositional inclusion (weight: %/ppm)
- `measured_by` — methodology or instrument
- `verified_by` — external confirmation
- `contradicts` — conflicting claim (weight: confidence 0–1)
- `improves` — optimization relation (weight: magnitude of improvement)
- `derives_from` — ancestry (raw material → processed)

---

## What NOT to Contribute

- **Speculation or unverified claims** — "vanadium recovery *might* be higher" ✗
- **Opinion** — "slag recovery is cool" ✗
- **Regulatory/compliance claims** — defer to official MSDS/regulations
- **Health/toxicity statements** — must cite peer-reviewed toxicology studies or official health bodies
- **Proprietary data** — you own or have permission to share (IP holder's consent required)
- **Duplicate facts** — search `data/chemfield-knits.json` for existing claims before authoring

---

## Review & Merge Workflow

1. **Author**: Writes knit/fiber, validates locally, pushes PR
2. **Peers** (≥2 domain experts): 
   - Re-weave on their machine → confirm CID matches yours
   - Verify sources (lab report date, instrument specs, published DOI)
   - Run `npm run verify -- --fact <cid>` (re-fetch from 3 independent peers, confirm consistency)
   - Approve PR via Knitweb quorum vote
3. **Merge**: Auto-merge once vote threshold reached (default: ≥2/3 quorum)
4. **Distribute**: Fact propagates to all peers via P2P sync (anti-entropy + eager announce)

---

## Quality Standards

### Sources
- **Lab records**: dated, include instrument model, calibration data, operator initials
- **Published papers**: DOI, journal name, peer-reviewed
- **Equipment manuals**: manufacturer, model, edition (date)
- **Process specs**: owner org, version number, date

### Precision
- Use **significant figures** matching your measurement resolution
  - "35.2% FeO" (3 sig figs) ✓
  - "35.234% FeO" (5 sig figs) requires ±0.01% precision instrument
- Always include **uncertainty range** when precision is <±5%
- Avoid false precision: "1.8%" not "1.8000%"

### Reproducibility
- **Exact conditions**: temperature, pressure, solvent, duration, batch source
- **Quantitative bounds**: "2–4 mol/L", never "about 3 mol/L"
- **References to parts**: "(Segment B, line 3 of SOP-42)" for traceability

---

## Best Practices

1. **Start small**: one knit + sources before your first PR
2. **Ask early**: open a GitHub discussion if you're unsure about claim format
3. **Search first**: check `knitweb.github.io/knitweb/chemfield/` (Explorer) for existing similar claims
4. **Cite comprehensively**: every claim needs ≥1 traceable source; proprietary data needs explicit permission
5. **Verify offline**: run `npm run validate` before pushing — broken schemas block CI
6. **Join the lab**: discuss mission chains and measurement gaps in `#chemfield` on Discord

---

## Integration with Other Fields

Your knits automatically relate to other Knitweb fields:

### FinField (Financial Data)
- Commodity prices (vanadium, steel, slag) auto-link to your recovery claims
- Cost-benefit analysis: `(recovery_yield × market_price) − leaching_cost`

### MolGang (Molecular Simulation)
- Molecular structures of slag components auto-connect
- Reaction pathways for extraction link to your process specs

### Reactive Lab (Interactive Learning)
- Your facts power the slag-leaching mission chain
- Students weave new knits and earn PLS rewards on verification

---

## Tools & Commands

### Local Development
```bash
npm run validate           # Full schema + signature check
npm run weave             # Sign & add to local Fabric
npm run verify -- --fact <cid>  # Re-fetch & verify from peers
npm test                  # Unit tests (schema, crypto, P2P sync)
npm run repl              # Interactive fact explorer
```

### Debugging
```bash
# View all local knits
npm run list-knits

# Show relations for a specific fact
npm run graph -- --focus "knit:slag-composition-xrf-2024"

# Check peer sync status
npm run relay-status
```

---

## Code of Conduct

- **Honesty first**: cite all sources; never hide conflicting evidence
- **Openness**: data is P2P-distributed; assume nothing is private
- **Rigor**: precision matters; if you're unsure, add uncertainty bounds
- **Respect**: disagree with other contributors' sources in public PRs; follow Knitweb dispute protocol

---

## FAQ

**Q: Can I delete a knit after I weave it?**  
A: No — content-addressing makes facts immutable. If you made an error, weave a new fact with a correction and link it via `contradicts` relation. The old knit remains (for provenance), but newer evidence can override it.

**Q: Who verifies my sources?**  
A: Domain experts on the peer-review quorum. They check lab-report dates, instrument specs, and may contact you for clarification. Verification is semi-automated (CID replication + signature) and semi-manual (human judgment on source quality).

**Q: Can I contribute unpublished lab data?**  
A: Yes, if you own it (it's your lab) or have owner consent. Include detailed metadata (date, institution, equipment model). Peers may reach out for calibration certificates.

**Q: How do I report a bad/fraudulent knit?**  
A: Open a GitHub issue with the CID and evidence (conflicting literature, instrument specs that don't match). The quorum will review and vote on whether to add a `contradicts` relation linking the fraudulent knit to corrected data.

---

## Getting Help

- **Documentation**: [Knitweb SPEC](https://github.com/Knitweb/knitweb/blob/main/docs/SPEC-v0.1.md)
- **Dataset schema**: [`DATASET_SCHEMA.md`](DATASET_SCHEMA.md) (this repo)
- **Community**: [Knitweb Discord](https://discord.gg/knitweb) · `#chemfield` channel
- **Issues**: [github.com/ChemField/openchem/issues](https://github.com/ChemField/openchem/issues)

---

**Last updated:** 2026-07-08  
**Maintained by:** ChemField Community  
**License:** CC0 (public domain — facts are not owned)
