# Random Pagination in a Stateless REST service
Working on a backend REST service that handles access to Cassandra while passing results back to a web front-end for display? You've come to the right place if you have any paging functionality in that web application. This example shows how to paginate the results returned by Cassandra in a web application via a backend REST service.


Contributors: [Tomasz Lelek](https://github.com/tomekl007), [Carlos Diaz](https://github.com/crdiaz324)

## Objectives
* Demonstrate how to use the paging state returned by Cassandra and encode it in HTTP URLs for a REST application.  
* Jump to a random page number by modifying the url.
   

## Project Layout
* [RandomPagingRestUi.java](/src/main/java/com/datastax/examples/RandomPagingRestUi.java) - The main application which creates and populates a schema and starts the rest service.


## How this Works
This application creates a table called `random_paging_rest_ui` in the `examples` keyspace. It then populates the table with 3 users and 49 videos for each user. It then starts a REST service for that data, accessible via the following endpoint, the port can be modified by changing `HTTP_PORT` in [RandomPagingRestUi.java](/src/main/java/com/datastax/examples/RandomPagingRestUi.java).
 
Cassandra does not support offset queries (CASSANDRA-6511)[https://issues.apache.org/jira/browse/CASSANDRA-6511], so we emulate it by restarting from the beginning each time, and iterating through the results until we reach the requested page. 

This is fundamentally inefficient (O(n) in the number of rows skipped), but the tradeoff might be
acceptable for some use cases; for example, if you show 10 results per page and you think users
are unlikely to browse past page 10, you only need to retrieve at most 100 rows.

## Setup and Running

### Prerequisites
* Java 8
* A Cassandra, DDAC, DSE cluster or Apollo database ( docker is a nice option for local install - [see docs](https://docs.datastax.com/en/docker/doc/docker/dockerQuickStart.html) )

### Running
This first step in the process is to build and package the application.  This can be done using the following command from within the root directory of this repo:

`mvn package`

This will compile the code and package it as a fat JAR file (located in target/random-paging-rest-ui-1.0-SNAPSHOT-jar-with-dependencies.jar), which contains all the dependencies needed to run the application.

Once you have compiled the application, you can run it with:

`java -jar target/random-paging-rest-ui-1.0-SNAPSHOT-jar-with-dependencies.jar`

By default, it will try to connect to your cluster at 127.0.0.1:9042, however you can change the contact points by adding a file called application.conf 
to your class path with the following contents:

```
datastax-java-driver {
   basic {
     contact-points = [ "1.2.3.4:9042", "5.6.7.8:9042" ]
     load-balancing-policy.local-datacenter = datacenter1
   }
 }
 ```

If you would like to connect to an Apollo cluster instead, simply follow the [Switch connection between on-prem and cloud example](https://github.com/DataStax-Examples/switch-connection-java)
