# DevOps Kubernetes Project
### Project Description
As a Kubernetes developer, demonstrate the usage of Volume to communicate between two Containers running in the same Pod. 
Check the health of the running pod by describing it with the list of events, inspecting its logs, and finally debugging its containers.

## Configure Environment
### Initialize Kubernetes Cluster

`minikube start`

### Deploy Pod
Create a configuration yaml file:

`vim pod.yaml`

Set the following contents:

```
apiVersion: v1
kind: Pod
metadata:
  name: mulit-container
spec: 
  terminationGracePeriodSeconds: 0
  containers:
  - name: webserver
    image: nginx:latest
    ports:
    - containerPort: 80
  - name: alpine
    image: alpine:3.5
    command: ["watch", "wget", "-qO-", "localhost"]
  volumes:
  - name: html-volume
    hostPath:
      path: /html
      type: Directory`
```

Create the pod:
`kubectl create -f pod.yaml`

## Create HTML File
