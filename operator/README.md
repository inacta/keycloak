# Keycloak on Quarkus

The module holds the codebase to build the Keycloak Operator on top of [Quarkus](https://quarkus.io/).
Using the [Quarkus Operator SDK](https://github.com/quarkiverse/quarkus-operator-sdk).

## Activating the Module

When build from the project root directory, this module is only enabled if the installed JDK is 11 or newer. 

## Building

Ensure you have JDK 11 (or newer) installed.

Build the Docker image with:

```bash
mvn clean package -Doperator -Dquarkus.container-image.build=true
```

## Configuration

The Keycloak image can be configured, when starting the operator, using the Java property:

```
operator.keycloak.image
```

And the imagePullPolicy with:

```
operator.keycloak.image-pull-policy
```

## Contributing

### Quick start on Minikube

Enable the Minikube Docker daemon:

```bash
eval $(minikube -p minikube docker-env)
```

Compile the project and generate the Docker image with JIB:

```bash
mvn clean package -Doperator -Dquarkus.container-image.build=true -Dquarkus.kubernetes.deployment-target=minikube
```

Install the CRD definition and the operator in the cluster in the `keycloak` namespace:

```bash
kubectl apply -k target
```

to install in the `default` namespace:

```bash
kubectl apply -k overlays/default-namespace
```

Remove the created resources with:

```bash
kubectl delete -k <previously-used-folder>
```

### Testing

Testing allows 2 methods specified in the property `test.operator.deployment` : `local` & `remote`. 

`local` : resources will be deployed to the local cluster and the operator will run out of the cluster

`remote` : same as local test but an image for the operator will be generated and deployed run inside the cluster

```bash
mvn clean verify \
  -Dquarkus.container-image.build=true \
  -Dquarkus.container-image.tag=test \
  -Dquarkus.kubernetes.deployment-target=kubernetes \
  -Dtest.operator.deployment=remote
```

To run tests on Mac with `minikube` and the `docker` driver you should run `minikube tunnel` in a separate shell and configure the Java properties as follows:
```bash
-Dtest.operator.kubernetes.ip=localhost
```

On Linux or on Mac using `minikube` on a VM, instead you should pass this additional property:
```bash
-Dtest.operator.kubernetes.ip=$(minikube ip)
```
