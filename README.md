# Kogito K8s Demo

Simples Setup von [[Kogito]] mit Maven, Quarkus und Kubernetes.

Getestet auf: Fedora

## Prerequisites

- Maven -> OpenJDK (`dnf install maven`)
- Docker and k3s (`dnf install docker`)
	- `curl -sfL https://get.k3s.io | sh -`
	- mit `k3s server --docker`
- [ ] optional: GraalVM with native image für native Kompilierung

## Kogito Examples

- Repo: <https://github.com/kiegroup/kogito-examples>
- Einfaches Beispiel: <https://github.com/kiegroup/kogito-examples/tree/main/kogito-quarkus-examples/process-scripts-quarkus>

## K8s

<https://quarkus.io/guides/deploying-to-kubernetes>

### Erstellt mit

```bash
quarkus create app org.acme:kogito-k8s-demo \
    --stream=2.13 \
    --extension='kogito,resteasy-reactive-jackson,kubernetes,quarkus-smallrye-openapi,container-image-docker' \
    --no-code
```

- test BPMN Datei bei `src/main/resources/org/acme/travels/`

### Build und Anpassen

```bash
mvn clean package
# `target/kubernetes/kubernetes.yml`: `imagePullPolicy: Never`
```

### Deploy

- local: `java -jar target/quarkus-app/quarkus-run.jar`
	- Test: `curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name": "john"}' http://localhost:8080/scripts`
- k8s: `kubectl apply -f target/kubernetes/kubernetes.yml`
	- Test: `curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name": "john"}' http://CLUSTERIP/scripts`

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:

```shell script
./mvnw compile quarkus:dev
```

> **_NOTE:_**  Quarkus now ships with a Dev UI, which is available in dev mode only at <http://localhost:8080/q/dev/>.

## Packaging and running the application

The application can be packaged using:

```shell script
./mvnw package
```

It produces the `quarkus-run.jar` file in the `target/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/quarkus-app/lib/` directory.

The application is now runnable using `java -jar target/quarkus-app/quarkus-run.jar`.

If you want to build an _über-jar_, execute the following command:

```shell script
./mvnw package -Dquarkus.package.jar.type=uber-jar
```

The application, packaged as an _über-jar_, is now runnable using `java -jar target/*-runner.jar`.

## Creating a native executable

You can create a native executable using:

```shell script
./mvnw package -Dnative
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using:

```shell script
./mvnw package -Dnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./target/kogito-k8s-demo-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult <https://quarkus.io/guides/maven-tooling>.

## Related Guides

- Kubernetes ([guide](https://quarkus.io/guides/kubernetes)): Generate Kubernetes resources from annotations
- Kogito ([guide](https://quarkus.io/guides/kogito)): Add business automation capabilities - processes and rules with Kogito (a toolkit that originates from projects Drools and jBPM)
- SmallRye OpenAPI ([guide](https://quarkus.io/guides/openapi-swaggerui)): Document your REST APIs with OpenAPI - comes with Swagger UI
