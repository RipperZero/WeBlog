# FROM eclipse-temurin:8-jdk-jammy
FROM tomcat:9.0.78-jdk8 AS base

# build stage → build the source
# TODO build in maven image or jenkins
FROM base AS build-stage

WORKDIR /app

# default value is in pom.xml
#ARG WAR_FILE_PATH
#ARG WAR_FILE_NAME
ARG WAR_FILE_PATH=target/weblog-web.war
ARG WAR_FILE_NAME=weblog-web

ENV WAR_FILE=${WAR_FILE_NAME}.war

COPY ${WAR_FILE_PATH} ./${WAR_FILE}

# EXPOSE 8082

# ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", ${WAR_FILE}]
# must use "sh", "-c" for analysis ${WAR_FILE}
# ENTRYPOINT ["sh", "-c", "java -Djava.security.egd=file:/dev/./urandom -jar ${WAR_FILE}"]

# production stage → copy built war then use tomcat to run it
FROM base AS production-stage

# copy WAR in Tomcat's webapps folder
COPY --from=build-stage /app/${WAR_FILE} /usr/local/tomcat/webapps/

# you can change TOMCAT_CONFIG_DIR_PATH with ARG
# defaule path is ["/usr/local/tomcat-config"]
ARG TOMCAT_CONFIG_DIR=/usr/local/tomcat-config

ENV TOMCAT_CONFIG_DIR_PATH=${TOMCAT_CONFIG_DIR}

# make folder to store your server.xml
RUN mkdir -p ${TOMCAT_CONFIG_DIR_PATH}

# must use relative path(because of docker build context)
# COPY /tomcat-config/server.xml /usr/local/tomcat/conf/server.xml
# copy your server.xml to ${TOMCAT_CONFIG_DIR_PATH}
COPY /tomcat-config/server.xml /usr/local/tomcat-config

VOLUME ["${TOMCAT_CONFIG_DIR_PATH}"]

EXPOSE 8080

# use your server.xml to run tomcat(which you can edit in /tomcat-config)
# [/usr/local/tomcat/conf/server.xml] doesn't work anymore
CMD ["catalina.sh", "run", "-config", "${TOMCAT_CONFIG_DIR_PATH}/server.xml"]