FROM trg/ambari-base:latest
MAINTAINER jimcke

ADD repos/ambari.repo /etc/yum.repos.d/ambari.repo
ADD repos/hdp.repo /etc/yum.repos.d/hdp.repo
ADD repos/hdp.gpl.repo /etc/yum.repos.d/hdp.gpl.repo


# initscripts (service wrapper for servicectl) is need othewise the Ambari is unable to start postgresql
#RUN yum localinstall -y ambari-server-2.7.5.0-0.x86_64.rpm
RUN yum install -y ambari-server && yum clean all
RUN yum install -y ambari-agent && yum clean all

# add ambari shell to the image so new users don't need the 1GB java image
#RUN curl -o /tmp/ambari-shell.jar https://s3-eu-west-1.amazonaws.com/maven.sequenceiq.com/releases/com/sequenceiq/ambari-shell/0.1.25/ambari-shell-0.1.25.jar
ADD shell/install-cluster.sh /tmp/
ADD shell/wait-for-host-number.sh /tmp/
ADD shell/ambari-shell.sh /tmp/

ENV PLUGIN_PATH /plugins
WORKDIR /tmp

ADD init/init-server.sh /opt/ambari-server/init-server.sh
RUN chmod u+x /opt/ambari-server/init-server.sh

# add mysql and psql connectors to ambari-server so it can be downloaded by services (e.g.: Ranger)
ADD https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.20/mysql-connector-java-8.0.20.jar /var/lib/ambari-server/resources/mysql-connector-java.jar
ADD https://repo1.maven.org/maven2/org/postgresql/postgresql/42.2.12/postgresql-42.2.12.jar  /var/lib/ambari-server/resources/postgres-jdbc-driver.jar

ADD init/ambari-server.service /etc/systemd/system/ambari-server.service
RUN systemctl enable ambari-server

RUN echo DefaultEnvironment=\"JAVA_HOME=$JAVA_HOME\" >> /etc/systemd/system.conf


#RUN sleep 20
#RUN curl -X POST -H "Content-Type: application/json" -u admin:admin -d @blueprint.json http://localhost:8080/api/v1/clusters/:trgcluster


EXPOSE 8080
