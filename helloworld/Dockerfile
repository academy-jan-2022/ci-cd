FROM gradle:7.4.0-jdk17 AS builder
RUN ls
COPY . /home/gradle/src
WORKDIR /home/gradle/src
RUN gradle build --no-daemon
RUN java -Djarmode=layertools -jar /home/gradle/src/build/libs/helloworld-*-SNAPSHOT.jar extract

FROM openjdk:17-jdk-alpine

RUN mkdir /deployments
RUN chown 1001 /deployments
RUN chmod "g+rwX" /deployments
RUN chown 1001:root /deployments

COPY --chown=1001 --from=builder /home/gradle/src/dependencies/ /deployments/
COPY --chown=1001 --from=builder /home/gradle/src/snapshot-dependencies/ /deployments/
COPY --chown=1001 --from=builder /home/gradle/src/spring-boot-loader/ /deployments/
COPY --chown=1001 --from=builder /home/gradle/src/application/ /deployments/

EXPOSE 8080
USER 1001

ENTRYPOINT ["java", "-cp", "deployments", "org.springframework.boot.loader.JarLauncher"]
