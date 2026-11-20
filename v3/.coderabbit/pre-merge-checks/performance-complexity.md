---
id: performance-complexity
name: Performance and Algorithmic Complexity Review
mode: warning
# DISABLED: example only (commented out in the V2 config). Set enabled: true to run this check.
enabled: false
---

- Carefully review this code for performance regressions, unnecessary algorithmic complexity, and hot-path inefficiencies
- In frontend code, flag unnecessary rerenders, repeated expensive selectors, large list rendering without virtualization, unstable memoization dependencies, or recomputation during render
- Treat nominally linear work as blocking when it is performed at high frequency, on large inputs, or redundantly across many calls such that caching, indexing, batching, or memoization is clearly warranted
- Flag O(n^2), O(n^3), or worse behavior when input sizes may be non-trivial, especially in request handlers, background jobs, rendering paths, or loops over collections
- Flag hidden nested work inside loops, including repeated scans, filtering, sorting, serialization, regex compilation, JSON parsing, DB lookups, HTTP calls, DOM queries, or recomputation of invariant values
- Flag N+1 query patterns and require batching, joins, preloading, or set-based retrieval where appropriate
- Flag repeated recalculation of deterministic values in hot paths when caching, memoization, precomputation, or moving work outside the loop would be appropriate
- Flag inefficient data structure choices, such as linear membership checks on arrays when sets/maps are more appropriate, repeated copying, repeated concatenation, or frequent front insertions
- Flag unbounded growth, memory leaks, retained listeners/subscriptions, accumulating buffers, or caches without eviction or cleanup
- Flag avoidable allocation churn in hot paths, including repeated object creation, string building, cloning, intermediate arrays, or conversions that increase GC pressure
- Flag repeated sorting or repeated full passes over large collections when a single pass, indexing, partial selection, or precomputation would suffice
- Flag recursive implementations that risk excessive recomputation, poor asymptotic behavior, or stack overflows when iterative or memoized approaches are safer
- Flag over-fetching or over-processing when code retrieves, deserializes, or processes more data than necessary
- Flag expensive synchronous work in critical paths, including compression, encryption, hashing, parsing, template rendering, logging, analytics, or debug-only work that should be deferred, sampled, batched, or removed
- Flag missing guardrails such as pagination, size limits, timeouts, concurrency limits, backpressure, or batching for large or adversarial inputs
- Flag process problems that generate excessive intermediate structures, duplicate traversals, discard reusable results, or turn simple iterative work into unnecessarily expensive recursive or tree-recursive work
