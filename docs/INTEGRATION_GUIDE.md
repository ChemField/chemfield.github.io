# ChemField Integration Guide

For developers building tools, apps, or services on top of ChemField.

---

## Overview

ChemField is a **P2P chemistry knowledge base**. You can integrate with it by:

1. **Reading facts** — Query the Fabric for chemistry knits
2. **Writing facts** — Publish your own chemistry data
3. **Building GUIs** — Create apps that visualize ChemField data
4. **Cross-linking** — Connect ChemField to other fields (FinField, MolGang, etc.)

---

## Quick Start: Read ChemField Facts

### Via P2P Node (Recommended)

```bash
# 1. Start a local Knitweb node
git clone https://github.com/Knitweb/knitweb.git
cd knitweb
npm install
npm run dev

# 2. Query ChemField facts
curl http://localhost:3000/query?field=chemfield&tags=slag,composition

# 3. Response: Array of knits
[
  {
    "id": "knit:slag-composition-xrf-2024",
    "claim": "BOF slag contains 35.2% FeO",
    "tags": ["slag", "composition", "FeO"],
    "sources": ["lab-report-2024-06-15", "ASTM-E1019"],
    "cid": "zDaA97G9VQ7bYXSRqPe8PJ5xN8M2xQ...",
    "signature": "3045...",
    "timestamp": 1720310400
  },
  ...
]
```

### Via REST API (Coming Soon)

```bash
# Once deployed
curl https://api.chemfield.io/facts?tags=slag,composition
```

---

## Creating Your Own ChemField App

### 1. Set Up a Knitweb Node

```bash
git clone https://github.com/Knitweb/knitweb.git
cd knitweb
npm install
npm run dev
```

Your node is now a full ChemField peer.

### 2. Read & Display Facts

**Example: Slag Composition Explorer**

```typescript
import { readFabric } from 'knitweb'

async function displaySlagComposition() {
  const fabric = await readFabric()
  
  // Query ChemField for slag composition facts
  const facts = fabric.query({
    field: 'chemfield',
    tags: ['slag', 'composition']
  })
  
  // Display results
  facts.forEach(knit => {
    console.log(`${knit.claim}`)
    console.log(`  Sources: ${knit.sources.join(', ')}`)
    console.log(`  Verified: ${knit.verified ? '✓' : '⏳'}`)
  })
}

displaySlagComposition()
```

### 3. Write & Publish Facts

```typescript
import { ChemfieldAuthor } from 'knitweb-chemfield'

async function publishMyData() {
  const author = new ChemfieldAuthor(privKey)
  
  // Create a fact
  const fact = {
    claim: "Steel slag recovered from EAF contains 28% FeO",
    tags: ["slag", "composition", "EAF"],
    sources: ["our-lab-report-2026-07-08", "ICP-MS-instrument"],
    uncertainty: "±2%"
  }
  
  // Weave to local Fabric
  const cid = await author.weave(fact)
  
  // Announce to peers
  await author.announce(cid)
  
  console.log(`Published fact: ${cid}`)
}
```

---

## Cross-Field Integration

### Link ChemField to FinField

```typescript
import { ChemfieldAuthor } from 'knitweb-chemfield'

async function linkChemistryToPrice() {
  const author = new ChemfieldAuthor(privKey)
  
  // Create a fiber relation
  const relation = {
    from: "knit:slag-recovery-yield",  // ChemField
    to: "fact:vanadium-price-2026-07",  // FinField
    rel: "implies",  // recovery yield implies value
    weight: 0.92  // high confidence
  }
  
  const relationCid = await author.weaveRelation(relation)
  console.log(`Linked: Recovery yield → Vanadium price`)
}
```

### Query Across Fields

```typescript
async function calculateROI() {
  const fabric = await readFabric()
  
  // Get slag recovery fact (ChemField)
  const recovery = fabric.query({
    field: 'chemfield',
    claim: /recovery.*vanadium/i
  })[0]
  
  // Get vanadium price fact (FinField)
  const price = fabric.query({
    field: 'finfield',
    tags: ['commodity', 'vanadium']
  })[0]
  
  // Calculate locally
  const roi = (recovery.yield * price.price) - cost
  
  console.log(`ROI: $${roi}/ton`)
}
```

---

## Building a GUI

### Web App (React/Vue/Svelte)

```javascript
// ChemFieldExplorer.svelte (Svelte example)
<script>
  import { onMount } from 'svelte'
  
  let facts = []
  let loading = true
  
  onMount(async () => {
    const res = await fetch('http://localhost:3000/query?field=chemfield&tags=slag')
    facts = await res.json()
    loading = false
  })
</script>

<div class="explorer">
  {#if loading}
    <p>Loading facts...</p>
  {:else}
    {#each facts as fact (fact.cid)}
      <div class="fact-card">
        <h3>{fact.claim}</h3>
        <p class="sources">Sources: {fact.sources.join(', ')}</p>
        <p class="verified">
          {#if fact.verified}
            ✓ Peer-verified
          {:else}
            ⏳ Pending review
          {/if}
        </p>
      </div>
    {/each}
  {/if}
</div>

<style>
  .fact-card {
    padding: 1rem;
    border: 1px solid #ccc;
    border-radius: 8px;
    margin-bottom: 1rem;
  }
</style>
```

### Mobile App (React Native)

```typescript
// ChemFieldMobile.tsx
import React, { useEffect, useState } from 'react'
import { View, Text, FlatList, ActivityIndicator } from 'react-native'
import { queryChem Field } from 'knitweb-mobile'

export function ChemFieldMobile() {
  const [facts, setFacts] = useState([])
  const [loading, setLoading] = useState(true)
  
  useEffect(() => {
    queryChemField({ tags: ['slag', 'composition'] })
      .then(setFacts)
      .finally(() => setLoading(false))
  }, [])
  
  if (loading) return <ActivityIndicator />
  
  return (
    <FlatList
      data={facts}
      renderItem={({ item }) => (
        <View style={{ padding: 16, borderBottomWidth: 1 }}>
          <Text style={{ fontSize: 18, fontWeight: 'bold' }}>
            {item.claim}
          </Text>
          <Text style={{ color: '#666' }}>
            Sources: {item.sources.join(', ')}
          </Text>
        </View>
      )}
      keyExtractor={(item) => item.cid}
    />
  )
}
```

---

## Verification & Trust

### Verify a Fact Yourself

```typescript
import { verify } from 'knitweb-chemfield'

async function verifyFact(knit) {
  const result = await verify(knit)
  
  if (result.signatureValid) {
    console.log('✓ Signature valid (authored by ' + result.author + ')')
  }
  
  if (result.digestMatch) {
    console.log('✓ Content digest matches (not tampered)')
  }
  
  if (result.peerReplication >= 3) {
    console.log('✓ Replicated on', result.peerReplication, 'peers')
  }
  
  console.log('Confidence:', result.confidence * 100 + '%')
}
```

### Check Peer Consensus

```typescript
async function checkConsensus(factCid) {
  const fabric = await readFabric()
  
  // How many peers have this fact?
  const replication = fabric.getReplication(factCid)
  
  // What do they say?
  const votes = fabric.getVotes(factCid)
  
  console.log(`Replicated: ${replication.count} peers`)
  console.log(`Votes: ${votes.accept} accept, ${votes.reject} reject`)
  
  if (votes.accept / (votes.accept + votes.reject) > 0.67) {
    console.log('✓ Consensus reached (2/3+ acceptance)')
  }
}
```

---

## Data Schemas

### Knit (Fact)

```typescript
interface Knit {
  id: string              // Unique ID (schema: knit:slug)
  claim: string           // The factual statement
  tags: string[]          // Classification tags
  sources: string[]       // References (≥1)
  uncertainty?: string    // E.g., "±2.5%"
  cid: string            // Content address (SHA-256 base32)
  signature: string      // ECDSA secp256k1
  timestamp: number      // Unix timestamp
  originator: string     // Public key of author
}
```

### Fiber (Relation)

```typescript
interface Fiber {
  from: string           // Source knit CID
  to: string            // Target knit CID
  rel: string           // Relation type
  weight: number        // Confidence (0-1)
  originator: string    // Who linked them
}
```

### Query Result

```typescript
interface QueryResult {
  facts: Knit[]         // Matching facts
  fibers: Fiber[]       // Relations involving those facts
  meta: {
    timestamp: number   // Query time
    peerCount: number   // Peers consulted
    consensus: number   // % agree on facts (0-1)
  }
}
```

---

## Common Patterns

### Pattern 1: Slag Composition Analysis

```typescript
// Find all slag composition facts
const slagFacts = fabric.query({
  field: 'chemfield',
  tags: ['slag', 'composition']
})

// Group by process type
const byProcess = groupBy(slagFacts, f => 
  f.claim.match(/BOF|EAF|LF/i)?.[0]
)

// Calculate averages
for (const [process, facts] of Object.entries(byProcess)) {
  const avgFeO = avg(facts.map(f => parseFloat(f.claim)))
  console.log(`${process}: avg FeO = ${avgFeO}%`)
}
```

### Pattern 2: Cost-Benefit Model

```typescript
// Get chemistry facts (ChemField)
const recovery = getFact('knit:vanadium-recovery-slag-leach')

// Get pricing facts (FinField)
const vPrice = getFact('fact:vanadium-price-2026-07')
const acidCost = getFact('fact:h2so4-price-2026-07')

// Model locally
const roi = (recovery.yield * vPrice.price) - acidCost.amount
const breakeven = acidCost.amount / vPrice.price

console.log(`ROI: $${roi}/ton`)
console.log(`Breakeven at ${breakeven}% recovery`)
```

### Pattern 3: Cross-Verify with Multiple Sources

```typescript
// Get multiple sources for same claim
const sources = fabric.query({
  claim: /slag.*composition/i
})

// Compare them
const agreement = checkAgreement(sources)

if (agreement.consensus > 0.9) {
  console.log('✓ Strong agreement across peers')
} else if (agreement.consensus > 0.67) {
  console.log('⚠️ Weak consensus; check sources')
} else {
  console.log('❌ Disagreement; conflicting facts')
}
```

---

## Deployment Checklist

- [ ] Local node running: `npm run dev`
- [ ] API responding: `curl http://localhost:3000/health`
- [ ] ChemField facts queryable: `curl http://localhost:3000/query?field=chemfield`
- [ ] Signature verification working
- [ ] Cross-field queries tested (if using FinField, MolGang)
- [ ] Error handling for network failures
- [ ] Caching implemented (optional, for performance)
- [ ] Documentation updated
- [ ] Tests written
- [ ] Ready for production

---

## Performance Tips

1. **Cache queries** — Facts don't change; store locally
2. **Batch requests** — Combine multiple queries into one
3. **Index by tags** — Most queries filter by tags
4. **Use local peer** — Running a node is faster than HTTP
5. **Limit results** — Use pagination or limit= parameter

---

## Security Best Practices

1. **Verify signatures** — Always check cryptographic signature
2. **Check sources** — Don't trust claims without references
3. **Verify replicas** — Ensure ≥3 peers have the fact
4. **Monitor consensus** — Watch for contradicting facts
5. **Audit trails** — Log all writes for compliance
6. **Key management** — Never expose private keys

---

## Troubleshooting

**Q: Queries return empty results**
- A: Check that node is fully synced. Run `npm run relay-status`

**Q: Signature verification fails**
- A: Fact may be corrupted. Re-download from peer.

**Q: Slow queries**
- A: Run local node (not HTTP). Add indexes if querying large datasets.

**Q: Cross-field facts not found**
- A: Verify fiber relations exist. Query with `@cross-field=true`

---

## API Reference

### Query Cheat Sheet

```typescript
// Find all slag facts
fabric.query({ field: 'chemfield', tags: ['slag'] })

// Find facts by author
fabric.query({ field: 'chemfield', author: 'EdwinHauwert' })

// Find contradictions
fabric.query({ relation: 'contradicts', field: 'chemfield' })

// Cross-field search
fabric.query({
  from: { field: 'chemfield', tags: ['vanadium'] },
  to: { field: 'finfield', tags: ['commodity'] }
})

// Temporal query
fabric.query({
  field: 'chemfield',
  before: Date.now(),
  after: Date.now() - 30*24*60*60*1000  // Last 30 days
})
```

---

## Examples

**Full repo of ChemField integration examples:**  
`github.com/ChemField/chemfield-integrations`

Examples include:
- Slag composition dashboard (React)
- Mobile fact browser (React Native)
- Cost-benefit calculator (Node.js CLI)
- Data import tool (CSV → ChemField)
- Cross-field visualization (D3.js + MolGang)

---

## Support

- **Issues:** `github.com/ChemField/openchem/issues`
- **Discussions:** `github.com/ChemField/openchem/discussions`
- **Discord:** Knitweb server, `#chemfield-dev` channel
- **Email:** chemfield-dev@knitweb.art (coming soon)

---

**Happy integrating! 🧪🚀**

