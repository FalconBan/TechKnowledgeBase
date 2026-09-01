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

## The C++ Memory Model (concurrency)
- **Data race:** two unsynchronized accesses to the same memory location in different threads, at least one a write, and neither sequenced before the other -> **undefined behavior**. This is the rule; "just use atomics or locks."
- **Ordering relationships** (C++ terms, analogous to happens-before): *sequenced before* (within a thread), *synchronizes with* (e.g., unlock->lock on the same mutex; atomic ops per memory order), and the derived *happens before*. If A synchronizes-with / is sequenced-before B, A's writes are visible to B.
- **`std::atomic` & memory orders:** `memory_order_relaxed` (no ordering, just atomicity), `acquire` (reads see prior releases), `release` (publishes prior writes), `acq_rel`, and `seq_cst` (strongest; the default). Use the defaults unless you have a measured reason.
- **`volatile` in C++ is NOT for synchronization.** Unlike Java, C++ `volatile` only marks accesses with observable side effects (e.g., memory-mapped hardware) - it gives NO thread-safety guarantees. Using it instead of atomics/locks is a bug.
- **Practical:** share immutable state freely; otherwise protect with mutexes, use `std::atomic` for simple counters/flags, or design around immutability.


## Concurrency
- `std::thread`, locks (`lock_guard` / `unique_lock` / `scoped_lock`), atomics, condition variables.
- **Data race = undefined behavior** - never share mutable state across threads without synchronization.
- Prefer RAII lock guards (exception-safe); use `std::jthread` over raw `std::thread` for safe join/cancel.

## RAII & exception safety deep dive
- **RAII principle:** bind resource lifetime to object scope - acquire in the constructor, release in the destructor. Cleanup becomes deterministic and exception-safe "for free."
- **Exception-safety levels:**
  - **No-throw guarantee:** never throws (e.g., `swap`, many std ops).
  - **Strong guarantee:** either fully succeeds or leaves state unchanged (rollback) - via copy-and-swap / transactional patterns.
  - **Basic (nothrow) guarantee:** no leaks, invariants hold, but state may change on exception.
- **Copy-and-swap idiom** for strong exception safety + efficient assignment:
```cpp
void vec::assign(const vec& other) {
    vec tmp(other);   // may throw
    swap(tmp);        // noexcept - old contents released here
}
```
- **Destructors must not throw** - a throwing destructor during stack unwinding calls `std::terminate`. Mark them `noexcept`.
- **Custom deleters:** `unique_ptr<T, Deleter>` controls release (e.g., `fclose`, `delete[]`, a lambda).
- **Rule of Five / Zero:** owning raw pointers are the smell; prefer smart pointers/containers so you need no special members.

## Move semantics & perfect forwarding deep dive
- **Rvalue references** (`T&&`) name expiring temporaries; `std::move` is just a cast to an rvalue reference - it moves nothing by itself.
- **Moved-from state invariant:** after a move the source must be valid but unspecified - destructible and assignable. Don't read moved-from values for logic.
- **Copy elision:** the compiler may omit copies/moves. Since C++17, returning a **prvalue** (unnamed temporary) is guaranteed to involve no copy/move ("guaranteed copy elision"); **NRVO** for a *named local variable* is still only *permitted*, not mandated (C++17-23). Never write `return std::move(x);` for a local - it can *prevent* elision.
- **Forwarding (universal) references:** a parameter of the form `T&&` where T is deduced is a *forwarding reference*, not an rvalue ref:
```cpp
template <class T> void f(T&& param) { } // forwarding reference
f(42);            // T = int, param binds as lvalue
f(std::move(x));  // T = int, param is an rvalue
```
- **`std::forward<T>(param)`** preserves value category (lvalue/rvalue) for perfect forwarding - cast back to the deduced type. Use it when forwarding onward; don't overuse it.


## Common interview topics / questions
- Pointers vs references; shallow vs deep copy; when slicing happens.
- Virtual functions: vtable/vptr mechanics, virtual destructors (a base with virtual fns must have one), overriding vs hiding.
- `const` correctness; static members; `friend`; name lookup (hiding vs overloading), ADL.
- Operator overloading - what can/can't be overloaded.
- Move vs copy; why move exists; perfect forwarding.
- Smart pointer pitfalls: cyclic `shared_ptr` (fix with `weak_ptr`), double-delete, custom deleters, `unique_ptr` from an array.
- UB examples: signed overflow, out-of-bounds, use-after-free, data races, uninitialized reads.
- ODR violations; template instantiation rules.

## Templates & metaprogramming deep dive
- **Instantiation:** templates produce code at compile time per instantiated type - no runtime cost (zero-cost abstraction); a template is instantiated on demand when used.
- **SFINAE** (Substitution Failure Is Not An Error): deduction/substitution failure in the *immediate context* isn't ill-formed - enables overload resolution / trait detection (e.g., member detection via `std::void_t`).
- **Type traits:** `std::is_*`, `std::enable_if`, `std::conditional_t` for compile-time decisions.
- **CRTP** (Curiously Recurring Template Pattern): static polymorphism - `class Derived : public Base<Derived>` gives "virtual-like" behavior without a vtable.
- **Template specialization:** full and partial specializations to handle specific types.
- **Modern C++20 alternatives:**
  - **Concepts** (`concept`, `requires`) constrain templates with readable conditions and better error messages than SFINAE:
```cpp
template <class T>
concept Printable = requires(T t, std::ostream& os) { os << t; };
```
  - **`if constexpr`** for compile-time branching inside one template body. Prefer concepts + `static_assert` over deep SFINAE where possible.

## Smart pointers & ownership deep dive
- **`std::unique_ptr<T>`:** exclusive ownership; zero overhead (same size as a raw pointer); non-copyable, movable. A custom deleter changes the type (`unique_ptr<T, Del>`). Use `make_unique`; array form is `unique_ptr<T[]>`.
- **`std::shared_ptr<T>`:** shared ownership via a reference count in a **control block**; ref counting is thread-safe (access to the pointee is not). Prefer `make_shared` (one allocation for object + control block) over `shared_ptr<T>(new T)`.
- **`std::weak_ptr<T>`:** observes without owning; breaks cycles. Use `lock()` to get a temporary `shared_ptr` (empty if expired).
- **Ownership rule:** exactly one owner -> `unique_ptr`; genuinely shared -> `shared_ptr`; observe only -> raw pointer/reference (non-owning).
- **Pitfalls:** cyclic `shared_ptr` (use `weak_ptr`), double-delete, building a `shared_ptr` from another's raw pointer (double ownership), array mismatch (`new T[]` with scalar delete - use `unique_ptr<T[]>`/custom deleter).

## Virtual functions & polymorphism internals
- **vtable/vptr:** each polymorphic class has a virtual table; its objects carry a hidden pointer (vptr) to it. A virtual call is indirect dispatch through the vtable - slightly slower than a direct call and not always inlineable.
- **Virtual destructor:** any base with at least one virtual function should have a virtual destructor, so deleting a derived object through a base pointer is well-defined. Deleting through a base pointer without one -> UB.
- **Overriding vs hiding:** use `override`/`final` to be explicit; omitting `virtual` in the override can accidentally *hide* (shadow) a base function instead of overriding it.
- **Slicing:** copying a derived object into a base by value drops the derived part - dangerous when the type has data or a non-virtual destructor.
- **Abstract classes:** at least one pure virtual function (`= 0`); can't be instantiated; still need a (often `protected`) destructor to force deletion through pointers.
- **Casts:** `dynamic_cast` (RTTI; safe downcast - null/throws for refs), `static_cast` (compile-time-checked up/downcast), `const_cast`, `reinterpret_cast` (low-level, dangerous). Prefer design + static checks over `reinterpret_cast`.


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
8. What exactly is a data race in C++, and why is it undefined behavior?
9. How is C++ `volatile` different from Java's - and why can't it be used for thread safety?
10. The three exception-safety guarantees, and how copy-and-swap gives you the strong one.
11. Why must a destructor not throw, and what happens if it does during unwinding?
12. Rvalue reference vs forwarding (universal) reference; when do you need `std::forward`?
13. What is SFINAE, and where does it fall short compared to concepts?
14. Why does `shared_ptr` have a control block, and how do you break a reference cycle?
15. Overriding vs hiding - what role do `override`/`final` play, and when is slicing dangerous?
