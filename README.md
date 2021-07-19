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
  name: multi-container
spec: 
  terminationGracePeriodSeconds: 0
  containers:
  - name: webserver
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: html-volume
      mountPath: /usr/share/nginx/html
  - name: worker
    image: nginx:latest
    command: ["/bin/sh"]
    args: ["-c", "while true; do date > /usr/share/nginx/html/index.html; sleep 1; done"]
    volumeMounts:
    - name: html-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: html-volume
    hostPath:
      path: /usr/share/nginx/html
      type: DirectoryOrCreate
```

Create the pod:
`kubectl create -f pod.yaml`

Forward port across kubernetes
`kubectl port-forward --address 0.0.0.0 pod/multi-container 1313:80`
We exposed port 80 on the container (default for nginx), but the container is nested within Kubernetes. This way we can bind kubernetes to port 1313 and it will direct us to our container.

Navigate to localhost:1313/index.html in a browser, the current date should be presented. 
If you refresh, the time will update due to the second container editing the file in the shared volume.

## Inspect the Pod
Describe the pod
`kubectl describe pods multi-container`

Inspect pod logs
`kubectl logs multi-container webserver`
`kubectl logs multi-container worker`
