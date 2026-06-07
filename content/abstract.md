## Abstract
<!-- Context      -->
Knowledge Graphs on the Web can be published using a variety of different interfaces,
which are called Linked Data Fragments (LDFs).
These include SPARQL endpoints, Triple Pattern Fragments, and more.
Client-side querying techniques exist for retrieving data from singular LDF types,
and there exist techniques that enable federated querying across multiple *heterogeneous* types of these LDFs.
However, these federation techniques make prior assumptions about the expressivity of these LDFs,
and are hardcoded to support only specific LDFs,
which limits their use for other LDFs that may not be known yet to the client.
<!-- Need         -->
As such, there is a need for a mechanism that allows LDF servers to represent their expressiveness in a generic manner,
so that clients can discover this and use it for query planning, without having to make assumptions on LDF expressivity.
<!-- Task         -->
Concretely, this article introduces *Fragment Selector Shapes* (FSSs) to describe LDF server expressiveness.
These shapes are composed of algebraic SPARQL operator references,
which can be used to represent expressivity ranging from the full SPARQL language to just single triple patterns.
<!-- Object       -->
We show how this language can be used to represent the capabilities of existing LDFs,
and how client-side querying techniques can use them during query planning.
<!-- Findings     -->
Our implementation experience within the Comunica engine shows that
this mechanism is effective for handling different LDFs without performance overhead.
However, more work is needed on systematic performance analyses,
and we currently lack a syntax to represent FSS in LDF metadata for full discoverability.
<!-- Conclusion   -->
In conclusion, FSSs not only generalize query decomposition across existing LDFs,
<!-- Perspectives -->
but they also lower the barrier towards the introduction of new LDFs,
without necessarily requiring client-side adaptations.
