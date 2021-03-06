FROM openjdk:8-jre

LABEL maintainer="Andrei Varabyeu <andrei_varabyeu@epam.com>"
LABEL version="@version@"
LABEL description="@description@"

ENV APP_FILE service-api-4.3.4
ENV APP_DOWNLOAD_URL https://dl.bintray.com/epam/reportportal/com/epam/reportportal/service-api/4.3.4/$APP_FILE.jar
ENV JAVA_OPTS="-Xmx1g -Djava.security.egd=file:/dev/./urandom"
ENV JAVA_APP=/app.jar
ENV JOLOKIA_AGENT="-javaagent:/opt/java/jolokia-jvm-1.6.0-agent.jar=port=7777,host=localhost"

RUN apt-get update && \
    apt-get install -y fonts-noto && \
    rm -rf /var/lib/apt/lists/*
	
# Installing Java Jolokia
RUN  mkdir /opt/java && cd /opt/java \
 && wget -O jolokia-jvm-1.6.0-agent.jar \
 http://search.maven.org/remotecontent?filepath=org/jolokia/jolokia-jvm/1.6.0/jolokia-jvm-1.6.0-agent.jar

# Installing Telegraf
RUN cd /tmp && wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.3-1_amd64.deb && \
    dpkg -i telegraf_1.8.3-1_amd64.deb
COPY ./docker/telegraf.conf /etc/telegraf/telegraf.conf


RUN sh -c "echo '#!/bin/sh \n\
service telegraf restart \n\
exec java $JOLOKIA_AGENT $JAVA_OPTS -jar $JAVA_APP' > /start.sh && chmod +x /start.sh"

VOLUME /tmp

RUN wget -O $JAVA_APP $APP_DOWNLOAD_URL

EXPOSE 8080
ENTRYPOINT ["/start.sh"]