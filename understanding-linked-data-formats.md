<!-- TITLE: Understanding Linked Data Formats -->
<!-- SUBTITLE: Turtle vs RDF/XML vs N-Triples vs JSON-LD -->


# Understanding Linked Data Formats

### Turtle vs RDF/XML vs N-Triples vs JSON-LD

[In my previous article](https://medium.com/wallscope/tackling-big-data-challenges-with-linked-data-278b0761a6de) I discussed the power of linked data and the advantages of RDF. In this article we will explore what RDF looks like by examining its four most common formats: N-Triples, Turtle, JSON-LD and RDF/XML.
> **Edit (04/05/2019):** I expanded this article to include JSON-LD and added the contents section below. I had not included JSON-LD originally because I never really use it but agree it needed to be added for completeness.

## Contents

**Introduction
What Do the Formats Look Like?
Which is the Best Format for Me?
Conclusion
TL;DR
Appendix**

## Introduction

Very simply, linked data is stored as triples that consist of a subject, predicate and object. These triples connect entities to other entities or literals (more on them later) to create a directed knowledge graph:

![[http://www27.cs.kobe-u.ac.jp/achieve/data/pdf/1285.pdf](http://www27.cs.kobe-u.ac.jp/achieve/data/pdf/1285.pdf)](https://cdn-images-1.medium.com/max/2000/1*ARfcTMDGzgPp11uAxSm4CQ.png)*[http://www27.cs.kobe-u.ac.jp/achieve/data/pdf/1285.pdf](http://www27.cs.kobe-u.ac.jp/achieve/data/pdf/1285.pdf)*

In this example we can see that Tokyo is located in the country Japan and has an area of 2188km². The entities (Tokyo and Japan) and predicates each have URIs to identify them in a machine readable format. The area in the rectangle does not however as it is what is called a literal (a constant value) which we discuss later in detail.

## What Do the Formats Look Like?

RDF is commonly stored in one of four formats: N-Triples (.nt), Turtle (.ttl), JSON-LD (.json) or RDF/XML (.rdf). Which you use is mainly down to preference as all of these formats are supported by the main RDF libraries and triplestores. There do exist benefits and disadvantages however that I will highlight later in this article.

### N-Triples

Storing and reading RDF as N-Triples is simple as every line of a .nt file is a single triple (<subject> <predicate> <object>) that together form a directed knowledge graph:

![](https://cdn-images-1.medium.com/max/2000/1*z6BnZAL65nJK0fTt3QeKXQ.png)

Similar to the small example at the beginning of this article, circles represent entities, rectangles represent literals and arrows illustrate the predicates. This knowledge graph, with the 11 connections, is represented in N-Triples format as such:

![Note: grey numbers are there for explanation purposes only: **NOT** N-Triples](https://cdn-images-1.medium.com/max/2000/1*8pHH-xuCc-5JI3LzOuVSrg.png)*Note: grey numbers are there for explanation purposes only: **NOT** N-Triples*
> **Note:** Code blocks of each example can be found in the appendix

Here we have 11 triples containing information about Bob Marley and Jamaica extracted from DBpedia — the linked data representation of Wikipedia. For illustration purposes I have coloured entities red, predicates purple, literals green and literal tags plus datatypes orange.

We can see from the first triple that Bob Marley is of type Person, where we use the foaf (friend of a friend) ontology as the URI for this type. Using standard ontologies like foaf allows the easy integration with other linked data that I discussed in my [previous article](https://medium.com/wallscope/tackling-big-data-challenges-with-linked-data-278b0761a6de). The second and third triples have standard predicates but have strings as subjects which are called literals. In DBpedia the URIs are human readable but this is not always the case so human readable labels are attached to entities. These two literals have attached language tags following the @ symbol. In these two examples “Bob Marley” is the same in both English and French whereas you can see that Jamaica is different in Italian if you look at the eighth triple. These string literals are often called “**plain literals**” as opposed to “typed literals” which I explain below.

The fifth triple in this example links the entity of Bob Marley to the entity of his birthplace, Jamaica. As you can see from the triples that follow, objects can be subjects (even predicates can be subjects or objects) to create the desired knowledge graph. You will notice that the **typed literals** in the ninth and tenth triples are numbers and have an attached type (hence the name typed literal), in this case float. This is often used to ensure numbers are valid, for example ages are usually integers. Finally in the eleventh triple, we can see the foaf ontology used once more to connect the entity of Jamaica to its homepage URL (subset of URI).

### Turtle

Reading (as a human) RDF in Turtle format is much easier as you can define prefixes at the beginning of the .ttl file, shortening each triple. Another feature of turtle is that multiple triples with the same subject are grouped into blocks (so the URI for Bob Marley for example is not repeatedly listed), for example:

![](https://cdn-images-1.medium.com/max/2000/1*WgqGqn1tcd_aSpQbN45aBw.png)

This represents the exact same knowledge graph as the N-Triples above. In the top section, prefixes are defined so that the long repeated sections of the URIs can be written in their short form. For example the line

    [@prefix](http://twitter.com/prefix) foaf: <[http://xmlns.com/foaf/0.1/](http://xmlns.com/foaf/0.1/)> .

lets the string foaf represent http://xmlns.com/foaf/0.1/. This allows the subject <http://xmlns.com/foaf/0.1/Person> to be shortened to foaf:Person.

One unique shortening is the predicate <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> which is so common, it is represented simply with the letter a. Hence the N-Triple

    <[http://dbpedia.org/resource/Bob_Marley](http://dbpedia.org/resource/Bob_Marley)> <[http://www.w3.org/1999/02/22-rdf-syntax-ns#type](http://www.w3.org/1999/02/22-rdf-syntax-ns#type)> <[http://xmlns.com/foaf/0.1/Person](http://xmlns.com/foaf/0.1/Person)> .

can be shortened in Turtle to

    dbr:Bob_Marley a foaf:Person

which is a lot clearer.

You can see that the data about Bob Marley and Jamaica is separated into separate blocks. This grouping along with defined prefixes, makes Turtle format a lot easier to understand than N-Triples. If you notice, related information to the subject is separated with a semi-colon and finished with a full-stop and a newline to indicate a new subject.

### JSON-LD

Next we have JSON-LD which is an older format as it fits linked data into an already existing format - JSON. Since its creation, Turtle which is more humanly readable was created but I compare these in more detail later in this article.

I have again colour coordinated this example which represents the same knowledge graph:

![](https://cdn-images-1.medium.com/max/2000/1*Uc2YJUchyg8mpOEzbHJ8LA.png)
> For clarity, I am referring to JSON objects as “blocks” here to avoid the confusion between JSON objects and linked data objects (subject, predicate, object).

Each entity (coloured red) is declared on the top level. This is why the entities within the Bob Marley and Jamaica blocks are declared again at the end of the .json file.

Within the top level declarations, the entities are connected to their types with @type which represents <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> like a did in Turtle. Entities are also connected to literals and other entities within these top level blocks. As JSON is hierarchical, literals are also contained within blocks alongside their language tag or type.

### RDF/XML

The oldest RDF format is RDF/XML which is not used as often anymore but is still standard due to this fact. Again, here is RDF/XML representing the exact same knowledge graph as the N-Triples, Turtle and JSON-LD above.

![](https://cdn-images-1.medium.com/max/2000/1*r6HFU3Ow1qHX9rT0cr1G5Q.png)

Like Turtle, prefixes can be defined at the top of RDF/XML files to avoid unnecessary repetition of URIs. As we can see however, RDF/XML is still not as humanly readable as Turtle but there are a few considerations to make before choosing which format you should store your RDF in.

## Which is the Best Format for Me?

It might seem like an obvious decision to always store your linked data in the much prettier (most humanly readable) Turtle format but, as I mentioned above, there are advantages and disadvantages to using each of the above formats.

### RDF/XML:

As mentioned, RDF/XML was the first RDF format created and is therefore considered the standard format. This means that most RDF libraries and triplestores output RDF in this format by default.

If you want to work with legacy RDF systems or want to use XML libraries to manipulate your data (as RDF/XML is valid XML) then RDF/XML should be the format you use.

This format however was created in attempt to store a new data structure in an old format. RDF/XML is therefore falling in popularity due to the benefits of the other newer RDF formats. If you are using a newer triplestore, you cannot load RDF/XML for example.

### JSON-LD:

Similarly to RDF/XML, JSON-LD is an attempt to store a new data structure into an existing format.

The advantages therefore are similar as JSON-LD is valid JSON which means you can manipulate it with the many standard JSON libraries.

Again however, this format is dropping in popularity so newer triplestores don’t even support loading it.

### Turtle:

Turtle is similar to RDF/XML in many ways but is quite simply, much nicer to look at. If your RDF is to be read by humans at any point, it is clearly best to store your RDF in Turtle format as all of the ‘mess’ is removed. This lack of ‘mess’ also makes Turtle the preferred format to use if bandwidth is an issue.

All modern RDF libraries and triplestores can work with Turtle RDF and as there are no opening and closing lines at the beginning and end of a Turtle file (unlike RDF/XML), the data can be streamed in blocks. Systems today often require live data streams or request data through APIs so the ability to stream linked data is something you must take into consideration.

### N-Triples:

N-Triples are simple, each line consists of a subject, predicate and object separated by a space and ending with a period. This makes N-Triples very easy to parse and like Turtle, all modern RDF libraries and triplestores can work with N-Triples.

N-Triples may seem expensive to store but this extreme verbosity, that makes them so easy to parse, assists modern compression techniques. RDF stored as N-Triples can therefore achieve highly efficient compression ratios.

As discussed above, Turtle can be streamed in blocks. N-Triples however can be streamed line by line which makes it much more robust. If a line of an N-Triples file is lost, one triple is lost… In a Turtle file however, that entire block is lost which could potentially be thousands of triples.

Similarly, if you rearrange the lines in an N-Triple file, your file is still valid and the knowledge graph that your triples represent is unaffected. If you do this however to a Turtle file, the RDF becomes invalid. This is a problem if you wish to efficiently process multiple incoming streams or API responses. If your system is processing N-Triples, your data can be handled as soon as you receive line one.

## Conclusion:

We have explored the three main RDF formats and outlined how you can decide which you want to use. In my next article I will walk through [transforming data from a csv into RDF](https://medium.com/wallscope/creating-linked-data-31c7dd479a9e).

## TL;DR:

Triples are made up of three parts: a subject, predicate and object.
These combine to create a directed knowledge graph.
Entities are represented by URIs.

Objects can hold information (constant values) about their subjects and these are called Literals.
Plain literals are strings that can optionally have attached tags (such as a language tag)
Typed literals have attached datatypes (such as a number having the attached integer datatype)

If you are working with legacy systems store your RDF in RDF/XML format.
If you want your RDF to be humanly readable or are concerned about bandwidth , store your RDF in Turtle format.
Otherwise store your RDF as N-Triples (this is our go to RDF format at [Wallscope](https://medium.com/wallscope) as we often stream).

## Appendix:

Each example can be found here:

### N-Triples

    <http://dbpedia.org/resource/Bob_Marley> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://xmlns.com/foaf/0.1/Person> .

    <http://dbpedia.org/resource/Bob_Marley> <http://www.w3.org/2000/01/rdf-schema#label> "Bob Marley"@en .

    <http://dbpedia.org/resource/Bob_Marley> <http://www.w3.org/2000/01/rdf-schema#label> "Bob Marley"@fr .

    <http://dbpedia.org/resource/Bob_Marley> <http://www.w3.org/2000/01/rdf-schema#seeAlso> <http://dbpedia.org/resource/Rastafari> .

    <http://dbpedia.org/resource/Bob_Marley> <http://dbpedia.org/ontology/birthPlace> <http://dbpedia.org/resource/Jamaica> .

    <http://dbpedia.org/resource/Jamaica> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://schema.org/Country> .

    <http://dbpedia.org/resource/Jamaica> <http://www.w3.org/2000/01/rdf-schema#label> "Jamaica"@en .

    <http://dbpedia.org/resource/Jamaica> <http://www.w3.org/2000/01/rdf-schema#label> "Giamaica"@it .

    <http://dbpedia.org/resource/Jamaica> <http://www.w3.org/2003/01/geo/wgs84_pos#lat> "17.9833"^^<http://www.w3.org/2001/XMLSchema#float> .

    <http://dbpedia.org/resource/Jamaica> <http://www.w3.org/2003/01/geo/wgs84_pos#long> "-76.8"^^<http://www.w3.org/2001/XMLSchema#float> .

    <http://dbpedia.org/resource/Jamaica> <http://xmlns.com/foaf/0.1/homepage> <http://jis.gov.jm/> .

### Turtle

    @prefix dbr:    <http://dbpedia.org/resource/> .
    @prefix dbo:    <http://dbpedia.org/ontology/> .
    @prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix foaf:   <http://xmlns.com/foaf/0.1/> .
    @prefix geo:    <http://www.w3.org/2003/01/geo/wgs84_pos#> .
    @prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .
    @prefix schema: <http://schema.org/> .

    dbr:Bob_Marley
    a foaf:Person ;
    rdfs:label "Bob Marley"@en ;
    rdfs:label "Bob Marley"@fr ;
    rdfs:seeAlso dbr:Rastafari ;
    dbo:birthPlace dbr:Jamaica .

    dbr:Jamaica
    a schema:Country ;
    rdfs:label "Jamaica"@en ;
    rdfs:label "Giamaica"@it ;
    geo:lat "17.9833"^^xsd:float ;
    geo:long "-76.8"^^xsd:float ;
    foaf:homepage <http://jis.gov.jm/> .

### JSON-LD

    [
      {
        "[@id](http://twitter.com/id)": "[http://dbpedia.org/resource/Bob_Marley](http://dbpedia.org/resource/Bob_Marley)",
        "[@type](http://twitter.com/type)": [
          "[http://xmlns.com/foaf/0.1/Person](http://xmlns.com/foaf/0.1/Person)"
        ],
        "[http://www.w3.org/2000/01/rdf-schema#label](http://www.w3.org/2000/01/rdf-schema#label)": [
          {
            "[@value](http://twitter.com/value)": "Bob Marley",
            "[@language](http://twitter.com/language)": "en"
          },
          {
            "[@value](http://twitter.com/value)": "Bob Marley",
            "[@language](http://twitter.com/language)": "fr"
          }
        ],
        "[http://www.w3.org/2000/01/rdf-schema#seeAlso](http://www.w3.org/2000/01/rdf-schema#seeAlso)": [
          {
            "[@id](http://twitter.com/id)": "[http://dbpedia.org/resource/Rastafari](http://dbpedia.org/resource/Rastafari)"
          }
        ],
        "[http://dbpedia.org/ontology/birthPlace](http://dbpedia.org/ontology/birthPlace)": [
          {
            "[@id](http://twitter.com/id)": "[http://dbpedia.org/resource/Jamaica](http://dbpedia.org/resource/Jamaica)"
          }
        ]
      },
      {
        "[@id](http://twitter.com/id)": "[http://dbpedia.org/resource/Jamaica](http://dbpedia.org/resource/Jamaica)",
        "[@type](http://twitter.com/type)": [
          "[http://schema.org/Country](http://schema.org/Country)"
        ],
        "[http://www.w3.org/2000/01/rdf-schema#label](http://www.w3.org/2000/01/rdf-schema#label)": [
          {
            "[@value](http://twitter.com/value)": "Jamaica",
            "[@language](http://twitter.com/language)": "en"
          },
          {
            "[@value](http://twitter.com/value)": "Giamaica",
            "[@language](http://twitter.com/language)": "it"
          }
        ],
        "[http://www.w3.org/2003/01/geo/wgs84_pos#lat](http://www.w3.org/2003/01/geo/wgs84_pos#lat)": [
          {
            "[@value](http://twitter.com/value)": "17.9833",
            "[@type](http://twitter.com/type)": "[http://www.w3.org/2001/XMLSchema#float](http://www.w3.org/2001/XMLSchema#float)"
          }
        ],
        "[http://www.w3.org/2003/01/geo/wgs84_pos#long](http://www.w3.org/2003/01/geo/wgs84_pos#long)": [
          {
            "[@value](http://twitter.com/value)": "-76.8",
            "[@type](http://twitter.com/type)": "[http://www.w3.org/2001/XMLSchema#float](http://www.w3.org/2001/XMLSchema#float)"
          }
        ],
        "[http://xmlns.com/foaf/0.1/homepage](http://xmlns.com/foaf/0.1/homepage)": [
          {
            "[@id](http://twitter.com/id)": "[http://jis.gov.jm/](http://jis.gov.jm/)"
          }
        ]
      },
      {
        "[@id](http://twitter.com/id)": "[http://dbpedia.org/resource/Rastafari](http://dbpedia.org/resource/Rastafari)"
      },
      {
        "[@id](http://twitter.com/id)": "[http://jis.gov.jm/](http://jis.gov.jm/)"
      },
      {
        "[@id](http://twitter.com/id)": "[http://schema.org/Country](http://schema.org/Country)"
      },
      {
        "[@id](http://twitter.com/id)": "[http://xmlns.com/foaf/0.1/Person](http://xmlns.com/foaf/0.1/Person)"
      }
    ]

### RDF/XML

    <?xml version="1.0" encoding="utf-8" ?>
    <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
             xmlns:foaf="http://xmlns.com/foaf/0.1/"
             xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
             xmlns:ns0="http://dbpedia.org/ontology/"
             xmlns:schema="http://schema.org/"
             xmlns:geo="http://www.w3.org/2003/01/geo/wgs84_pos#">
    
      <foaf:Person rdf:about="http://dbpedia.org/resource/Bob_Marley">
        <rdfs:label xml:lang="en">Bob Marley</rdfs:label>
        <rdfs:label xml:lang="fr">Bob Marley</rdfs:label>
        <rdfs:seeAlso rdf:resource="http://dbpedia.org/resource/Rastafari"/>
        <ns0:birthPlace>
          <schema:Country rdf:about="http://dbpedia.org/resource/Jamaica">
            <rdfs:label xml:lang="en">Jamaica</rdfs:label>
            <rdfs:label xml:lang="it">Giamaica</rdfs:label>
            <geo:lat rdf:datatype="http://www.w3.org/2001/XMLSchema#float">17.9833</geo:lat>
            <geo:long rdf:datatype="http://www.w3.org/2001/XMLSchema#float">-76.8</geo:long>
            <foaf:homepage rdf:resource="http://jis.gov.jm/"/>
          </schema:Country>
        </ns0:birthPlace>
    
      </foaf:Person>
    
    </rdf:RDF>
