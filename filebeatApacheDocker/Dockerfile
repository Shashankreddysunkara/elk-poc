# Ubuntu OS
FROM ubuntu:16.04

# Java
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y openjdk-8-jdk && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/oracle-jdk8-installer;

    # Fix certificate issues, found as of
    # https://bugs.launchpad.net/ubuntu/+source/ca-certificates-java/+bug/983302
RUN apt-get update && \
    apt-get install -y ca-certificates-java && \
    apt-get clean && \
    update-ca-certificates -f && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/oracle-jdk8-installer;

# Define working directory.
WORKDIR /data

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
RUN export JAVA_HOME

# Filebeat (from https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html)
RUN apt-get update && apt-get upgrade -y && \
    apt-get install wget -y && \
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | apt-key add - && \
    apt-get install apt-transport-https -y && \
    echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | tee -a /etc/apt/sources.list.d/elastic-5.x.list && \
    apt-get update && apt-get install filebeat && \
    apt-get clean
RUN update-rc.d filebeat defaults

# place our filebeat configuration
COPY filebeat.yml /etc/filebeat/filebeat.yml

# Apache
RUN apt-get update -y && \
    apt-get install -y apache2 && \
    apt-get update -y && \
    apt-get clean

RUN echo "ServerName localhost" | tee /etc/apache2/conf-available/fqdn.conf && \
    a2enconf fqdn

# Make port 80 available outside this container
EXPOSE 80

# copy the startup script
COPY ./startup.sh /usr/local/bin/startup.sh
RUN chmod +x /usr/local/bin/startup.sh

# execute the startup script
ENTRYPOINT ["/usr/local/bin/startup.sh"]