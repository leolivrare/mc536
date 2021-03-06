## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank: "DB04817", name:"Metamizole"})
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (d1:Drug {name:"Metamizole"})
MATCH (d2:Drug {name:"Dipyrone"})
CREATE (d1)-[:SameAs]->(d2)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (:Drug {name:"Metamizole"})-[sa1:SameAs]->(:Drug {name:"Dipyrone"})
DELETE sa1
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (p1)<-[t:Treats]->(p2)
ON CREATE SET t.weight=1
ON MATCH SET t.weight=t.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
CREATE (:sideEffect {idPerson:line.idPerson, codePathology:line.` codePathology`});
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
CREATE (:drugUse {drugCode:line.codedrug, codePathology:line.codepathology,idPerson:line.idperson});
MATCH (p:Pathology)
MATCH (s:sideEffect)
MATCH (u:drugUse)
MATCH (d:Drug)
WHERE u.idPerson=s.idPerson AND d.code=u.drugCode AND s.codePathology=p.code
MERGE (d)-[h:hasSideEffect]->(p)
ON CREATE SET h.weight=1
ON MATCH SET h.weight=h.weight+1
MATCH (d:Drug)-[h:hasSideEffect]->(p:Pathology)
WHERE h.weight>30
RETURN p,d
~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

### Resolução
Podemos ver qual o medicamento que mais tem efeitos colaterais associados a fim de identificar qual o mais periculoso.
~~~cypher
MATCH (d:Drug)-[h:hasSideEffect]->(:Pathology)
RETURN d, count(h) as edges
ORDER BY edges desc
LIMIT 1
~~~
