# Network Flow
- **Flow networks** are directed graphs that are commonly used to describe phenomena such as transportation networks, where nodes pass traffic (known as **flow**) to other nodes
    - Positive weights on edges are known as **capacities**, representing the maximum amount of flow that can pass between the two nodes
        - A flow *f(e)* carried by edge *e*, must satisfy the **capacity** and **conservation** conditions
            - Capacity: *0 <= f(e) <= c<sub>e</sub>*
            - Conservation: *Σ<sub>e into v</sub> f(e) = Σ<sub>fe out of v</sub> f(e)* 
    - Each flow network has a single **source node s**, which has no incoming edges, and a single **sink node t**, which has no outgoing edges
        - All flow enters the network through the source node and exits the network through the sink node
        - The value of a flow *f* is the amount of flow exiting the source or, alternatively, the amount of flow entering the sink
            - *v(f) = f <sup>out</sup>(s) = f <sup>in</sup>(t)*
    - ![Flow Network](../Images/Flow_Network.png)
## Maximum Flow
- The Maximum-Flow Problem seeks, given a flow network, a flow of maximum value
    - When thinking about this problem, an approach can be to divide the nodes of the graph into two sets *A* and *B* such that *s is in A* and *t is in B*
        - Any flow must cross from *A* to *B* in order to get from *s* to *t*, so the edge capacity from **set** *A* to **set** *B* is used up
        - This implies that a *cut* of the graph, or a partition into sets *A* and *B*, places a bound on the maximum possible flow, and it can be proved that the maximum flow is equal to the minimum capacity of any cut
- Consider pushing a flow through an arbritary path from *s* to *t*, such that the flow is as high as possible without breaking any of the edges' capacities (that is, push flow equal to the smallest capacity, the **bottleneck capacity**, of the path)
    - It may be the case that, after performing this operation, the maximum flow can be achieved by "undoing" flow on a certain edge and diverting it elsewhere
- It is often useful to consider the **residual graph** *G<sub>f</sub>* with respect to the flow pushed *f*
    - *G<sub>f</sub>* has the same nodes as *G*, but now there may be two edges for each original edge
    - If, for *e = (u, v)*, it is the case that *f(e) < c<sub>e</sub>*, then there is residual capacity of *c<sub>e</sub> - f(e)*
        - The **forward edge** *(u, v)* is included *G<sub>f</sub>* with a **residual capacity** of *c<sub>e</sub> - f(e)*
    - If, for *e = (u, v)*, it is the case that *f(e) > 0*, then there is *f(e)* units of flow that can be diverted
        - The **backward edge** *(v, u)* is included in *G<sub>f</sub>* with a **residual capacity** of *f(e)*
- The process of **augmenting** a path in the residual graph *P* involves identifying the **bottleneck capacity**, or minimum residual capacity, of any edge on *P*, and then altering the flow by this capacity - this creates a new flow *f'*
    -     augment(f, P)
            Let b = bottleneck(P, f)
            For each edge (u, v) in P
              If e = (u, v) is a forward edge then
                increase f(e) in G by b
              Else ((u, v) is a backward edge, and let e = (v, u))
                decrease f(e) in G by b
        - The capacity conditions are still maintained because of the choice of using the *bottleneck capacity*
            - Forward Edge: *0 <= f(e) <= f'(e) = f(e) + bottleneck(P, f) <= f(e) + (c<sub>e</sub> - f(e)) = c<sub>e</sub>*
            - Backward Edge: *c<sub>e</sub> >= f(e) >= f'(e) = f(e) - bottleneck(P, f) >= f(e) - f(e) = 0*
        - The conservation conditions are also still maintained
        - ![Augment Path](../Images/Augment_Path.png)
- **Ford-Fulkerson Algorithm** for Maximum Flow:
    -     Max-Flow
            Initially f(e) = 0 for all e in G
            While there is an s-t path in the residual graph Gf
              Let P be a simple s-t path in Gf
              f' = augment(f, P)
              Update f to be f'
              Update the residual graph Gf to be Gf'
            Return f
- At every stage of the Ford-Fulkerson Algorithm, the flow values and residual capacities are *integers* - this is because all edge capacities are integers, and so all residual capacities will also be integers
    - This fact can be used to show that the algorithm eventually terminates: *Let f be a flow in G, and let P be a simple s-t path in G<sub>f</sub>. Then v(f') = v(f) + bottleneck(P, f); and since bottleneck(P, f) > 0, it is the case that v(f') > v(f)*
    - An upper bound on the maximum flow value can be established as the sum of capacities for edges out of *s*, denoted as *C*
        - The Ford-Fulkerson Algorithm terminates in at most *C* iterations of the loop, since the value of the flow could, in the worst case, increase by 1 for *C* times until the upper bound is reached
- During each iteration of the algorithm, it takes *O(m + n) = O(m)* time to find an *s-t* path in *G<sub>f</sub>* (since each vertex has at least one edge, so there are more edges that vertices), *O(n)* time to perform an augmentation, and *O(m)* time to build a new residual graph
    - Each iteration can be bounded by *O(m)*, and since there can be at most *C* iterations, the algorithm is bounded by *O(mC)* time
## Maximum Flows and Minimum Cuts in a Network
- A **cut** in a flow network is a division of nodes into two sets, *A* and *B*, such that *s ∈ A* and *t ∈ B*
    - The *capacity* of a cut *(A, B)*, denoted as *c(A, B)* is the sum of all capacities out of *A*: *Σ<sub>e out of A</sub>c<sub>e</sub>*
    - *f<sup> out</sup>(A) = f<sup> in</sup>(B)*
    - *f<sup> in</sup>(A) = f<sup> out</sup>(B)*
- It can be shown that for any *s-t* flow *f*, and any *s-t* cut *(A, B)*, that *v(f) = f<sup> out</sup>(A) - f<sup> in</sup>(A)* - the flow of a network can be measured by the amount that exits *A* minus the amount that enters back into *A*
    - This can be proven using the fact that *v(f) = f<sup> out</sup>(s)* and *f<sup> in</sup>(s) = 0* and for any node in *A* other than *s*, *f<sup>out</sup>(v) - f<sup>in</sup>(v) = 0*, so:
        - $v(f) = \sum\limits_{v\in A}(f^{out}(v) - f^{in}(v))$
        - $\sum\limits_{v\in A}(f^{out}(v) - f^{in}(v)) = \sum\limits_{e\ out\  of \  A}f(e) - \sum\limits_{e \ into \ A}f(e) = f^{out}(A) - f^{in}(A)$
- If *f* is any *s-t flow* and *(A, B)* is any cut, then *v(f) ≤ c(A, B)*; the value of every flow is upper bounded by the capacity of every cut
    - $v(f) = f^{out}(A) - f^{in}(A) \leq f^{out}(A) = \sum\limits_{e \ out \ of \ A}f(e) \leq \sum\limits_{e \ out \ of \ A}c_e = c(A, B)$
    - Since the value of every flow is bounded by the capacity of every cut, if some minimum cut *c\** is found, then this minimum cut will correspond to a maximum flow
- The Ford-Fulkerson Algorithm terminates when the flow *f* has no *s-t* path in the residual graph *G<sub>f</sub>* - this can be used to show that there is an *s-t* cut *(A\*, B\*)* such that *v(f) = c(A\*, B\*)* and that this cut has the minimum capacity of any *s-t* cut in *G* (and therefore *f* has the maximum value of any in flow in *G*)
    - Let *A\** denote all nodes in *G* in which there is an *s-v* path in *G<sub>f</sub>* and *B\** denote *V - A\**
    - Consider *e = (u, v)* in *G*, where *u ∈ A\** and *v ∈ B\**; it *must* be the case that *f(e) = c<sub>e</sub>* as otherwise *e* would be a forward edge in the residual graph, implying a *s-v* path which would contradict the assumption that *v ∈ B\**
    - Consider *e' = (u', v')* in *G*, where *u' ∈ B\** and *v' ∈ A\**; it *must* be the case that *f(e') = 0* as otherwise there would be a backward edge in the residual graph, implying a *s-u'* path which would contradict the assumption that *u' ∈ B\**
    - $v(f) = f^{out}(A^{\*}) - f^{in}(A^{\*}) = \sum\limits_{e \ out \ of \ A^{\*}}c_e - 0 = c(A^{\*}, B^{\*})$
    - ![Maximum Flow Minimum Cut](../Images/Maximum_Flow_Minimum_Cut.jpg)
- If all capacities in a flow network are integers, then there is a maximum flow *f* for which every flow value *f(e)* is an integer
    - If capacities were non-integer values, it is possible for the Ford-Fulkerson Algorithm to loop infinitely, as there could be cases where the value of the flow keeps increasing but in smaller and smaller increments
## Choosing Good Augmenting Paths
- For large values of *C*, the upper bound of *O(mC)* on the Ford-Fulkerson Algorithm can result in slow performance
    - ![Ford Fulkerson Slow Performance](../Images/Ford_Fulkerson_Slow_Case.jpg)
        - The maximum flow is 200, and it could be easily found by pushing 100 units of flow through the top path and then 100 units of flow through the bottom path
        - However, it could be the case that an algorithm keeps choosing a *s-t* path in the residual graph where the bottleneck is 1, resulting in the flow value incrementing by 1 each time for 200 iterations
- One way to optimize the Ford-Fulkerson Algorithm is to maintain a scaling parameter Δ and looking for paths that have a bottleneck capacity of at least Δ; *G<sub>f</sub>(Δ)* is the subset of the residual graph containing edges with residual capacity at least Δ
    -     Scaling Max-Flow
            Initially f(e) = 0 for all e in G
            Initially set Δ to be the largest power of 2 that is no larger than the maximum capacity out of s
            While Δ >= 1
                While there is an s-t path in the graph Gf(Δ)
                  Let P be a simple s-t path Gf(Δ)
                  f' = augment(f, P)
                  Update f to be f' and update Gf(Δ)
                Δ = Δ/2
            Return f
- The initial value of Δ is C and then it drops by factors of 2, so the number of iterations of the outer while loop is at most $1 + \lceil log_2C \rceil$
    - During each Δ-scaling phase (iteration of the outermost loop), each augmentation increases the flow value by at least Δ
    - If *f* is the flow at the end of the Δ-scaling phase, then there is an *s-t* cut *(A, B)* in *G* where *c(A, B) ≤ v(f) + mΔ*, implying that the maximum flow in the network has a value of at most *v(f) + mΔ*   
        - Let *A* be the set of nodes *v* in *G* for where there is an *s-v* path in *G<sub>f</sub>(Δ)* and *B* be *V - A*
            - If there is an edge *e = (u, v)* where *u* is in *A* and *v* is in *B*, then *c<sub>e</sub> < f(e) + Δ* as otherwise *e* would be a forward edge in *G<sub>f</sub>(Δ)*, which contradicts *v* being in *B*
            - If there is an edge *e' = (u', v')* where *u'* is in *B* and *v'* is in *A*, then *f(e') < Δ* as otherwise *e'* would be a backward edge in *G<sub>f</sub>(Δ)*, which contradicts *u'* being in *B*
            - ![Scaling Factor Proof](../Images/Scaling_Phase_Proof.jpg)
    - The number of augmentations in a scaling phase is at most *2m*
        - Let *f<sub>p</sub>* be the flow at the end of the previous phase, with Δ' = 2Δ as the parameter (Δ is the current phase's scaling factor)
        - *v(f\*) ≤ v(f<sub>p</sub>) + mΔ' = v(f<sub>p</sub>) + 2mΔ*
        - Each augmentation changes the flow by at least Δ, and by the inequality there can be at most *2m* augmentations
- The Scaling Max-Flow Algorithm runs in at most *O(m<sup>2</sup>log<sub>2</sub>C)* time since the outermost while loop runs in *O(log<sub>2</sub>C)* time and there are *2m* augmentations in each scaling phase, each of which take *O(m)* time
