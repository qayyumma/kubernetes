
1. Labels

```
metadata:
  labels:
    app: frontend
    tier: web
```

2. Label Selector

```
selector:
  matchLabels:
    app: frontend

```
Set-based selectors (in, notin, exists)

```
selector:
  matchExpressions:
    - key: tier
      operator: In
      values:
        - web
        - backend
```


3. Service selecting that pod using labels:

```
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
    env: prod
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

```

4. Node and Pod 

```
kubectl label nodes <node-name> disktype=ssd
```

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-ssd
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    disktype: ssd
```
