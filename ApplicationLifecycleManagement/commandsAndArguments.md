🐳 Docker: ENTRYPOINT vs CMD

Aspect	ENTRYPOINT	CMD
Purpose	Defines the main executable	Provides default arguments
Overridable?	Not easily overridden	Easily overridden by docker run ...
Used Together?	Yes, CMD gives args to ENTRYPOINT	Alone, CMD runs as command

🔧 Dockerfile Examples:
1. Only CMD

Dockerfile
```
FROM ubuntu
CMD ["sleep", "1000"]
```

→ docker run image runs:
sleep 1000

2. Only ENTRYPOINT

Dockerfile
```
FROM ubuntu
ENTRYPOINT ["sleep"]
```
→ docker run image 2000 runs:
sleep 2000


3. Both ENTRYPOINT and CMD

Dockerfile
```
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["1000"]
```
→ docker run image runs:

sleep 1000

→ docker run image 5000 runs:

sleep 5000

☸️ Kubernetes Pod: command and args

Pod Field	Maps to Docker	Purpose

command	ENTRYPOINT	Overrides the container’s entrypoint

args	CMD	Overrides the container’s default arguments

✅ Kubernetes Pod Example:

```
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep"]
    args: ["1000"]
```

→ This works like:

sleep 1000

Even if Docker image has its own ENTRYPOINT or CMD, command and args override them in Kubernetes.

🔄 Docker vs Kubernetes Mapping
Dockerfile	Kubernetes Pod
ENTRYPOINT	command
CMD	args

🧪 Advanced Example: Bash command
```
containers:
- name: busybox
  image: busybox
  command: ["/bin/sh", "-c"]
  args: ["echo hello && sleep 5000"]
```
This runs a shell command in the container.

📝 Tips
Don’t use command if you want to preserve the Docker image's default behavior.

Use both command and args if you want full control.

If a container isn't starting as expected, check if your command is overriding the original Docker ENTRYPOINT.
