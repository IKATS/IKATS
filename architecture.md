# ![IKATS Logo](https://ikats.github.io/img/Logo-ikats-icon.png) IKATS Main Architecture

## Architecture principles

IKATS is developed following the main principles of a microservices achitecture. We have structured the components in containers using [Docker](https://docs.docker.com/engine). And the deployments of all the system is realised depending on the target :
- Docker Compose for the [IKATS Sandbox](https://github.com/IKATS/ikats-sandbox)
- [Kubernetes](https://kubernetes.io/) in a cluster.
The containers images are build from the Github sources by the Docker Hub services and publicily made available through the [IKATS Docker repositories](https://hub.docker.com/r/ikats/).

These choices are lead to improve the **scalability** and the **reliability** of the application

The IKATS Toolbox is composed of core components and a set of user tools, the _IKATS operators_ and their associated _IKATS viztools_. The Operators and VizTools are API dependant of the IKATS core. They are deployed at runtime. This allows any power user to modify or contribute to any Viztool or Operator (following the [CONTRIBUTING](CONTRIBUTING.md) guide) 

- _IKATS software core_:
  - [ikats-ingestion](https://github.com/IKATS/ikats-ingestion): IKATS data ingestion application
  - [ikats-datamodel](https://github.com/IKATS/ikats-datamodel): java resources API (and java operators)
  - [ikats-hmi](https://github.com/IKATS/ikats-hmi): IKATS Graphical User Interface (javascript)
  - [ikats-pybase](https://github.com/IKATS/ikats-pybase): python core of IKATS (for operators: catalog management and running engine)
  - [ikats-baseimages](https://github.com/IKATS/ikats-baseimages): docker base stack components
- _IKATS operators_:
  - [op-*](https://github.com/IKATS?q=op-): list of all IKATS operators
- _IKATS viztools_:
  - [vt-*](https://github.com/IKATS?q=vt-): list of all IKATS viztools (results visualization tools)

## Logical infrastructure

Here are represented the components interactions within an IKATS deployement.

> NOTE: The following schemas describes the IKATS reference cluster and could be adapted to another kind of deployment

![Doc Archi](img/LogicalView.png)

## Physical infrastructure

In order to implement the above logical structure, IKATS can be deployed on a cluster composed of a set of physical or virtual machines.
You can use as many computer machines as you need according to the computing power needs.
For example, by deploying additional **Worker Nodes**, you increase the amount of data that can be computed and/or the computing celerity of operators and visualization tools.

> NOTE: The following schemas describes the IKATS reference cluster and could be adapted to another kind of deployment.
> Following properties in terms of resource (vcpu/RAM per node) can be used for a cluster:

![Doc Archi](img/PhysicalView.png)

## Third party components

IKATS could compute Big Data database of industrial Time Series thanks to Apache Spark. Thus the underlying resources and storage components are those of the Apache Hadoop ecosystem:

- Hadoop distributed filesystem - HDFS
- Apache Spark for the computing backend
- OpenTSDB for the time series storage
- HBase as the OpenTSDB database
- PostgreSQL for the IKATS datamodel
- Scikit-learn library
- Numpy library
- Docker for containers build
