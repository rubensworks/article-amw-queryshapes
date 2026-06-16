## Query Decomposition
{:#querydecomposition}

[Heling et al.](cite:cites heterogeneous_lars) provide an interface-aware query decomposition algorithm.
However, this algorithm is limited to only handling triple patterns and BGPs,
and it does not precisely define how interace support for query expressions is tested.

Within our query decomposition approach,
we assume that we start with the atomic query decomposition,
i.e., the query has already undergone [exhaustive source assignment](cite:cites heterogeneous_fedqpl),
where federation members have been pushed down onto triple patterns.
In other words, every triple pattern has a federation member (or *source*) annotated to it.
If multiple federation member apply to a triple pattern,
then a union of multiple annotated triple patterns exists.
Based on this atomic query decomposition, we perform an *interface-aware grouping of query expressions into sources* algorithm,
as described in [](#query-grouping-algorithm)
This algorithm is generic enough to handle all SPARQL query expressions,
and tests interface support using $$[[q]]^{match}_{fss}$$ (which corresponds to `matchesFss` in the following pseudocode).
An implementation of this algorithm can be found in the [`@comunica/actor-optimize-query-operation-group-source` package](https://github.com/comunica/comunica/tree/v5.2.3/packages/actor-optimize-query-operation-group-sources).

This algorithm works in a bottom-up recursive manner,
which starts with the leaves of the query,
and tries to group larger and larger parts of the query together if they share a federation member
and if that federation member's FSS can handle that subquery.
Concretely, the algorithm starts (line 1) by checking if the current query expression is already directly annotated with a source
or has no child expressions, and returns the expression as-is in that case.
Initially, this will always be the case for expressions such as triple patterns.
Next (line 3), we recusively invoke the `groupIntoSources` function for all child expressions.
Then (line 4), we cluster all of these mapped expressions into clusters, based on their source annotation.
If we find only one cluster (line 5), we check if the source for this cluster accepts the current query expression (line 6),
and if so, assigns the source to it (line 8).
If we find multiple clusters (line 9), we iterate over all clusters (line 10),
and check if the cluster's source matches this clusters's query expression (line 12).
If so, we assign the cluster's source to this query expression (line 14).
Finally (line 15), we instantiate a new query expression (e.g. `UNION` or `JOIN`) with the same type as the original query expression,
but containing the modified query expressions from the clusters.
In all other cases (line 16), we return the query expression as-is.

<figure id="query-grouping-algorithm" class="listing">
````/code/query-grouping-algorithm.txt````
<figcaption markdown="block">
An algorithm that groups parts of the subquery into federation members (sources).
It starts from the atomic decomposition
and groups operations together in an interface-aware manner.
</figcaption>
</figure>