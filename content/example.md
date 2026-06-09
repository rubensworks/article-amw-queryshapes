## Motivating Example
{:#example}

Hereafter, we introduce a motivating example, which we reuse from [Heling et al](cite:cites heterogeneous_lars).
Concretely, the federated SPARQL query in [](#motivating-example) retrieves *all American presidents with their political party, including the predecessors and successors*.

<figure id="motivating-example" class="listing">
````/code/motivating-example.txt````
<figcaption markdown="block">
A federated SPARQL query over Wikidata and DBpedia to retrieve all American presidents with their political party, including the predecessors and successors.
</figcaption>
</figure>

In this example, we assume that we want to execute this SPARQL query over Wikidata and DBpedia,
for which we want to use the public [Wikidata SPARQL endpoint](https://query.wikidata.org/sparql){:.mandatory} and the public [DBpedia Triple Pattern Fragments interface](http://fragments.dbpedia.org/2016-04/en){:.mandatory}.
While the Wikidata SPARQL endpoint accepts full SPARQL queries,
the DBpedia Triple Pattern Fragments interface only accepts triple pattern queries.
As such, the query is to be decomposed in an interface-aware manner.
In this example, Wikidata produces results for $$ tp1 $$, $$ tp2 $$, and $$ tp3 $$,
while DBpedia produces results for $$ tp3 $$, $$ tp4 $$, and $$ tp5 $$.

Interface-agnostic approaches such as [FedX](cite:cites fedx) would decompose this query
in such a way that $$ tp1 $$ and $$ tp2 $$ are sent exclusively to Wikidata,
$$ tp4 $$ and $$ tp5 $$ exclusively to DBpedia,
and $$ tp3 $$ is sent to both Wikidata and DBpedia before being unioned.
The result of this source selection phase can be seen in [](#motivating-example-service)

<figure id="motivating-example-service" class="listing">
````/code/motivating-example-service.txt````
<figcaption markdown="block">
The federated query from [](#motivating-example) after query decomposition with FedX.
</figcaption>
</figure>

Due to FedX being interface-agnostic, the query in [](#motivating-example-service) can not be executed.
Concretely, the problem is that a join of two triple patterns is being sent to the DBpedia Triple Pattern Fragments interface,
while this interface can only accept triple pattern queries.
As such, this query should be decomposed in a slightly different manner,
where $$ tp4 $$ and $$ tp5 $$ are sent separately to DBpedia,
and their join is executed within the federation engine.
This is exactly what approaches such as the one from [Heling et al.](cite:cites heterogeneous_lars) can achieve,
as shown in [](#motivating-example-service-correct).
While existing query decomposition approaches assume prior knowledge of the language of each interface,
our contribution focuses on query decomposition based on generic *Fragment Selector Shapes* (FSSs) that are announced by servers.

<figure id="motivating-example-service-correct" class="listing">
````/code/motivating-example-service-correct.txt````
<figcaption markdown="block">
The federated query from [](#motivating-example) after interface-aware query decomposition with the approach from [Heling et al](cite:cites heterogeneous_lars).
</figcaption>
</figure>
