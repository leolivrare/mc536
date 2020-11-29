## Tarefa de análises feitas no Cypher

## Exercício 1

Calcule o Pagerank do exemplo da Wikipedia em Cypher:

~~~cypher
//Load dos dados:
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:Page {name:line.source})
MERGE (p2:Page {name:line.target})
CREATE (p1)-[:LINKS]->(p2)
~~~
~~~cypher
Solução:
CALL gds.graph.create(
  'prGraph',
  'Page',
  'LINKS'
);

CALL gds.pageRank.stream('prGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC;
~~~

![PageRank](images/lab07_1.png)

## Exercício 2

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results:

~~~cypher
//Criando o grafo:
CALL gds.graph.create(
  'communityGraph',
  'Drug',
  {
    Relates: {
      orientation: 'UNDIRECTED',
      properties: 'weight'
    }
  }
)
~~~

~~~cypher
//Com pesos:
CALL gds.louvain.stream('communityGraph',  {relationshipWeightProperty: 'weight'})
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC;

MATCH (a:Drug) -[r:Relates]->(b:Drug)
RETURN a.name as source, b.name as target;
~~~

![Comunidade](images/lab07_2.png)

~~~cypher
//Sem pesos:
CALL gds.louvain.stream('communitDrugGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).code AS code, communityId
ORDER BY communityId ASC
~~~