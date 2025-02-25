---
title: "Knowledge Graph Construction from Network Data Sources"
abbrev: "kg-construct"
category: info

docname: draft-marcas-nmop-kg-construct-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 1
area: "Operations and Management"
workgroup: "Network Management Operations"
keyword:
 - knowledge graph
 - semantics
 - data integration
 - RDF
venue:
  group: "Network Management Operations"
  type: "Working Group"
  mail: "nmop@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/nmop/"
  github: "idomingu/knowledge-graph-yang"
  latest: "https://idomingu.github.io/knowledge-graph-yang/draft-marcas-knowledge-graph-yang.html"

author:
- initials: I. D.
  surname: Martinez-Casanueva
  fullname: Ignacio Dominguez Martinez-Casanueva
  organization: Telefonica
  email: ignacio.dominguezmartinez@telefonica.com
- initials: L.
  surname: Cabanillas
  fullname: Lucia Cabanillas
  organization: Telefonica
  email: lucia.cabanillasrodriguez@telefonica.com
- initials: P.
  surname: Martinez-Julia
  fullname: Pedro Martinez-Julia
  organization: NICT
  email: pedro@nict.go.jp

normative:

informative:
  CSVW:
    title: CSVW - CSV on the Web
    target: https://csvw.org
  ETSI-GS-CIM-009:
    title: "Context Information Management (CIM); NGSI-LD API"
    target: https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf
    date: March 2024
  GNMI:
    title: gRPC Network Management Interface (gNMI)
    author:
      organization: OpenConfig
    target: https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md
  Fuseki:
    title: Apache Jena Fuseki
    author:
      organization: Apache
    target: https://jena.apache.org/documentation/fuseki2/
  JSON-LD:
    title: "JSON-LD 1.1: A JSON-based Serialization for Linked Data"
    author:
      organization: W3C
    target: https://www.w3.org/TR/json-ld11/
    date: July 2020
  Poveda-Villalon2022:
    title: "LOT: An industrial oriented ontology engineering framework"
    author:
      organization: Engineering Applications of Artificial Intelligence
    target: https://doi.org/10.1016/j.engappai.2022.104755
    date: May 2022
  Iglesias-Molina2023:
    title: "The RML Ontology: A Community-Driven Modular Redesign After a Decade of Experience in Mapping Heterogeneous Data to RDF"
    author:
       - name: Ana Iglesias-Molina
    target: https://doi.org/10.1007/978-3-031-47243-5_9
    date: October 2023
    seriesinfo: The Semantic Web – ISWC 2023
  Neo4j:
    title: rdflib-neo4j - RDFLib Store backed by neo4j
    target: https://github.com/neo4j-labs/rdflib-neo4j
  W3C-KGC:
    title: Knowledge Graph Construction Community Group
    author:
      organization: W3C
    target: https://www.w3.org/community/kg-construct/
  EERVC:
    title: "Exploiting External Events for Resource Adaptation in Virtual Computer and Network Systems, IEEE Transactions on Network and Service Management 15 (2018): 555-566."
    author:
      organization: Pedro Martinez-Julia, Ved P. Kafle, Hiroaki Harai.
  SECDEP:
    title: "Secure deployment of third-party applications over 5G-NFV ML-empowered infrastructures, the 7th International Conference on Mobile Internet Security (MobiSec '23), Dec 19-21, 2023, Okinawa, Japan."
    author:
      organization: Ana Hermosilla, Jose Manuel Manjón-Cáliz, Pedro Martinez-Julia, Antonio Pastor, Jordi Ortiz, Diego R. Lopez, Antonio Skarmeta.
  TKDP:
    title: "Telemetry Knowledge Distributed Processing for Network Digital Twins and Network Resilience. NOMS 2023-2023 IEEE/IFIP Network Operations and Management Symposium (2023): 1-6."
    author:
      organization: Pedro Martinez-Julia, Ved P. Kafle, Hitoshi Asaeda.
  ANSA:
    title: "Application of Category Theory to Network Service Fault Detection. IEEE Open Journal of the Communications Society 5 (2024): 4417-4443."
    author:
      organization: Pedro Martinez-Julia, Ved P. Kafle, Hitoshi Asaeda.



--- abstract

This document discusses the mechanisms that support the management and creation of knowledge graphs from data sources specific to the network management domain. The document provides background on core aspects such as ontology development, identifies methodologies and standards, and shares guidelines for integrating network data sources.

--- middle

# Introduction

Knowledge graph introduces a new paradigm in data management that facilitates the integration of heterogenous data silos thanks to a semantic layer. In the case of network management, knowledge graphs provide a data integration solution that can cope with the diverse network data sources and telemetry mechanisms {{?I-D.mackey-nmop-kg-for-netops}}.

The construction of knowledge graphs is a challenging activity that requires the combination of skills in semantic modelling and data engineering. Semantic data models are represented by ontologies and other forms of structured knowledge, which must be kept in sync with the data pipelines that integrate the different data silos into the knowlede graph. The data integration process is based on the ingestion of raw data from their data sources, the mapping of the raw data to the respective ontologies, and the transformation of the data into a graph structure semantically-annotated.

In this sense, Knowledge Graph Construction (KGC) underpinned by two pillars: i) ontology development; and ii) knowledge graph construction pipeline. These pillars are described in detail in the following sections.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

## Terminology

This document defines the following terms:

Data integration: Process of combining data from diverse sources into a unified view.

Data mapping: Technique that defines how data from one data model corresponds to another data model.

Data materialization: Technique that collects data from remote data source and persists a copy the data in a target data storage. This process can also be seen as Extract-Transform-Load (ETL).

Data virtualization: Technique wherein an intermediate component (i.e., data virtualization layer) exposes data available in a remote data sources without creating an copy of the data. The data virtualization layer keeps pointers to the original location of data, so when a data consumer asks for these data, the virtualization layer collects the data from the source and directly serves the data to the consumer.

Ontology: Formal, shared representation of knowledge in a domain.

## Acronyms

CQ: Competency Question

ETL: Extract-Transform-Load

KG: Knowledge Graph

KGC: Knowledge Graph Construction

LOT: Linked Open Terms

OWL: Web Ontology Language

RDF: Resource Description Framework

RDFS: RDF Schema

RML: RDF Mapping Language

SAREF: Smart Applications REFerence

SHACL: Shapes Constraint Language

W3C: World Wide Web Consortium

# Ontology Development {#sec-onto}

Ontologies provide the formal representation of the conceptual models that capture the semantics of data, and building on this, the integration of data in the knowledge graph. Ontologies can be developed following different techniques, ranging from manual to fully automated, depending on the characteristics of the data to be integrated in the knowledge graph (e.g., format or schema).

## Standard Development Methodologies

Developing an ontology is a challenging task that requires skills in knowledge management and semantic modelling. To ease this process, a good practice is to follow mature, proven methodologies that provide thorough guidelines and recommend tools that can help in the development of an ontology. An example of these methodologies is Linked Open Terms (LOT) {{Poveda-Villalon2022}}.

LOT is an ontology development methodology that adopts best practices from agile software development. The methodology has been widely used in European projects as well as in the creation of the ETSI SAREF ontology and its extensions. Precisely, with SAREF Ontology ETSI tackled a similar problem in the scope of IoT, where there is a heterogeneous variety of standard data models and protocols. The methodology iterates over a workflow of the following four activities:

1. ontology requirements specification
2. ontology implementation
3. ontology publication, and
4. ontology maintenance.

The workflow starts with the specification of requirements that the ontology must fulfill. To that aim, the methodology requires collecting knowledge from domain experts, but also by analyzing the data sources (e.g., network devices) and schemas for the data (e.g., YANG data models) to be ingested and integrated in the knowledge graph. LOT recommends several approaches such as competency questions (CQs), natural language statements, or tabular information inspired by METHONTOLOGY.

## Automatic Knowledge Extraction from YANG Models

The extraction of knowledge from YANG models could be automated, for example, by analyzing YANG identities to generate controlled vocabularies and taxonomies.

{{?RFC7950}} defines a YANG identity as "globally unique, abstract, and untyped identity", therefore, a relation between a YANG identity and a concept is straightforward. Additionally, YANG identities can inherit from other YANG identities via the "base" statement. These ideas align with the notion of a taxonomy, where concepts are hierarchically linked with other concepts.

To support the creation of knowledge structures like taxonomies or thesauri, the W3C standardized the Simple Knowledge Organization System (SKOS). In such ontology, a concept scheme comprises a set of concepts that can be linked with other concepts via hierarchical and associative relations. Typically, a YANG model containing YANG identities can be represented as an instance of the "skos:ConceptScheme" class. Next, all YANG identities included in a YANG model can be represented as "skos:Concept instances" that are contained in the concept scheme. Lastly, those YANG identities that include the "base" statement, the respective SKOS concept will include a relation "skos:broader" whose range is the SKOS concept representing the parent YANG identity.

# Knowledge Graph Construction Pipeline  {#sec-pipe}

## Knowledge Objects

The intrinsic nature of knowledge graphs is to connect as much knowledge as possible within certain scope---time and/or space. However, not all processes and operations require whole knowledge graphs. For instance, the communication of a piece of telemetry data, organized according to NTF {{?RFC9232}}, can be repreented as a subset of the knowledge graph of all measurements.

A knowledge object, as defined in {{EERVC}}, consists in a knowledge graph subset of an arbitrary size---from single atoms to tens or hundreds of triples---that is decorated with metadata to facilitate its contextualization.

Knowledge objects are particularly well suited to enable entities that work with knowledge graphs to communicate to each other knowledge pieces, obtained from their knowledge graphs or newly created from other sources, such as monitoring. It has been demonstrated in {{SECDEP}}.

## Pipeline Steps

The construction of a knowledge graph is supported by a data pipeline that follows the archetypical Extract-Transform-Load (ETL), wherein the raw data is collected from the source(s), transformed, and finally, stored for consumption. The knowledge graph creation pipeline can thus be split into multiple steps as depicted in {{fig:kgc-pipeline-architecture}}.

~~~
+-----------+       +---------+       +-----------------+
|           |       |         |       |                 |
| Ingestion +------>| Mapping +------>|   Integration   |
|           | Raw   |         | RDF   |                 |
+-----------+ data  +---------+ data  +--------+--------+
      ^                                        |
 Raw  |                                        | RDF
 data |                                        | data
      |                                        |
      |                                        v
+-----+----+                             +-----------+
|   Data   |                             | Knowledge |
|  Source  |                             |   Graph   |
| (device) |                             | Database  |
+----------+                             +-----------+
~~~
{: #fig:kgc-pipeline-architecture title="High-level architecture of a Knowledge Graph Construction Pipeline" artwork-align="center"}

These steps are the following: ingestion, mapping, and integration.

### Ingestion

Represents the first step in the creation of the knowledge graph. This step is realized by means of collectors that ingest raw data from the selected data source. These collectors implement data access protocols which are specific to the technology and type of the data source. For instance, when it comes to network management protocols based on YANG, these protocols can be NETCONF {{?RFC6241}}, RESTCONF {{?RFC8040}} and gNMI {{GNMI}}.

Two main types of data sources are identified based on the techniques used to ingest the data, namely, batch and streaming. In the case of batch data sources data are pulled (once or periodically) from the data source.

Regarding streaming data sources, the collector subscribes to a YANG server to receive notifications of YANG data periodically or upon changes in the data source (e.g., a network device whose interface goes down). These subscriptions can be realized, either based on configuration or dynamically, using mechanisms like YANG Push {{?RFC8641}}. But additionally, another common scenario is the use of message broker systems like Apache Kafka for decoupling the ingestion of streams of YANG data {{?I-D.netana-nmop-yang-message-broker-integration}}. Hence, knowledge graph collectors could also support the ingestion of YANG data from these kinds of message brokers.

### Mapping

This second step consists at receiving the raw data data from the Ingestion step. Here, the raw data is mapped to the concepts captured in one or more ontologies. By applying these mapping rules, the raw data is semantically annotated and transformed into RDF data. Depending on the nature of the raw data, different techniques can applied.

In the case of (semi-)structured data such as tabular data (e.g., CSV, relational databases) or hierarchical data (e.g., JSON, XML) these mappings can be defined by using declarative languages like RDF Mapping Language (RML) {{Iglesias-Molina2023}}.

RML is a declarative language that is currently being standardized within the W3C Knowledge Graph Construction Community group {{W3C-KGC}} that allows for defining mappings rules for raw data encoded in semi-structured formats like XML or JSON. The benefits of using a declarative language like RML are twofold: i) the engine that implements the RML rules is generic, thus the mappings rules are decoupled from the code; ii) the explicit representation of mapping and transformation rules as part of the knowledge graph provides data lineage insights that can greatly improve data quality and the troubleshooting of data pipelines. RML is making progress towards becoming a standard, but support of additional YANG encoding formats like CBOR {{?RFC8949}} or Protobuf remains a challenge. The knowledge payload carried by CBOR and/or Protobuf is organized as knowledge objects transmitted by the mapping entities and received by the materialization entities. The use of knowledge objects allows them to easily "cut" knowledge graphs into smaller pieces, transmit them, and "paste" and/or "glue" the pieces onto the destination knowledge graph. Consistency is retained by making the same ontologies be used with the particular knowledge objects.

### Integration

This is the final step of the knowledge graph creation. This step receives as an input the knowledge object that contains RDF data generated in the Mapping step, which has easily manageable semantic triples---or quadruples---, as well as metadata to contextualize them and facilicate the incorporation of the knwoledge to the local knowledge graph storage element. At this point, the RDF data can be sent to an RDF triple store like Apache Jena Fuseki {{Fuseki}} for consumption via SPARQL. But alternatively, this step may transform the RDF data into an LPG structure and store the resulting data in a graph database like Neoj4 {{Neo4j}}. Similarly, the RDF data could also be transformed into the ETSI NGSI-LD standard {{ETSI-GS-CIM-009}} and stored in an NGSI-LD Context Broker.

# Challenges

Ontology development:
: Time-consuming task that requires skills in knowledge management and conceptual modeling. Additionally, ontology developers should maintain a tight coordination with domain owners and ontology users. Following a standard methodology like LOT provides guidance in the process but still, the development of the ontology requires manual work. Tools that can produce or bootstrap ontologies from existing data models in a semi-automatic, or even automatic, are desirable. In this sense, data models could include explicit semantics in the data models, in the same way that JSON-LD {{JSON-LD}} or CSVW {{CSVW}} include metadata indicating which concepts from concepts are referenced by the data.

Pipeline performance:
: To integrate the raw data from the original data source into the knowledge graph entails several steps as described before. This steps add an extra latency before having the data stored in the knowledge graph for consumption. This latency can be an important limitation for real-time analytics use cases.

Scalability:
: The knowledge graph must be able to integrate massive amounts of data collected from the network. Distributed and federated architectures can improve the scalability of a global, composable knowledge graph. However, these architectures add complexity to the management of knowledge graph as well as extra latency when federating requests.

Virtualization:
: The common approach for data integration is by materializing the data in the knowledge graph, which entails duplicating the data. However, this approach presents multiple limitations in terms of data governance and data cadence. Regarding data governance, having copies of the original data hampers keeping track of all the available data. With respect to data cadence, in particular for batch data sources, data are periodically pulled from the source at particular frequency, which might not be optimal depending on the use case. In this sense, data virtualization introduces a new data access technique that can overcome these limitations. With this technique, the knowledge graph defines pointers to the data at the original source, and the KGC pipeline performs the ingestion and mapping of the data, and eventually the delivery of data to the consumer, only when requested on demand.

# Security Considerations

Access control to data:
: The knowledge graph becomes an integrator of data, and, in many cases, sensible. Therefore, data access control mechanisms must be present to ensure that only authorized consumers can discover and access data from the knowledge graph. Access control policies based on roles or attributes are common approaches, but additional aspects like sensitivity of data could be included in the policy.

Integrity and authenticity of mappings:
: The declaration of mappings of raw data to concepts in ontologies is a critical step in the knowledge graph construction. Unauthorized mappings, or even tampered mappings, can lead to security breaches and anomalies producing a great impact on  analytics and machine learning applications that consume data from the knowledge graph. To protect consumers from these scenarios, the knowledge graph must include mechanisms that verify the correctness, authenticity, and integrity of the mappings used in the construction of the graph. Only data owners, as accountable of their data, should be authorized to define and deploy mappings for the knowledge graph construction.

Data provenance:
: Keeping track of the history of data as they go through the knowledge graph construction pipeline can improve the quality of the data of the knowledge graph. As part of the knowledge graph construction, signatures can be appended to the data {{?I-D.lopez-opsawg-yang-provenance}}, can help in verifying that such data come from the golden data source, and therefore, that the data can be trusted.

# IANA Considerations

This document has no IANA actions.

# Open Issues

* Should this document provide guidelines for generating URIs of nodes/subjects in the knowledge graph? Take into account there are several levels of abstraction device vs network/service level. For example, the URI that identifies a network interface cannot be generated only from the name of the interface as there could conflicts with other interfaces of other network devices having the same name.

* Implementations? References to examples based on open-source implementations. Integration with YANG-Push-Kafka architecture. Target future hackathons.

--- back

# NETCONF Data Sources

This appendix presents a scenario that demonstrates the construction of a knowledge graph based on YANG data collected from a NETCONF server. In particular, the scenario tackles the creation of a data catalog based on a knowledge graph that keeps registry of the YANG data sources and the YANG data models that they implement.

As described in {{?I-D.ietf-netconf-yang-library-augmentedby}}, data catalog implementations backed by knowledge graphs provide powerful solutions that can easily incorporate additional context to the catalog. As an evolution of the YANG Catalog service, the resulting knowledge graph facilitates the navigation across dependencies of YANG modules, but more importantly, enables the combination of these data with other data silos such as the network topology {{?RFC8345}}  or network hardware inventory {{?I-D.ietf-ivy-network-inventory-yang}}.

To create a knowledge graph that supports the data catalog, the proposed approach is based on collecting data from the YANG Library from devices running in the network, in this case, from a NETCONF server. For this, the RML engine queries the NETCONF server to retrieve the YANG Library data, and then, applies the RML mappings to transform the YANG data into RDF according to the target ontology.

This prototype was conducted as part of the paper "Declarative Construction of Knowledge Graphs from NETCONF Data Sources" sent to the Semantic Web Journal (currently under review): https://www.semantic-web-journal.net/content/declarative-construction-knowledge-graphs-netconf-data-sources-0

## Prototype Architecture

A high-level architecture of the prototype that validates the implementation is shown below:

~~~
                         |
                         |
                         | RML
                         | Mappings
                         v
+-----------+       +---------+       +-----------------+
|           |       |         |       |                 |
| Ingestion +------>| Mapping +------>|   Integration   |
|           | XML   | (BURP)  | RDF   |                 |
+-----------+ data  +---------+ data  +--------+--------+
      ^                                        |
 XML  |                                        | RDF
 data |                                        | data
      |                                        |
      |                                        v
+-----+------+                           +-----------+
|  NETCONF   |                           | Knowledge |
|  Server    |                           |   Graph   |
| (netopeer2)|                           | Database  |
+------------+                           +-----------+
~~~
{: #fig:netconf-prototype title="Architecture of prototype to construct knowledge graph from NETCONF data source" artwork-align="center"}

BURP was selected as the open-source implementation of an RML engine that was chosen for this prototype. The NETCONF server is emulated using the netopeer2.

## Target Ontology

The YANG Library Ontology was developed to represent the implementation details of YANG module and submodules, along with their interdependencvies, in the different datastores of YANG server. The ontology was developed following the LOT methodology and is publicly available at: https://w3id.org/yang/library

The code of the ontology and all related artifacts are publicly available on GitHub: https://github.com/candil-data-fabric/yang-library-ontology

## KGC Pipeline

In addition to the YANG Library Ontology, the YANG Server Ontology was developed to represent YANG data sources such as NETCONF servers and operations to retrieve data from them such as queries or subscriptions. Similarly, this ontology was developed following the LOT methodology and is publicly available at: https://w3id.org/yang/server

The code of the ontology and all related artifacts are publicly available on GitHub: https://github.com/candil-data-fabric/yang-server-ontology

The YANG Server Ontology is used in combination with the RML vocabulary to describe the access to YANG servers, from which the collected data are transformed into RDF. In this sense, BURP was extendended to support the ingestion of YANG data from NETCONF servers using NETCONF queries.

The following subsections include excerpts of the raw XML data ({{fig:yang-lib-xml}}), RML mappings ({{fig:rml-netconf-yang-lib}}), and final RDF data ({{fig:yang-lib-xml}}). The complete examples can be found on: https://github.com/candil-data-fabric/yang-library-ontology/tree/main/knowledge-graph/xpath

### Raw data

~~~~xml
<modules-state xmlns="urn:ietf:params:xml:ns:yang:ietf-yang-library"
  xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0">
  <module-set-id>1</module-set-id>
  <module>
    <name>ietf-yang-patch</name>
    <revision>2017-02-22</revision>
    <schema>file:///etc/sysrepo/yang/ietf-yang-patch@2017-02-22.yang</schema>
    <namespace>urn:ietf:params:xml:ns:yang:ietf-yang-patch</namespace>
    <conformance-type>import</conformance-type>
  </module>
  <module>
    <name>ietf-ip</name>
    <revision>2018-02-22</revision>
    <schema>file:///etc/sysrepo/yang/ietf-ip@2018-02-22.yang</schema>
    <namespace>urn:ietf:params:xml:ns:yang:ietf-ip</namespace>
    <conformance-type>implement</conformance-type>
  </module>
</modules-state>
~~~~
{: #fig:yang-lib-xml title="Excerpt of YANG Library data collected from a NETCONF server" artwork-align="center"}

### Mappings

~~~~
@prefix yl: <https://w3id.org/yang/library#> .
@prefix ys: <https://w3id.org/yang/server#> .
@prefix rml: <http://w3id.org/rml/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix core: <https://ontology.unifiedcyberontology.org/uco/core/> .
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix observable: <https://ontology.unifiedcyberontology.org/uco/observable/> .
@base <https://netconf-rml-demo.org/> .

# Connection details to NETCONF server
<netconf-server-1> a ys:NetconfServer ;
    ys:socketAddress <netconf-server-1/socket-address> ;
    ys:serverAccount <netconf-server-1/account> ;
    ys:hostKeyVerification "false" ;
    ys:capability ys:XpathCapability ,
                  ys:YangLibrary1.0 .

<netconf-server-1/datastores/operational> a ys:OperationalDatastore ;
    ys:server <netconf-server-1> .

<netconf-server-1/datastores/running> a ys:RunningDatastore ;
    ys:server <netconf-server-1> .

<netconf-server-1/socket-address> a observable:SocketAddress ;
    observable:addressValue "localhost:830" .

<netconf-server-1/account> a ys:ServerAccount ;
    ys:username "netconf" ;
    core:hasFacet <netconf-server-1/account/authentication> .

<netconf-server-1/account/authentication> a observable:AccountAuthenticationFacet ;
    observable:password "netconf" ;
    observable:passwordType "plain-text" .

<filters/xpath/yang-library> a ys:XPathFilter ;
    ys:xpathValue "/yanglib:modules-state";
    ys:namespace [ a ys:Namespace ;
      ys:namespacePrefix "yanglib" ;
      ys:namespaceURL "urn:ietf:params:xml:ns:yang:ietf-yang-library" ;
    ];
.

<#TriplesMap> a rml:TriplesMap;
  rml:logicalSource [ a rml:LogicalSource;
    rml:source [ a ys:Query, rml:Source ;
      ys:sourceDatastore <netconf-server-1/datastores/operational> ;
      ys:filter <filters/xpath/yang-library>
    ];
    rml:referenceFormulation [ a ys:NetconfQuerySource ;
      rml:namespace [ a rml:Namespace ;
        rml:namespacePrefix "yanglib" ;
        rml:namespaceURL "urn:ietf:params:xml:ns:yang:ietf-yang-library" ;
      ];
    ];
    rml:iterator "/yanglib:modules-state/yanglib:module";
  ];
  rml:subjectMap [ a rml:SubjectMap;
    rml:template "http://example.org/module/{yanglib:name/text()}:{yanglib:revision/text()}";
    rml:class yl:Module;
  ];
  rml:predicateObjectMap [ a rml:PredicateObjectMap;
    rml:predicateMap [ a rml:PredicateMap;
      rml:constant yl:moduleName;
    ];
    rml:objectMap [ a rml:ObjectMap;
      rml:reference "yanglib:name/text()";
      rml:datatype xsd:string;
    ];
  ];
  rml:predicateObjectMap [ a rml:PredicateObjectMap;
    rml:predicateMap [ a rml:PredicateMap;
      rml:constant yl:revisionDate;
    ];
    rml:objectMap [ a rml:ObjectMap;
      rml:reference "yanglib:revision/text()";
      rml:datatype xsd:date;
    ];
  ];
  rml:predicateObjectMap [ a rml:PredicateObjectMap;
    rml:predicateMap [ a rml:PredicateMap;
      rml:constant yl:namespace;
    ];
    rml:objectMap [ a rml:ObjectMap;
      rml:reference "yanglib:namespace/text()";
      rml:datatype xsd:anyURI;
    ];
  ];
.
~~~~
{: #fig:rml-netconf-yang-lib title="RML mappings that collect YANG Library from a NETCONF server and map them to the YANG Library Ontology" artwork-align="center"}

### RDF data

~~~~
<http://example.org/module/ietf-ip:2018-02-22>
  <https://w3id.org/yang/library#moduleName>
  "ietf-ip"^^<http://www.w3.org/2001/XMLSchema#string> .
<http://example.org/module/ietf-ip:2018-02-22>
  <https://w3id.org/yang/library#revisionDate>
  "2018-02-22"^^<http://www.w3.org/2001/XMLSchema#date> .
<http://example.org/module/ietf-ip:2018-02-22>
  <https://w3id.org/yang/library#namespace>
  "urn:ietf:params:xml:ns:yang:ietf-ip"^^<http://www.w3.org/2001/XMLSchema#anyURI> .
<http://example.org/module/ietf-ip:2018-02-22>
  <http://www.w3.org/1999/02/22-rdf-syntax-ns#type>
  <https://w3id.org/yang/library#Module> .
<http://example.org/module/ietf-yang-patch:2017-02-22>
  <https://w3id.org/yang/library#moduleName>
  "ietf-yang-patch"^^<http://www.w3.org/2001/XMLSchema#string> .
<http://example.org/module/ietf-yang-patch:2017-02-22>
  <https://w3id.org/yang/library#revisionDate>
  "2017-02-22"^^<http://www.w3.org/2001/XMLSchema#date> .
<http://example.org/module/ietf-yang-patch:2017-02-22>
  <https://w3id.org/yang/library#namespace>
  "urn:ietf:params:xml:ns:yang:ietf-yang-patch"^^<http://www.w3.org/2001/XMLSchema#anyURI> .
<http://example.org/module/ietf-yang-patch:2017-02-22>
  <http://www.w3.org/1999/02/22-rdf-syntax-ns#type>
  <https://w3id.org/yang/library#Module> .
~~~~
{: #fig:rdf-yang-lib title="Excerpt of RDF triples generated using the RML mappings and the YANG Library data" artwork-align="center"}

# Acknowledgments
{:numbered="false"}
This document is based on work partially funded by the EU Horizon Europe projects aerOS (grant agreement no. 101069732) and ROBUST-6G (grant agreement no. 101139068).

The authors would like to thank Med, Benoit, Lionel, and Thomas for their review and valuable comments.
