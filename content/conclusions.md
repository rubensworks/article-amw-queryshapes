## Outlook
{:#conclusions}

In this article, we formally introduced the *Fragment Selector Shapes Language*
as a language for representing the expressivity of acceptable queries to LDF servers.
FSSL allows *Fragment Selector Shapes* to be represented,
which we used to represent the expressivity of all well known LDF interface types.
Furthermore, we provide an approach to match a SPARQL query against an FSS.
We use this matching approach for a query decomposion algorithm that is LDF interface-aware.

FSSs have been used within the Comunica query engine since March 2024,
but this is the first article that formalizes the approach.
Concretely, FSSs are being used to query over TPF, brTPF, and SPARQL endpoints.
Passage -- which internally makes use of Comunica -- also offers its own FSS.
Besides using FSSs for query decomposition,
Comunica also uses it for other purposes,
such as determining if filters can be pushed down into sources or not.
Related to this, Comunica also uses an extension to FSSL that allows [SPARQL extension functions](https://www.w3.org/TR/sparql12-query/#extensionFunctions){:.mandatory} to be captured,
to [determine which extension functions can be pushed into the server and which should be executed client-side](cite:cites timefunctions).

Currently, FSSL is only defined in a formal manner.
In future work, we aim to define a concrete syntax or vocabulary
to allow LDF interfaces to express their FSS through metadata.
This metadata should properly link an FSS to the existing [hypermedia controls](cite:cites hydra) for determining interface inputs,
and the FSS should be linked to the [outputs of the interface](cite:cites taelman_kcap_2017).
Currently, Comunica has an internal hardcoded mapping of LDF interface types to their FSS.
As soon as such a syntax or vocabulary would exist,
this internal mapping can be removed,
and get replaced by automatic discovery.

In future work, there is a need to extend the formalization of our query matching definition,
which currently only informally defines how $$ A_{scope} $$ is handled.
Related to this, we aim to explore the algebraic properties of FSSL.
Finally, we aim to systematically evaluate the performance of our FSS-based query decomposition approach.

In conclusion, we foresee FSSL playing an important role to support the heterogeneous landscape of Knowledge Graph interfaces,
where it becomes easier for publishers to come up with their own interfaces,
and where client-side query engines can autonomously make use of them,
without having to rely on hardcoded interface support.
