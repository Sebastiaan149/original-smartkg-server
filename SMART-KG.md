# SMART-KG: Democratizing access to Open Knowledge Graphs by enabling Web-scale SPARQL Querying

* [Abstract](#abstract)
* [SmartKG Installation](#smartkg-installation)
  * [SmartKG Server](#smartkg-server)
  * [SmartKG Client](#smartkg-client)
* [Servers and clients used](#servers-and-clients-used)
* [Experiments](#experiments)
  * [Hardware configuration](#hardware-configuration)
  * [Software configuration](#software-configuration)
  * [Resource monitoring](#resource-monitoring)
  * [Start scripts](#start-scripts)
  * [Queries and datasets used](#queries-and-datasets-used)
  * [Results](#results)

## Abstract

Knowledge Graphs (KGs), have recently gained popularity within large companies as centralized, graph-structured, knowledge models serving various applications. However, while also Open KGs have been created and established and even interconnected through the  principles of Linked Data, mechanisms to effectively and efficiently serve, access and process such open, decentralized KGs remain limited: while Linked Data (LD) provides standards for publishing (RDF) and querying (SPARQL) KGs on the Web, serving efficient, scalable query interfaces for a large number of users on LD datasets is practically impossible, as query limitations and timeouts on publicly available SPARQL endpoints show. Solutions such as Triple Pattern Fragments (TPF) attempt to tackle this problem by pushing most of the query processing workload to the client side, but they still suffer from poor performance and unnecessary transfer of irrelevant data on complex queries with a large number of (intermediate) results. Based on this idea, this paper aims at a new generation of KG clients with an emphasis to balance the load between servers and clients, while also significantly reducing data transfer volume, by shipping locally cached, compressed KG partitions. We present a cost-based query optimizer for such smart clients, combining TPF and partition shipping. Our evaluations show that our approach outperforms the query performance of state-of-the-art clientside solutions and increases server availability for more costeffective and balanced hosting of Open, decentralized KGs.


## SmartKG Installation

Build from source using maven
```
mvn install
```

Or to create a runnable jar with dependencies
```
mvn compile assembly:single
```

### SmartKG Server
* Create a configuration file 
```
{
  "title": "TPF/SmartKG Linked Data Fragments server",

  "metadatapath": "statsPart_watdiv1000M.json",
  "moleculesdatapath": "/libhdt/data/part_families_watdiv_1000M/hdt/",
  "datasourcetypes": {
    "HdtDatasource"       : "org.linkeddatafragments.datasource.hdt.HdtDataSourceType",
    "JenaTDBDatasource"   : "org.linkeddatafragments.datasource.tdb.JenaTDBDataSourceType"
  },

  "datasources": {
    "watdiv": {
      "title": "watdiv",
      "type": "HdtDatasource",
      "description": "WatDiv DataSet with an HDT back-end",
      "settings": { "file": "/data/watdiv.1000M.hdt" }
    }

  },
  "prefixes": {
    "rdf":         "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "rdfs":        "http://www.w3.org/2000/01/rdf-schema#",
    "xsd":         "http://www.w3.org/2001/XMLSchema#",
    "dc":          "http://purl.org/dc/terms/",
    "foaf":        "http://xmlns.com/foaf/0.1/",
    "dbpedia":     "http://dbpedia.org/resource/",
    "dbpedia-owl": "http://dbpedia.org/ontology/",
    "dbpprop":     "http://dbpedia.org/property/",
    "hydra":       "http://www.w3.org/ns/hydra/core#",
    "void":        "http://rdfs.org/ns/void#"
  }
}
```

* Run the server 

```
java -jar ldf-server.jar config.json
```

### SmartKG Client

* Create a configuration file
```
{
  "ExperimentName": "SmartKG-Exp1-watdiv100Mnew-Olaf-80Client-20MB-NoCache-testResult",
  "TimeInMin": "5",
  "datasource": "http://server:8080/watdiv",
  "server": "http://server:8080/molecule/watdiv/",
  "cacheReuse": false,
  "metadatapath": "Client.Java/statsPart_watdiv.json",
  "cachedpartitions": "Client.Java/downloadedPartitions/",
  "queriespath": "Queries/",
  "downloadedpartitions": "Client.Java/downloadedPartitions/",
  "prefixes": {
    "rdf":         "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "rdfs":        "http://www.w3.org/2000/01/rdf-schema#",
    "owl":         "http://www.w3.org/2002/07/owl#",
    "skos":        "http://www.w3.org/2004/02/skos/core#",
    "xsd":         "http://www.w3.org/2001/XMLSchema#",
    "dc":          "http://purl.org/dc/terms/",
    "dcterms":     "http://purl.org/dc/terms/",
    "dc11":        "http://purl.org/dc/elements/1.1/",
    "foaf":        "http://xmlns.com/foaf/0.1/",
    "geo":         "http://www.w3.org/2003/01/geo/wgs84_pos#",
    "dbpedia":     "http://dbpedia.org/resource/",
    "dbpedia-owl": "http://dbpedia.org/ontology/",
    "dbpprop":     "http://dbpedia.org/property/",
    "schema":      "http://schema.org/"
  }
}
```

* Run the client
```
java -jar smartKG-client-jar-with-dependencies.jar config.json
```


## Servers and clients used

* [SmartKG client/server](https://git.ai.wu.ac.at/beno/smartkg)
* [SaGe python server](https://github.com/sage-org/sage-engine) v 2.0.1
* [SaGe java client](https://github.com/sage-org/sage-jena) v1.1
* [Virtuoso Open-Source Edition](http://vos.openlinksw.com/owiki/wiki/VOS) v7.2.5.1
* [TPF client](https://github.com/LinkedDataFragments/Client.js) v2.0.5
* [BrTPF client/server](http://olafhartig.de/brTPF-ODBASE2016/)

## Experiments

The performance of SmartKG was evaluated in comparison with state-of-the-art SPARQL query engines using real and synthetic datasets and varying numbers of clients (1, 10, 20, 40, 80). 


### Hardware configuration
The clients were installed and evaluated on 80 physical machines with following hardware configuration:

* CPU: Intel(R) Core(TM) i7-7700 CPU @ 3.60GHz
* RAM: 32GB DDR4
* Storage: 512GB M.2 NVMe SSD
* Network: Realtek Gigabit Ethernet card (The network speed on each client was limited to 20 MBit using tc. This limitation only affects the connection between the client and the SPARQL server. Everything else is unrestricted) 
* OS: Fedora 29 (Linux Kernel 5.0.14)

The server software was run on a virtual machine with following configuration:

* vCPUs: 4 
* RAM: 32GB 

Specifications of the hypervisor the VM was running on:

* CPU:   Intel(R) Xeon(R) CPU E5-2650v2 @ 2.60GHz
* RAM: 384GB DDR3
* Storage: 16 GBit SAN (HDDs + SSD Cache)
* Network: Intel Gigabit Ethernet
* OS: CentOS7 (Linux Kernel 3.10.0)
* Hypervisor: QEMU + KVM (2.12)

### Software configuration

Example server configuration files can be found [here](https://git.ai.wu.ac.at/beno/smartkg/tree/master/experiments/conf_files).

### Resource monitoring

CPU, RAM and Network usage was monitored on all clients and servers using a [psutil](https://psutil.readthedocs.io/en/latest/) Python [script](https://git.ai.wu.ac.at/beno/smartkg/tree/master/experiments/scripts/resource_mon). 

Stats measured:
* CPU Percentage of the client/server process. The clients have 8 threads in total available, the servers 4. Therefore, the CPU percentage reported in the files may go over 100%. A utilization of 400% for instance means a full load on all 4 available threads on the server.
* RAM: Monitored the Resident Set Size (rss), which is the non-swapped physical memory a process has used. 
* Network: Total upload and download of the network interface and the current upload/download bandwidth. 

### Start scripts

All scripts that were used to start the servers and the clients are published in this repository in the [scripts folder](https://git.ai.wu.ac.at/beno/smartkg/tree/master/experiments/scripts)

### Queries and datasets used

The performance was evaluated using the [Waterloo SPARQL Diversity Tests suite](http://dsg.uwaterloo.ca/watdiv/) in hdt format. Additionally, a dbpedia dataset was also used. 

All queries used are published in [this repository](https://git.ai.wu.ac.at/beno/smartkg/tree/master/experiments/queries)

### Results

The raw results are available [here](https://git.ai.wu.ac.at/beno/smartkg/tree/master/experiments/results). For the most part, there are 3 iterations of various client number configurations.

Filenames always contain the hostname, dataset, and the name of the set of queries used for the experiments. Examples below:
* net_hostname_queryset_numberOfClients.csv -> Network usage report. Available for both server and clients
* os_hostname_queryset_numberOfClients.csv -> CPU/RAM usage report. Available for both server and clients
* execution_times.csv -> Sage and Virtuoso detailed results are stored in this format
* SmartKG-Exp1-watdiv100M-complex-10Client-20MB-NoCache-testResult -> Detailed results of SmartKG clients
* white2.wu-wien.ac.at-tpf-10M-Amr.txt -> Detailed results of TPF clients


