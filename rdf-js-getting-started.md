<!-- TITLE: Getting Started with RDF/JS -->
<!-- SUBTITLE: A short guide through RDF/JS basics  -->

# Getting Started with RDF/JS

# What is RDF/JS?

For many years there were multiple RDF libraries and interfaces available in the JavaScript world. In 2013 the [RDF JavaScript Libraries Community Group](https://www.w3.org/community/rdfjs/) was initiated. Results of this group are several [RDF/JS Specifications](http://rdf.js.org/), most notably the [Data Model Specification](http://rdf.js.org/data-model-spec/) and the (WIP) [Dataset Specification](https://rdf.js.org/dataset-spec/).

# How to work with RDF/JS

## Data Model

The **RDF/JS Data Model** is a low-level interface for working with RDF and Linked Data in ECMAScript platforms like Web browsers and Node.js. Full specification: [RDF/JS Data Model Specification](http://rdf.js.org/data-model-spec/).

### Basics

A **Triple** is made up of *subject*, *predicate* and *object*.

A **Quad** is a *Triple* with an additional *context*, where the context represents the [graph-concept in RDF 1.1](https://www.w3.org/TR/rdf11-primer/#section-multiple-graphs). The Data Model can only represent quads. This is not a real limitation since quads declared without a context are added to the so-called "[default graph](http://rdf.js.org/data-model-spec/#dom-quad-graph)".


### Examples

#### Creating a Quad

```js
const rdf = require('rdf-ext')

const subject = rdf.namedNode('http://example.org/subject') 
const predicate = rdf.namedNode('http://example.org/predicate')
const object = rdf.literal('object')

const quad = rdf.quad(subject, predicate, object)

// log the quads to console with toString():
// note that this is N-Quads serialization in rdf-ext
console.log(quad.toString())
```

```shell
$ node ./create.js
<http://example.org/subject> <http://example.org/predicate> "object" .
```

[Code for this example](https://github.com/rdf-ext/rdf-examples/blob/develop/rdf-ext-basics.js)

In this example we start with an `rdf` object from the `rdf-ext` package. We then create a [subject, a predicate and an object](https://www.w3.org/TR/rdf11-primer/#section-triple). As always with RDF, subject and predicate need to be [IRIs](https://www.w3.org/TR/rdf11-primer/#section-IRI). The object can either be an IRI or a [literal](https://www.w3.org/TR/rdf11-primer/#section-literal), as in our example.

We then create the quad. Here we omit explicitly declaring the context for the quad, therefore the triple is simply added to the so-called "[default graph](http://rdf.js.org/#dom-quad-graph)".

In the last line we use `quad.toString()` to log the triple/quad to the console. In rdf-ext, a quad is always in [N-Quads](https://www.w3.org/TR/n-quads/)-like syntax with one quad per line and a dot at the end of the line. Assuming the example code is stored in a file named `create.js`, the output it will print is shown above.

If you want to create a [blank node](https://www.w3.org/TR/rdf11-primer/#section-blank-node) instead of a named-node, simply use the `blankNode` function:

```js
const bnode = rdf.blankNode()
```

#### Parsing RDF into RDF/JS

While creating quads by hand is useful, in most cases you will also have RDF data stored in some serialization format that you will need to parse into RDF/JS objects.

The previous example used [rdf-ext](https://github.com/rdf-ext/rdf-ext), let's use a different package here by taking a look at [N3.js](https://github.com/rdfjs/N3.js)' [parsing capabilities](https://github.com/rdfjs/N3.js#parsing).

```js
const N3 = require('n3')
const turtleParser = new N3.Parser({ format: 'Turtle' })
const ntriplesParser = new N3.Parser({ format: 'N-Triples' })

const ttl = `PREFIX c: <http://example.org/cartoons#>
   c:Tom a c:Cat.
   c:Jerry a c:Mouse;
           c:smarterThan c:Tom.`

turtleParser.parse(ttl, (err, quad, prefixes) => {
  if (err) {
    throw err
  }
  if (quad) {
    console.log({ quad });
  }
  else {
    console.log({ prefixes });
  }
})

/*
{ quad:
   { subject: 'http://example.org/cartoons#Tom',
     predicate: 'http://www.w3.org/1999/02/22-rdf-syntax-ns#type',
     object: 'http://example.org/cartoons#Cat',
     graph: '' } }
{ quad:
   { subject: 'http://example.org/cartoons#Jerry',
     predicate: 'http://www.w3.org/1999/02/22-rdf-syntax-ns#type',
     object: 'http://example.org/cartoons#Mouse',
     graph: '' } }
{ quad:
   { subject: 'http://example.org/cartoons#Jerry',
     predicate: 'http://example.org/cartoons#smarterThan',
     object: 'http://example.org/cartoons#Tom',
     graph: '' } }
{ prefixes:
   [Object: null prototype] { c: 'http://example.org/cartoons#' } }
*/

const nt = `
  _:alice <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://xmlns.com/foaf/0.1/Person> .
  _:alice <http://xmlns.com/foaf/0.1/knows> _:bob .
  _:bob <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://xmlns.com/foaf/0.1/Person> .
  _:bob <http://xmlns.com/foaf/0.1/knows> _:alice .
`

ntriplesParser.parse(nt, (err, quad, prefixes) => {
  if (err) {
    throw err
  }
  if (quad) {
    console.log({ quad });
  }
  else {
    console.log({ prefixes });
  }
})

/*
{ quad:
   { subject: '_:b0_alice',
     predicate: 'http://www.w3.org/1999/02/22-rdf-syntax-ns#type',
     object: 'http://xmlns.com/foaf/0.1/Person',
     graph: '' } }
{ quad:
   { subject: '_:b0_alice',
     predicate: 'http://xmlns.com/foaf/0.1/knows',
     object: '_:b0_bob',
     graph: '' } }
{ quad:
   { subject: '_:b0_bob',
     predicate: 'http://www.w3.org/1999/02/22-rdf-syntax-ns#type',
     object: 'http://xmlns.com/foaf/0.1/Person',
     graph: '' } }
{ quad:
   { subject: '_:b0_bob',
     predicate: 'http://xmlns.com/foaf/0.1/knows',
     object: '_:b0_alice',
     graph: '' } }
{ prefixes: [Object: null prototype] {} }
*/
```

More about [N3.js](https://github.com/rdfjs/N3.js/blob/master/README.md).

## Datasets

A RDF/JS Dataset is the easiest way of working with a collection of quads. Its interface is defined in the [RDF/JS Dataset Specification](https://rdf.js.org/dataset-spec/). The goal is to have a common API for storing, searching, matching, filtering, manipulating collections of quads.

### Examples

#### Parse triples from a file

Let's install the npm package [tbbt-ld](https://www.npmjs.com/package/tbbt-ld) which provides several characters from The Big Bang Theory described in [Turtle](https://www.w3.org/TR/turtle/) format. Here we will use the parser package `rdf-parser-n3` to parse it into a dataset structure. Note that the [N3 parser](https://github.com/rdfjs/N3.js) (used by `rdf-parser-n3`) can parse [Turtle](https://www.w3.org/TR/turtle/), [TriG](https://www.w3.org/TR/trig/), [N-Triples](https://www.w3.org/TR/n-triples/), [N-Quads](https://www.w3.org/TR/n-quads/), and [Notation3 (N3)](https://www.w3.org/TeamSubmission/n3/).

```js
const fs = require('fs')
const rdf = require('rdf-ext')
const N3Parser = require('rdf-parser-n3')

// create N3 parser instance
const parser = new N3Parser({factory: rdf})

// Read a Turtle file and stream it to the parser
const quadStream = parser.import(fs.createReadStream('./node_modules/tbbt-ld/data/person/sheldon-cooper.ttl'))

;(async function () {
  // create a new dataset and import the quad stream into it (reverse pipe) with Promise API
  const dataset = await rdf.dataset().import(quadStream)
  console.log(`Dataset contains ${dataset.size} quads`)

  const jobTitles = dataset.match(null, rdf.namedNode('http://schema.org/jobTitle')).toArray()
  jobTitles.forEach(({ object }) => {
    console.log(`Sheldon's jobTitle is ${object.value}`)
  })

  const relations = dataset.match(null, rdf.namedNode('http://schema.org/knows')).toArray()
  console.log(`Sheldon knows ${relations.map(({ object }) => object.value).join(', ')}`)
})()

/*
Dataset contains 19 quads
Sheldon's jobTitle is theoretical physicist
Sheldon knows amy-farrah-fowler, bernadette-rostenkowski, howard-wolowitz, leonard-hofstadter, penny, rajesh-koothrappali, stuart-bloom
*/
```
