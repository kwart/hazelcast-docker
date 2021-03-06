FROM alpine:3.8

# Versions of Hazelcast and Hazelcast plugins
ARG HZ_VERSION=3.11
ARG CACHE_API_VERSION=1.1.0
ARG HZ_KUBE_VERSION=1.3.1
ARG HZ_EUREKA_VERSION=1.0.2
ARG NETTY_VERSION=4.1.32.Final
ARG NETTY_TCNATIVE_VERSION=2.0.20.Final

# Build constants
ARG HZ_HOME="/opt/hazelcast"
ARG HZ_JAR="hazelcast-enterprise-all-${HZ_VERSION}.jar"
ARG CACHE_API_JAR="cache-api-${CACHE_API_VERSION}.jar"

# Runtime constants / variables
ENV CLASSPATH_DEFAULT="${HZ_HOME}/*" \
    JAVA_OPTS_DEFAULT="-Djava.net.preferIPv4Stack=true -Dhazelcast.mancenter.enabled=false" \
    MIN_HEAP_SIZE="" \
    MAX_HEAP_SIZE="" \
    HZ_LICENSE_KEY="" \
    MANCENTER_URL="" \
    CLASSPATH="" \
    JAVA_OPTS=""

# Expose port
EXPOSE 5701

COPY *.xml *.sh ${HZ_HOME}/

# Install
RUN echo "Updating Alpine system" \
    && apk upgrade --update-cache --available \
    && echo "Installing new APK packages" \
    && apk add openjdk8-jre maven bash apr openssl curl procps \
    && echo "Installing Hazelcast" \
    && cd "${HZ_HOME}" \
    && curl -sf -o ${HZ_HOME}/${HZ_JAR} \
         -L https://hazelcast.bintray.com/release/com/hazelcast/hazelcast-enterprise-all/${HZ_VERSION}/${HZ_JAR} \
   && echo "Installing JCache" \
   && curl -sf -o "${HZ_HOME}/${CACHE_API_JAR}" \
         -L "https://repo1.maven.org/maven2/javax/cache/cache-api/${CACHE_API_VERSION}/${CACHE_API_JAR}" \
   && echo "Installing Hazelcast plugins" \
   && mvn -f dependency-copy.xml \
           -Dhazelcast-kubernetes-version=${HZ_KUBE_VERSION} \
           -Dhazelcast-eureka-version=${HZ_EUREKA_VERSION} \
           -Dnetty.version=${NETTY_VERSION} \
           -Dnetty-tcnative.version=${NETTY_TCNATIVE_VERSION} \
           dependency:copy-dependencies \
   && echo "Maven clean-up" \
   && apk del maven \
   && rm -rf ~/.m2 \
   && rm -f dependency-copy.xml \
   && echo "Granting read permission to ${HZ_HOME}" \
   && chmod -R +r $HZ_HOME \
   && echo "Setting Pardot ID to 'docker'" \
   && echo 'hazelcastDownloadId=docker' > hazelcast-download.properties \
   && echo "Cleaning APK packages" \
   && rm -rf /var/cache/apk/*

WORKDIR ${HZ_HOME}

# Start Hazelcast server
CMD ["/opt/hazelcast/start-hazelcast.sh"]
