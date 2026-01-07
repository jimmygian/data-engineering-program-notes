
### 2.1 - Introduction to Cypher 

Cypher is a declarative query language designed for expressing queries across graph databases. It provides a concise and intuitive syntax for performing operations such as creating, updating, retrieving, and deleting data within graph structures. It reuses syntax from SQL and mixes it with ASCII elements to represent graph elements. 

OpenCypher, on the other hand, is an open standard for graph query languages inspired by Cypher. It aims to standardize the Cypher query language across different graph database implementations. 

Alongside Cypher, Gremlin and SPARQL are the most popular Graph Query Languages.


#### Nodes

A node is used to capture a data item, usually an entity, like a customer, an order, a product, etc.

- `()`: This represents a *node*. You did not specify a specific type of node or any properties of that node. It's not relevant to the query.

- `(n)`: This represents a *node* referred to by the variable **n**. You can refer to this variable in other parts of your query.

- `(n:Airport)`: Nodes can have different types (i.e. they can belong to different classes/categories). You can add a *label* to your node to specify its type. Here you assign nodes with the type **Airport** to the variable **n**.

- `(n:Airport {code: 'BOS', desc: 'Boston Logan'})`: A node can have properties, which you can specify with `{}`. Here you are assigning to the variable **n** the nodes of type **Airport** that have specific values for the **code** and **desc** properties.

- `n.code`: You can access a specific property using this syntax, in this case, the **code** from the node denoted by **n**.


#### Relationships / Edges

A relationship or edge is used to describe a **connection** between two nodes.

- `[r]`: This represents a *relationship* referred to by the variable **r**. You can refer to this variable in other parts of your query.

- `[r:Route]`: Relationships can have different types (i.e. they can belong to different classes/categories). You can add a *label* to your *relationship* to specify its type. Here you are assigning the relationships with the type **Route** to the variable **r**.

- `[:Route]`: A *relationship* with the *label* **Route** not referred to by any variable.

- `[r:Route {dist:809}]`: Relationships can have properties, which you can specify with `{}`. Here you are assigning to the variable **r** the *relationships* of type **Route** that have specific values for the **dist** property.

- `[r:Route*..4]`: This syntax is used to match a pattern where the relationship **r** with the label **route** can be repeated between 1 to 4 times. In other words, it matches paths where the **route** relationship occurs consecutively at least once and at most four times.


#### Paths

A path is used to capture the graph structure.

- `(a:Airport)-[:Route]-(b:Airport)`: This represents a *path* that describes that node **a** and node **b** are connected by a **Route** relationship.

- `(a:Airport)-[:Route]->(b:Airport)`: A path can be directed. In this case, this represents a path that describes a directed relationship from node **a** to node **b**, but not the other way around.

- `(a:Airport)<-[:Route]-(b:Airport)`: A path that describes a directed relationship from node **b** to node **a**, but not the other way around.

- `(a:Airport)-[:Route]-(b:Airport)-[:Route]-(c:Airport)`: A path can chain multiple relationships and any of them can be directional.


The **variables** will appear by naming parts of the patterns or a query to reference them. You will see the examples below.


#### Pattern Matching Syntax

In the following table you can find the characters that represent each component in the Cypher language:

| Cypher Pattern|Description|

| `( )`      | A node                                                                              |
| ---------- | ----------------------------------------------------------------------------------- |
| `[ ]`      | An edge                                                                             |
| `-->`      | Follow outgoing edges from a node                                                   |
| `<--`      | Follow incoming edges to a node                                                     |
| `--`       | Follow edges in either direction                                                    |
| `-[]->`    | Include the outgoing edges in the query (for example, to check a label or property) |
| `<-[]-`    | Include the incoming edges in the query (for example, to check a label or property) |
| `-[]-`     | Include edges in either direction in the query                                      |
| `-[]->( )` | The node on the other end of an outgoing edge                                       |
| `<-[]-()`  | The node on the other end of an incoming edge                                       |

To find more information about Cypher/OpenCypher, you can visit these resources:

- [Cypher Manual](https://neo4j.com/docs/cypher-manual/current/introduction/)
- [LearnXinYminutes Cypher](https://learnxinyminutes.com/docs/cypher/)


## Simple Queries

#### MATCH 
`MATCH` statements in Cypher are used to retrieve data from the graph by specifying patterns of nodes and relationships. These patterns define the structure of the data you want to retrieve or manipulate. The `MATCH` statement is used to specify patterns of nodes and relationships to match in the graph. It is the primary way to retrieve data from the graph. The `RETURN` keyword is used to specify what data to include in the query result. It specifies the properties of nodes and relationships to return, as well as any computed values. The `LIMIT` keyword limits the number of returned values. Run each of the following cells to better understand how `MATCH` statements work.

**Match all nodes**: Let's get some nodes, limited only to 20 values. Note that you are using `(n)` to get a *node* referred to the variable **n**, as mentioned in an earlier section

```Cypher
MATCH (n) RETURN n LIMIT 20
```



**Get node labels and count the number of nodes per label**. This involves using the `labels()` function, which retrieves the labels for a given node `n`, and the `count(*)` function, which counts the number of nodes. Use the `DISTINCT` keyword straight after the `RETURN` keyword to get only the unique labels.

```Cypher
MATCH (n) RETURN DISTINCT labels(n), count(n)
```


## Relationships

Now explore the **relationships between the nodes**. 

```Cypher
MATCH (n)-[r:Route]-() RETURN n, r LIMIT 10
```

Remember that relationships are defined by using square brackets such as `[r:route]`. In this case, you will see that you are searching for all nodes `()` that are related. To find all the relationships that are directed, you can use the syntax provided above in which by using `->` you can indicate a directed relationship.

You can count the relationships of a dataset:

```Cypher
MATCH ()-[r]->() RETURN count(r) AS relationships_count
```


**Match nodes according to relationships (undirected)**: You are going to search the paths that connect two airports. In this case, there is no direction in the relationship as you can travel from one airport to another and vice versa. This is indicated by using a simple hyphen (`-`) between the nodes and the relationship.
```Cypher
MATCH (a:Airport)-[:Route]-(b:Airport)
RETURN a,b
LIMIT 10
```


**Match nodes according to relationships (directed)**: now you're going to search the paths that represent directed relationships. In your dataset, Countries and Continents contain Airports, which indicates a directed relationship as the inverse relationship is not true (for example, Airports do not contain Continents). For that, you will use the syntax `()-[r:Contains]->()` where the `->` indicates the direction of the relation and `Contains` is the label of the relationship. When returning a path, you assign the entire pattern to a variable, as seen below where you'll assign the path to a variable `p`.

```Cypher
MATCH p=()-[r:Contains]->() RETURN p LIMIT 10
```


To check the properties of a node:
```Cypher
MATCH (a:Airport) RETURN properties(a) LIMIT 1
```


**Match nodes by label and property**: You can use the properties to filter your results and extract only the nodes with a particular value of a certain property.

```Cypher
MATCH (a:Airport {code : 'BOS'}) RETURN a
```


**Match all relationships and aggregate a property**: Properties of relationships can be used to perform aggregations. For example, let's get the average distance of all routes.

```Cypher
MATCH ()-[r:Route]->() RETURN avg(r.dist)
```


## `WHERE`- Filtering Statements

**Match nodes with a `WHERE` clause**: The `WHERE` statement is used to filter the results based on conditions. It allows you to specify conditions that nodes, relationships, or properties must meet to be included in the result. The syntax looks like

```cypher
MATCH <PATTERN>
WHERE <NODE-OR-RELATION>.<PROPERTY> = <VALUE>
RETURN <PATTERN>
```

e.g.

```Cypher
MATCH (a:Airport)-[r:Route]->(b:Airport) WHERE r.dist > 1000 RETURN a LIMIT 5
```


## Create Statements

Let's perform some **creation operations**. The `CREATE` statement is used to create new nodes and relationships in the graph. It allows you to specify the structure and properties of the new elements. To create a new node with some properties and return the node you can use a syntax like:

```cypher
CREATE (<VARIABLE>:<LABEL> {<PROPERTY> : <VALUE>, ..., <PROPERTY> : <VALUE>})
RETURN <VARIABLE>
```


e.g.
```Cypher
CREATE (a:Airport {country : 'US', longest : 1008, code: 'CLR', city: 'Calipatria', lon: -115.521389, type: 'airport', elev: -182, icao: 'KCLR', id: 3800, region: 'US-CA', runaways: 1, lat: 33.131389, desc: 'Cliff Hatfield Memorial Airport' }) RETURN a
```

This creates an `Airport` node with the following properties:
  * Country: "US"
  * Longest: 1008
  * Code: "CLR"
  * City: "Calipatria"
  * Longitud: -115.521389
  * Type: "airport"
  * Elevation: -182
  * ICAO Code: "KCLR"
  * ID: 3800
  * Region: "US-CA"
  * Runaways: 1
  * Latitude: 33.131389
  * Description: "Cliff Hatfield Memorial Airport"


To create a new relationship between the nodes, you can use `MATCH` statement to search the nodes you want to join according to a property and then the `CREATE` statement to introduce the relationship, such as:

```cypher
MATCH (<NODE1>:<TYPE> {<PROPERTY>: <VALUE>}), (<NODE2>:<TYPE> {<PROPERTY>: <VALUE2>})
CREATE (<NODE1>)-[:<RELATIONSHIP> {<PROPERTY>: <VALUE>}]->(<NODE2>)
```

Take into account that at creation time, relationships between nodes must be directed in Cypher. Despite that, you can always query it as an undirected relationship. 

e.g.
```Cypher
MATCH (a:Airport {code: 'CLR'}), (b:Airport {code: 'BWC'}) CREATE (a)-[r:Route {dist: 12}]->(b) RETURN r
```

Output:
```Shell
[
  {
    "r": [
      {},
      "Route",
      {}
    ]
  }
]
```


## Update Statements

You can use `MATCH`, `WHERE` and `SET` statements to update node properties, using a syntax like:

```cypher
MATCH (<NODE>:<TYPE>)
WHERE <NODE>.<CONDITIONAL-PROPERTY> = <CONDITIONAL-VALUE>
SET <NODE>.<PROPERTY-TO-UPDATE> = <NEW-VALUE>
```

With `MATCH` and `WHERE` you will select and filter a node according to a particular condition, while the `SET` statement is used to update the values. 

## Delete Statements

```Cypher
MATCH (a:Airport)-[r]-() WHERE a.code = 'CLR' DELETE a, r
```



## Advanced Queries

In the previous section, you have learned the basic syntax for the CRUD operations in Cypher. In the following exercises, you will create some more advanced queries to search for complex paths and patterns in your data.

In the next exercise, you will use **WITH** clause which allows you to chain query parts together, carrying over variables or introducing new ones for further operations. As it has been noted above, variables do not get passed to the subsequent parts of the query, unless they are included in the WITH clause. This feature is particularly useful for performing aggregations, filtering results, or managing complex queries by breaking them down into simpler, manageable parts.

```Cypher
MATCH (a:Airport)-[r:Route]->(b:Airport) WITH a, count(r) AS count_routes WHERE count_routes=1 and a.country = 'US' RETURN a LIMIT 10
```


Find the possible routes that use *only one intermediary airport* starting from Columbia Regional Airport (`'COU'`) and ending in Miami International Airport (`'MIA'`). Remember the syntax to define how many relationships are needed in a path.

The syntax `[:Route*..2]` is used to capture paths with up to 2 relationships, meaning direct flights and those with one intermediary airport.


#### reduce()
This statement returns all paths consisting of exactly 3 Route relationships from the airport with code 'SSA' to the airport with code 'ATH', where the total distance of these routes is less than 5800.

```Cypher
MATCH paths=(origin:Airport {code: 'SSA'})-[:Route*3..3]->(dest:Airport {code: 'ATH'})
WHERE reduce(totalDist = 0, route in relationships(paths) | totalDist + route.dist) < 5800
RETURN paths
```

This is using the function `reduce()`. To understand how reduce works see the following example:

```Cypher
WITH [1, 2, 3, 4, 5] AS numbers
RETURN reduce(total = 0, n IN numbers | total + n) AS sum
```


#### shortestPath()

Finally, using the [shortestPath()](https://neo4j.com/docs/cypher-manual/current/appendix/tutorials/shortestpath-planning/)  function, you can find the shortest path between the airport Guillermo León Valencia (`'PPN'`) in Colombia and Newman Airport (`'ZNE'`) in Australia.

```Cypher
MATCH p=shortestPath((origin:Airport {code: 'PPN'})-[:Route*1..20]->(dest:Airport{code: 'ZNE'})) RETURN p
```

This query will return the shortest path assuming all routes are the same, but the air routes graph does contain the distance for each route as a property. So you can refer to the air routes graph as a weighted graph, where each edge (i.e. route) between nodes has a weight which is denoted by the distance of that route. To perform a more accurate search in a weighted graph you should use an algorithm such as the Dijkstra Shortest Path Algorithm. This is one of the procedures that has already been implemented in Neo4j and in most Graph Databases. This algorithm is out of the scope of this lab, but if you are interested, you can read about it [here](https://neo4j.com/docs/graph-data-science/current/algorithms/dijkstra-single-source/).


## Vector Search

Vector search is a powerful technique in information retrieval, it relies on data representations as vectors in a high-dimensional space. These vectors capture the semantic relationships and similarities between the data points. By leveraging embeddings, which are dense vector representations learned from data, vector search enables efficient and accurate retrieval of relevant information. 

**Neo4j** can integrate vector search capabilities by storing embeddings as properties of graph nodes or relationships. This integration empowers Neo4j to perform advanced similarity searches, recommendation systems, and other Machine Learning tasks within the context of graph data structures, enhancing its utility in various domains such as recommendation engines, fraud detection, and knowledge graphs.


### Links to Data and Cypher Instructions

The graph example shown in the video was created using the CSV files of the [Northwind dataset](https://github.com/neo4j-documentation/developer-resources/tree/gh-pages/data/northwind) and the instructions in this file:

[neo4j_importer_cypher_script_2024-04-24](https://d3c33hcgiwev3.cloudfront.net/pgpKDZwQTtqVimph1IHwog_cdd08f6d0f104646b5e60c0a67aa74f1_neo4j_importer_cypher_script_2024-04-24.cypher?Expires=1767260603&Signature=YPsuuQSiocTT5v9u7vRzXbrpZP4yQY1sEk8kHpV-X1xAlS~9QYdTP1QZX14iq3GQl0qqcLSn1wga0pxi4xj9296Z6BI5kXn-R6~q1HGavb6I8qiUXUdmoDlabPhRYypTSEh5veEAW7qkLpxHn8lsbtidgM6yc8FW4TEz4SsxfOA_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)

**Reference**:
- [Tutorial: Import data from a relational database into Neo4j](https://neo4j.com/docs/getting-started/appendix/tutorials/guide-import-relational-and-etl/)

