# 🚀 OpenShift & Docker Cheat Sheet

A practical OpenShift and Docker command reference while learning containerization, deployments, scaling, builds, templates, and services.

---

# 🐳 Building Containers

<p align="center">
  <img src="./src/images/docker-made-easy.png" width="900"/>
</p>

## Build an image based on the current directory
```bash
docker build .
```

## Build an image with a tag
```bash
docker build -t your-tag .
```

---

# ▶️ Running Containers

## Check local images
```bash
docker images
```

## Run a container using an Image ID
```bash
docker run -it <your-image-id>
```

## Run a container using a tag
```bash
docker run -it <image-tag>
```

## Run the Hello World image
```bash
docker run -it quay.io/practicalopenshift/hello-world
```

## Run Hello World with port forwarding
```bash
docker run -it -p 8080:8080 quay.io/practicalopenshift/hello-world
```

---

# 🛑 Stopping Containers

## List running containers
```bash
docker ps
```

## Stop a running container
```bash
docker kill <container-id>
```

---

# 🔐 OpenShift Login & Logout

## Login to the configured cluster
```bash
oc login
```

## Login to a specific cluster
```bash
oc login <cluster-address>
```

## Logout
```bash
oc logout
```

---

# 📦 Project Basics

## View current project
```bash
oc project
```

## Create a new project
```bash
oc new-project demo-project
```

## List projects
```bash
oc projects
```

## Switch projects
```bash
oc project <project-name>
```

---

# 📘 Pod Documentation

## Pod documentation
```bash
oc explain pod
```

## Pod spec details
```bash
oc explain pod.spec
```

## Pod container details
```bash
oc explain pod.spec.containers
```

---

# 📄 Creating Pods From Files

## Create a Pod
```bash
oc create -f pods/pod.yaml
```

## View running Pods
```bash
oc get pods
```

---

# 🌐 Port Forwarding

## Forward local traffic to a Pod
```bash
oc port-forward <pod-name> <local-port>:<pod-port>
```

## Example
```bash
oc port-forward hello-world-pod 8080:8080
```

---

# 🖥️ Shell Into Pods

## Open remote shell
```bash
oc rsh <pod-name>
```

## Test inside container
```bash
wget localhost:8080
```

## Exit shell
```bash
exit
```

## Watch live Pod updates
```bash
oc get pods --watch
```

---

# ❌ Deleting Pods

## Delete a resource
```bash
oc delete <resource-type> <resource-name>
```

## Delete example Pod
```bash
oc delete pod hello-world-pod
```

---

# 🚀 DeploymentConfigs

## Deploy an image
```bash
oc new-app <image-tag> --as-deployment-config
```

## Deploy Hello World
```bash
oc new-app quay.io/practicalopenshift/hello-world \
  --as-deployment-config
```

## Deploy from Git
```bash
oc new-app <git-repo-url> \
  --as-deployment-config
```

## Example Git deployment
```bash
oc new-app https://gitlab.com/practical-openshift/hello-world.git \
  --as-deployment-config
```

## Follow build logs
```bash
oc logs -f bc/hello-world
```

## Deploy with a custom name
```bash
oc new-app quay.io/practicalopenshift/hello-world \
  --name demo-app \
  --as-deployment-config
```

---

# 🔍 DeploymentConfig Information

## Describe DeploymentConfig
```bash
oc describe dc/hello-world
```

## Get DeploymentConfig YAML
```bash
oc get -o yaml dc/hello-world
```

---

# 🧹 Delete Application Resources

## Delete all resources by label
```bash
oc delete all -l app=hello-world
```

---

# 🔄 Rollouts & Rollbacks

## Deploy latest version
```bash
oc rollout latest dc/hello-world
```

## Rollback deployment
```bash
oc rollback dc/hello-world
```

---

# ⚡ Trigger Management

## List triggers
```bash
oc set triggers dc/<dc-name>
```

## Remove ConfigChange trigger
```bash
oc set triggers dc/<dc-name> \
  --remove \
  --from-config
```

## Re-add ConfigChange trigger
```bash
oc set triggers dc/<dc-name> --from-config
```

## Remove ImageChange trigger
```bash
oc set triggers dc/<dc-name> \
  --remove \
  --from-image <image-name>:<tag>
```

## Re-add ImageChange trigger
```bash
oc set triggers dc/<dc-name> \
  --from-image <image-name>:<tag> \
  -c <container-name>
```

---

# 🪝 Deployment Hooks

## General syntax
```bash
oc set deployment-hook dc/<dc-name> \
  (--pre, --post, or --mid) \
  -c <container-name> \
  -- <command>
```

## Example hook
```bash
oc set deployment-hook dc/hello-world \
  --pre \
  -c hello-world \
  -- /bin/echo Hello from pre-deploy hook
```

## Verify hook
```bash
oc describe dc/hello-world
```

---

# 🔁 Recreate Strategy

## Edit DeploymentConfig
```bash
oc edit dc/hello-world
```

## Change strategy
```yaml
strategy:
  type: Recreate
```

---

# ❤️ Readiness & Liveness Probes

## General syntax
```bash
oc set probe dc/<dc-name> \
  (--liveness or --readiness) \
  (--open-tcp, --get-url, or -- <command>)
```

## Liveness probe
```bash
oc set probe dc/hello-world --liveness --open-tcp=8080
```

## Readiness probe with URL
```bash
oc set probe dc/hello-world \
  --readiness \
  --get-url=http://:8080/health/readiness
```

## Readiness probe with command
```bash
oc set probe dc/hello-world \
  --readiness \
  -- exit 0
```

---

# 🏗️ BuildConfigs

## Create BuildConfig
```bash
oc new-build <git-url>
```

## Example
```bash
oc new-build https://gitlab.com/practical-openshift/hello-world.git
```

## Build a branch
```bash
oc new-build https://gitlab.com/practical-openshift/hello-world.git#update-message
```

## Build from subdirectory
```bash
oc new-build https://gitlab.com/practical-openshift/labs.git \
  --context-dir hello-world
```

---

# 🔨 Working With Builds

## Start build
```bash
oc start-build bc/hello-world
```

## Build logs
```bash
oc logs -f build/hello-world-1
```

## Latest build logs
```bash
oc logs -f bc/hello-world
```

## Cancel build
```bash
oc cancel-build bc/hello-world
```

## View BuildConfig YAML
```bash
oc get -o yaml buildconfig/hello-world
```

## List builds
```bash
oc get build
```

---

# 🪝 Build Hooks

## Add post-commit hook
```bash
oc set build-hook bc/hello-world \
  --post-commit \
  --script="echo Hello from build hook"
```

## Failing hook example
```bash
oc set build-hook bc/hello-world \
  --post-commit \
  --script="exit 1"
```

## View events
```bash
oc get events
```

## Remove hook
```bash
oc set build-hook bc/hello-world \
  --post-commit \
  --remove
```

---

# 🔗 WebHooks

## Get BuildConfig YAML
```bash
oc get -o yaml buildconfig/hello-world
```

## Export secret
```bash
export GENERIC_SECRET=<generic-token>
```

## Get webhook URL
```bash
oc describe buildconfig/hello-world
```

## Trigger webhook
```bash
curl -X POST -k <webhook-url>
```

---

# 🗂️ ConfigMaps

## Create from literal
```bash
oc create configmap <configmap-name> \
  --from-literal KEY="VALUE"
```

## Create from file
```bash
oc create configmap <configmap-name> \
  --from-file=MESSAGE.txt
```

## Create with key override
```bash
oc create configmap <configmap-name> \
  --from-file=MESSAGE=MESSAGE.txt
```

## Create from directory
```bash
oc create configmap <configmap-name> \
  --from-file pods
```

## Verify ConfigMap
```bash
oc get -o yaml configmap/<configmap-name>
```

---

# 🌍 ConfigMaps as Environment Variables

## Set environment variables
```bash
oc set env dc/hello-world --from cm/<configmap-name>
```

---

# 🔒 Secrets

## Create Secret
```bash
oc create secret generic <secret-name> \
  --from-literal KEY="VALUE"
```

## View Secret
```bash
oc get -o yaml secret/<secret-name>
```

## Use Secret as env vars
```bash
oc set env dc/<dc-name> \
  --from secret/<secret-name>
```

---

# 🖼️ ImageStreams

## Import image
```bash
oc import-image --confirm <image-tag>
```

## Example
```bash
oc import-image --confirm quay.io/practicalopenshift/hello-world
```

## Tag image
```bash
oc tag quay.io/image-name:tag image-name:tag
```

## List ImageStreams
```bash
oc get is
```

## List ImageStreamTags
```bash
oc get istag
```

## Deploy using ImageStream
```bash
oc new-app myproject/hello-world
```

---

# ☁️ Private Images

## Build remote image
```bash
docker build -t quay.io/$REGISTRY_USERNAME/private-repo .
```

## Login to registry
```bash
docker login quay.io
```

## Push image
```bash
docker push quay.io/$REGISTRY_USERNAME/private-repo
```

---

# 🔑 Private Registry Access in OpenShift

## Load credentials
```bash
source credentials.env
```

## Create registry secret
```bash
oc create secret docker-registry <secret-name> \
  --docker-server=$REGISTRY_HOST \
  --docker-username=$REGISTRY_USERNAME \
  --docker-password=$REGISTRY_PASSWORD \
  --docker-email=$REGISTRY_EMAIL
```

## Link secret
```bash
oc secrets link default <secret-name> --for=pull
```

## Verify service account
```bash
oc describe serviceaccount/default
```

## Deploy private image
```bash
oc new-app quay.io/$REGISTRY_USERNAME/private-repo \
  --as-deployment-config
```

---

# 🌐 Services

## Service documentation
```bash
oc explain service
```

## Service spec
```bash
oc explain service.spec
```

## Service YAML
```bash
oc get -o yaml service/hello-world
```

## Route YAML
```bash
oc get -o yaml route/hello-world
```

---

# 🔌 Creating Services

## Expose Pod
```bash
oc expose --port 8080 pod/hello-world-pod
```

## Expose DeploymentConfig
```bash
oc expose --port 8080 dc/hello-world
```

## Verify connection
```bash
oc status
```

---

# 🌍 Environment Variables for Services

## List env variables
```bash
env
```

## Use service environment variables
```bash
wget -qO- \
$HELLO_WORLD_POD_PORT_8080_TCP_ADDR:\
$HELLO_WORLD_POD_PORT_8080_TCP_PORT
```

---

# 🌎 Routes

## Create Route
```bash
oc expose svc/hello-world
```

## Get Route URL
```bash
oc status
```

## Test Route
```bash
curl <route-url>
```

---

# ⚙️ Source-to-Image (S2I)

## Deploy app without Dockerfile
```bash
oc new-app <git-url> \
  --as-deployment-config
```

## Create S2I build
```bash
oc new-build <git-url>
```

## Example
```bash
oc new-app https://gitlab.com/practical-openshift/labs.git \
  --context-dir s2i/ruby \
  --as-deployment-config
```

---

# 🧱 Specify Builder Image

## General syntax
```bash
oc new-app builder-image~<git-url> \
  --as-deployment-config
```

## Ruby example
```bash
oc new-app \
  ruby~https://gitlab.com/practical-openshift/labs.git \
  --context-dir s2i/ruby \
  --as-deployment-config
```

---

# 🛠️ Overriding S2I Scripts

## Script locations
```text
.s2i/bin/assemble
.s2i/bin/run
```

## Original scripts
```text
/usr/libexec/s2i/assemble
/usr/libexec/s2i/run
```

---

# 📈 Manual Scaling

## Scale DeploymentConfig
```bash
oc scale dc/<dc-name> --replicas=<count>
```

## Scale to 3
```bash
oc scale dc/hello-world --replicas=3
```

## Scale to 1
```bash
oc scale dc/hello-world --replicas=1
```

---

# 📊 Autoscaling

## Create HPA
```bash
oc autoscale dc/<dc-name> \
  --min <min-pods> \
  --max <max-pods> \
  --cpu-percent=<target-cpu>
```

## Example
```bash
oc autoscale dc/hello-world \
  --min 1 \
  --max 10 \
  --cpu-percent=80
```

## View HPA
```bash
oc get hpa
```

## Describe HPA
```bash
oc describe hpa/hello-world
```

## HPA YAML
```bash
oc get -o yaml hpa/hello-world
```

---

# 💾 Volumes

## Mount emptyDir
```bash
oc set volume dc/<dc-name> \
  --add \
  --type emptyDir \
  --mount-path <path>
```

## Example
```bash
oc set volume dc/hello-world \
  --add \
  --type emptyDir \
  --mount-path /empty-dir-demo
```

---

# 📁 ConfigMap Volumes

## Create ConfigMap
```bash
oc create configmap cm-volume \
  --from-literal file.txt="ConfigMap file contents"
```

## Mount ConfigMap
```bash
oc set volume dc/hello-world \
  --add \
  --configmap-name cm-volume \
  --mount-path /cm-directory
```

---

# 📚 Volume Documentation

## Kubernetes Volumes
:contentReference[oaicite:0]{index=0}

## OpenShift volume spec
```bash
oc explain persistentvolume.spec
```

---

# 🧩 Templates

## Create template
```bash
oc create -f template/hello-world-template.yaml
```

## List templates
```bash
oc get template
```

## Deploy template
```bash
oc new-app hello-world
```

---

# ⚙️ Template Parameters

## Override parameters
```bash
oc new-app hello-world \
  -p MESSAGE="Hello from parameter override."
```

---

# 🔄 Process Templates

## Process template
```bash
oc process hello-world
```

## YAML output
```bash
oc process hello-world -o yaml
```

## Process with parameters
```bash
oc process hello-world -o yaml \
  -p MESSAGE="Hello from oc process"
```

## Save processed template
```bash
oc process hello-world -o yaml \
  -p MESSAGE="Hello from oc process" \
  > processed-objects.yaml
```

## Verify file
```bash
head processed-objects.yaml
```

## Create resources
```bash
oc create -f processed-objects.yaml
```

---

# 📄 Process Template From File

## Process file template
```bash
oc process -o yaml \
  -f template/hello-world-template.yaml
```

## Deploy file template
```bash
oc new-app \
  -f template/hello-world-template.yaml \
  -p MESSAGE="Hello from oc new-app with a file"
```

---

# 🏗️ Creating Custom Templates

## Export resources
```bash
oc get -o yaml dc,is,bc,svc,route
```

## Save template
```bash
oc get -o yaml dc,is,bc,svc,route \
  > test-template.yaml
```

## Edit template
```bash
vi test-template.yaml
```

---

# 📝 Template Conversion Steps

1. Change `items` to `objects`
2. Change `kind: List` to `kind: Template`
3. Add a `metadata.name`
4. Remove `status`
5. Clean unnecessary metadata
6. Remove auto-generated resources
7. Optionally add parameters

---

# 🧪 Example Template Parameter

```yaml
- description: Message to respond to requests with
  displayName: Message
  name: MESSAGE
  required: false
  value: Hello from the default value for the template
```

## Use parameter in YAML
```yaml
env:
  - name: MESSAGE
    value: ${MESSAGE}
```

---

# 📌 Useful Commands Summary

| Command | Purpose |
|---|---|
| `oc get pods` | List Pods |
| `oc describe <resource>` | Detailed resource info |
| `oc logs -f` | Stream logs |
| `oc status` | Project overview |
| `oc rollout latest` | Trigger deployment |
| `oc scale` | Scale replicas |
| `oc autoscale` | Configure HPA |
| `oc expose` | Create services/routes |

---

# 📚 Learning Resources

- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}
- :contentReference[oaicite:3]{index=3}

---
