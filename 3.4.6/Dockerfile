# DESC: Docker file to create Apache Zookeeper container

FROM alpine:latest

ENV ZK_VERSION 3.4.6
ENV ZK_USER zookeeper
ENV ZK_GROUP zookeeper
ENV ZK_DIR /var/lib/zookeeper
ENV ZK_HOME /opt/zookeeper

ENV PKG_URL "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64"

ENV JAVA_VERSION_MAJOR 8
ENV JAVA_VERSION_MINOR 45
ENV JAVA_VERSION_BUILD 14
ENV JAVA_BASE /usr/local/java
ENV JAVA_HOME ${JAVA_BASE}/jdk
ENV PATH $PATH:$JAVA_HOME/bin

ADD zkStart.sh /usr/local/bin/zkStart.sh

RUN apk --update add \
      curl \
      bash &&\
    curl --insecure --silent --location "${PKG_URL}/glibc-2.21-r2.apk" --output /tmp/glibc-2.21-r2.apk &&\
    curl --insecure --silent --location "${PKG_URL}/glibc-bin-2.21-r2.apk" --output /tmp/glibc-bin-2.21-r2.apk &&\
    apk add --allow-untrusted \
      /tmp/glibc-2.21-r2.apk \
      /tmp/glibc-bin-2.21-r2.apk &&\
    /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib &&\
    mkdir -p ${JAVA_BASE} /opt $ZK_DIR/data $ZK_DIR/log &&\
    curl --insecure --silent --location --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/jdk-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz | tar zxf - -C ${JAVA_BASE} &&\
    ln -s ${JAVA_BASE}/jdk1.${JAVA_VERSION_MAJOR}.0_${JAVA_VERSION_MINOR} ${JAVA_HOME} &&\
    curl --insecure --silent --location http://mirrors.sonic.net/apache/zookeeper/zookeeper-${ZK_VERSION}/zookeeper-${ZK_VERSION}.tar.gz | tar zxf - -C /opt &&\
    ln -s /opt/zookeeper-${ZK_VERSION} $ZK_HOME &&\
    rm -rf /tmp/* &&\
    addgroup $ZK_GROUP &&\
    adduser -h $ZK_DIR -D -s /bin/bash -G $ZK_GROUP $ZK_USER &&\
    chown -R $ZK_USER:$ZK_GROUP $ZK_DIR &&\
    chmod +x /usr/local/bin/zkStart.sh

USER $ZK_USER

# Expose client port (2188/tcp), peer connection port (2888/tcp), leader election port (3888/tcp)
EXPOSE 2181 2888 3888

VOLUME ["${ZK_HOME}/conf", "${ZK_DIR}"]

ENTRYPOINT ["/usr/local/bin/zkStart.sh"]
CMD [""]

