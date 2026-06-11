## Introduction
{:#introduction}

One of the most techniques today to publish a Knowledge Graph, is through a [SPARQL endpoint](cite:cites spec:sparqlprot12).
Despite their popularity, [SPARQL endpoints suffer from availability issues](cite:cites sparqlreadyforaction,monitoringsparql),
which is partly due to the costly hardware requirements caused by their computational expressivity.
For this reason, [alternative interfaces](cite:cites tpf,vtpf,smartkg,sage,spf,wisekg,brtpf,passage) have been introduced over the recent years
to publish Knowledge Graphs.
In contrast to SPARQL endpoints where clients simply send a SPARQL query to the endpoint and servers carry out all of the effort in query execution,
these alternative interfaces introduce different trade-offs between server and client effort.
For this reason, these interfaces can be compared to each other using the *Linked Data Fragments* axis as shown in [](#ldf-axis),
and these interfaces are hence often referred to as Linked Data Fragments (LDFs).

<figure id="ldf-axis">
<img src="img/ldf.svg" alt="Linked Data Fragments Axis">
<figcaption markdown="block">
The Linked Data Fragments axis shows the trade-offs between server-side and client-side effort for SPARQL query execution with different approaches.
</figcaption>
</figure>

Add URIF and VTPF to axis
{:.todo}

While each LDF interface comes with its own client-side algorithm to query full SPARQL queries over it,
federated querying over multiple instances of this LDF interface is only introduced for some.
[Most federated querying algorithms](cite:cites fedx,hibiscus,splendid) have been designed specifically for only SPARQL endpoints.
Furthermore, the problem of federated querying across *different* (heterogeneous) LDF interface types has only limited research attention.
In prior work, we introduced [Comunica](cite:cites comunica) with an algorithm to query over heterogeneous interfaces by decomposing the query up into triple patterns.
Similarly, a client was proposed to query [replicas of datasets via heterogeneous interfaces](cite:cites heterogeneous_replicas).
Following that, [FedQPL](cite:cites heterogeneous_fedqpl) was introduced as a formal language to represent logical query plans over heterogeneous federations.
Next, [a more intelligent query decomposition and query planning](cite:cites heterogeneous_lars) was introduced where more than just triple patterns can be assigned to federation members if the interface supports it.
More recently, [a study was done](cite:cites heterogeneous_sourceselectionfit) to analyze which of the source selection techniques for SPARQL endpoint federation are portable to federation over heterogeneous interfaces.
Then, [a cost model](cite:cites heterogeneous_costmodel) was introduced that captures resource requirements of query operators for each given federation member.

What is common among all these works, is that they make the assumption of prior knowledge as to what the expressivity of each federation member is.
For example, if one federation member is a [TPF server](cite:cites tpf), it is assumed to accept only triple pattern queries,
or if another federation member is a [brTPF server](cite:cites brtpf), it is assumed to accept triple pattern queries with optional `VALUES` bindings.
This means that heterogeneous federation engines must have prior knowledge as to which LDF type maps to which *language* or level of expressivity.
As a consequence, LDF interface types that are unknown and not part of this prior type-to-language mapping,
would not be queryable by this federation engine until support for it was added manually by adding new code.

The goal of this work is close this gap by introducing a new formal language called the *Fragment Selector Shapes Language* (FSSL)
to describe *Fragment Selector Shapes* (FSSs), which represent the expressiveness of LDF servers.
LDF servers can expose their expressiveness as an FSS,
which can be used by clients to determine how they should decompose their query,
without requiring prior knowledge of this LDF interface type.
In other words, the goal of FSSs is to enable client-side engines to be generic enough
to not require any specific adapter logic for specific LDF interface types,
but can decompose their query purely based on the server-provided FSSs.
While this is the first publication that talks about FSSs,
they have already been in use for several years within the Comunica query engine (since version 3.0.0, March 2024)
to enable interface-aware query decomposition.
As such, the approach has undergone several iterations based on implementation experience, practical usage, and performance analyses.
The goal of this article is to formalize the findings of this end result.

In the next section, we introduce a motivating example,
followed by the necessary preliminaries onto which FSSL will be based.
Next, we introduce FSSL formally,
after which we provide a query decomposition algorithm
that can be used during source selection over federations of heterogeneous interfaces.
Finally, we conclude with next steps for future work.
