Our app needs some environment variables to start with it such as,

* which port and address it has to listen always?
* where the internal log should write into and how would be the debug level?
* which model it needs to look for and specify the location?

So, we could pass all the above values via configMap and could point to our application. Let's create configMap first,

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: ce-predict-cm
  labels:
    type: prediction
    name: ce-predict
data:
  CE_PREDICT_HOST: "0.0.0.0"
  CE_PREDICT_PORT: "7000"
  CE_PREDICT_LOG_PATH: "/app/logs"
  CE_PREDICT_LOG_LEVEL: "INFO"
  CE_PREDICT_MODEL_PATH: "/model/"
EOF
```{{ execute }}

Now, we can jump into deployment and deploy our application by connecting all together what we have done so far,

```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
      name: ce-predict
      type: prediction
  name: ce-predict-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      name: ce-predict
      type: prediction
  template:
    metadata:
      labels:
        name: ce-predict
        type: prediction
    spec:
      initContainers:
      - name: model-container
        # Here is the model image in our private registry
        image: 127.0.0.1:30050/ner_model
        command: [ "/bin/sh","-c", "cp -rf /model/* /tmp/model/" ]
        volumeMounts:
          - name: ce-predict-model-volume
            mountPath: /tmp/model
      containers:
        # Here is the application image in our private registry
      - image: 127.0.0.1:30050/ner_app
        name: ce-predict
        envFrom:
        - configMapRef:
            name: ce-predict-cm
        volumeMounts:
          - name: ce-predict-model-volume
            mountPath: /model
      # A shared emptyDir {} volumes for both above containers
      volumes:
        - name: ce-predict-model-volume
          emptyDir: {}
EOF
```{{ execute }}

Check the status of deployment and pod status,

`kubectl get deployments`{{ execute }}

`kubectl get pods -o wide --show-labels`{{ execute }}

Finally expose the deployment via NodePort, so that we could make calls externally via the port *30070* and get the app responses,

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  labels:
    name: ce-predict
    type: prediction
  name: ce-predict-svc
spec:
  ports:
  - port: 7000
    protocol: TCP
    targetPort: 7000
    nodePort: 30070
  selector:
    name: ce-predict
    type: prediction
  type: NodePort
EOF
```{{ execute }}

Now, check for the service and endpoint status and make sure those resolved properly,

`kubectl get services`{{ execute }}

`kubectl get ep`{{ execute }}

ALL SET !!! Let's test the APP and wrap it up !!!