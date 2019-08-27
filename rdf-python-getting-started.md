<!-- TITLE: Getting Started with RDF Python -->
<!-- SUBTITLE: A short guide through RDF Python basics  -->

# Getting Started with RDF Python

### Examples
#### Creating a Triple
```python
from rdflib import URIRef, Literal, Graph

subject = URIRef("http://example.org/subject")
predicate = URIRef("http://example.org/predicate")
object = Literal("object")

g = Graph()

g.add( (subject, predicate, object) )

print (g.serialize(format='nt').decode())
```

```shell
$ python ./create.py
<http://example.org/subject> <http://example.org/predicate> "object" .
```
In this example we start by creating 3 objects (`subject`, `predicate`, `object`) that will later form a [triple](https://www.w3.org/TR/rdf11-primer/#section-triple). Then we create an empty graph, `g`. As always with RDF, subject and predicate need to be [IRIs](https://www.w3.org/TR/rdf11-primer/#section-IRI). The object can either be an IRI or a [literal](https://www.w3.org/TR/rdf11-primer/#section-literal), as in our example.

Then the `add()`function is used to add this triple to the graph.

In the last line we serialize the graph into [N-Triples](http://www.w3.org/TR/rdf-testcases/#ntriples) and call the `decode()` function on it for pretty printing. Assuming the example code is stored in a file named `create.py`, the output it will print is shown above.