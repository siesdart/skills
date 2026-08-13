# UI Tree Architecture Review

**Target:** `<application, route, screen, widget, or tree>`  
**Scope:** `<files, entry points, and exclusions>`  
**Date:** `<YYYY-MM-DD>`  
**Confidence:** `High | Medium | Low`

## Executive summary

`<One short paragraph: overall boundary health, the main leverage point, and the next smallest useful action.>`

## Tree under review

```text
<top-level entry>
├── <node> — <responsibility>
│   ├── <node> — <responsibility>
│   └── <node> — <responsibility>
└── <node> — <responsibility>
```

### Scope and evidence

- **Inspected:** `<entry points, implementation files, tests, fixtures, styles, data/effects>`
- **Excluded:** `<generated/vendor/unavailable areas and why>`
- **Observed limitations:** `<missing evidence or none>`

## Boundary map

| Node | Responsibility | State/effect owner | Contract to parent | Contract to children | Verification | Boundary judgment |
|---|---|---|---|---|---|---|
| `<path or node>` | `<what it owns>` | `<state/effect>` | `<inputs/outputs/content/context>` | `<inputs/outputs/content/context>` | `<tests/stories/manual path>` | `Keep | Split | Merge | Investigate` |

## Representative change paths

| Change | Files/nodes touched | Boundary friction | Evidence |
|---|---|---|---|
| `<visual>` | `<paths>` | `<what crosses the boundary>` | `<observed fact>` |
| `<behavior>` | `<paths>` | `<what crosses the boundary>` | `<observed fact>` |
| `<state/data>` | `<paths>` | `<what crosses the boundary>` | `<observed fact>` |
| `<accessibility>` | `<paths>` | `<what crosses the boundary>` | `<observed fact>` |
| `<cross-cutting>` | `<paths>` | `<what crosses the boundary>` | `<observed fact>` |

## Findings

Use one subsection per finding. Keep observed facts, diagnosis, recommendation, and uncertainty separate.

### `<ID>` — `<short finding title>`

- **Priority:** `Critical | High | Medium | Low`
- **Location:** `<file path and node/boundary>`
- **Affected change axis:** `<visual | behavior | state/data | accessibility | lifecycle | cross-cutting>`
- **Observed evidence:** `<specific code path, contract, test, or runtime behavior>`
- **Diagnosis:** `<why the current boundary helps or hurts ownership, locality, contract depth, or DX>`
- **Impact:** `<developer workflow, runtime behavior, testability, accessibility, or maintenance effect>`
- **Recommendation:** `<smallest useful split, merge, contract change, or explicit keep decision>`
- **Trade-offs:** `<what becomes easier and what becomes more costly>`
- **Confidence:** `High | Medium | Low` — `<why>`

## Boundary decisions

| Boundary | Decision | Reason tied to change axis and contract | What remains intentionally local |
|---|---|---|---|
| `<parent → child>` | `Keep | Split | Merge | Investigate` | `<evidence-backed reason>` | `<non-goals>` |

## Recommended next slice

1. **Change:** `<one smallest useful change>`
2. **Owner/boundary:** `<node or contract>`
3. **Preserve:** `<existing locality, public behavior, or integration>`
4. **Validate with:** `<specific test, isolated state, or representative user change>`
5. **Stop when:** `<checkable completion condition>`

## Unresolved questions

- `<Question whose answer could change a recommendation, or “None”.>`

## Handoff

`<Summarize the decision the reader needs to make next: proceed with the recommended slice, investigate an unresolved question, or review another tree.>`
