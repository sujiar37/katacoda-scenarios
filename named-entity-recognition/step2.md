We will follow the official docs from Docker documentation to setup the private registry and make the necessary modifications to deploy the same in kubernetes. Note that, this deployment is purely development purpose only. In realtime, the private registry in production scenario is completely different as it should be properly secured.

1. Create a Pod named *myregistry* using the image **registry:2**

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: myregistry
  name: myregistry
spec:
  containers:
  - image: registry:2
    name: myregistry
    resources: {}
  restartPolicy: Never
EOF
```{{ execute }}

Now, check for the pod status and make sure it is in running state,

`kubectl get pods --show-labels -o wide`{{ execute }}

2. Create a service to expose the private registry via NodePort. So that way, we could access directly from all nodes using the nodeport *35000*

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  labels:
    run: myregistry
  name: myregistry
spec:
  ports:
  - port: 5000
    protocol: TCP
    targetPort: 5000
    nodePort: 30050
  selector:
    run: myregistry
  type: NodePort
EOF
```{{ execute }}

Now, check for the service and endpoint status and make sure those resolved properly,

`kubectl get services`{{ execute }}

`kubectl get ep`{{ execute }}

3. Let's Test whether private registry is working or not by pushing a custom image to there. For that, we can pull an image from DockerHub, then tag it with our private registry name and finally push it,


`docker pull hello-world`{{ execute }}

`docker tag hello-world 127.0.0.1:35000/hello-world`{{ execute }}

`docker push 127.0.0.1:35000/hello-world`{{ execute }}

YEAH !!! ALL SET, now, we are ready to build and push our applicaton and model images