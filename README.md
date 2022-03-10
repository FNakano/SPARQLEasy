# SPARQLEasy
**SPARQL easy SELECT**

Proposta para TCC curto.

## Motivação

Nesta proposta, gostaria de usar como afirmação inicial que: "Dados, geralmente, estão armazenados em bases de conhecimento como bancos de dados e grafos de conhecimento".

O interesse do público em geral em explorar dados é cada vez maior.
 
Explorar dados com a maior liberdade possível é atingível com o uso de linguagens de consulta.

Escrever consultas requer conhecer a linguagem de consulta e a estrutura da base de conhecimento. A aprendizagem necessária para ter razoável domínio desse conhecimento desestimula pessoas com formação geral a investir esforço nisso, o que torna esse conhecimento restrito a pessoas com alguma formação em TI. Consequentemente, restringindo a quantidade de pessoas que poderiam analisar dados.

DBMS como MS-Access têm ferramentas gráficas para construir consultas SQL. (OUTROS DBMS TÊM??) Curiosamente, a existência dessa ferramenta parece não alavancar o uso do DBMS por pessoas com formação geral.

É comum que a infraestrutura para servidores web contenha um DBMS, mas, de meu conhecimento, não há site que disponibilize uma interface de consulta SQL. É mais comum que as consultas, ou modelos das consultas, estejam pré-definidas e sejam executadas pelo usuário. Por exemplo, quando busca-se alguma mercadoria em um site de compras.

Em DBMS clássicos, a estrutura de tabelas de um particular DB pode ser diferente da de outro DB. Esta estrutura está armazenada em tabelas de sistema, geralmente inacessíveis por usuários sem privilégios de administração. Por outro lado, para o usuário geral que deseja explorar os dados, para essa exploração ser possível, ele precisa conhecer, ou consultar, a estrutura do DB que ele deseja explorar.

Em grafos de conhecimento, o bloco básico é constituído por triplas, geralmente interpretadas como Sujeito, Predicado, Objeto. Baseado nessas triplas, bases de conhecimento podem ser armazenadas com as mesmas funcionalidades oferecidas por DBMS clássicos.

Conjuntos de triplas podem ser representados por grafos, onde, para cada tripla, sujeito e objeto correspondem a nós e predicados correspondem a arestas.

A título de ilustração da equvalência entre tabelas e conjuntos de triplas, considere a tabela:

| ID | descrição | preço |
| --- | --- | --- |
| 001 | ventilador marca X, potência Y,... | 199,00 |
| 002 | geladeira marca W,... | 1299,00 |
| 003 | televisor marca Z, tamanho K,... | 1999,00 |
| ... | ... | ... |

A informação nela contida pode ser codificada em um grafo se seguirmos algumas regras:

1. O cabeçalho de cada coluna corresponde a uma classe;
   - No exemplo existem três classes: ID, descrição e preço.
2. Cada célula corresponde a uma instância da respectiva classe, designada pela coluna;
3. As células que estão na mesma linha são associadas por predicados;
4. Cada instância de ID é um identificador único. Por isso, é conveniente que seja a origem de todas as associações de que participa;
   - por 3 e 4, no exemplo, convém que haja dois predicados: `temDescrição` e `temPreço`

As triplas resultantes da aplicação das regras acima sobre o exemplo são:

```
001 `temDescrição` "ventilador marca X, potência Y,..."
001 `temPreço` "199,00"
002 `temDescrição` "geladeira marca W,..."
002 `temPreço` "1299,00"
003 `temDescrição` "televisor marca Z, tamanho K,..."
004 `temPreço` "1999,00"
...
```


O grafo com as classes e instâncias elicitadas é:

**colocar diagrama**

Uma exemplo simplificado de consulta SPARQL que retorna todas as linhas da tabela seria:

```
curl -F "query=
select ?id ?desc ?prec where {
?id temDescrição ?desc.
?id temPreço ?prec.
}
```

## Requisitos conhecidos

Uma consulta SPARQL SELECT, com mais elementos sintáticos, dentro de uma requisição HTTP executada com o aplicativo `curl`, sobre um grafo criado pelos orientadores é mostrada abaixo:

```
curl -F "query=
prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
prefix xsd: <http://www.w3.org/2001/XMLSchema#> 
prefix sosa: <http://www.w3.org/ns/sosa/>
select ?s ?o ?t where {
?s sosa:observedProperty ?o2.
?s sosa:hasSimpleResult ?o.
?s sosa:resultTime ?t.
FILTER ( ?t >= '2022-02-25T0:00Z'^^xsd:dateTime &&  ?t <= '2022-02-25T8:00Z'^^xsd:dateTime )
} order by desc(?t)" http://ip-50-62-81-50.ip.secureserver.net:8890/sparql -H "Accept: text/csv" -o output.csv
```

A partir desta consulta, é possível generalizar que uma consulta tem um bloco que define prefixos, seguido de SELECT, seguido de uma lista de variáveis desejadas, seguido de WHERE, seguido de triplas que especificam as variáveis desejadas, seguido (ou não) de FILTER, seguido de uma especificação sobre os valores das variáveis desejadas que são de interesse, seguido por especificações de ordem de apresentação e formato de serialização.

O principal entregável do projeto consiste em uma aplicação (GUI), usável por pessoas com pouco ou nenhum conhecimento sobre DBs, SQL, SPARQL, que auxilia a construção de consultas SPARQL SELECT, com a capacidade de exibir algum diagrama ou lista como resultado.

A aplicação deve permitir ao usuário:

- definir quantos prefixos quiser, 
- quantas variáveis de resultado quiser, 
- quantas triplas de seleção (WHERE) quiser, 
- quantas condições (LIMIT) quiser. 
- Escolher entre ordens de apresentação e formatos de serialização possíveis/disponíveis.
- salvar e carregar consultas feitas anteriormente
   - formato de armazenamento: texto/SPARQL
   - o usuário pode querer que a aplicação carregue uma consulta mal formada ou com elementos que (ainda) não são tratados
    
A aplicação pode ser ou *standalone* com interface gráfica (por exemplo, Python+TkInter), ou *web* com interface através de navegador (por exemplo, Node+React).

A aplicação deve ser expansível por novos desenvolvedores - permitir incluir mais elementos do tipo WHERE, FILTER,... ter documentados os pontos do código que precisariam ser ajustados.

## Sugestão de projeto da GUI

**Acrescentar diagrama**

## Teste de aceitação da aplicação

A aplicação será usada para gerar consultas contra o grafo de conhecimento mantido pelos orientadores. Estas consultas serão executadas e os resultados analisados. Se os resultados forem satisfatórios, considera-se que a aplicação passou no teste.

## Entregáveis

Além do teste de aceitação, deverão ser entregues:

1. Programa (GUI) da aplicação;
   - Código-fonte e executável em repositório público no github
2. Tutorial com exemplos para usuários com instruções de instalação e uso da aplicação;
3. Manual do desenvolvedor;
   - Referência de bibliotecas e funções usadas;
      - ex. usou-se o módulo csv2rdf a biblioteca Python RDFLib (https://rdflib.readthedocs.io/en/stable/apidocs/rdflib.tools.html#module-rdflib.tools.csv2rdf)
      - ex. a postagem: https://stackoverflow.com/questions/17144088/using-python-rdflib-how-to-include-literals-in-sparql-queries ensina como criar literais com etiqueta de linguagem usando a função `Literal`
   - Referência de funções desenvolvidas no TCC;
   - Instalação do ambiente de desenvolvimento;
   - Arquitetura da aplicação;
   - Fluxograma (ou equivalente) da aplicação;
4. Monografia
   - Documento mais acadêmico, contextualizando a aplicação e a necessidade de tal, com referencial científico

## Linguagens de desenvolvimento preferenciais

Alternativas podem ser aceitas. O principal critério para aceitação será a facilidade para colegas darem continuidade ao projeto.

Python com TkInter;
Python com Flask ou com Django;
JavaScript/TypeScript com Angular ou React ou Node;

## Experiência prévia que pode facilitar o desenvolvimento

- Experiência com web semântica e SPARQL;
- Experiência com bancos de dados e SQL;
- Experiência com desenvolvimento de GUI;
