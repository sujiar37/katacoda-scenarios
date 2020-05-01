We will follow the official docs from Docker documentation to setup the private registry and make the necessary modifications to deploy the same in kubernetes. Note that, this deployment is purely development purpose only. In realtime, the private registry in production scenario is completely different as it should be properly secured.

1. Create a Pod named *myregistry* using the image **registry:2**

```shell
`cat <<EOF | kubectl apply -f -
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
EOF`{{ execute }}
```