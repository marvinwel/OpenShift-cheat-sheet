# OpenShift Cheat Sheet

This comprehensive cheat sheet provides quick references for common commands and operations in OpenShift 4.10.40. Learn how to build and run containers, manage pods, services, routes, templates, scaling, and more.

## Table of Contents

- [Building Containers](#building-containers)
- [Running Containers](#running-containers)
- [Stopping Containers](#stopping-containers)
- [Logging In and Out](#logging-in-and-out)
- [Project Basics](#project-basics)
- [Pod Documentation](#pod-documentation)
- [Creating Pods from Files](#creating-pods-from-files)
- [Port Forwarding for Pods](#port-forwarding-for-pods)
- [Shell into Pods](#shell-into-pods)
- [Deleting Pods](#deleting-pods)
- [Deleting all resources for an app](#deleting-all-resources-for-an-app)
- [Deploying as DeploymentConfigs](#deploying-as-deploymentconfigs)
- [Starting and Reverting Versions](#starting-and-reverting-versions)
- [Trigger Management](#trigger-management)
- [Deployment Hooks](#deployment-hooks)
- [Switching to Recreate Strategy](#switching-to-recreate-strategy)
- [Readiness and Liveness Probes](#readiness-and-liveness-probes)
- [Creating and Using ConfigMaps](#creating-and-using-configmaps)
- [Creating and Using Secrets](#creating-and-using-secrets)
- [Using Private Images with OpenShift](#using-private-images-with-openshift)
- [Creating and Using ImageStreams](#creating-and-using-imagestreams)
- [Working with WebHooks](#working-with-webhooks)
- [Using Source-to-Image (S2I)](#using-source-to-image-s2i)
- [Using Templates](#using-templates)
- [Mounting Volumes](#mounting-volumes)
- [Autoscaling](#autoscaling)
- [Creating Services](#creating-services)
- [Creating Routes](#creating-routes)

## Building Containers

Build Docker images based on the current directory.

```
docker build .
docker build -t your-tag .
```

## Running Containers

Start and manage Docker containers.

```
docker images
docker run -dit <image tag>
docker run -dit quay.io/practicalopenshift/hello-world 
docker run -dit --name <name-of-container> -p 8080:8080 quay.io/practicalopenshift/hello-world
```

## Stopping Containers

Stop running Docker containers.

```
docker ps
docker kill <Container ID>
```

## Logging In and Out

Log in and out of OpenShift clusters.

```
oc login
oc login <cluster address>
oc login --token= <token> --server=<server>  --insecure-skip-tls-verify=true
oc logout
```

## Project Basics

Manage projects in OpenShift.

```
oc project
oc new-project demo-project
oc projects
oc project <project name>
```

## Pod Documentation

Explore built-in documentation for Pods.

```
oc explain pod
oc explain pod.spec
oc explain pod.spec.containers
```

## Creating Pods from Files

Create and manage Pods using files.

```
oc create -f pods/pod.yaml
oc get pods
```

## Port Forwarding for Pods

Forward ports to Pods.

```
oc port-forward <pod name> <local port>:<pod port>
oc port-forward hello-world-pod 8080:8080
```

## Shell into Pods

Access and interact with Pods.

```
oc rsh <pod name>
```

## Deleting Pods

Delete Pods and resources.

```
oc delete <resource type> <resource name>
oc delete pod hello-world-pod
```

## Deleting all resources for an app

Delete all resources in an app

```
oc describe dc/<name-of-deploment-config>
oc delete all -l app=<name-of-app>
```


## Deploying as DeploymentConfigs

Deploy applications using Image.

```
oc new-app --name <desired name> <image tag> --as-deployment-config
```

Deploy applications using a Git URL.

```
oc new-app <git repo URL> --as-deployment-config
```


## Starting and Reverting Versions

Roll out and rollback application versions.

```
oc rollout latest dc/hello-world
oc rollback dc/hello-world
```

## Trigger Management

Manage triggers for DeploymentConfigs.

```
oc set triggers dc/<dc name>
oc set triggers dc/<dc name> --remove --from-config
oc set triggers dc/<dc name> --from-config
oc set triggers dc/<dc name> --remove --from-image <image name>:<tag>
oc set triggers dc/<dc name> --from-image <image name>:<tag> -c <container name>
```

## Deployment Hooks

Manage deployment hooks for DeploymentConfigs.

```
oc set deployment-hook dc/hello-world --pre -c hello-world -- /bin/echo Hello from pre-deploy hook
```

## Switching to Recreate Strategy

Switch to the Recreate strategy for DeploymentConfigs.

```
oc edit dc/hello-world
# Change spec.strategy.type to Recreate
```

## Readiness and Liveness Probes

Configure readiness and liveness probes.

```
oc set probe dc/hello-world --liveness --open-tcp=8080
oc set probe dc/hello-world --readiness --get-url=http://:8080/health/readiness
oc set probe dc/hello-world --readiness -- exit 0
```

## Creating and Using ConfigMaps

Create and consume ConfigMaps.

```
oc create configmap <configmap-name> --from-literal KEY="VALUE"
oc create configmap <configmap-name> --from-file=MESSAGE.txt
oc create configmap <configmap-name> --from-file=MESSAGE=MESSAGE.txt
oc create configmap <configmap-name> --from-file pods
oc get -o yaml configmap/<configmap-name>
```

## Creating and Using Secrets

Create and consume Secrets.

```
oc create secret generic <secret-name> --from-literal KEY="VALUE"
oc get -o yaml secret/<secret-name>
oc set env dc/<dc-name> --from secret/<secret-name>
```

## Using Private Images with OpenShift

Build, push, and use private images.

```
docker build -t quay.io/$REGISTRY_USERNAME/private-repo .
docker login quay.io
docker push quay.io/$REGISTRY_USERNAME/private-repo
oc create secret docker-registry <secret name> --docker-server=$REGISTRY_HOST --docker-username=$REGISTRY_USERNAME --docker-password=$REGISTRY_PASSWORD --docker-email=$REGISTRY_EMAIL
oc secrets link default <secret
```

## Creating and Using ImageStreams

Create ImageStreams.

```
oc import-image --confirm <image tag>
```

List ImageStreams

```
oc get is
```

Deploy an application based on your new ImageStream

```
oc new-app myproject/hello-world --name <name-of-app> --as-deployment-config
```

## Working with WebHooks

print build config yaml to screen
```
oc status
oc get -o yaml bc/<name-of-buildconfig>
```

Copy Secret in yaml file triggers.generic.secret
```
export GENERIC_SECRET=<secret> 
```

Get the webhook URL Webhook.Generic.URL
```
oc describe buildconfig/<name-of-buildconfig>
```

Test connection

```
curl -X POST -k <webhook URL>
```

## Using Source-to-Image (S2I)

Build and deploy using S2I.

```
oc new-app <Git URL with no Dockerfile> \
  --as-deployment-config
```
Specifying a builder image

```
oc new-app <builder-Image>~<git-URL> --as-deployment-config
```

## Using Templates

Create the template from the file

```
oc create -f template/hello-world-template.yaml
```

Check the template

```
oc get template
```

Create an application based on the template

```
oc new-app hello-world
```

## Mounting Volumes

Mount volumes in Pods.

```
oc set volume dc/<deploy-config> --add --type emptyDir --mount-path /path-inside-container>
```

## Manual Scaling

Manually scale DeploymentConfigs.

```
oc scale dc/<dc name> --replicas=<desired replicas>
```

## Autoscaling

General Syntax to create a HorizontalPodAutoscaler (HPA)

```
oc autoscale dc/<dc name> \
  --min <desired minimum pods> \
  --max <desired maximum pods> \
  --cpu-percent=<desiredTargetCPU>
```

Check the HPA

```
oc get hpa
```
## Creating Services

Create a service for a single pod

```
oc expose --port 8080 pod/hello-world-pod
```

Create a service for a DeploymentConfig 

```
oc expose --port 8080 dc/hello-world
```


## Creating Routes

Create routes to expose services.

```
oc expose svc/hello-world
```
Get the Route URL

```
oc status
```

Check the route

```
curl <route from oc status>
```
