# ChemField Quick Start

Get up and running with ChemField in 5 minutes.

---

## 1. Join the P2P Network (30 sec)

Install the Knitweb node software:

```bash
git clone https://github.com/Knitweb/knitweb.git
cd knitweb
npm install
npm run dev  # Start local node on port 3000
```

Your node is now listening for peers and announcing facts to the relay.

---

## 2. Set Up Your Identity (1 min)

ChemField uses cryptographic keypairs. Generate yours:

```bash
npm run keygen
# Output: public_key.txt, private_key.txt

# Store securely
export CHEMFIELD_PRIVATE_KEY=$(cat private_key.txt)

# (Optional) Add to ~/.bashrc or ~/.zshrc for convenience
echo 'export CHEMFIELD_PRIVATE_KEY=$(cat ~/path/to/private_key.txt)' >> ~/.bashrc
```

**Keep `private_key.txt` safe** — losing it means you can't sign new facts (but old facts remain, signed by your public key).

---

## 3. Author Your First Fact (2 min)

Create a file `my-fact.json`:

```json
{
  "claim": "Steel slag contains 32-40% FeO by mass (BOF process)",
  "tags": ["slag", "composition", "FeO"],
  "sources": ["BOF-process-spec-2024", "ASTM-E1019-standard"],
  "uncertainty": "±3%"
}
```

Validate it locally:

```bash
cd /tmp/chemfield
npm run validate -- --fact my-fact.json
```

Expected output:
```
✓ Schema valid
✓ Sources cited
✓ Ready to weave
```

---

## 4. Weave to the Fabric (1.5 min)

Sign and publish your fact:

```bash
npm run weave -- --fact my-fact.json
```

Output:
```
✓ Wove knit (CID: zDaA97G9VQ7bYXSRqPe8PJ5xN8M2xQ...)
✓ Signed by: EdwinHauwert
✓ Announced to peers

Your fact is now propagating via P2P!
```

---

## 5. Verify Others' Facts (30 sec)

See facts from other peers:

```bash
npm run list-knits
# Output: 42 knits, 108 fibers

npm run graph -- --focus "knit:slag-composition-xrf-2024"
# Visualize the fact and its relations
```

Re-verify a fact from peers:

```bash
npm run verify -- --fact "zDaA97G9VQ..."
# (Automatically re-fetches from 3 random peers, confirms consistency)
```

---

## 6. Create Your First Relation (1 min)

Link two facts together:

```json
{
  "from": "knit:slag-composition-xrf-2024",
  "to": "knit:slag-leaching-recovery",
  "rel": "implies",
  "weight": 0.85
}
```

Weave the relation:

```bash
npm run weave -- --fiber my-relation.json
```

Now `slag-composition` and `slag-leaching` are connected in the knowledge graph.

---

## 7. Open a Pull Request

Add your facts to the repository:

```bash
cd ChemField/openchem
git checkout -b my-slag-facts
cp /tmp/my-fact.json data/chemfield-knits.json

git add data/
git commit -m "Add: FeO composition in BOF slag from ASTM-E1019"
git push origin my-slag-facts

gh pr create --title "Add: FeO composition data from ASTM-E1019" \
  --body "**Source:** ASTM E1019 standard (Standard Test Method)
**Claim:** BOF slag FeO content = 32-40% by mass
**CID:** zDaA97G9VQ7bYXSRqPe8PJ5xN8M2xQ...
**Verification:** Peers replicated · Signature valid"
```

Domain experts will review your sources, verify your CID, and vote to merge.

---

## 8. Contribute to the Lab (Optional)

ChemField is also a **Reactive Lab** — interactive missions chain facts into learnable sequences:

1. **Mission: Slag Composition**
   - Learn: What's in steel slag?
   - Weave: Your own knits about composition
   - Solve: Cost-benefit puzzle
   - Earn: PLS for verified knits

2. **Mission: Leaching Process**
   - Learn: How to extract vanadium
   - Simulate: Acid concentration + temperature
   - Build: Your own procedure
   - Earn: Points on completion

---

## Useful Commands

```bash
# List all facts
npm run list-knits

# Search facts
npm run search -- "FeO" "slag" "composition"

# View knowledge graph
npm run graph

# Check relay status
npm run relay-status

# Validate schema
npm run validate -- --field chemfield

# Run tests
npm test

# Interactive REPL
npm run repl
```

---

## Next Steps

- **Learn the data model:** `/docs/DATASET_SCHEMA.md`
- **Contribution guide:** `/docs/CONTRIBUTING.md`
- **Knitweb architecture:** `github.com/Knitweb/knitweb/blob/main/docs/SPEC-v0.1.md`
- **Join the community:** Discord `#chemfield` channel
- **Read others' facts:** `knitweb.github.io/knitweb/chemfield/` (live explorer)

---

## Troubleshooting

**"Private key not found"**
```bash
# Re-generate and store securely
npm run keygen
# or use existing key
export CHEMFIELD_PRIVATE_KEY=$(cat ~/path/to/key.txt)
```

**"CID mismatch"**
- Re-weave locally: `npm run weave -- --fact my-fact.json`
- Compare CID: if different, something changed in the fact structure
- Canonical encoding ensures bit-identical hashing

**"Peers not connecting"**
```bash
npm run relay-status
# Should show: Connected to 3–5 peers
# If 0: check firewall, relay address in .env
```

**"Fact rejected during PR review"**
- Check sources: each must be traceable (lab date, equipment model, DOI)
- Check uncertainty: if precision >5%, must cite instrument spec
- Check tags: no hedging language ("probably", "might")
- Reach out: open a discussion in the PR

---

## Philosophy

ChemField is **peer-verified knowledge**. No central server decides truth. Instead:

1. **You weave** facts into the Fabric (signed with your key)
2. **Peers receive** facts via anti-entropy P2P sync
3. **Peers verify** independently (re-execute, check signature)
4. **Quorum decides** whether to reward (accept) or slash (reject)

This creates accountability: bad facts are costly, good facts earn rewards.

---

**Ready? Start with:** `/docs/CONTRIBUTING.md` for the full workflow.

**Questions?** Open an issue: `github.com/ChemField/openchem/issues`

🧪 Happy weaving!
