# Graphs
## Graph Traversals
- There are two traversal algorithms that can be used to determine node-to-node connectivity (that is, if there is a path *s* to *t* in *G*) - breadth-first search and depth-first search
### Breadth-First Search
- In **breadth-first search**, exploration is done outward from *s* in all possible directions adding nodes *layer by layer*
    - Let layer *L<sub>1</sub>* consist of all nodes that are neighbors of *s*, and layer *L<sub>j + 1</sub>* consist of all nodes that do not belong to an earlier layer and that have an edge in layer *L<sub>j</sub>*
    - It is the case that a layer *L<sub>j</sub>* is the set of all nodes at a distance *j* exactly from *s* - so breadth-first search computes not only the nodes that *s* can reach but also the shortest (unweighted) paths to them
- Breadth-first search produces a tree *T* rooted at *s* on the set of nodes reachable from *s* - when a node *v* is first discovered, the node *u* it was discovered from is used to add an edge *(u, v)* to the tree *T*