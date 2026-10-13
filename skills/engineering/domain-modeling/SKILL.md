---
name: domain-modeling
description: Build and sharpen a project's domain model. Use when discussing codebase terminology, writing or editing a glossary.yaml or ubiquitous-language.yaml, or recording or editing an ADR.
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. This is the *active* discipline: challenging terms, inventing edge-case scenarios, and writing the terms and decisions down the moment they crystallise. (Merely *reading* the term files for vocabulary is not this skill: that's a one-line habit any skill can do. This skill is for when you're changing the model, not just consuming it.)

## File structure

Terms live in two YAML files, split by kind:

- **`glossary.yaml`**: the repo's domain terms, the concepts unique to this project.
- **`ubiquitous-language.yaml`**: general developer vocabulary, one canonical word per concept, so the user and every agent mean the same thing.

Route each term with the test in [TERMS-FORMAT.md](./TERMS-FORMAT.md).

Most repos have a single context:

```
/
├── glossary.yaml
├── ubiquitous-language.yaml
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

A repo with multiple contexts gives each context its own `glossary.yaml` beside its code. `ubiquitous-language.yaml` stays single at the root, because developer vocabulary spans contexts. Search for `glossary.yaml` to find the contexts:

```
/
├── ubiquitous-language.yaml
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── glossary.yaml
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── glossary.yaml
│       └── docs/adr/
```

When multiple contexts exist, infer which one the current topic relates to. If unclear, ask.

Create files lazily: only when you have something to write. If a term file is missing, create it when the first term that belongs in it is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the term files

When the user uses a term that conflicts with an entry in `glossary.yaml` or `ubiquitous-language.yaml`, or a word an entry lists under `rejected`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account': do you mean the Customer or the User? Those are different things."

### Split only what earns its own term

A concept that exists to explain, qualify, identify, or describe another one is part of that one. It earns its own term when it has behaviour, relationships, a lifecycle, or reuse of its own. "You've named City and Postcode separately: does anything act on a City by itself, or do they travel with the Address?"

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible. Which is right?"

### Update the term files inline

When a term is resolved, add or update its entry in the file it belongs in, right there. Don't batch these up: capture them as they happen. Use the formats in [TERMS-FORMAT.md](./TERMS-FORMAT.md).

Keep both files to definitions and naming rulings. An entry's `identity` and `enforced_at` name where a term shows up and where its rules hold; record how the code works in the code, its specs, and its ADRs.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).
