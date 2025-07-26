Deployment Strategies in Kubernetes

Kubernetes uses Deployment Strategies to manage how Pods are updated when a Deployment is changed.

1. RollingUpdate (Default Strategy)

Description:
Gradually replaces old Pods with new ones.

Ensures zero downtime by keeping some old Pods running while new ones come up.

Configuration:
```
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1       # Extra pods to add temporarily during the update
    maxUnavailable: 1 # Number of pods allowed to be unavailable
```

Default values:
maxSurge: 25%
maxUnavailable: 25%

Use when:
You want high availability.
Your application supports running multiple versions at once.


2. Recreate

Description:

Deletes all existing Pods before creating new ones.
Causes downtime, but ensures only one version runs at a time.

Configuration:

```
strategy:
  type: Recreate
```

Use when:
Your app cannot handle multiple versions running at once.
Database migrations or other stateful changes are involved.

Rolling Update Process (Simplified)
Create new pod with updated spec.

Wait until the new pod is ready.

Terminate one old pod.

Repeat until all old pods are replaced.

Check Rollout Status

kubectl rollout status deployment/<deployment-name>

⏪ Rollback

kubectl rollout status deployment/<name>	Check rollout progress

kubectl rollout undo deployment/<name>	Rollback to previous revision

kubectl rollout history deployment/<name>	View rollout revision history

kubectl rollout undo deployment/<name> --to-revision=<rev>	Rollback to a specific revision

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: kodekloud/webapp-color:v1


```
