FROM openjdk:8-jre-alpine

LABEL maintainer "Andrei Varabyeu <andrei_varabyeu@epam.com>"
LABEL version="@version@"
LABEL description="@description@"

RUN apk --no-cache add unzip openssl

ENV APP_FILE service-authorization-4.2.0
ENV APP_DOWNLOAD_URL="https://dl.bintray.com/epam/reportportal/com/epam/reportportal/service-authorization/4.2.0/$APP_FILE.zip"
ENV JAVA_OPTS="-Xmx512m -Djava.security.egd=file:/dev/./urandom"
ENV JAVA_APP=/app/app.jar

ENV JOLOKIA_AGENT="-javaagent:/opt/java/jolokia-jvm-1.6.0-agent.jar=port=7778,host=localhost"

RUN apk update \
  && apk add -y wget \
  && apk add -y dpkg \
  && rm -rf /var/lib/apt/lists/*
  
 # Installing Java Jolokia
RUN mkdir -p /opt/java && cd /opt/java \
 && wget -O jolokia-jvm-1.6.0-agent.jar \
 http://search.maven.org/remotecontent?filepath=org/jolokia/jolokia-jvm/1.6.0/jolokia-jvm-1.6.0-agent.jar
 
 # Downloading Telegraf
ADD https://dl.influxdata.com/telegraf/releases/telegraf-1.8.3-static_linux_amd64.tar.gz ./

# Installing telegraf
RUN tar -C . -xzf telegraf-1.8.3-static_linux_amd64.tar.gz && \
        chmod +x telegraf/* && \
        cp telegraf/telegraf /usr/bin/ && \
        rm -rf *.tar.gz* telegraf/
COPY docker/telegraf.conf /etc/telegraf/telegraf.conf


RUN sh -c "echo $'#!/bin/sh \n\
telegraf & \n\
exec java $JOLOKIA_AGENT $JAVA_OPTS -jar $JAVA_APP' > /start.sh && chmod +x /start.sh"

VOLUME /tmp

RUN mkdir /app
RUN wget -O /app/$APP_FILE.zip $APP_DOWNLOAD_URL
RUN unzip /app/$APP_FILE.zip -d /app/ && rm -rf /app/$APP_FILE.zip
RUN mv /app/$APP_FILE.jar $JAVA_APP

EXPOSE 8080
ENTRYPOINT ["/start.sh"]