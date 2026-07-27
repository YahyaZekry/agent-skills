---
name: code-craft
description: >
  Switches Claude into minimal-code mode: write the least code that correctly solves the task,
  match the surrounding codebase's abstraction level, and stop before adding anything not asked
  for. Activate when the user says "code-craft", "code-craft mode", "minimal mode", "no
  over-engineering", "keep it simple", "just make it work", "stop over-engineering", or
  "simpler code". Stay active for the rest of the conversation. Also trigger when the user
  shares code and says "this is too complex", "too many lines", "simplify this", or
  "de-engineer this". Deactivate on "code-craft off" or "verbose mode".
---

# Code Craft

Code that does less is better than code that does more — until "more" is actually needed.

## Activation / Deactivation

- `code-craft` / `minimal mode` / `no over-engineering` → reply "Code-craft ON." then apply rules below for the rest of the conversation
- `code-craft off` / `verbose mode` → reply "Code-craft OFF." and return to normal

---

## While Active

### Pre-code gate

Before writing any code, answer three questions silently:

1. Would a function work where I'm about to write a class?
2. Is everything I'm about to add actually asked for?
3. Would deleting the last thing I added break a stated requirement?

If 1 → yes: write the function. If 2 → no: remove it. If 3 → no: it never belonged.

### Core rules

- **Solve the specific case, not the general one.** The user has one input shape, not ten. Write for what exists now.
- **Inline until reused 3×.** Don't extract a helper called once. Don't extract a constant used twice.
- **Match the altitude of the surrounding code.** If everything around you is functions, don't introduce a class hierarchy.
- **No speculative flexibility.** No `strategy` parameter for a single strategy. No plugin hooks nobody asked for. No `if x is None` guards the type system already enforces.
- **No comments that restate the name.** `# find duplicates` above `def find_duplicates()` is noise.

### Anti-pattern checklist

Run this before responding with code. If any item fires → remove or simplify before sending.

- [ ] Class wrapping a single function (class = state + behavior together; no state means it's a function)
- [ ] Config/dataclass for 2–3 parameters used in one place
- [ ] Strategy/factory with only one real branch now
- [ ] Helper extracted and called exactly once
- [ ] Cache added without a measured performance reason
- [ ] Error handling for paths the framework guarantees won't run
- [ ] `Optional[X]` / `List[Any]` / complex generics for a concrete single-type case
- [ ] Abstract base class for a 2-variant problem (two functions do it)
- [ ] Docstring that restates the function name in different words
- [ ] Extension points for requirements nobody stated

### What good looks like

**Asked:** "write a function that finds duplicates"

Over-engineered (~30 lines):
```python
class DuplicateFinder:
    def __init__(self, strategy="hash"):
        self.strategy = strategy
        self._cache = {}
    def find(self, items: List[Any]) -> List[Any]:
        if self.strategy == "hash":
            return self._find_with_hash(items)
        elif self.strategy == "sort":
            return self._find_with_sort(items)
    def _find_with_hash(self, items): ...
```

Code-craft (7 lines):
```python
def find_duplicates(items):
    seen, dupes = set(), []
    for x in items:
        if x in seen:
            dupes.append(x)
        seen.add(x)
    return dupes
```

### What never gets cut

- Full correctness — shorter is not an excuse for wrong
- Edge cases the user actually mentioned
- Error handling at real system boundaries (user input, external APIs, file I/O)
- Type hints that genuinely prevent bugs, not decorative ones

---

## Modules

If a task raises a genuine question about *when* a pattern is warranted, read `antipatterns.md` — it has before/after pairs for the top 5 violations with clear "use when" thresholds.
