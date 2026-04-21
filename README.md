# Original SmartKG Server

To run the Original SmartKG Server, follow the instructions below:

From the root directory of the project, you can build the server using maven:

```
mvn install
```

Next up, make sure to create a configuration file (e.g., `config.json`) with the appropriate settings for your data sources and prefixes. An example configuration is provided in the `config-example.json` file. The existing `config.json` file is the configuration used for the benchmarks in the paper.

Finally, you can run the server using the following command from the root directory of the project:

```
java -jar target/ldf-server.jar config.json
```

### Explanation of the configuration file

The `metadatapath` field specifies the path to the metadata file in json format. This file can be generated using the `smartKG-creator-types` repository.
The `moleculesdatapath` field specifies the path to the directory containing the molecule files, in other words, the folder that contains the partitioned HDT files.
Additionally, you should also add the original data source in HDT format, so the server can serve the original data as well. This is done in the `datasources` field, where you can specify the path to the HDT file and a description of the data source.

IMPORTANT NOTE: for this type of server, only the original partitioning is supported. This means that typed partitioning is not supported, and that the partitions and metadata files should only be generated using the original partitioning method. More information on this can be found in the `smartKG-creator-types` repository.


### Usage

To access the server, the following endpoints are available:

- `http://localhost:8080/smartkg`: the original data source in HDT format. This should be used for executing queries on the original data, and for executing queries that cannot be executed on the partitioned data or when no partition can be selected for a query. `smartkg` could be replaced with whatever name you have given to the HDT data source in the configuration file. An example of a query looks like this: `http://localhost:8080/smartkg?subject=&predicate=&object=`
- `http://localhost:8080/molecule/smartkg`: returns the metadata of the partitioned data. It should be noted when your data contains lots of partitions, the metadata file can be quite large, and it might take a while to load the metadata.
- `http://localhost:8080/molecule/smartkg/2.hdt`: returns the partition with id 2. The name of the partition should be the same as the name of the partition files in the metadata file. It should be noted that if the `numTriples` field is 0, the partition is empty and no file will be returned, as it does not exist.


NOTE: if you're using Comunica, it is advised you name your dataset `smartkg` instead of `watdiv`. The reason for this is that the current server implementation doesn't support additional metadata to be sent through the Comunica client for dynamic handling.