# Java (up to Java 21)

## The core mental model
- Everything is an object on the **heap** (except primitives); you don't manage memory directly - the **GC** does.
- You *do* own: reference semantics, immutability, and thread safety.
- Runs as bytecode on the **JVM**; JIT compiles hot paths to native. "Write once, run anywhere."

## Memory & object model
- **Heap** (objects) vs **stack** (references, local vars, frames) vs statics.
- **`==`** = reference equality; **`.equals()`** = value equality. Always override `equals()` together with `hashCode()` (the contract: equal objects must have equal hashes).
- **GC:** generational (young/old); **G1** is the default since Java 9+; **ZGC / Shenandoah** for low-latency, large heaps.
- Object lifecycle: allocate -> reachable from GC roots -> unreachable -> collected.

## Language features & modern additions (8 -> 21)
- **Java 8:** lambdas, **streams API**, `@FunctionalInterface`, `Optional`, default methods in interfaces.
- **Java 9:** modules (JPMS), collection factories (`List.of`, `Map.of`).
- **Java 10:** `var` for local variable type inference.
- **Java 15/16:** text blocks, records, sealed classes, `instanceof` pattern matching.
- **Java 17 (LTS):** sealed types + records matured; strong encapsulation of JDK internals.
- **Java 21 (LTS):** **virtual threads** (final), **pattern matching for `switch`** (final), **record patterns** (final), **sequenced collections**, structured concurrency (preview), string templates (preview), foreign function & memory API (preview).

## Concurrency & the new threading model
- `Thread`, `Runnable`, `ExecutorService`, thread pools, `CompletableFuture`.
- `java.util.concurrent`: `ConcurrentHashMap`, atomics, locks (`ReentrantLock`), `ForkJoinPool`.
- **Immutability is the primary thread-safety strategy** - share immutable state freely.
- **Virtual threads (Java 21):** lightweight M:N threads; ideal for high-concurrency I/O-bound code. Caveat: don't **pin** them (avoid `synchronized` blocks / native calls on a virtual thread).

## Collections & common APIs
| Collection | Backing | Notes |
|-----------|---------|-------|
| `ArrayList` | dynamic array | fast random access, O(1) get |
| `LinkedList` | doubly-linked list | fast mid insert/remove, poor cache locality |
| `HashMap` / `TreeMap` / `LinkedHashMap` | hash / red-black tree / linked | unordered / sorted / insertion-ordered |
| `HashSet` / `LinkedHashSet` / `TreeSet` | via maps | unique elements |
| Deque / Queue | array/deque | FIFO + both ends |
| concurrent variants | CAS/locks | thread-safe (`ConcurrentHashMap`, etc.) |

**Streams:** intermediate ops are lazy (`filter`, `map`); terminal ops trigger execution (`collect`, `reduce`). Parallel streams help CPU-bound work but not always - beware shared-state bugs and overhead.

## OOP & design (interview favorites)
- Encapsulation, inheritance, polymorphism, abstraction; **composition over inheritance**.
- Abstract class vs interface - when to use each (state/ctor vs capability contract).
- SOLID (brief); common patterns: Builder, Factory, Strategy, Observer, Adapter, Decorator - and why Singleton is often a code smell.

## Common interview topics / questions
- `String` vs `StringBuilder` vs `StringBuffer`; why `String` is immutable.
- `==` vs `.equals()`; the `hashCode`/`equals` contract.
- `final` vs `finally` (and deprecated `finalize`).
- Checked vs unchecked exceptions; when to use each; avoid catching `Exception` broadly.
- Generics: **type erasure**, wildcards, PECS (`? extends` for produce, `? super` for consume).
- Fail-fast iterators and `ConcurrentModificationException`.
- Memory model: `volatile`, happens-before, visibility across threads.

## Gotchas / pitfalls
- **Boxing/unboxing** cost; NPE from unboxing a null `Integer`.
- Object churn in loops (autoboxing, string concatenation) - prefer `StringBuilder`.
- String interning & the string pool; large strings as map keys.
- Deadlocks with nested/overlapping locks.
- **Don't use `synchronized` on virtual threads** (causes pinning).
- Integer overflow and silent wrap-around in arithmetic.

## Self-test questions
1. Why is `String` immutable, and what are the trade-offs?
2. Explain the `equals()`/`hashCode()` contract and why both must be overridden together.
3. What's a virtual thread, when would you use it, and what should you avoid inside one?
4. Records vs a regular class - what do records generate for you?
5. Generics: what is type erasure, and how do wildcards + PECS help?
6. G1 vs ZGC - when would you reach for the low-latency collector?
