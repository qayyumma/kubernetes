1. Co-located Pod

Start simultaneously  all pod 

```
apiVersion: v1
kind: Pod
metadata:
  name: colocated-pod
spec:
  containers:
  - name: main-app
    image: busybox
    command: ["sh", "-c", "while true; do echo $(date) Writing log >> /var/log/app.log; sleep 2; done"]
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log

  - name: logger
    image: busybox
    command: ["sh", "-c", "tail -n+1 -F /var/log/app.log"]
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log

  volumes:
  - name: shared-logs
    emptyDir: {}

```

2. Init pod

start container execute job and exit container before starting main container 

```
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  initContainers:
  - name: wait-for-db
    image: busybox
    command: ['sh', '-c', 'until nslookup mysql; do echo waiting for mysql; sleep 2; done']

  containers:
  - name: app
    image: busybox
    command: ['sh', '-c', 'echo App started! && sleep 3600']
```

3. Sidercar Pod 

start container before starting main container run till the live of pod 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: alpine:latest
          command: ['sh', '-c', 'while true; do echo "logging" >> /opt/logs.txt; sleep 1; done']
          volumeMounts:
            - name: data
              mountPath: /opt
      initContainers:
        - name: logshipper
          image: alpine:latest
          restartPolicy: Always
          command: ['sh', '-c', 'tail -F /opt/logs.txt']
          volumeMounts:
            - name: data
              mountPath: /opt
      volumes:
        - name: data
          emptyDir: {}
```
