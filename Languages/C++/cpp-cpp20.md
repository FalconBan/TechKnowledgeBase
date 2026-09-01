# C++ (up to C++20)

## The core mental model
- **Value semantics by default:** copying a value is cheap and well-defined; you own what you allocate.
- **RAII** is the central idea: acquire resources in a constructor, release in the destructor. Scope = lifetime.
- **"Don't pay for what you don't use":** zero-cost abstractions - templates & inlining mean abstractions cost nothing at runtime.
- **Rule of Three / Five / Zero:** if you define any of {destructor, copy ctor, copy assign, move ctor, move assign}, think about the rest. Prefer Rule of Zero (no raw owning pointers).

## Memory & object model
- **Stack** (automatic lifetime) vs **heap** (dynamic via `new`/allocators). Prefer stack - it's faster and auto-releases.
- **Ownership:** `std::unique_ptr` (exclusive), `std::shared_ptr` (refcounted shared), `std::weak_ptr` (breaks reference cycles / observes without owning). A **raw pointer is a non-owning observer** by default in modern code.
- Dangling references/pointers and lifetime bugs are the #1 source of UB - watch temporaries and returned references.

## Modern C++ idioms (C++11 -> 20)
- `auto` for type inference; range-based `for`; `constexpr` / `consteval` (immediate) / `constinit`.
- **Move semantics:** rvalue references, `std::move`, moved-from state, perfect forwarding (`std::forward`).
- **Lambdas** with explicit capture modes (`[=]`, `[&]`, init-capture).
- **Strong typing:** `enum class`, `std::optional`, `std::variant`, `std::any` (C++17) - type-safe replacements for null/union/`void*`.
- **Templates & metaprogramming:** function/class templates, CRTP, SFINAE basics; **C++20 concepts** (`concept`) + `requires` to constrain templates and improve errors.
- **C++20 highlights:** ranges (composable pipelines), coroutines, modules, `std::span`, `std::format`, three-way comparison `<=>` ("spaceship"), designated initializers, `std::jthread` (auto-join + `stop_token`).

## Concurrency
- `std::thread`, locks (`lock_guard` / `unique_lock` / `scoped_lock`), atomics, condition variables.
- **Data race = undefined behavior** - never share mutable state across threads without synchronization.
- Prefer RAII lock guards (exception-safe); use `std::jthread` over raw `std::thread` for safe join/cancel.

## Common interview topics / questions
- Pointers vs references; shallow vs deep copy; when slicing happens.
- Virtual functions: vtable/vptr mechanics, virtual destructors (a base with virtual fns must have one), overriding vs hiding.
- `const` correctness; static members; `friend`; name lookup (hiding vs overloading), ADL.
- Operator overloading - what can/can't be overloaded.
- Move vs copy; why move exists; perfect forwarding.
- Smart pointer pitfalls: cyclic `shared_ptr` (fix with `weak_ptr`), double-delete, custom deleters, `unique_ptr` from an array.
- UB examples: signed overflow, out-of-bounds, use-after-free, data races, uninitialized reads.
- ODR violations; template instantiation rules.

## Standard library containers (quick)
| Container | Backing | Access / notes |
|-----------|---------|----------------|
| `vector` | dynamic array | fast random access; reallocates (use `reserve`) |
| `deque` | blocks of arrays | O(1) push both ends |
| `list` / `forward_list` | linked | stable iterators, no random access |
| `array` | fixed size | compile-time size, stack-friendly |
| `map`/`set` | red-black tree | ordered, O(log n) |
| `unordered_map`/`unordered_set` | hash table | avg O(1), unordered |
| `stack`/`queue`/`priority_queue` | adapters | built on deque/vector/list |
| `string` / `string_view` | SSO buffer / view | `string_view` is non-owning (lifetime!) |

## Gotchas / pitfalls
- **Slicing:** copying a derived object into a base by value drops the derived part.
- Returning a reference to a local; lifetime-extension only applies to *direct* binding of a temporary.
- **Initialization-order fiasco** across translation units for global objects.
- `std::vector` reallocation invalidates iterators/references - call `reserve`.
- Exception safety levels (basic/strong/nothrow); **never throw from a destructor**.
- `string_view` pointing at freed data is a classic dangling bug.

## Self-test questions
1. Explain RAII and why it's central to C++ design.
2. `unique_ptr` vs `shared_ptr` vs `weak_ptr` - when each? When would you use a raw pointer?
3. What is move semantics, and why can't we just copy everything?
4. What's in a vtable, and why should a polymorphic base have a virtual destructor?
5. Name three sources of undefined behavior and how to detect/prevent them.
6. Why does `std::vector` need `reserve`, and what breaks if you don't call it?
7. C++20: what problem do concepts solve compared to SFINAE?
