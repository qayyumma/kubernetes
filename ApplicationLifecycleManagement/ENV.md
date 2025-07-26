1. Literal Values
Used to set a static value directly in the YAML.

✅ Example:
```
env:
- name: ENVIRONMENT
  value: "production"
```

2. From ConfigMap or Secret

Used to inject values from Kubernetes ConfigMaps or Secrets.

a) From ConfigMap
```
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: my-config
      key: db_host
```

b) From Secret
```
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: my-secret
      key: db_password
```

You can also load all keys from a ConfigMap or Secret:

envFrom:
- configMapRef:
    name: my-config
- secretRef:
    name: my-secret

3. Dynamic Values (FieldRef / ResourceFieldRef)

a) From Pod Fields (FieldRef)
Used to pull values from pod metadata or status.
```
env:
- name: POD_NAME
  valueFrom:
    fieldRef:
      fieldPath: metadata.name
- name: POD_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP
```

Common fields:

metadata.name

metadata.namespace

status.podIP

spec.nodeName

b) From Container Resource Limits (ResourceFieldRef)

```
env:
- name: CPU_LIMIT
  valueFrom:
    resourceFieldRef:
      resource: limits.cpu

```

Other options:

limits.memory

requests.cpu

requests.memory

✅ Summary Table

Type	Key	Example Use

Literal	value	value: "prod"

ConfigMap	valueFrom.configMapKeyRef	key: db_url from a ConfigMap

Secret	valueFrom.secretKeyRef	key: password from a Secret

Pod Field	valueFrom.fieldRef	metadata.name, status.podIP

Resource Field	valueFrom.resourceFieldRef	limits.cpu, requests.memory
