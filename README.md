# cassandra-issue-images
This repository has a couple of examples showing  a Cassandra (3.11.0) issue.
The issue is visible after executing in an cqlsh the following

``` sql
## This works:
select * from "metrics"."app_36437104577c4432_calculateMetrics" where application='application--1792657053' and session='X5' and user='user-1375724504';

## This does not:
--this does not work, giving the error described, something related to json encoding
select json * from "metrics"."app_36437104577c4432_calculateMetrics" where application='application--1792657053' and session='X5' and user='user-1375724504';  

```
The result in the second case is:

Cqlsh:

![alt text](https://github.com/lrodriguez2002cu/cassandra-issue-images/blob/master/issue/data.PNG?raw=true)

*Note: sometimes it is needed to run the second query two times to see the issue.*


# With the original image (only has added the data for showing the issue)
_build the image_
```
docker build -t lrodriguez2002cu/cassandra-test:1.0 .

#lets run the image
docker run -d --name ctest lrodriguez2002cu/cassandra-test:1.0

#exec the init (actually creates the keyspace, table, and insert the data into the table)

docker exec -it -d ctest bash /cassandra-test/init.sh  

#for opening the cqlsh

docker exec -it ctest cqlsh   

#See query.cql for the queries related to the issues  

docker exec -it ctest cqlsh -f /cassandra-test/query.cql

```
# Image  with the replaced libs
In this case apparently the error does not happen. The image is the same as before, just that the jackson libraries jackson-core-asl-1.9.2.jar and jackson-mapper-asl-1.9.2.jar have been replaced by jackson-core-asl-1.9.13.jar and jackson-mapper-asl-1.9.13.jar, **note that those are also old.**

ENV core_lib_url   http://central.maven.org/maven2/org/codehaus/jackson/jackson-core-asl/${JACKSON_VERSION}/jackson-core-asl-${JACKSON_VERSION}.jar
ENV mapper_lib_url http://central.maven.org/maven2/org/codehaus/jackson/jackson-mapper-asl/${JACKSON_VERSION}/jackson-mapper-asl-${JACKSON_VERSION}.jar

```
#build the image with the replaced libraries
docker build -t lrodriguez2002cu/cassandra-test-modif-libraries:1.0 -f  ./DockerfileReplacedLibs .

#run the modified container
docker run -d --name ctestmodif lrodriguez2002cu/cassandra-test-modif-libraries:1.0

#setup the things
docker exec -it -d ctestmodif bash /cassandra-test/init.sh  

#run the queries
docker exec -it ctestmodif cqlsh -f /cassandra-test/query.cql

#docker rm -f ctestmodif ||  docker rmi -f lrodriguez2002cu/cassandra-test-modif-libraries:1.0
#docker rmi -f lrodriguez2002cu/cassandra-test-modif-libraries:1.0
#docker exec -it ctestmodif bash
#docker exec -it ctestmodif ls -l /usr/share/cassandra/lib/
```
