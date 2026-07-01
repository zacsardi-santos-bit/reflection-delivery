Implement a topological sort utility for the u-root project under `cmds/exp/tsort`. This utility should read pairs of items from standard input or a specified file, where each pair indicates a dependency, and output a valid ordering of all items that respects these constraints.

*   Implement the `run` function with the following signature:
    ```go
    func run(stdin io.Reader, stdout io.Writer, stderr io.Writer, args ...string) error
    ```
    *   Accept input from `stdin` if no arguments are provided, or from a file specified by `args[0]`.
    *   Tokenize input using all Unicode whitespace characters as delimiters.
    *   Handle empty or whitespace-only input by producing no output and returning `nil`.
    *   Return `errOddDataCount` if the token count is odd.
    *   Output a single node name followed by a newline for self-referencing pairs (e.g., 'a a').
    *   Output a valid topological order for a directed acyclic graph (DAG) to `stdout`.
    *   Return `errNonFatal` and write cycle diagnostics to `stderr` if cycles are detected, while still outputting all nodes.
    *   Return an error containing the filename if a specified file does not exist, without writing to `stdout` or `stderr`.
    *   Propagate any I/O errors encountered during reading from `stdin`.

*   Implement the `set` type as a map of strings with methods:
    *   `add(value string)`: Insert a value.
    *   `has(value string) bool`: Check membership.

*   Implement the `graph` type with methods:
    *   `putEdge(source, target string)`: Add a directed edge, ignoring duplicates.
    *   `addNode(node string)`: Add a node without edges.
    *   `successors(node string) set`: Return direct successors, panic if node is absent.
    *   `inDegree(node string) int`: Return the number of incoming edges, 0 if absent.
    *   `removeEdge(source, target string)`: Remove a directed edge, panic if source or target is absent.

*   Implement the `multiset` type with methods:
    *   `isEmpty() bool`: Return true if empty.
    *   `has(value string) bool`: Return true if value has a positive count.
    *   `count(value string) int`: Return the count of value, 0 if absent.
    *   `add(value string, count int)`: Add count copies of value, panic if count <= 0.
    *   `removeOne(value string)`: Decrement count by 1, remove if count reaches 0, panic if value is absent.
    *   `forEachUnique(fn func(string) bool)`: Iterate over each unique value, stop if fn returns false.

*   Implement the `queue` type with methods:
    *   `isEmpty() bool`: Return true if the queue is empty.
    *   `enqueue(value string)`: Add value to the back of the queue.
    *   `dequeue() string`: Remove and return the front value, panic if the queue is empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.