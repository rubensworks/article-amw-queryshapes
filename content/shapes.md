## Fragment Selector Shapes
{:#shapes}

In this section, we introduce a Fragment Selector Shape (FSS) as a nested expression to represent the language $$ l $$ of an LDF interface,
after which we define how an FSS is matched to a SPARQL query.
Afterwards, we provide several examples of instantiated FSSs for well known LDF languages.

### Grammar

These expressions build upon references to [algebraic SPARQL 1.2 query expressions](spec:sparqlquery12),
for which we consider $$ e $$ as an arbitrary query expression with the set of all query expressions $$ E $$.
Concretely an FSS ($$ fss $$) is defined by the following grammar:

$$
\begin{array}{ll}
fss  & = & fsse \ | \ (fss \land fss) \ | \ (fss \lor fss) \ | \ \neg (fss) \ | \ | \ fssa \ | \ \epsilon \\
fsse & = & \langle e, A_{scope}, fss \rangle_e \\
fssa & = & \langle fss, \mathbb{N}, \mathbb{N} \rangle_a
\end{array}
$$

Within this grammar,
$$ \epsilon $$ refers to a wildcard shape that matches with all possible query expressions.

Next, $$ fsse $$ refers to a shape that binds to a specific query expression,
considers the arguments $$ A_{scope} \subseteq ( \mathcal{V} \cup \mathcal{I} \cup \mathcal{B} \cup \mathcal{L} ) $$ in scope for this operation,
together with zero or more children.
In the notation of $$ e $$, query expressions can be written directly with their name (e.g. $$ tp $$ for a triple pattern),
or they can list their arguments (e.g. $$ tp(s, p, o) $$ for a triple pattern).
If such an argument is annotated with a star (e.g. $$ s^* $$),
this means that only concrete RDF terms ($$ \mathcal{I} \cup \mathcal{B} \cup \mathcal{L} $$) can be filled into this position.
We allow writing $$ \langle e, \emptyset, \epsilon \rangle_e $$ through the shorthand $$ \langle e \rangle_e $$ 

Finally, $$ fssa $$ refers to a shape that can have a certain arity,
where the first natural number refers to the minimum required number of occurrences (inclusive),
and the second natural number refers to the maximum allowed number of occurrences (exclusive).

### Query Matching

Hereafter, we recursively define the matching of a SPARQL query $$ q $$ against an FSS $$ fss $$ as $$[[q]]^{match}_{fss}$$.
The evaluation of this a match is important at different places within query optimization, and will be used within [](##querydecomposition).

1. If $$ fss $$ is $$ \epsilon $$, then $$ [[q]]^{match}_{fss} = true $$.
2. If $$ fss $$ is $$ fss_1 \land fss_2 $$, then $$ [[q]]^{match}_{fss} = [[q]]^{match}_{fss_1} \land [[q]]^{match}_{fss_2} $$.
3. If $$ fss $$ is $$ fss_1 \lor fss_2 $$, then $$ [[q]]^{match}_{fss} = [[q]]^{match}_{fss_1} \lor [[q]]^{match}_{fss_2} $$.
4. If $$ fss $$ is $$ \neg fss_1 $$, then $$ [[q]]^{match}_{fss} = \neg [[q]]^{match}_{fss_1} $$.
5. If $$ fss $$ is $$ \langle e, A_{scope}, \emptyset \rangle_e $$ and $$ q $$ is $$ e $$, then $$ [[q]]^{match}_{fss} = true $$.
6. If $$ fss $$ is $$ \langle e, A_{scope}, fss_c^* \rangle_e $$ and $$ q $$ is $$ e $$, then $$ [[q]]^{match}_{fss} = \bigwedge_{e_c \in_{\text{direct child expression}} e} [[e_c]]^{match}_{fss_c} $$.
7. If $$ fss $$ is $$ \langle fss_c, l, u \rangle_a $$ and $$ q $$ is has the direct child expressions $$ e_1, e_2, \ldots, e_n $$, then $$ [[q]]^{match}_{fss} = l < n \land n \leq u \land [[e_1]]^{match}_{fss_c} \land [[e_2]]^{match}_{fss_c} \land \ldots \land [[e_n]]^{match}_{fss_c} $$.

Within this definition, we informally consider the arguments in $$ A_{scope} $$ to propagate down into its children.
This means that if an argument $$ a $$ occurs at any point within an $$ fss $$, and it is matched to a certain RDF term $$ t $$,
then all of the direct or indirect children of $$ fss $$ that contain this argument $$ a $$ must match to this RDF term $$ t $$ within the query expression that is considered at this level.

### LDF Language Examples

The simplest example of a FSS is for the language $$ l_{SPARQL12} $$ corresponding to a SPARQL 1.2 endpoint;
$$ fss_{SPARQLEP} = \epsilon $$.
[SaGe](cite:cites sage) also shares this language, as it can accept any SPARQL query, and is only limited by execution time.

Another straighforward example is an FSS for the language $$ l_{TP} $$ corresponding to a [Triple Pattern Fragments (TPF) interface](cite:cites tpf);
$$ fss_{TPF} = \langle tp \rangle_e $$, with $$ tp $$ referring to the algebraic triple pattern expression.

As an extension, the FSS for the [Quad Pattern Fragments interface](cite:cites spec:qpf) (an extension of (TPF) with named graph support)
can be expressed as $$ fss_{QPF} = fss_{TPF} \ | \ \langle graph, \emptyset, fss_{TPF} \rangle_e $$, with $$ graph $$ referring to the algebraic graph query expression.

Similar to TPF, the FSS for the [conceptual URI-lookup interface](cite:cites formalldf)
where triples can be looked up by only specifying a given subject
can be expressed as $$ fss_{URIF} = \langle tp(s^*, \_, \_), \emptyset, \emptyset \rangle_e $$.

[brTPF](cite:cites brtpf) is another extension to TPF that allows bindings to be pushed into triple patterns.
As such, we can express the FSS for brTPF as $$ fss_{brTPF} = fss_{TPF} \ | \ \langle values, \emptyset, fss_{TPF} \rangle_e $$, with $$ values $$ referring to the algebraic values query expression, which allows bindings to be passed.

Unlike TPF, [Star Pattern Fragments (SPF)](cite:cites spf) supports not only triple pattern queries,
but it supports multiple triple patterns (at least one) in a star shape,
which means that triple patterns must share the same subject.
As such, we can express the FSS for SPF as $$ fss_{SPF} = \langle bgp, \{ s \}, \langle tp(s, p, o), 1, \infty \rangle_a \rangle_e $$.

Smart-KG is an LDF approach that builds upon brTPF,
but also allows multiple triple patterns to be sent at once.
Unlike SPF, SmartKG does not requires star patterns,
but it does not accept variables in the predicate position.
Hence, we can express the FSS for smart-KG as $$ fss_{SMARTKG} = fss_{brTPF} \ | \ \langle bgp, \emptyset, \langle tp(s, p^*, o), 1, \infty \rangle_a \rangle_e $$.

WiseKG, being a combination of both SPF and smart-KG, can be expressed as $$ fss_{WISEKG} = fss_{SPF} \ \mid \ fss_{SMARTKG} $$

[Passage](cite:cites passage) is an LDF approach supports what they call *Core SPARQL*,
which includes triple patterns, BGPs, joins, unions, optionals, filters, offsets, and binds.
Hence, we can express the FSS for Passage as
$$ fss_{PASSAGE} = \langle tp \rangle_e \lor \langle bgp \rangle_e \lor \langle join \rangle_e \lor \langle union \rangle_e \lor \langle optional \rangle_e \lor \langle filter \rangle_e \lor \langle slice \rangle_e \lor \langle bind \rangle_e $$.

Finally, [Versioned Triple Pattern Fragments (VTPF)](cite:cites vtpf) is an extension to TPF
that allows triple patterns to be queried in the context of [RDF archives](cite:cites ostrich) across [3 versioned queries types](cite:cites rdfarchives):
$$ mat(q, v) $$ to execute a query expression $$ q $$ at a given version $$ v $$,
$$ diff(q, v_1, v_2) $$ to return the difference in results for the query $$ q $$ between versions $$ v_1 $$ and $$ v_2 $$,
and $$ ver(q) $$ to execute a query $$ q $$ and annotate all results with the versions in which they apply.
For this, we express the FSS for VTPF as $$ fss_{VTPF} = \langle mat, \emptyset, fss_{TPF} \rangle_e \lor \langle diff, \emptyset, fss_{TPF} \rangle_e \lor \langle ver, \emptyset, fss_{TPF} \rangle_e $$.