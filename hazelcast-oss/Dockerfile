FROM openjdk:8-jre-alpine

ENV HZ_VERSION 3.8.3
ENV HZ_HOME /opt/hazelcast/

RUN mkdir -p $HZ_HOME \
  && apk -U add bash

WORKDIR $HZ_HOME

# Download hazelcast jars from maven repo.
ADD https://repo1.maven.org/maven2/com/hazelcast/hazelcast-all/$HZ_VERSION/hazelcast-all-$HZ_VERSION.jar $HZ_HOME
COPY server.sh stop.sh /$HZ_HOME/

RUN chmod +x /$HZ_HOME/*.sh

# Start hazelcast standalone server.
CMD ["./server.sh"]

EXPOSE 5701
