I'm messing around with D2's glob patterns to bulk-style nodes and I've hit a wall. Right now the glob filters let me match on whether a node exists or on its label value, but I can't filter by where a node sits in the tree or whether it's wired up to anything, and I really need both.

First thing I want is a leaf filter, basically match on whether a node has children or not. When it's disabled it should match only container nodes (the non-leaves), and when it's enabled it should match only leaf nodes. So if I've got a three-level nested hierarchy and I apply a fill to everything that's NOT a leaf, I'd expect the two outer container levels to get styled and the deepest node to be left alone since it's a leaf.

Second thing is a connectivity filter based on whether a node shows up in any edge. When it's enabled it matches only nodes that appear as a source or destination in at least one connection, and when disabled it matches only the isolated ones. Say I've got two nodes joined by an edge plus a third node sitting off on its own, turning this on should style the two connected nodes and skip the loner.

Big thing for both of these: when a node doesn't meet the filter condition it needs to be skipped completely, none of the attributes in the glob block should touch it, as if the glob never applied to that node at all. It shouldn't be partially styled or anything weird like that.

The whole point is that without these I'm stuck manually enumerating container nodes or connected nodes, which kind of defeats the purpose of using globs in the first place. Can you add these two boolean filter conditions into the glob system so I can express this stuff with a single pattern?
