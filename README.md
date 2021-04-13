# docker-build
Docker CLI and docker-compose inside container


## Use inside Kubernetes

```yaml

apiVersion: batch/v1
kind: Job
metadata:
  name: build-web-dev
spec:
  template:
    spec:
      containers:
      - name: build-web-dev
        image: vtrifonovdocker/docker-build:latest
        command: ["/bin/sh"]
        args: ["-c", "docker build -t web:dev -f /src/Dockerfile /src"]
        volumeMounts:
          - mountPath: /var/run/docker.sock
            name: docker-sock
          - mountPath: /root/.docker/
            name: docker-config
          - name: src
            mountPath: /src
      restartPolicy: Never
      volumes:
        - name: docker-sock
          hostPath:
            path: /var/run/docker.sock
            type: Socket
        - name: docker-config
          emptyDir: {}
        - name: src
          hostPath:
            path: {{ .Values.image.hostSrcPath }}
  backoffLimit: 4

```

## Use in Docker

```
docker run -it -v /var/run/docker.sock:/var/run/docker.sock vtrifonovdocker/docker-build:latest bash
```