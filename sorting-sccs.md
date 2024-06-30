We want to do a reverse topological sort on the strongly connected components of a digraph G, where an SCC is defined by the properties that (a) there is a route from every node in it to every other node (b) it is maximal with respect to this property.

If we have an algorithm which performs such a sort on all the unvisited points reachable from a given node p, then clearly we can achieve our aim by repeatedly selecting a random unvisited node p in G, getting the sort from that starting point, and repeating until every node has been visited.

I claim that the following performs such a sort.

For convenience of exposition, in what follows I do not pass the graph G as a parameter. This is because we want the annotation as to whether a node in the graph has been visited to be a piece of "global state", as it were — if it has been visited by one branch of a recursive call, it should be perceived as visited by another. That way our algorithm will have to visit each node only once. Similarly the "result list" is an implicit piece of global state, a list[set[node]] which we just append to when we find an SCC.

So. The algorithm:

```
sort (p node) -> list[set[node]]
	mark p as visited
	let  q₀ … qₙ be the nodes such that p -> qᵢ is in G.
	for each unvisited qᵢ :
		let Qᵢ = sort(qᵢ) for each i in 0 … n
	let P = {p}
	for each Q in ΣQᵢ
        	if there is no edge from Q to p :
			append Q to the result list
		else :
			let P = P ∪ Q
	append P to the result list
```

We will prove that it works by induction.

Clearly when p is the only element reachable from p, then the algorithm will return {p} as required.

So let the set R(p) of unvisited nodes reachable from p have size n > 1, and suppose that the algorithm works for any node x with R(x) < n.

Then for any q in q₀ … qₙ, we do have R(q) < R(P), because since q is reachable from p, R(P) must contain R(q), but it must also contain p, which R(q) cannot because it's created after p has been marked as visited.

So sort does work on each of the qᵢ.

Now consider the two cases in the final loop of our algorithm. If Q has no back edges to p, then bringing p back into consideration changes nothing: Q is still connected, and the addition of p doesn't stop it from being maximal with respect to that property, because of the absence of a back edge from Q to p.

So then consider the final thing we return, the union of {p} with all the Q in ΣQᵢ that do have back edges to it. It is clearly connected, because by construction from any x and y in this set we have a route from x to p, and a route from p to y. So we have a route from x to y.

Is it maximal? Well, if it wasn't, then there must be some z with a route from p to z and a route from z to p such that z is not in our final value of P. Hence it can't be p itself, and it can't be in any of the Q that have back edges to p, because that's just what we united with {p} to get our final value of P. And it can't be in any of the Q that we added to the result list, because by definition they have no back edges to p. But that exhausts all the unvisited nodes that are reachable from p. So z cannot exist.

And the final value of P that we add to the list at the end is clearly in the right place in the list because it contains p, and so is closer to p than all the other SCCs we added.

This completes the proof.
