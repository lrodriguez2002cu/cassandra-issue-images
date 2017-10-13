FROM cassandra:3.11

RUN mkdir /cassandra-test


# adds the query files
ADD files/* /cassandra-test/        

#share the libs to a volume
VOLUME /usr/share/cassandra/lib
