
In this lab, you will use the Cypher query language to query highly connected data in the graph database Neo4j. Relationships between data entities can be just as important as the data itself, and graph databases are designed to answer questions about the relationships. You will also leverage the graph database to perform vector search: a method of information retrieval where data points are represented as vectors. One can perform a similarity search between data points by comparing their vector representations.

When using Cypher to query your graph database, you need to understand the difference between nodes, relationships, and paths. Let's take a closer look at these components.

### 2.1 - Introduction to Cypher 

Cypher is a declarative query language designed for expressing queries across graph databases. It provides a concise and intuitive syntax for performing operations such as creating, updating, retrieving, and deleting data within graph structures. It reuses syntax from SQL and mixes it with ASCII elements to represent graph elements. OpenCypher, on the other hand, is an open standard for graph query languages inspired by Cypher. It aims to standardize the Cypher query language across different graph database implementations. Alongside Cypher, Gremlin and SPARQL are the most popular Graph Query Languages.

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

You will see more about nodes, relationships and paths in the next exercises while exploring the syntax of the language.

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

