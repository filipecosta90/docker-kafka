# Kafka and Zookeeper
# AlpineLinux

FROM java:openjdk-8-jre

ENV DEBIAN_FRONTEND noninteractive
ENV SCALA_VERSION 2.11
ENV KAFKA_VERSION 1.0.0
ENV METRICBEAT_VERSION 6.2.3

ENV KAFKA_HOME /opt/kafka_"$SCALA_VERSION"-"$KAFKA_VERSION"

# Install Kafka, Zookeeper and other needed things
RUN apt-get update 
#RUN apt-get install -y awk 
RUN apt-get install -y zookeeper wget supervisor dnsutils 
RUN rm -rf /var/lib/apt/lists/*
RUN apt-get clean
RUN wget -q https://archive.apache.org/dist/kafka/"$KAFKA_VERSION"/kafka_"$SCALA_VERSION"-"$KAFKA_VERSION".tgz -O /tmp/kafka_"$SCALA_VERSION"-"$KAFKA_VERSION".tgz
RUN tar xfz /tmp/kafka_"$SCALA_VERSION"-"$KAFKA_VERSION".tgz -C /opt
RUN rm /tmp/kafka_"$SCALA_VERSION"-"$KAFKA_VERSION".tgz

# CRLF line terminators windows to linux 
ADD scripts/start-kafka.sh /tmp/start-kafka.sh
RUN awk '{ sub("\r$", ""); print }' /tmp/start-kafka.sh > /usr/bin/start-kafka.sh
RUN chmod  guo+rx /usr/bin/start-kafka.sh
# Supervisor config
ADD supervisor/kafka.conf supervisor/zookeeper.conf /etc/supervisor/conf.d/


# Download and Install metricbeat
RUN wget https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-"$METRICBEAT_VERSION"-amd64.deb
RUN dpkg -i metricbeat-"$METRICBEAT_VERSION"-amd64.deb
RUN rm metricbeat-"$METRICBEAT_VERSION"-amd64.deb

# Update the System init script -- will not be needed by any other daemon
RUN update-rc.d metricbeat defaults 95 10 

# 2181 is zookeeper, 9092 is kafka
EXPOSE 2181 9092

CMD ["supervisord", "-n"]
