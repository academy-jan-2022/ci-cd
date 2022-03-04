FROM gradle:7.4.0-jdk17 AS builder
RUN ls
#COPY . /src
#WORKDIR /src
RUN gradle build --no-daemon
ARG JAR_FILE=build/libs/helloworld-*.jar
COPY ${JAR_FILE} app.jar
RUN java -Djarmode=layertools -jar app.jar extract

#FROM openjdk:17-alpine as builder
#ARG JAR_FILE=build/libs/helloworld-*.jar
#COPY ${JAR_FILE} app.jar
#RUN java -Djarmode=layertools -jar app.jar extract

FROM openjdk:17-jdk-alpine

RUN mkdir /deployments \
    && chown 1001 /deployments \
    && chmod "g+rwX" /deployments \
    && chown 1001:root /deployments

COPY --chown=1001 --from=builder dependencies/ /deployments/
COPY --chown=1001 --from=builder snapshot-dependencies/ /deployments/
COPY --chown=1001 --from=builder spring-boot-loader/ /deployments/
COPY --chown=1001 --from=builder application/ /deployments/

EXPOSE 8080
USER 1001

ENTRYPOINT ["java", "-cp", "deployments", "org.springframework.boot.loader.JarLauncher"]
