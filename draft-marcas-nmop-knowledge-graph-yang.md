---
title: "Knowledge Graphs for YANG-based Network Management"
abbrev: "knowledge-graph-yang"
category: info

docname: draft-marcas-nmop-knowledge-graph-yang-latest
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

normative:
  RFC7950:

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
    target: https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md
  Fuseki:
    title: Apache Jena Fuseki
    target: https://jena.apache.org/documentation/fuseki2/
  JSON-LD:
    title: "JSON-LD 1.1: A JSON-based Serialization for Linked Data"
    author:
      organization: W3C
    target: https://www.w3.org/TR/json-ld11/
    date: July 2020
  oc-interfaces:
    title: openconfig-interfaces modules
    author:
      organization: OpenConfig
    target: https://openconfig.net/projects/models/schemadocs/yangdoc/openconfig-interfaces.html
  Poveda-Villalon2022:
    title: "LOT: An industrial oriented ontology engineering framework"
    author:
      organization: Engineering Applications of Artificial Intelligence
    target: https://doi.org/10.1016/j.engappai.2022.104755
    date: May 2022
  Iglesias-Molina2023:
    title: "The RML Ontology: A Community-Driven Modular Redesign After a Decade of Experience in Mapping Heterogeneous Data to RDF"
    autor:
      organization: The Semantic Web – ISWC 2023
    target: https://doi.org/10.1007/978-3-031-47243-5_9
    date: October 2023
  Neo4j:
    title: rdflib-neo4j - RDFLib Store backed by neo4j
    target: https://github.com/neo4j-labs/rdflib-neo4j
  OWL:
    title: "OWL 2 Web Ontology Language Document Overview (Second Edition)"
    author:
      organization: W3C
    target: https://www.w3.org/TR/owl2-overview/
    date: December 2012
  RDF:
    title: "Resource Description Framework (RDF): Concepts and Abstract Syntax"
    author:
      organization: W3C
    target: https://www.w3.org/TR/rdf11-concepts/
    date: February 2014
  RDFS:
    title: "RDF Schema 1.1"
    author:
      organization: W3C
    target: https://www.w3.org/TR/rdf-schema/
    date: February 2014
  SHACL:
    title: "Shapes Constraint Language (SHACL)"
    author:
      organization: W3C
    target: https://www.w3.org/TR/shacl/
    date: July 2017
  SPARQL:
    title: "SPARQL 1.1 Query Language"
    author:
      organization: W3C
    target: https://www.w3.org/TR/sparql11-query/
    date: March 2013
  Tailhardat2023:
    title: "Designing NORIA: a Knowledge Graph-based Platform for Anomaly Detection and Incident Management in ICT Systems"
    autor:
      organization: "KGCW’23: 4th International Workshop on Knowledge Graph Construction"
    target: https://ceur-ws.org/Vol-3471/paper3.pdf
    date: May 2023
  W3C-KGC:
    title: Knowledge Graph Construction Community Group
    author:
      organization: W3C
    target: https://www.w3.org/community/kg-construct/

--- abstract

The success of the YANG language and YANG-based protocols for managing the network has unlocked new opportunities in network analytics. However, the wide heterogeneity of YANG models hinders the consumption and analysis of network data. Besides, data encoding formats and transport protocols will differ depending on the network management protocol supported by the network device. These challenges call for new data management paradigms that facilitate the discovery, understanding, integration and access to silos of heterogenous YANG data, abstracting from the complexities of the network devices.

This document introduces the knowledge graph paradigm as a solution to this data management problem, with focus on YANG-based network management. The document provides background on related topics such as ontologies and graph standards, and shares guidelines for implementing knowledge graphs from YANG data.

--- middle

# Introduction

The size and complexity of networks keeps increasing, thus the path towards enabling an autonomous network requires the combination of network telemetry mechanisms {{?RFC9232}}. These mechanisms range from legacy protocols like SNMP to the recent model-driven telemetry (MDT) based on the YANG language {{!RFC7950}} and network management protocols such as NETCONF {{?RFC6241}}, RESTCONF {{?RFC8040}}, or gNMI {{GNMI}}.

MDT, in particular, has drawn the attention of the network industry owing to the benefits of modeling configuration and status data of the network with a formal data modeling language like YANG. However, since the inception of YANG, the network industry has experienced the massive creation of YANG data models developed by vendors, standards developing organizations (e.g., IETF), and consortia (e.g., OpenConfig). In turn, these data models target different abstraction layers of the network, namely, network elements, and network service {{?RFC8199}}. Additionally, YANG data models may augment (or deviate from) other models to define new features (or remove or adjust existing ones) depending on the implementation. In summary, this trend has resulted into a wide variety of independent YANG data models, hence, the creation of data silos in the network. Refer to Sections 4.1 and 4.4 of {{?I-D.boucadair-nmop-rfc3535-20years-later}} for a discussion on the fragmented YANG ecosystem and the integration complexity issues.

Such amount and heterogeneity of YANG data models has hindered the collection and combination of network data for advanced network analytics. The current landscape shows different YANG models referencing the same concepts in a different way. For example, the IETF "ietf-interface" {{?RFC8343}} and OpenConfig "openconfig-interfaces" {{oc-interfaces}} follow different structures and syntax, but both reference the same "interface" concept.

Similarly, there are YANG models, like Service Assurance {{?RFC9418}}, that convey semantic relationships with other concepts via identifiers. {{ex-sain-device-tree}} depicts the YANG tree diagram {{?RFC8340}} for a subservice augmentation where the leaf "device" hints a relationship between the "subservice" concept and the "device" concept.

~~~
module: ietf-service-assurance-device

  augment /sain:subservices/sain:subservice/sain:parameter:
    +--rw parameters
      +--rw device    string
~~~
{: #ex-sain-device-tree title="YANG tree diagram of a subservice augmentation." artwork-align="center"}

The extraction of this hidden knowledge from YANG models would enable the integration of YANG data silos at a conceptual level, regardless of the physical implementation (i.e., the YANG schema, syntax, and encoding format). In this regard, the knowledge graph is a promising technology that can link data silos based on common concepts like "device" that are captured in ontologies. Besides, by transforming the YANG data into a graph structure the relationships between data silos are represented as first class citizens in the graph instead of "foreign keys" where the relationship is made implicit. This document provides guidelines for building a knowledge graph for data sources based on the YANG language.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

## Terminology

This document defines the following terms:

Data materialization: Technique that collects data from remote data source and persists a copy the data in a target data storage. This process can also be seen as Extract-Transform-Load (ETL).

Data virtualization: Technique wherein an intermediate component (i.e., data virtualization layer) exposes data available in a remote data sources without creating an copy of the data. The data virtualization layer keeps pointers to the original location of data, so when a data consumer asks for these data, the virtualization layer collects the data from the source and directly serves the data to the consumer.

Ontology: Formal, shared representation of knowledge in a domain.

## Acronyms

CQ: Competency Question

ETL: Extract-Transform-Load

KG: Knowledge Graph

KGC: Knowledge Graph Construction

LOT: Linked Open Terms

LPG: Labelled Property Graph

OWL: Web Ontology Language

RDF: Resource Description Framework

RDFS: RDF Schema

RML: RDF Mapping Language

SAREF: Smart Applications REFerence

SHACL: Shapes Constraint Language

W3C: World Wide Web Consortium

# A Bief Introduction to Knowledge Graphs

## What is a Knowledge Graph?

A knowledge graph contains a collection of facts alongside what know we about them and represents following a graph structure. Knowledge graphs enable a contextualized understanding of data as the data (e.g., the individuals, instance) travel with the meaning of the data themselves (e.g., the concepts, knowledge). For example, a knowledge graph may contain data about an interface "eth0", but also, that an interface can be physical or virtual, belongs to a network device, and has a name, description, and an MTU.

To this end, knowledge graphs build upon on ontologies, which are explicit representations of conceptualizations in a specific domains. In other words, ontologies can be seen as representations of conceptual models following a formal logic that allows machines to understand and reason over them. In this regard, a conceptual model model, also known as information model, may translate into different data models depending on the data source technology {{?RFC3444}}.

By mapping data models (i.e., physical level) with the concepts represented in ontologies (i.e., conceptual level), we can find heterogenous datasets scattered in the network that reference common concepts such as "interface" or "device". Based on this semantic mapping, in addition to the flexibility of the graph structure, knowledge graphs enable the integration of heterogenous data based their semantics is what knowledge graphs can deliver.

## Key Graph Standards

The Resource Description Framework (RDF) {{RDF}} data model from the W3C Semantic Web has been considered as the standard graph data model given its maturity. For that reason, most of the knowledge graph implementations have relied upon the RDF standard and other standards from the Semantic Web like RDF Schema (RDFS) {{RDFS}}, Ontology Language (OWL) {{OWL}}, Shapes Constraint Language (SHACL) {{SHACL}}, and SPARQL {{SPARQL}}.

However, the late success of graph databases like Neo4j have proved the Labelled Property Graph (LPG) data model as an alternative for implementing knowledge graphs. Aiming to bridge the gap between these two graph data models, the W3C RDF-Star working group is investigating evolving RDF to facilitate the representation of statement about statements.

Similarly, the ETSI ISG CIM defined the NGSI-LD standard {{ETSI-GS-CIM-009}}, which builds upon two:

* An NGSI-LD information model which derives from the Labeled Property Graph (LPG) model and grounds on the RDF for a semantic annotation of the data in the graph.
* The NGSI-LD API, which defines a REST API for building and interacting with the graph.

# Knowledge Graph Construction (KGC)

The construction of a knowledge graph can be divided into two main activities: ontology development ({{sec-onto}}) and knowledge graph construction pipeline ({{sec-pipe}}).

## Ontology Development {#sec-onto}

Ontologies provide the formal representation of the conceptual models that capture the semantics of data, and building on this, the integration of data in the knowledge graph. Ontologies can be developed following different techniques, ranging from manual to fully automated, depending on the characteristics of the data to be integrated in the knowledge graph (e.g., format or schema).

### Automatic knowledge Extraction from YANG Models

The extraction of knowledge from YANG models can be automated, in particular, by analyzing YANG identities to generate controlled vocabularies and taxonomies.

{{!RFC7950}} defines a YANG identity as "globally unique, abstract, and untyped identity", therefore, a relation between a YANG identity and a concept is straightforward. Additionally, YANG identities can inherit from other YANG identities via the "base" statement. These ideas align with the notion of a taxonomy, where concepts are hierarchically linked with other concepts.

To support the creation of knowledge structures like taxonomies or thesauri, the W3C standardized the Simple Knowledge Organization System (SKOS). In such ontology, a concept scheme comprises a set of concepts that can be linked with other concepts via hierarchical and associative relations. Typically, a YANG model containing YANG identities can be represented as an instance of the "skos:ConceptScheme" class. Next, all YANG identities included in a YANG model can be represented as "skos:Concept instances" that are contained in the concept scheme. Lastly, those YANG identities that include the "base" statement, the respective SKOS concept will include a relation "skos:broader" whose range is the SKOS concept representing the parent YANG identity.

### Standard Development Methodologies

Automating the extraction of all the knowledge from YANG models is impossible, and therefore, manual intervention from domain experts is required. To ease this process, a recommended practice is to develop the ontology by following a standard methodology like Linked Open Terms (LOT) {{Poveda-Villalon2022}}.

LOT is an ontology development methodology that adopts best practices from agile software development. The methodology has been widely used in European projects as well as in the creation of the ETSI SAREF ontology and its extensions. Precisely, with SAREF Ontology ETSI tackled a similar problem in the scope of IoT, where there is a heterogeneous variety of standard data models and protocols. The methodology iterates over a workflow of the following four activities:

1. ontology requirements specification
2. ontology implementation
3. ontology publication, and
4. ontology maintenance.

The workflow starts with the specification of requirements that the ontology must fulfill. To that aim, the methodology requires collecting knowledge from domain experts, but also by analyzing the data sources (e.g., network devices) and schemas for the data (e.g., YANG models) to be ingested and integrated in the knowledge graph. LOT recommends several approaches such as competency questions (CQs), natural language statements, or tabular information inspired by METHONTOLOGY.

## Knowledge Graph Construction Pipeline  {#sec-pipe}

The construction of a knowledge graph is supported by a data pipeline that follows the archetypical Extract-Transform-Load (ETL), wherein the raw data is collected from the source(s), transformed, and finally, stored for consumption. The knowledge graph creation pipeline can thus be split into multiple steps as depicted in {{ex-construction}}.

~~~
+-----------+       +---------+       +-----------------+
|           |       |         |       |                 |
| Ingestion +------>| Mapping +------>| Materialization |
|           | Raw   |         | RDF   |                 |
+-----------+ data  +---------+ data  +--------+--------+
      ^      (YANG)                            |
 Raw  |                                        | RDF
 data |                                        | data
(YANG)|                                        |
      |                                        v
+-----+----+                             +-----------+
|   Data   |                             | Knowledge |
|  Source  |                             |   Graph   |
| (device) |                             +-----------+
+----------+
~~~
{: #ex-construction title="High-level architecture of a Knowledge Graph Construction Pipeline" artwork-align="center"}

These steps are the following: ingestion, mapping, and materialization.

### Ingestion

Represents the first step in the creation of the knowledge graph. This step is realized by means of collectors that ingest raw data from the selected data source. These collectors implement data access protocols which are specific to the technology and type of the data source. When it comes to network management protocols based on YANG, these protocols can be NETCONF {{?RFC6241}}, RESTCONF {{?RFC8040}} and gNMI {{GNMI}}.

Two main types of data sources are identified based on the techniques used to ingest the data, namely, batch and streaming. In the case of batch data sources data are pulled (once or periodically) from the data source. This could be represented by queries sent to a YANG server like an SDN controller to fetch the network topology {{?RFC8345}}.

Regarding streaming data sources, the collector subscribes to a YANG server to receive notifications of YANG data periodically or upon changes in the data source (e.g., a network device whose interface goes down). These subscriptions can be realized, either based on configuration or dynamically, using mechanisms like YANG Push {{?RFC8641}}. But additionally, another common scenario is the use of message broker systems like Apache Kafka for decoupling the ingestion of streams of YANG data {{?I-D.netana-nmop-yang-message-broker-integration}}. Hence, knowledge graph collectors could also support the ingestion of YANG data from these kinds of message brokers.

### Mapping

This second step consists at receiving the raw data data from the Ingestion step. Here, the raw data is mapped to the concepts capture in one or more ontologies. By applying these mapping rules, the raw data is semantically annotated and transformed into RDF data. These mappings can be declared using declarative languages like RDF Mapping Language (RML) {{Iglesias-Molina2023}}.

RML is a declarative language that is currently being standardized within the W3C Knowledge Graph Construction Community group {{W3C-KGC}} that allows for defining mappings rules for raw data encoded in semi-structured formats like XML or JSON. The benefits of using a declarative language like RML are twofold: i) the engine that implements the RML rules is generic, thus the mappings rules are decoupled from the code; ii) the explicit representation of mapping and transformation rules as part of the knowledge graph provides data lineage insights that can greatly improve data quality and the troubleshooting of data pipelines. RML is making progress towards becoming a standard, but support of additional YANG encoding formats like CBOR {{?RFC8949}} or Protobuf remains a challenge.

### Materialization

This is the final step of the knowledge graph creation. This step receives as an input the RDF data generated in the Mapping step. At this point, the RDF data can be sent to an RDF triple store like Apache Jena Fuseki {{Fuseki}} for consumption via SPARQL. But alternatively, this step may transform the RDF data into an LPG structure and store the resulting data in a graph database like Neoj4 {{Neo4j}}. Similarly, the RDF data could also be transformed into the ETSI NGSI-LD standard and stored in an NGSI-LD Context Broker.

# Knowledge Graph Applications

Network performance KPIs:
: The integration of data at different levels of abstraction in the network can facilitate the computation of network performance KPIs, such as throughput or packet loss ratio. By integrating data silos such as the network topology with the status of network interfaces, a network analytics application could ask the knowledge graph to compute the throughput or packets loss ratio at a specific link in the network.

Anomaly detection and incident management:
: Projects like NORIA {{Tailhardat2023}} have demonstrated how knowledge graphs can help in the detection of anomalies in network systems. This approach links data pertaining to different data silos like network infrastructure, logs, alarms, and ticketing. In another example, the combination network topology data with data about network interface status, consumers of the knowledge graph can detect network anomalies like link fault because an network interface has been unexpectedly disabled but it was configured to be enabled.

Service assurance:
: A knowledge graph can enable the implementation of the service assurance for intent-based networking architecture defined in {{?RFC9417}}. Precisely, this architecture,  and the companion YANG data models from RFC 9418, define an assurance graph where dependencies among network services and their associated health and symptoms are captured. All these data, which can be further linked with other data silos like network topology or network interface status, can be naturally integrated and represented in a knowledge graph.

Network digital twin:
: Knowledge graph are considered promising candidates for the realization of network digital twins {{?I-D.irtf-nmrg-network-digital-twin-arch}}. The ability to integrate heterogenous silos of data, in combination with the explicit representation of the semantics of the data, make knowledge graph a powerful technology for building and connecting multiple network digital twins. In addition, the representation of concepts by means of ontologies, produces abstract representations of network digital twins, regardless of the complexities of the underlying technologies. For instance, an abstract representation of a network topology Digital Map {{?I-D.havel-nmop-digital-map}} in the knowledge graph can be translated into a descriptor or data model that is specific to the technology used (e.g., KNE, ContainerLab, or OSM).

Evolution of YANG Catalog:
: The flexibility and extensibility of knowledge graphs have made them a popular choice for implementing data catalogs. The purpose of a data catalog is to provide consumers with a registry of datasets exposed by data sources where to find data of interest. Additionally, these datasets can be linked to the (business) concepts that they refer to, so that consumers can search for datasets based on relevant concepts such as “interface”. Taking inspiration from these implementations, and building on a knowledge graph, the YANG Catalog could evolve towards a data catalog, where the YANG modules represent those datasets of interest. The dependencies between YANG models (import, deviations, augments) can be naturally represented in the knowledge graph. In turn, these YANG models can be linked with concepts that are represented in ontologies. Additionally, these YANG models, can be combined with the implementation details of network devices yang lib augment {{?I-D.lincla-netconf-yang-library-augmentation}} that could be part of an inventory {{?I-D.ietf-ivy-network-inventory-yang}}.

Contextualized telemetry data:
: Having context of how YANG telemetry data {{?I-D.ietf-opsawg-collected-data-manifest}} is being collected can improve the understanding of the data for network analytics or closed-loop automation. Knowledge graphs can help in this task by linking the collected data with: (i) the metadata that characterizes the platform producing the data; and (ii) the metadata that characterizes how and when the data were metered.

# Challenges

Ontology development:
: Time-consuming task that requires skills in knowledge management and conceptual modeling. Additionally, ontology developers should maintain a tight coordination with domain owners and ontology users. Following a standard methodology like LOT provides guidance in the process but still, the development of the ontology requires manual work. Tools that can produce or bootstrap ontologies from existing YANG data models in a semi-automatic, or even automatic, are desirable. In this sense, the future release of the YANG language could be extended to facilitate this task at design time. YANG data models could include explicit semantics in the data models, in the same way that JSON-LD {{JSON-LD}} or CSVW {{CSVW}} include metadata indicating which concepts from concepts are referenced by the data. In the current version of YANG, this could be achieved at runtime using the YANG Metadata extension {{?RFC7952}}. With this extension, YANG data models could include additional metadata to indicate the ontology concept a YANG data node is referring to, though this approach only works at runtime, and additionally, it would require augmenting existing YANG data models.

Pipeline performance:
: To integrate the raw data from the original source into the knowledge graph entails several steps as described before. This steps add an extra latency before having the data stored in the knowledge graph for consumption. This latency can be an important limitation for real-time analytics use cases.

Scalability:
: The knowledge graph must be able to integrate massive amounts of data collected from the network. Distributed and federated architectures can improve the scalability of a global, composable knowledge graph. However, these architectures add complexity to the management of knowledge graph as well as extra latency when federating requests.

Virtualization:
: The common approach for data integration is by materializing the data in the knowledge graph, which entails duplicating the data. However, this approach presents multiple limitations in terms of data governance and data cadence. Regarding data governance, having copies of the original data hampers keeping track of all the available data. With respect to data cadence, in particular for batch data sources, data are periodically pulled from the source at particular frequency, which might not be optimal depending on the use case. In this sense, data virtualization introduces a new data access technique that can overcome these limitations. With this technique, the knowledge graph defines pointers to the data at the original source, and the KGC pipeline performs the ingestion and mapping of the data, and eventually the delivery of data to the consumer, only when requested on demand.

Network configuration:
: This document has focused on integrating telemetry data in the knowledge graph for monitoring purposes. But knowledge graphs could also be leveraged for integrating data related to the configuration of devices and services in the network. This approach could enable closed-loop network management since both configuration and operational data are stored in the knowledge graph.

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

* Should RML mappings reference data at the YANG level using XPath or subtree filters? Or references should remain based on the actual encoding format used by the network management protocol, e.g., JSON, XML.

* Should this document provide guidelines for generating URIs of nodes/subjects in the knowledge graph? Take into account there are several levels of abstraction device vs network/service level. For example, the URI that identifies a network interface cannot be generated only from the name of the interface as there could conflicts with other interfaces of other network devices having the same name.

* Definition of YANG data sources with formal vocabulary, similar to what Web of Things ontology has done for MQTT or REST APIs or D2RQ ontology for relational databases. Having the specification of the data source in the knowledge graph improves provenance and decouples the configuration from the implementation, e.g., via custom INI config file.

* Implementations? References to examples based on open-source implementations. Integration with YANG-Push-Kafka architecture. Target future hackathons.

* Document focused on YANG data sources. Should the document open the scope to other kinds of data sources like IPFIX?

--- back

# Acknowledgments
{:numbered="false"}
This document is based on work partially funded by the EU Horizon Europe projects aerOS (grant agreement no. 101069732) and ROBUST-6G (grant agreement no. 101139068).

The authors would like to thank Mohamed Boucadair and Benoit Claise for their review and valuable comments.
