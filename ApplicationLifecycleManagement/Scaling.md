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
