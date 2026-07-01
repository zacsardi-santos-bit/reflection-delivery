Implement the `Copy` and `CopyWeighted` functions in the `graph/graph.go` file to correctly copy nodes and edges between graph representations, ensuring proper handling of directed and undirected graphs, as well as preserving edge weights.

Requirements:

*   Implement the `Copy` function with the following signature:
    *   `Copy(dst Builder, src Graph)`
    *   Copy all nodes from the source graph `src` into the destination graph `dst`, including isolated nodes.
    *   Copy all edges from `src` into `dst` using new edges created by `dst`, not directly from `src`.
    *   If `src` is undirected and `dst` is directed, create two directed edges in `dst` for each undirected edge in `src`.
    *   If `src` is directed and `dst` is undirected, convert directed edges from `src` into undirected edges in `dst`.
    *   If `src` and `dst` are of the same type (both directed or both undirected), ensure `dst` has identical nodes and edges as `src`.

*   Implement the `CopyWeighted` function with the following signature:
    *   `CopyWeighted(dst WeightedBuilder, src Weighted)`
    *   Copy all nodes from the weighted source graph `src` into the weighted destination graph `dst`, including isolated nodes.
    *   Copy all weighted edges from `src` into `dst`, preserving edge weights, using new edges created by `dst`.
    *   If `src` is a weighted undirected graph and `dst` is a weighted directed graph, create two directed edges in `dst` for each undirected edge in `src`, both with the same weight.
    *   If `src` is a weighted directed graph and `dst` is a weighted undirected graph, convert directed edges from `src` into undirected edges in `dst`, preserving weights.
    *   If `src` and `dst` are of the same type (both weighted directed or both weighted undirected), ensure `dst` has identical nodes, edges, and weights as `src`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.