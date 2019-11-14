# Random Pagination in a Stateless REST service
This example application shows how to paginate results returned by Cassandra bidirectionally over a REST UI.  


Contributors: [Tomasz Lelek](https://github.com/tomekl007), [Carlos Diaz](https://github.com/crdiaz324)

## Objectives
* To demonstrate how to paginate bidirectionally.  
* Jump to a random page number by modifying the url.
   

## Project Layout
* [RandomPagingRestUi.java](/src/main/java/com/datastax/examples/RandomPagingRestUi.java) - The main application which will create and populate a schema and start the rest service.


## How this Sample Works
This appication will:
 * create a new keyspace "examples" in the cluster. If a keyspace with this name already
 exists, it will be reused
 * creates a table "examples.random_paging_rest_ui". If it already exists, it will be reused
 * Populate the table with 3 users and 49 videos each.  
 * launches a REST server listening on HTTP_PORT.
 
Navigation is bidirectional, and you can jump to a random page (by modifying the URL).
Cassandra does not support offset queries (see
https://issues.apache.org/jira/browse/CASSANDRA-6511), so we emulate it by restarting from the
beginning each time, and iterating through the results until we reach the requested page. This is
fundamentally inefficient (O(n) in the number of rows skipped), but the tradeoff might be
acceptable for some use cases; for example, if you show 10 results per page and you think users
are unlikely to browse past page 10, you only need to retrieve at most 100 rows.

## Setup and Running

### Prerequisites
* Java 8
* A DSE/DDAC/C* cluster or an Apollo database to connect to with the appropriate connection information

### Running
This first step in the process is to build and package the application.  This can be done using the following command from within the root directory of this repo:

`mvn package`

This will compile the code and package it as a fat JAR file (located in target/random-paging-rest-ui-1.0-SNAPSHOT-jar-with-dependencies.jar), 
which contains all the dependencies needed to run the application.

Once you have compiled the application, you can run it with:

`java -jar target/random-paging-rest-ui-1.0-SNAPSHOT-jar-with-dependencies.jar`

By default, it will try to connect to your cluster at 127.0.0.1:9042, however you can change the contact points by adding a file called application.conf 
to your class path with the following contents:

`datastax-java-driver {
   basic {
     contact-points = [ "1.2.3.4:9042", "5.6.7.8:9042" ]
     load-balancing-policy.local-datacenter = datacenter1
   }
 }`

If you would like to connect to an Apollo cluster instead, simply follow the [switching-connection-configurations-java-driver](https://github.com/DataStax-Examples/switching-connection-configurations-java-driver-oss-v3)
