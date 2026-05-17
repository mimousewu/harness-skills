# State Display Templates

ASCII state panels shown before questions and at workflow transitions. Keep panels compact — they're progress indicators, not documentation.

---

## Step 0: Inventory Complete

```
┌─────────────────────────────────────────┐
│  SAD Generation — Inventory             │
├─────────────────────────────────────────┤
│  Product: {product name from vision}    │
│                                         │
│  Artifacts Found:                       │
│    {✓|✗} Vision                         │
│    {✓|✗} Personas ({count})             │
│    {✓|✗} Capabilities ({count})         │
│    {✓|✗} Journeys ({count})             │
│    {✓|✗} Features ({count})             │
│    {✓|✗} User Stories ({count})         │
│    {✓|✗} Domain Model ({bc_count} BCs)  │
│    {✓|✗} Event Storming ({count})       │
│                                         │
│  Coverage: {N}/11 sections auto-derive  │
│  Questions needed: ~{0-3}              │
└─────────────────────────────────────────┘
```

---

## Step 1: Auto-Derive Complete

```
┌─────────────────────────────────────────┐
│  SAD Generation — Derivation Complete   │
├─────────────────────────────────────────┤
│  Sections derived:                      │
│    ✓ §1 Introduction & Goals            │
│    ✓ §2 Constraints                     │
│    ✓ §3 Context & Scope                 │
│    {✓|◐|—} §4 Solution Strategy         │
│    ✓ §5 Building Block View             │
│    ✓ §6 Runtime View                    │
│    {✓|◐|—} §7 Deployment View           │
│    {✓|◐} §8 Cross-cutting Concepts      │
│    ✓ §9 Quality Requirements            │
│    ✓ §10 Risks & Technical Debt         │
│    ✓ §11 Glossary                       │
│                                         │
│  Legend: ✓=complete ◐=partial —=needs Q  │
│                                         │
│  Diagrams generated: {count}            │
│  TBD markers: {count}                   │
└─────────────────────────────────────────┘
```

---

## Step 2: Before Each Question

```
┌─────────────────────────────────────────┐
│  SAD Generation — 需要确认 ({N}/{max})   │
├─────────────────────────────────────────┤
│  已完成:                                 │
│    ✓ {list of completed sections}       │
│                                         │
│  当前缺失:                               │
│    ⚠ {what's missing and why}           │
│    → 影响: {which sections affected}    │
└─────────────────────────────────────────┘
```

---

## Step 3: Generation Complete

```
┌─────────────────────────────────────────┐
│  SAD Generation — Document Ready        │
├─────────────────────────────────────────┤
│  Sections:     {complete}/{total}       │
│  With TBD:     {count}                  │
│  Diagrams:     {count}                  │
│    - Context:    {1}                    │
│    - Container:  {1}                    │
│    - Component:  {per_bc_count}         │
│    - Sequence:   {count}                │
│    - Deployment: {0|1}                  │
│  Glossary:     {term_count} terms       │
│  Risks:        {risk_count}             │
│                                         │
│  Output: architecture/sad.md            │
│  Size: ~{estimate} lines                │
└─────────────────────────────────────────┘
```

---

## Step 4: Saved

```
┌─────────────────────────────────────────┐
│  SAD Generation — Complete ✓            │
├─────────────────────────────────────────┤
│  Saved: product-management/             │
│         architecture/sad.md             │
│                                         │
│  Next steps:                            │
│    • Review diagrams render correctly   │
│    • Fill [TBD] sections when decided   │
│    • Re-run after adding new BCs        │
└─────────────────────────────────────────┘
```

---

## Compact Variant (for mid-flow updates)

When updating during iteration, use single-line status:

```
[SAD] §{N} {section_name} — {✓ derived | ◐ partial | ⚠ needs Q | ✗ blocked}
```

Example:
```
[SAD] §5 Building Blocks — ✓ derived (2 BCs modeled, 2 planned)
[SAD] §7 Deployment — ⚠ needs confirmation (assuming K8s)
```
