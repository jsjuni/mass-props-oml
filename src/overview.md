# Overview of OML Mass Properties Model
## Objectives
* Demonstrate scaling of OML code to models with tens of thousands of elements.
* Demonstrate disciplined metrology for quantities and quantity values, particularly mass properties and their uncertainties.
* Demonstrate application of generic, reusable recursive analysis code to mass properties computations.
* Demonstrate a model partitioning pattern that lends itself to large enterprises with many collaborating authorities, e.g., a multi-level acquirer-supplier pattern.

## Scaling

Model size:
* descriptions: 604
* concept instances: 6,058
* axioms: 78,379

## Metrology

## Recursive Analysis

## Partitioning Pattern

The fundamental technique for managing large-scale problems in systems engineering is to decompose and delegate. The proper instrument of decomposition and delegation in systems engineering is the _Work Breakdown Structure_ (WBS), a tree whose vertices represent a domain of authority and whose edges represent delegation of authority.

In this example there are two kinds of authority. One is a single Project at the top of the hierarchy and the other is a tree of Work Packages. We will illustrate the features of this pattern using work package WP.4.2.2, its parent, and its children. For the moment, however, we elaborate the principles that guide that pattern.

Just as it is essential to control the interfaces in an interconnected set of subsystems, it is essental to control the inter-authority interfaces in the delegation of authority. To that end, we assert two principles:
1. Every assertion in a model must be attributable to an authority.
2. It must be possible to scope model queries such that only assertions attributed to specific authorities appear in the result.

To illustrate, consider the work package WP.4.2.2, which supplies the component C.1.2.2 (Element 2). The parent work package WP.4.2 is the customer or acquirer of C.1.2.2 and the supplier of C.1.2 (Segment 2); it will have asserted requirements on C.1.2.2, perhaps in the form of constraints on defined quantities that charcterize Element 2. It must be possible to attribute these requirements and constraints to WP.4.2.

Conversely, WP.4.2.2 should design in response only to constraints and requirements asserted by its customer (or other recognized authority). Any other assertion purporting to be a requirement is simply invalid for the purpose of WP.4.2.2's work.

OML organizes models into vocabularies, vocabulary bundles, descriptions, and description bundles. For the purposes of this demonstration, we focus on descriptions. The overwhelming majority of assertions in a system model appear in descriptions.

Each OML description has values for zero or more annotations properties. These properties provide metadata about the model for any purpose whatever. We employ them here to denote authorship and access rights. In particular, we use the `dc:creator` and `dc:rights` annotation properties from the international standard Dublin Core Metadata.

The pattern is simple:
* Each authority (Program, Project, WorkPackage) is defined as a concept instance in some description, and is therefore uniquely identified by a Uniform Resource Identifier (URI).
* Each description has a single value for `dc:creator` that is the URI of the authority that authored it. Each assertion in the description is therefore attributable to that authority.
* Each description has zero or more values for `dc:rights` that denote the URIs of the authorities permitted to access it. The value may be a regular expression that matches a class of authority URIs.

Note that OML Code is not itself a model repository and does not enforce distributed access controls. The pattern illustrated here is the policy that would inform any distribution and collaboration infrastructure. Nevertheless, we will take steps to ensure that this policy and pattern have the desired properties.

When translated to OWL, an OML description becomes a so-called _ABox_ ontology. Description annotations become ontology annotations. When loaded into a quad store, the ABox ontology can be queried as a named graph. Using these mechanisms, any assertion can be attributed to the authority designated by the `dc:creator` annotation of the graph in which it appears. Moreover, any SPARQL query can be scoped to include only assertions attributable to specific authorities through properly constructed `GRAPH {}` clauses. This protocol achieves the two principles stated above.

We illustrate in more detail using our example Work Package WP.4.2.2.

Suppose we are the authority designated as _WP.4.2.2 Element 2_. That means, among other things, that there exists in some model an instance of the concept `project:WorkPackage` with those designations. Here it is:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4/WP/WP.4.2#WP.4.2"^^xsd:anyURI
@dc:rights "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#> as WP.4.2.2 {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    uses <http://imce.jpl.nasa.gov/foundation/project#> as project
    uses <http://imce.jpl.nasa.gov/foundation/base#> as base
    uses <http://studioj.us/base#> as base1
    extends <http://studioj.us/mass-props-oml/WP.4.2/C/C.1.2.2#> as C.1.2.2

    instance WP.4.2.2 : project:WorkPackage [
        base:hasCanonicalName "Element 2"
        base1:hasWorkPackageIdentifier "WP.4.2.2"
        base:hasSortKey "040202"
        project:supplies C.1.2.2:C.1.2.2
    ]
}
```
The `@dc:creator` annotation indicates that this instance is attributable to a work package whose URI stem is WP.4.2. In a normal work breakdown structure, the parent of WP.4.2.2 would be WP.4.2, so that makes sense. We'll understand this in more detail later. The `@dc:rights` annotation indicates that our work package (WP.4.2.2) is permitted to see this description, which is fitting. This is how our customer delegates to us: by creating a work package, assigning it an identifier and a name, and exposing it to us.

Note the `project:supplies` relation. That is more model content pertinent to us:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4/WP/WP.4.2#WP.4.2"^^xsd:anyURI
@dc:rights "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2/C/C.1.2.2#> as C.1.2.2 {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    uses <http://imce.jpl.nasa.gov/foundation/mission#> as mission
    uses <http://imce.jpl.nasa.gov/foundation/base#> as base
    uses <http://studioj.us/base#> as base1
    extends <http://studioj.us/mass-props-oml/WP.4.2/IT/IT.C.1.2.1_out#> as IT.C.1.2.1_out
    extends <http://studioj.us/mass-props-oml/WP.4.2/IT/IT.C.1.2.2_out#> as IT.C.1.2.2_out
    uses <http://www.w3.org/2000/01/rdf-schema#> as rdfs
    uses <http://studioj.us/metrology#> as metrology
    uses <http://imce.jpl.nasa.gov/foundation/analysis#> as analysis

    instance C.1.2.2 : mission:Component [
        base:hasCanonicalName "Element 2"
        base1:hasComponentIdentifier "C.1.2.2"
        base:hasSortKey "010202"
        ...
    ]

...
}
```
Here our customer has asserted the existence of a `mission:Component` _C.1.2.2 (Element 2)_, which is the target of the `project:supplies` relation above. WP.4.2.2 is named "Element 2" because its purpose is to supply the "Element 2" component. Though not shown above, our customer will also have asserted facts about Element 2 in the form of quantity definitions, requirements, operational contexts, etc., and made those assertions visible to us through the `@dc:rights` annotation.
In summary, our customer is telling us what they want us to supply.

Thus authorized, consider how we go about that work.

After some analysis we may arrive at a physical decomposition of our supplied element into its constituent subsystems. This decomposition will take the form of creating a concept instance for each subsystem and asserting the `mission:contains` relation that connects the whole to its parts.

In principle, this physical decomposition is proprietary to us. Our customer has asked us to deliver an Element 2 that satisfies certain constraints, including requirements. The specific implementation of Element 2, however, is within our delegated design authority. We may elect, therefore, to make the system breakdown description and its diagrammatic representation invisible to our customer.

To make it slightly more interesting, let us suppose that out Element 2 is composed of five subsystems, identified as C.1.2.2.1 though C.1.2.2.5, each which will be supplied by a work package to which we delegate. Further assume that we will integrate those five systems into two intermediate assemblies designated C.1.2.2.A and C.1.2.2.B.

How should we partition this content into descriptions that honor our access control conventions?

If each of our subsystems will be supplied by a separate work package, then we should create each subsystem in its own description with a `@dc:rights` annotation that makes it available to the supplier. Here, for example, is the description C.1.2.2.1:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
@dc:rights "http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.1#WP.4.2.2.1"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.1#> as C.1.2.2.1 {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    uses <http://imce.jpl.nasa.gov/foundation/mission#> as mission
    uses <http://imce.jpl.nasa.gov/foundation/base#> as base
    uses <http://studioj.us/base#> as base1
    extends <http://studioj.us/mass-props-oml/WP.4.2/IT/IT.C.1.2.1_out#> as IT.C.1.2.1_out
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/IT/IT.C.1.2.2.1_out#> as IT.C.1.2.2.1_out
    uses <http://www.w3.org/2000/01/rdf-schema#> as rdfs
    uses <http://studioj.us/metrology#> as metrology
    uses <http://imce.jpl.nasa.gov/foundation/analysis#> as analysis

    instance C.1.2.2.1 : mission:Component [
        base:hasCanonicalName "Subsystem 1"
        base1:hasComponentIdentifier "C.1.2.2.1"
        base:hasSortKey "01020201"
        ...
    ]
...
}
```

Similarly, we create a work package WP.4.2.2.1 to supply C.1.2.2.1:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
@dc:rights "http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.1#WP.4.2.2.1"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.1#> as WP.4.2.2.1 {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    uses <http://imce.jpl.nasa.gov/foundation/project#> as project
    uses <http://imce.jpl.nasa.gov/foundation/base#> as base
    uses <http://studioj.us/base#> as base1
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.1#> as C.1.2.2.1

    instance WP.4.2.2.1 : project:WorkPackage [
        base:hasCanonicalName "Subsystem 1"
        base1:hasWorkPackageIdentifier "WP.4.2.2.1"
        base:hasSortKey "04020201"
        project:supplies C.1.2.2.1:C.1.2.2.1
    ]
}
```

Note that these two descriptions are visible (per the `@dc:rights` annotations) to WP.4.2.2.1. Just as our customer authorized us to supply a component, we now authorize our supplier.

Having described all subsystems and made those descriptions visible to their suppliers, we can describe our System Breakdown Structure:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2.2/SBS#> as WP.4.2.2_SBS {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    extends <http://studioj.us/mass-props-oml/WP.4.2/C/C.1.2.2#> as C.1.2.2
    uses <http://imce.jpl.nasa.gov/foundation/mission#> as mission
    uses <http://studioj.us/base#> as base
    uses <http://imce.jpl.nasa.gov/foundation/base#> as base1
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.1#> as C.1.2.2.1
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.2#> as C.1.2.2.2
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.3#> as C.1.2.2.3
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.4#> as C.1.2.2.4
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/C/C.1.2.2.5#> as C.1.2.2.5

    ref instance C.1.2.2:C.1.2.2 [
        base1:contains C.1.2.2.A, C.1.2.2.B
    ]

    instance C.1.2.2.A : mission:Component [
        base:hasComponentIdentifier "C.1.2.2.A"
        base1:hasCanonicalName "C.1.2.2.A"
        base1:contains C.1.2.2.1:C.1.2.2.1, C.1.2.2.2:C.1.2.2.2
    ]

    instance C.1.2.2.B : mission:Component [
        base:hasComponentIdentifier "C.1.2.2.B"
        base1:hasCanonicalName "C.1.2.2.B"
        base1:contains C.1.2.2.3:C.1.2.2.3, C.1.2.2.4:C.1.2.2.4, C.1.2.2.5:C.1.2.2.5
    ]
}
```
Note that this description grants no rights. As mentioned above, the internal decomposition of our Element 2 may be proprietary. In this case we choose not to reveal it to our customer or any of our suppliers.

Also note that the intermediate assemblies C.1.2.2.A and C.1.2.2.B may also be proprietary, in which case we can describe them here.

Here is our Work Breakdown Structure:

```
@dc:creator "http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#WP.4.2.2"^^xsd:anyURI
description <http://studioj.us/mass-props-oml/WP.4.2.2/WBS#> as WP.4.2.2_WBS {
    uses <http://purl.org/dc/elements/1.1/> as dc
    uses <http://www.w3.org/2001/XMLSchema#> as xsd
    extends <http://studioj.us/mass-props-oml/WP.4.2/WP/WP.4.2.2#> as WP.4.2.2
    uses <http://imce.jpl.nasa.gov/foundation/project#> as project
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.1#> as WP.4.2.2.1
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.2#> as WP.4.2.2.2
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.3#> as WP.4.2.2.3
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.4#> as WP.4.2.2.4
    extends <http://studioj.us/mass-props-oml/WP.4.2.2/WP/WP.4.2.2.5#> as WP.4.2.2.5

    ref instance WP.4.2.2:WP.4.2.2 [
        project:authorizes WP.4.2.2.1:WP.4.2.2.1, WP.4.2.2.2:WP.4.2.2.2, WP.4.2.2.3:WP.4.2.2.3, WP.4.2.2.4:WP.4.2.2.4, WP.4.2.2.5:WP.4.2.2.5
    ]
}
```
