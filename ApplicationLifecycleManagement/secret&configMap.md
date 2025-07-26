What is a ConfigMap?
A ConfigMap is used to store non-sensitive configuration data as key-value pairs.
Examples: application settings, file paths, feature flags.

🔐 What is a Secret?
A Secret stores sensitive data, like passwords, tokens, and API keys.
They are base64-encoded and treated more securely by Kubernetes.

🔧 Comparison: ConfigMap vs Secret
Feature	ConfigMap	Secret
Stores	Non-sensitive config	Sensitive data
Encoded	No	Yes (base64)
Use with Env	✅ Yes	✅ Yes
Use with Volume	✅ Yes	✅ Yes
Auto-mounted	✅ Optional	✅ Optional
Encryption	❌ Plaintext in etcd (by default)	✅ Can be encrypted at rest

📦 How to Create a ConfigMap
1. From literals:

kubectl create configmap app-config \
  --from-literal=APP_ENV=production \
  --from-literal=APP_PORT=8080

2. From a file:

kubectl create configmap app-config \
  --from-file=config.txt

3. From a directory (each file becomes a key):

kubectl create configmap app-config \
  --from-file=/etc/myapp/configs/

🔐 How to Create a Secret
1. From literals:

kubectl create secret generic db-secret \
  --from-literal=DB_USER=root \
  --from-literal=DB_PASS=password123

2. From a file:

kubectl create secret generic tls-secret \
  --from-file=cert.pem \
  --from-file=key.pem

3. From base64 manually (YAML):
```
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: cm9vdA==         # base64 of "root"
  password: cGFzc3dvcmQxMjM= # base64 of "password123"
```

🧪 How to Use ConfigMap or Secret in a Pod

✅ As Environment Variables

envFrom:
- configMapRef:
    name: app-config
- secretRef:
    name: db-secret

✅ As Volume Mounts
```
volumes:
- name: config-vol
  configMap:
    name: app-config

- name: secret-vol
  secret:
    secretName: db-secret

volumeMounts:
- name: config-vol
  mountPath: /etc/config

- name: secret-vol
  mountPath: /etc/secret
```
📋 View, Edit, and Delete

View:

kubectl get configmap
kubectl get secret

kubectl describe configmap app-config
kubectl describe secret db-secret

Decode secret value:

kubectl get secret db-secret -o jsonpath="{.data.DB_USER}" | base64 -d

Edit:

kubectl edit configmap app-config
kubectl edit secret db-secret

Delete:

kubectl delete configmap app-config
kubectl delete secret db-secret

