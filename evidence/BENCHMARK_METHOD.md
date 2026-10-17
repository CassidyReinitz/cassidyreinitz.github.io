# Graph benchmark method and limits

Instructor recommendation: test increasingly large and dense prerequisite graphs.
Command: make build/graph_benchmark && ./build/graph_benchmark
Compiler: g++ C++17, -O2, warning flags listed in Makefile.

Twelve generated DAG configurations: sparse (up to three prerequisites), chain,
and wide fan-out at 250, 1000, 4000 vertices; fully connected forward DAGs at
250, 500, 1000 vertices. Dense edge counts are V(V-1)/2.
Three repetitions per configuration; retain median wall-clock milliseconds.
Dataset and lookup-key generation are excluded from timing. Parsing, validation,
graph construction and load-time cycle detection are included in load time.
Separate cycle and topological timings measure those complete public methods.

Each schedule must contain every course exactly once; an independent position map
checks EVERY generated edge. Repeated schedules must be identical.
Each run verifies all 10,000 successful normalized-ID lookups.
A 4000-vertex chain with a closing back-edge must be rejected and leave the
catalog empty. Failure makes the benchmark return a nonzero status.

Results are actual local measurements on synthetic inputs, not production latency
guarantees or a statistical scaling proof. Medians of three runs are descriptive,
not confidence intervals. No fixed latency threshold is imposed.

Important source-level corrections: hasCycle sorts V vertex IDs before its linear
DFS, so the complete method is expected O(V log V + E), not simply O(V + E).
buildGraph also sorts dependent lists; its cost includes sum(d log d).
Kahn's min-heap implementation has expected O(E + V log V) time (assuming expected
constant-time hash access). Identifier lengths and CSV bytes add parsing costs.
Alphabetical display copies Course objects, including prerequisite vectors, so it
also incurs O(V + E) copying before O(V log V) comparisons.
Recursive DFS may exhaust the stack at greater depths than tested. No arbitrary
graph size is claimed. Hash-table worst cases are not bounded by these medians.

AI use: ChatGPT assisted with the benchmark, execution, and this documentation.

Sanitizer rerun: ten regression tests passed with AddressSanitizer and
UndefinedBehaviorSanitizer. ASAN_OPTIONS=detect_leaks=0 was required because
LeakSanitizer cannot inspect processes under this environment's tracing.
Leak detection was not completed; no leak-free claim is made.
