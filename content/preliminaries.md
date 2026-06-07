## Preliminaries
{:#preliminaries}

Write me: RDF/SPARQL (reuse from my link traversal paper), FedQPL, heterogeneous_lars
{:.todo}

Hereafter, we summarize the semantics of [SPARQL in the context of RDF](cite:cites sparqlsemantics),
and include the relevant definitions and notations from [FedQPL](cite:cites heterogeneous_fedqpl) and [Heling et al](cite:cites heterogeneous_lars).

The infinite set of *RDF triples* is formalized as $$ \mathcal{T} = (\mathcal{I} \cup \mathcal{B}) \times \mathcal{I} \times (\mathcal{I} \cup \mathcal{B} \cup \mathcal{L}) $$,
where $$\mathcal{I}$$, $$\mathcal{B}$$, and $$\mathcal{L}$$ respectively denote the disjoint, infinite sets of *IRIs*, *blank nodes*, and *literals*.
Furthermore, $$\mathcal{V}$$ is the infinite set of all variables that is disjoint from $$\mathcal{I}$$, $$\mathcal{B}$$, and $$\mathcal{L}$$.
A tuple $$tp \in (\mathcal{V} \cup \mathcal{I}) \times (\mathcal{V} \cup \mathcal{I}) \times (\mathcal{V} \cup \mathcal{I} \cup \mathcal{L})$$ is called a *triple pattern*.
A finite set of these triple patterns is called a *basic graph pattern* (BGP).
For the formalization,
we only consider BGPs since they form the foundational building block of a SPARQL query;
our implementation incorporates all of SPARQL 1.1.
The query results of a SPARQL query $$P$$ over a set of RDF triples $$G$$ are called *solution mappings*,
which are denoted by $$[[P]]_G$$, consisting of partial mappings $$\mu : \mathcal{V} \rightarrow (\mathcal{I} \cup \mathcal{B}\cup \mathcal{L})$$.
An RDF triple $$t$$ *matches* a triple pattern $$tp$$ if $$\exists \mu : t = \mu[tp]$$, where $$\mu[tp]$$ is the triple pattern that is obtained by replacing all variables from $$\mu$$ in $$tp$$.

We reuse the definition of [Heling et al.](cite:cites heterogeneous_lars) that defines the language $$ L $$ of an LDF interface.
They define $$ L $$ as the universe of interface languages, where the interface language $$ l \in L $$ is the fragment of SPARQL expressions that an interface can evaluate.
We can denote $$ p \in l $$ if $$ p $$ is a SPARQL expression that is part of an interface language $$ l $$.
For example, the language $$ l_{SPARQL12} $$ of a SPARQL endpoint contains all possible [SPARQL 1.2 expressions](cite:cites spec:sparqlquery12),
and the language $$ l_{TP} $$ of a [TPF interface](cite:cites tpf) contains only triple patterns.

On top of that [Heling et al.](cite:cites heterogeneous_lars) defines
a Linked Data Fragment interface a 2-tuple $$ f = ( l_f , m_f ) $$,
where $$ l_f ∈ L $$ is the interface language,
and $$ m_f : p \rarr G $$ is the interface metadata for an expression $$ p $$.
Furthermore, we can distinguish LDF *interfaces* (which define the language and metadata)
from LDF *services* (which are Web servers that implement such an interface).
The function $$ int (c) = ( l_c , m_c ) $$ can be used to obtain the LDF interface from a given LDF service $$ c $$.

Finally, a *Federation of Linked Data Fragment services* is then defined as a 3-tuple $$ F = (C, int, ep) $$
where $$ C = {c_1, ... , c_n } \subset U $$ , a set of URIs for LDF services,
$$ int $$, a function that maps an LDF service to its interface,
and $$ ep $$, a function that maps each LDF service to the RDF dataset available at that service.

Using this notation, our motivating example can be expressed as $$ F_{ex} = ({c_1, c_2 }, int, ep) $$,
with $$ c_1 = \text{https://query.wikidata.org/sparql} $$,
$$ c_2 = \text{http://fragments.dbpedia.org/2016-04/en} $$,
$$ int(c_1) = (l_{SPARQL12}, m_{SPARQLEP}) $$,
$$ int(c_2) = (l_{TP}), m_{TPF}) $$,
$$ ep(c_1) = D_{Wikidata} $$,
and $$ ep(c_2) = D_{Dbpedia} $$.