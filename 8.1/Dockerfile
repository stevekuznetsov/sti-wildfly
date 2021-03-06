# This image provides a base for building and running WildFly applications.
# It builds using maven and runs the resulting artifacts on WildFly 8.1.

FROM openshift/base-centos7

MAINTAINER Ben Parees <bparees@redhat.com>

EXPOSE 8080

ENV WILDFLY_VERSION 8.1

LABEL io.k8s.description="Platform for building and running JEE applications on WildFly 8.1" \
      io.k8s.display-name="WildFly 8.1" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,wildfly,wildfly81" \
      io.openshift.s2i.destination="/opt/s2i/destination"

# Install Maven, Wildfly 8
RUN yum install -y --enablerepo=centosplus \
    tar unzip bc which lsof java-1.8.0-openjdk java-1.8.0-openjdk-devel && \
    yum clean all -y && \
    (curl -0 http://mirror.sdunix.com/apache/maven/maven-3/3.0.5/binaries/apache-maven-3.0.5-bin.tar.gz | \
    tar -zx -C /usr/local) && \
    ln -sf /usr/local/apache-maven-3.0.5/bin/mvn /usr/local/bin/mvn && \
    mkdir -p /wildfly && \
    (curl -0 http://download.jboss.org/wildfly/8.1.0.Final/wildfly-8.1.0.Final.tar.gz | tar -zx --strip-components=1 -C /wildfly) && \
    mkdir -p /opt/app-root/source && \
    mkdir -p /opt/s2i/destination

# Add sti wildfly customizations
ADD ./contrib/wfmodules/ /wildfly/modules/
ADD ./contrib/wfbin/standalone.conf /wildfly/bin/standalone.conf
ADD ./contrib/wfcfg/standalone.xml /wildfly/standalone/configuration/standalone.xml

# Copy the STI scripts from the specific language image to /usr/local/sti
COPY ./.sti/bin/ /usr/local/sti

# Create wildfly group and user, set file ownership to that user.
RUN chmod -R go+rw /wildfly && \
    chmod -R go+rw /opt/s2i/destination

USER 1001

CMD ["usage"]
