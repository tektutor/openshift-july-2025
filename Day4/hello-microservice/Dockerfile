FROM registry.access.redhat.com/ubi8/openjdk-17:1.15-1.1682053058 AS builder

RUN mkdir project
WORKDIR /home/jboss/project
COPY pom.xml .
RUN mvn dependency:go-offline

COPY src src
RUN mvn package -Dmaven.test.skip=true

FROM registry.access.redhat.com/ubi8/openjdk-17-runtime:1.15-1.1682053056
COPY --from=builder /home/jboss/project/target/hello-1.0.0.jar  /deployments/app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/deployments/app.jar"]
