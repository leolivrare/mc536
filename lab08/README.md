# Modelo para Apresentação do Lab08 - PubChem e DRON com XQuery/XPath

Estrutura de pastas:

~~~
├── README.md  <- arquivo apresentando a tarefa
~~~

## Tarefas com Publicações

## Questão 1
Construa uma comando SELECT que retorne dados equivalentes a este XPath
~~~xquery
//individuo[idade>20]/@nome
~~~

### Resolução
~~~xquery
select
    nome
from fichario as f
inner join
    individuo on i as i.nome = f.chave
where
    i.idade > 20
~~~

## Questão 2
Qual a outra maneira de escrever esta query sem o where?

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~
### Resolução
~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo[idade>17])
return {data($i/@nome)}
~~~

## Questão 3
Escreva uma consulta SQL equivalente ao XQuery:
~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~

### Resolução
~~~sql
select
    i.nome
from fichario as f
inner join
    individuo as i on i.nome = f.chave
where i.idade > 17
~~~

## Questão 4
Retorne quantas publicações são posteriores ao ano de 2011.

### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")

return count($info/publications/publication[year>2011])
~~~

## Questão 5
Retorne a categoria cujo `<label>` em inglês seja 'e-Science Domain'.

### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")

return $info/publications/categories/category[label/@lang="en-US" and label="e-Science Domain"]
~~~

## Questão 6
Retorne as publicações associadas à categoria cujo `<label>` em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml")

for $cat in $info/publications/categories/category[label/@lang="en-US" and label="e-Science Domain"],
    $pub in $info/publications/publication
where
    $pub/key = $cat/@key
return $pub
~~~

## Tarefas com DRON e PubChem

## Questão 1

Liste o nome de todas as classificações que estão apenas dois níveis imediatamente abaixo da raiz.

### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in $info/drug/drug/drug
return {data($i/@name)}
~~~

## Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de `Acetylsalicylic Acid`) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in
    $info//drug[drug/drug/@name="HYDROXYANTHRAQUINONES"]
let $n := $i/@name
group by $n
order by $n
return {data($n),'&#xa;'}
~~~

## Questão 3

### Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

#### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i in
    $info//drug[substring(@id,1,36)="http://purl.obolibrary.org/obo/CHEBI"]
let $id := substring($i/@id,38)
group by $id
order by $id
return {$id ,'&#xa;'}
~~~

### Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

#### Resolução
~~~xquery
let $info := doc("https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml")

for $i
    in $info//drug[substring(@id,1,36)="http://purl.obolibrary.org/obo/CHEBI"]
let $i_id := substring($i/@id,38)
let $i_name := $i/@name
group by $i_id,$i_name
order by $i_id,$i_name
return {$i_id, $i_name ,'&#xa;'}
~~~

### Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

#### Resolução
~~~xquery
(escreva aqui a resolução em XQuery)
~~~