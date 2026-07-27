# Anti-pattern Reference

Before/after pairs for the top over-engineering moves. The threshold throughout: "does deleting this break a stated requirement?" If no → remove it.

---

## 1. Class vs. function

**The tell:** the class has no meaningful per-instance state — `__init__` just stores config, and there's one public method.

Before:
```python
class EmailValidator:
    def __init__(self, strict=False):
        self.strict = strict
        self._pattern = re.compile(r'[^@]+@[^@]+\.[^@]+')
    def validate(self, email: str) -> bool:
        return bool(self._pattern.match(email))
```

After:
```python
_EMAIL = re.compile(r'[^@]+@[^@]+\.[^@]+')

def is_valid_email(email: str) -> bool:
    return bool(_EMAIL.match(email))
```

**Use a class when:** instances carry state that varies between them, or multiple instances with different configurations coexist at runtime.

---

## 2. Config object for a handful of parameters

**The tell:** `@dataclass class Config` or `class Options` with 2–4 fields, instantiated exactly once, passed to exactly one function.

Before:
```python
@dataclass
class ProcessorConfig:
    batch_size: int = 100
    timeout: float = 30.0
    retry: bool = True

def process(items, config: ProcessorConfig): ...
```

After:
```python
def process(items, batch_size=100, timeout=30.0, retry=True): ...
```

**Use a config object when:** it crosses 3+ function boundaries, is serialized/deserialized, or has 7+ fields where keyword args become unwieldy.

---

## 3. Strategy pattern for a single strategy

**The tell:** a `strategy` or `algorithm` parameter with an `if/elif` chain, where only one branch is ever actually called.

Before:
```python
class Sorter:
    def __init__(self, algorithm="quicksort"):
        self.algorithm = algorithm
    def sort(self, items):
        if self.algorithm == "quicksort":
            return sorted(items)
        elif self.algorithm == "mergesort":
            return self._mergesort(items)  # never invoked
```

After:
```python
def sort_items(items):
    return sorted(items)
```

**Use a strategy when:** there are multiple real implementations already in the codebase, the user can meaningfully choose between them, and they have different tradeoffs that matter for the current task.

---

## 4. Helper extracted and called once

**The tell:** a private `_helper()` function whose only call site is the function it was extracted from.

Before:
```python
def process_user(user):
    name = _normalize_name(user.name)
    return User(name=name, id=user.id)

def _normalize_name(name: str) -> str:
    return name.strip().title()
```

After:
```python
def process_user(user):
    return User(name=user.name.strip().title(), id=user.id)
```

**Extract when:** the operation appears in 3+ places, or the inline version is genuinely hard to read (multi-step, non-obvious logic that needs a name to be understood).

---

## 5. Proactive cache

**The tell:** `self._cache = {}` or `@lru_cache` added before anyone measured a performance problem or confirmed the function is called with the same arguments repeatedly.

Before:
```python
class Processor:
    def __init__(self):
        self._cache = {}
    def compute(self, key):
        if key not in self._cache:
            self._cache[key] = self._do_compute(key)
        return self._cache[key]
```

After:
```python
def compute(key):
    return do_compute(key)
```

**Add a cache when:** there's a measured performance issue, the function is demonstrably called with repeated keys in the same run, and the result is pure (same input → same output, always).
