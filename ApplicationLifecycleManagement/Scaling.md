✅ Horizontal Scaling in Kubernetes
🔹 Manual Scaling

kubectl scale deployment myapp --replicas=6
This directly sets the number of pod replicas.

🔹 Automatic Scaling via HPA

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80  # Target: 80% CPU usage
🧠 Important: HPA needs:


resources:
  requests:
    cpu: 100m
  limits:
    cpu: 200m
🔢 HPA Autoscaling Formula

desiredReplicas = currentReplicas × ( currentCPU utilization / target utilization )
📊 Example Calculation
Runtime Usage: 160m

CPU Request: 200m

Utilization: (160 / 200) × 100 = 80%

Target Utilization in HPA: 80%

➡️ Since current = target, no scaling needed.
➡️ HPA sees the system as balanced.

✅ Everything you've written is accurate and a great summary of CPU-based HPA behavior in Kubernetes.

Let me know if you'd like to extend this for memory-based HPA or custom metrics!


In-place Resize of Pod Resources

To use Inplace Scaling this need to be enable 
➡️ This must be set in the Kubelet and API server startup config.
➡️ It's disabled by default because it's still in alpha as of Kubernetes v1.27.

FEATURE_GATES=InPlacePodVerticalScaling=true


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        resizePolicy:
        - resourceName: cpu
          restartPolicy: NotRequired
        - resourceName: memory
          restartPolicy: RestartContainer
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"

```


| Field                | Meaning                                                       |
| -------------------- | ------------------------------------------------------------- |
| `resizePolicy`       | Tells Kubernetes what to do when you **resize CPU or memory** |
| `NotRequired`        | CPU can be changed **without restart**                        |
| `RestartContainer`   | Memory changes **will restart** the container                 |
| `resources.requests` | Minimum guaranteed resources to allocate                      |
| `resources.limits`   | Maximum resources container can use                           |


Vertical Pod Autoscaling

Vertical Scaling in Kubernetes
Vertical scaling means adjusting the CPU/memory resources allocated to your Pods. This can be done manually or automatically using VPA.

🔹 Manually Adjusting Resources (Vertical Scaling)
You can manually change resource requests and limits:

resources:
  requests:
    cpu: 250m
    memory: 256Mi
  limits:
    cpu: 500m
    memory: 512Mi
Apply via:

kubectl apply -f deployment.yaml
Or edit an existing deployment:

kubectl edit deployment myapp

🔹 Automatically: Using VPA (Vertical Pod Autoscaler)

✅ VPA YAML Example:

```
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       myapp
  updatePolicy:
    updateMode: "Auto"  # Other options: "Off", "Initial"
  resourcePolicy:
    containerPolicies:
    - containerName: "*"
      controlledResources: ["cpu", "memory"]
```

| Mode      | Behavior                                                              |
| --------- | --------------------------------------------------------------------- |
| `Off`     | VPA **recommends**, but doesn’t update pods                           |
| `Initial` | Applies recommendations **only at pod creation**                      |
| `Auto`    | VPA **automatically updates pods** by restarting them with new values |


🔢 VPA Scaling Logic (Simplified)

VPA works by:

Watching historical usage of CPU and memory

Comparing it to the current requests

Recommending new values (or applying them if in Auto mode)

There is no formula like HPA, but behind the scenes, VPA uses:

Pod usage over time (from metrics server)

Confidence intervals

Stability window (to avoid frequent restarts)

📊 View VPA Recommendation

kubectl describe vpa myapp-vpa
Example output:

Recommendation:
  Container Recommendations:
    Container Name: myapp
    Lower Bound:
      cpu:     100m
      memory:  200Mi
    Target:
      cpu:     250m
      memory:  300Mi
    Upper Bound:
      cpu:     500m
      memory:  512Mi
