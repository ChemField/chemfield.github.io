# ChemField FAQ

Frequently asked questions about ChemField, the Knitweb chemistry domain.

---

## Getting Started

### Q: What is ChemField?

**A:** ChemField is a peer-to-peer, open knowledge base for chemistry and materials science. Instead of a central database, facts are **cryptographically signed** and **content-addressed** (deterministic hashing). Every peer stores the full graph, verifies independently, and decides what to accept.

Think of it as "Wikipedia for chemistry, but decentralized and auditable."

### Q: Do I need to be a chemist?

**A:** No. ChemField welcomes contributions from:
- **Chemists & materials scientists** (expert sourcing)
- **Lab technicians** (experimental data)
- **Engineers** (process specifications)
- **Students** (learning + verification)
- **Enthusiasts** (curating + cross-linking)

Verification is peer-driven: domain experts review sources and vote.

### Q: How do I get started?

**A:** See `/docs/QUICK_START.md` — 5 minutes to your first fact.

---

## How ChemField Works

### Q: What's a "knit"?

**A:** A **knit** is a single factual claim with full provenance:

```json
{
  "id": "knit:slag-composition-xrf-2024",
  "claim": "BOF steel slag contains 35.2% FeO by XRF (June 2024)",
  "tags": ["composition", "slag", "FeO"],
  "sources": ["lab-report-xrf-2024-06-15", "ASTM-E1019"],
  "uncertainty": "±2.5%"
}
```

Every knit is immutable, content-addressed (CID = SHA-256 hash), and signed by the originator.

### Q: What's a "fiber"?

**A:** A **fiber** is a **typed relation** linking facts:

```json
{
  "from": "knit:slag-composition-xrf-2024",
  "to": "knit:leaching-recovery-yield",
  "rel": "implies",
  "weight": 0.85
}
```

Common relations: `contains`, `measured_by`, `verified_by`, `contradicts`, `improves`, `derives_from`.

Fibers create the **knowledge graph** — they show how facts relate.

### Q: How is this different from a wiki?

**A:**

| Aspect | ChemField | Wiki |
|--------|-----------|------|
| Storage | P2P (every peer) | Central server |
| Verification | Every peer re-verifies | Trust admins |
| Immutability | Facts permanent (CID-addressed) | Pages can be deleted |
| Attribution | Cryptographic signature | Edit history |
| Offline | Works offline (local sync) | Requires network |
| Censorship | No single point to shut down | Can be shut down |

### Q: What does "content-addressed" mean?

**A:** Every fact has a unique **fingerprint** (SHA-256 hash) based on its exact content. Change even one character, and the hash changes — so references break, making tampering obvious.

```
Fact:  "FeO content: 35.2%"  →  CID: zDaA97G9VQ7b...
Fact:  "FeO content: 35.3%"  →  CID: z4K2nL8xB9p... (different!)
```

This is how peers independently verify: they recompute the hash and confirm it matches.

### Q: Who verifies facts?

**A:** **Peer-to-peer domain experts**. When you submit a fact:

1. You sign it with your private key (crypto proof: "I authored this")
2. Peers receive it via P2P sync
3. ≥2 domain experts independently verify:
   - Check the sources (lab date, instrument model, DOI)
   - Re-execute if possible (re-measure, re-compute)
   - Vote in the quorum
4. Fact is accepted if vote threshold reached (default: 2/3)

No central authority. Decentralized quorum.

---

## Contribution & Quality

### Q: What makes a good fact?

**A:** A fact must be:

- **Verifiable** — cite specific sources (lab report date, equipment model, standard ref)
- **Precise** — "35.2%" (3 sig figs), not "about 35%"
- **Scoped** — include exact conditions (temperature, solvent, batch)
- **Honest** — include uncertainty bounds if precision >±5%
- **Sourced** — at least one traceable reference

**Example (good):**
```json
{
  "claim": "Steel slag leached in 2M H₂SO₄ at 65°C yields 1.8% V₂O₅",
  "sources": ["lab-protocol-sop-42", "literature-patel-2023"],
  "uncertainty": "±0.3%"
}
```

**Example (bad):**
```json
{
  "claim": "Slag recovery is pretty good",  // vague
  "sources": []  // no sources!
}
```

### Q: Can I contribute proprietary data?

**A:** Yes, if:

1. **You own it** (it's your lab)
2. **You have permission** (from the IP holder)
3. **You cite it** (include date, institution, equipment spec)

Peers may contact you for calibration certificates or verification details. Be prepared to share (or clearly mark as "private research," which stays local).

### Q: How is plagiarism prevented?

**A:** Every fact is **signed** with your public key. You can't claim someone else's work — the cryptographic signature proves who authored it. Plagiarized facts are detected during quorum review (peer recognizes the source) and rejected.

### Q: Can I delete a fact I published?

**A:** No — facts are **immutable**. If you made an error:

1. **Publish a correction**: new knit linking to the old one via `contradicts` relation
2. **Explain the error** in the new fact's sources
3. **Peers vote** on the correction

This preserves history and prevents tampering.

---

## P2P Sync & Verification

### Q: How do facts propagate?

**A:** Via **anti-entropy P2P sync**:

1. You weave a fact on your node
2. Your node announces it via the Knitweb relay (`5smart.ml`)
3. Peers receive the announcement and pull the fact
4. Each peer independently verifies the signature + digest
5. Facts merge into local Fabric (a distributed ledger)

No central approval needed. Decentralized sync.

### Q: What if peers disagree about a fact?

**A:** That's the point! Disagreement is **healthy conflict**:

1. Peer A: "FeO is 35.2%" (sources: lab report)
2. Peer B: "FeO is 34.8%" (sources: older spec)
3. Both facts stay in the graph (both CIDs are valid)
4. **Fiber relation**: link them via `contradicts` with confidence weights
5. **Users decide** which to trust based on source age, peer reputation, etc.

ChemField captures the **state of knowledge**, including disagreements.

### Q: What if my internet is down?

**A:** ChemField works **offline**:

- You can weave facts locally (they're signed, so no peer approval needed)
- When you reconnect, they sync automatically
- Peers accept them based on signature validity + policy

You don't need to be online to contribute.

---

## Technical Questions

### Q: How much storage do I need?

**A:** ~500 MB for the full ChemField knowledge graph (21 compositions, 108 relations, as of 2026-07). Grows with contributions.

Each peer stores the full graph (no sharding), so disk space scales with community size.

### Q: Is this blockchain?

**A:** No, but it uses similar ideas:

- **Content-addressing** (like IPFS, not like Bitcoin)
- **Cryptographic signatures** (like Bitcoin, but no consensus algorithm)
- **Immutability** (like blockchain, not like centralized database)
- **P2P propagation** (like BitTorrent, not like blockchain)

ChemField is **cryptographically signed data**, not **blockchain**. It's simpler and more efficient.

### Q: What cryptography do you use?

**A:** ECDSA secp256k1 signing (same as Bitcoin) + SHA-256 hashing (same as Bitcoin, but for content-addressing, not consensus).

No exotic cryptography — proven, auditable, standard.

### Q: Can I run a ChemField node?

**A:** Yes! See `/docs/QUICK_START.md` Step 1.

```bash
git clone https://github.com/Knitweb/knitweb.git
npm install && npm run dev
```

Your node joins the P2P network and starts syncing facts.

---

## Community & Moderation

### Q: Is there moderation?

**A:** **Peer review replaces moderation**. Bad facts are rejected by quorum vote. No central authority decides what's "wrong" — peers do.

**Consequences of bad facts:**
- Rejected during PR review (peers vote no)
- If accepted anyway, contradicted by good facts (high-weight `contradicts` relations)
- Originator's reputation drops (fewer facts accepted)
- Eventually ignored by users

This is **market-based moderation**: bad information loses value.

### Q: How do I contact the maintainers?

**A:** 
- **Issues**: `github.com/ChemField/openchem/issues`
- **PRs**: `github.com/ChemField/openchem/pulls`
- **Discord**: `knitweb` server, `#chemfield` channel
- **Email**: (coming soon — contact via Discord)

### Q: How is ChemField governed?

**A:** **Consensus-based**, not top-down:

- **Public roadmap**: `github.com/ChemField/openchem/issues`
- **Voting**: Peers vote on PRs (quorum-based merge)
- **No leader**: Decisions made by super-majority (2/3+)
- **Transparent**: All votes public on GitHub

---

## Use Cases

### Q: I'm a student. How can I use ChemField?

**A:** 
1. **Learn**: Read facts + sources (interactive explorer at `chemfield.github.io`)
2. **Verify**: Pick a fact, check sources, weave a re-verification
3. **Contribute**: Add new facts from your lab (supervised by instructor)
4. **Earn**: Verified knits earn PLS (Proof-of-Useful-Work rewards)

### Q: I'm a researcher. How can I use ChemField?

**A:**
1. **Publish reproducible data**: Weave lab results (no journal review lag)
2. **Cross-link literature**: Create fibers linking to papers, standards, specs
3. **Build reputation**: Each verified fact builds your peer score
4. **Collaborate**: Other researchers build on your knits, credit you via fiber relations

### Q: I'm an engineer. How can I use ChemField?

**A:**
1. **Process documentation**: Weave SOP specs, equipment models, costs
2. **Cost modeling**: Combine facts (recovery yield × market price − acid cost)
3. **Decision support**: Cross-field queries (slag chemistry + commodity prices)
4. **Scale-up planning**: Pull real data, not assumptions

---

## Troubleshooting

### Q: I published a fact, but it's not showing up.

**A:** 
1. Check your local node: `npm run list-knits` (is it there?)
2. Check relay status: `npm run relay-status` (connected to peers?)
3. Re-weave: `npm run weave -- --fact my-fact.json`
4. Verify others can reach you: ask in Discord

Typical delay: 5–30 seconds to propagate to 3+ peers.

### Q: My PR was rejected. Why?

**A:** Check the review comments. Common reasons:

- **Missing sources**: Add at least 1 traceable ref (lab date, DOI, manual)
- **False precision**: "35.234%" without ±0.01% tolerance evidence
- **Vague claim**: "recovery is good" → rephrase quantitatively
- **No uncertainty**: If precision >±5%, cite instrument spec

Reach out in the PR thread; maintainers will help.

### Q: CID doesn't match when I re-weave the same fact.

**A:** 
- Check canonical encoding: use `npm run validate` before weaving
- No floating-point numbers: use integers (ppm, %, not decimals)
- No null values: omit fields that are null
- No custom types: only str, int, bool, list, dict

`CID = Hash(canonical(fact))`. Different encoding → different CID.

### Q: Someone contradicted my fact. Is it wrong?

**A:** Not necessarily. Check:

1. **Sources**: Is theirs newer/more authoritative?
2. **Conditions**: Different temp/solvent/batch?
3. **Precision**: Do uncertainty bounds overlap?

If you disagree, publish a counter-fact with your reasoning. Peers vote on both. **Disagreement is data.**

---

## Roadmap

### Coming Soon
- [ ] 3D knowledge-graph visualization (`chemfield.github.io/graph.html`)
- [ ] Cross-field queries (ChemField ↔ FinField)
- [ ] Interactive missions (Reactive Lab)
- [ ] Quantum simulation integration (MolGang)

### Community Wishlist
- [ ] Bulk import (CSV → facts)
- [ ] Template-based facts (standard forms)
- [ ] Auto-citation (CIF → knits)
- [ ] Kalman filtering (consensus for changing properties)

---

## Resources

- **Spec**: `github.com/Knitweb/knitweb/blob/main/docs/SPEC-v0.1.md`
- **Quick Start**: `/docs/QUICK_START.md`
- **Contributing**: `/docs/CONTRIBUTING.md`
- **Data Model**: `/docs/DATASET_SCHEMA.md`
- **Knitweb P2P Docs**: `github.com/Knitweb/docs`
- **Discord**: Knitweb server, `#chemfield` channel

---

**Last Updated:** 2026-07-08  
**Maintained by:** ChemField Community  
**Questions?** Open an issue or ask in Discord.

🧪 Keep it factual. Keep it sourced. Keep it open.
