# OpenShift 4.4 Workshop: Knative, Tekton & Quarkus

Hands-on workshop exploring the next generation of cloud-native development on OpenShift 4.4. Co-presented with Red Hat, covering serverless computing with Knative, CI/CD pipelines with Tekton, and supersonic Java with Quarkus.

## Workshop Recording

**Watch the full session:** [YouTube](https://www.youtube.com/watch?v=gs71zQUA_6k&t=1806s)

## Topics Covered

1. **Quarkus** — Supersonic, subatomic Java framework
   - Creating a project from scratch
   - Dev mode with live reload
   - JVM vs Native compilation (GraalVM)
   - Container-optimized builds
   
2. **Deploying on OpenShift** — From source to running pods
   - Binary builds with `oc new-build`
   - Native image deployment
   - Service exposure and routing
   - Serverless with Knative

3. **Tekton Pipelines** — Kubernetes-native CI/CD
   - Tasks and TaskRuns
   - Pipeline composition
   - CLI usage with `tkn`

## Quick Start: Quarkus

### Create a new project

```bash
mvn io.quarkus:quarkus-maven-plugin:1.4.2.Final:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=quarkus-started \
    -DclassName="org.acme.getting.started.HelloResource" \
    -Dpath="/hello"
```

Or use the web generator: https://code.quarkus.io/

### Run in dev mode

```bash
cd quarkus-started
mvn compile quarkus:dev
# App available at http://localhost:8080
```

### Build native executable

```bash
mvn clean package -DskipTests -Pnative
```

## Deploy to OpenShift

```bash
# Create project
oc new-project quarkus-started

# Create build config
oc new-build --binary --name=quarkus-started -l app=quarkus-started

# Set native Dockerfile
oc patch bc/quarkus-started -p '{"spec":{"strategy":{"dockerStrategy":{"dockerfilePath":"src/main/docker/Dockerfile.native"}}}}'

# Start build
oc start-build quarkus-started --from-dir=. --follow

# Create app and expose
oc new-app --image-stream=quarkus-started:latest
oc expose service quarkus-started
```

## Tekton Pipeline Example

```bash
# Apply a task
oc apply -f tekton/task-helloworld.yaml

# List tasks
tkn task ls

# Run a task
tkn task start echo-hello-world

# View logs
tkn task logs echo-hello-world
```

## Repository Structure

```
workshop-ocp-tekton/
├── quarkus-started/    # Quarkus demo application
├── tekton/             # Tekton task and pipeline definitions
├── tekton-php/         # Tekton pipeline for PHP apps
└── README.md
```

## Resources

- [Quarkus - Container-ready native apps](http://www.mastertheboss.com/soa-cloud/quarkus/building-container-ready-native-applications-with-quarkus)
- [OpenShift Serverless (Knative)](https://docs.openshift.com/container-platform/4.3/serverless/installing_serverless/installing-knative-eventing.html)
- [OpenShift Pipelines Tutorial](https://github.com/openshift/pipelines-tutorial)
- [Tekton CLI](https://github.com/tektoncd/cli)

## License

MIT
