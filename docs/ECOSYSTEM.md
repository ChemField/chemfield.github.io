# ChemField in the Knitweb Ecosystem

ChemField is one domain in the **Knitweb** peer-to-peer knowledge fabric. This guide explains how ChemField connects to other fields and how data flows across the ecosystem.

---

## The Knitweb Ecosystem

```
┌─────────────────────────────────────────────────────────┐
│                   KNITWEB HUB                            │
│              knitweb.github.io (knitweb.art)            │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │ChemField │  │FinField  │  │MolGang   │  │CodeField│ │
│  │Chemistry │  │Finance   │  │Molecules │  │Software │ │
│  └──────────┘  └──────────┘  └──────────┘  └─────────┘ │
│       ↓              ↓              ↓           ↓        │
│  chemfield.    5smart.ml    molgang.geo    codefield.   │
│  github.io              (via knitweb.art)   github.io   │
│                                                           │
│  All connected via P2P Fabric (InventoryRelay + Consensus) │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

---

## ChemField's Role

**ChemField** is the chemistry + materials domain. It provides:

- **Knits** (facts): Chemical compositions, process specs, material properties
- **Fibers** (relations): "contains", "measured_by", "improves", "contradicts"
- **P2P network**: Every peer stores the full chemistry graph
- **Peer verification**: Domain experts vote on acceptance

**Example ChemField fact:**
```json
{
  "claim": "Steel slag leached in 2M H₂SO₄ at 65°C yields 1.8% V₂O₅ by weight",
  "sources": ["lab-protocol-sop-42", "literature-patel-2023"],
  "tags": ["recovery", "leaching", "vanadium"]
}
```

---

## Cross-Field Integrations

### ChemField ↔ FinField (5smart.ml)

**What links:**
- Commodity prices (vanadium, steel, slag) ← FinField
- Recovery yields ← ChemField
- **Combined:** Cost-benefit analysis of slag processing

**Example query:**
```
Find: slug recovery (ChemField) × vanadium price (FinField) − acid cost (FinField)
Result: ROI for extraction process
```

**Integration:** Cross-field fact search on knitweb.art hub

---

### ChemField ↔ MolGang (molgang.geo)

**What links:**
- Molecular structures of slag components (FeO, CaO, SiO₂) ← MolGang
- Composition facts ("slag contains 35% FeO") ← ChemField
- **Combined:** 3D visualization of slag chemistry

**Example:**
```
Slag contains FeO
    ↓
MolGang renders FeO molecule in 3D
    ↓
User visualizes chemical structure
    ↓
Understands leaching process better
```

**Integration:** Linked knowledge graph at `knitweb.art/graph.html`

---

### ChemField ↔ Reactive Lab (Interactive Learning)

**Mission chains in ChemField:**

1. **Mission: Slag Composition**
   - Learn: What's in steel slag?
   - Source: ChemField knits + ASTM standards
   - Task: Weave your own composition fact
   - Reward: PLS for verified knit

2. **Mission: Leaching Process**
   - Learn: How to extract vanadium
   - Simulate: Acid concentration + temperature variables
   - Solve: Cost-benefit puzzle (FinField + ChemField data)
   - Reward: Points + certificate

3. **Mission: Full Value Chain**
   - Integrate: Chemistry (ChemField) + Economics (FinField) + Molecules (MolGang)
   - Build: Complete business model for slag recycling
   - Publish: Your own knits to the network
   - Earn: PLS rewards + peer reputation

---

## Data Flow Architecture

### Publishing a ChemField Fact

```
Your Lab (ChemField origin)
    ↓
1. Create knit: {claim, sources, tags, uncertainty}
2. Sign with your key (crypto proof of authorship)
3. Weave to local Fabric (deterministic CID)
    ↓
4. Announce via P2P relay (5smart.ml InventoryRelay)
    ↓
Peer nodes (automatic sync)
    ↓
5. Receive announcement
6. Download full fact
7. Verify signature + digest (independent re-execution)
8. Store in local Fabric
    ↓
9. Domain experts in quorum
10. Review sources + cast votes
11. Fact accepted (≥2/3 votes)
    ↓
12. Visible on knitweb.art explorer
13. Linked in knowledge graph
14. Used in cross-field queries
```

### Cross-Field Fact Query

```
User: "What's the ROI for slag vanadium recovery?"
    ↓
1. Query ChemField: "Recovery yield for slag"
   → Result: 1.8% V₂O₅ by weight (knit with source + timestamp)
    ↓
2. Query FinField: "Vanadium spot price"
   → Result: $4,200/kg (live market data)
    ↓
3. Query FinField: "Acid (H₂SO₄) price"
   → Result: $150/ton (commodity pricing)
    ↓
4. Combine locally (P2P client calculates):
   ROI = (0.018 kg V₂O₅ × $4,200/kg) − ($150/ton acid cost)
   → User sees: "Profitable at current prices"
    ↓
5. Link back: Show sources
   - ChemField knit (lab-protocol-sop-42, literature reference)
   - FinField facts (market data timestamp)
   - Transparency: User can verify every number
```

---

## Network Topology

### Peer Network

```
            ┌─────────────┐
            │  5smart.ml  │
            │  Relay Hub  │
            │(InventoryRelay)
            └──────┬──────┘
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
    ┌────────┐ ┌────────┐ ┌────────┐
    │ Node A │ │ Node B │ │ Node C │
    │ChemField│ │FinField│ │MolGang │
    └────────┘ └────────┘ └────────┘
        ↓ P2P facts via │
        │ InventoryRelay
        └────────────────┘
```

**How sync works:**
1. Node A weaves a ChemField fact
2. Announces to relay (5smart.ml)
3. Relay broadcasts to all peers
4. Nodes B & C receive announcement
5. Each independently downloads and verifies
6. Facts merge into local knowledge graph
7. No central authority needed

---

## API Integration Points

### ChemField API (via Knitweb Fabric)

**Query facts:**
```
GET /query?field=chemfield&tags=slag,composition
→ Returns: All slag composition knits
```

**Submit fact:**
```
POST /weave
{
  "claim": "Slag FeO content: 35.2%",
  "sources": [...],
  "tags": ["slag", "composition"]
}
→ Returns: CID (content address of your fact)
```

**Link to FinField:**
```
GET /cross-field?from=chemfield&to=finfield&rel=uses
→ Returns: All ChemField facts that use FinField data
```

---

## Standards & Compatibility

### ChemField Data Format

**Knit (Fact):**
```json
{
  "id": "knit:slag-composition-2024",
  "claim": "String",
  "tags": ["tag1", "tag2"],
  "sources": ["reference1", "reference2"],
  "uncertainty": "±3%"  (optional)
}
```

**Fiber (Relation):**
```json
{
  "from": "knit:fact-a",
  "to": "knit:fact-b",
  "rel": "contains|measured_by|verifies|contradicts|improves",
  "weight": 0.85  (confidence 0-1)
}
```

### Canonical Encoding

All facts use **deterministic CBOR encoding** so that:
- Same fact always produces same CID
- Peers independently verify via hash
- No tampering possible (hash breaks)

### Cryptographic Signatures

All facts signed with **ECDSA secp256k1**:
- Proves authorship (no impersonation)
- Standard (Bitcoin-compatible)
- Fast & auditable

---

## Contributing Across Fields

### For Chemists

```
ChemField facts
    ↓
Lab → Weave → ChemField network → Peer review → Published
    ↓
FinField data (pricing) + ChemField fact (yield)
    ↓
Users model economic feasibility
    ↓
Your research gets cited in business models
```

### For Economists

```
FinField (prices) + ChemField (process yields)
    ↓
Create cross-field facts linking chemistry to economics
    ↓
Users see: "Recovery is profitable at $X/kg"
    ↓
Your analysis impacts decision-making
```

### For Learners

```
Interactive missions (Reactive Lab)
    ↓
Combine ChemField (chemistry) + FinField (economics) + MolGang (molecules)
    ↓
Weave your own fact to the network
    ↓
Domain experts verify
    ↓
Earn PLS for verified contribution
    ↓
Build peer reputation
```

---

## Live Examples

### 1. Slag Composition Fact
**Source:** ChemField openchem repo  
**Knit:** `knit:slag-composition-xrf-2024`  
**Claim:** "BOF steel slag contains 35.2% FeO by XRF assay (June 2024)"  
**Sources:** ASTM-E1019 standard, lab report dated 2024-06-15  
**Status:** ✅ Peer-verified

### 2. Vanadium Recovery Yield
**Source:** ChemField lab protocol SOP-42  
**Knit:** `knit:vanadium-recovery-slag-leach`  
**Claim:** "Slag leached in 2M H₂SO₄ at 65°C yields 1.8% V₂O₅ by weight"  
**Sources:** Lab protocol SOP-42, literature (Patel et al. 2023)  
**Status:** ✅ Peer-verified

### 3. Cross-Field Query Result
**Query:** What's the ROI for vanadium recovery from slag?  
**Uses:** ChemField knit (1.8% yield) + FinField data (vanadium $4,200/kg)  
**Result:** User sees: "Profitable; ROI = (0.018 × $4,200) − acid cost"  
**Transparency:** All facts traceable to sources

---

## Roadmap: Deeper Integration

### Phase 1 (Done)
- ✅ ChemField facts weaved to P2P
- ✅ FinField pricing data available
- ✅ Basic cross-field queries

### Phase 2 (In Progress)
- 🔄 Interactive 3D visualization (MolGang)
- 🔄 Cross-field knowledge graph
- 🔄 Reactive Lab missions

### Phase 3 (Planned)
- 📋 AI-powered insights ("What if" scenarios)
- 📋 Automated fact synthesis (combining knits)
- 📋 Peer marketplace (buy/sell research access)

### Phase 4 (Research)
- 📋 Quantum simulation (molecular accuracy)
- 📋 Supply chain optimization (full traceability)
- 📋 Climate impact scoring (carbon + water)

---

## Getting Involved

**Choose your role:**

| Role | How to Contribute | Reward |
|------|-------------------|--------|
| **Chemist** | Author facts from your lab | PLS + peer reputation |
| **Economist** | Link chemistry to pricing | Co-author cross-field insights |
| **Student** | Verify existing facts | Learning + certification |
| **Developer** | Build integrations | Grant + community credit |
| **Curator** | Organize + link facts | Reputation boost |

---

## FAQ: Cross-Field Questions

**Q: Can I query across all fields at once?**  
A: Yes. `knitweb.art/query` supports cross-field searches. Query ChemField, FinField, and MolGang simultaneously.

**Q: Who decides which cross-field links are valid?**  
A: Peers do. Anyone can propose a link (fiber); domain experts vote. Disagreement is preserved in the graph.

**Q: Is my ChemField data visible to FinField users?**  
A: Yes, if linked via fiber relations. But ChemField data stays in ChemField repo; FinField accesses it via P2P.

**Q: What if ChemField and FinField disagree?**  
A: Both facts stay in the graph with `contradicts` relation. Users see: "Fact A says X, Fact B says Y; see sources." Disagreement is data.

**Q: Can I run a cross-field node?**  
A: Yes. A node can host multiple fields. One Knitweb instance → multiple domain knowledge bases.

---

## Resources

- **Knitweb Spec:** `github.com/Knitweb/knitweb/blob/main/docs/SPEC-v0.1.md`
- **ChemField Docs:** This site (`chemfield.github.io/docs/`)
- **FinField:** `5smart.ml`
- **MolGang:** `molgang.geo` (via knitweb.art)
- **Hub:** `knitweb.github.io` (knitweb.art)
- **P2P Node:** `github.com/Knitweb/knitweb` (run locally)

---

**The goal:** One distributed knowledge fabric. Many domains. Peer-verified. Transparent. Unstoppable.

🧪🧬💰📊 Chemistry, Molecules, Economics, Data — all woven together.

