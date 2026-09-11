## Commands to investigate image
- Pull image
```
> docker pull nvcr.io/nvidia/gpu-operator:v26.7.0
```

- List images
```
> docker image ls
IMAGE                                 ID             DISK USAGE   CONTENT SIZE
docker/getting-started:latest         d79336f4812b       72.9MB         20.8MB
nvcr.io/nvidia/gpu-operator:v26.7.0   ddba086bb593        850MB          213MB
```

- Creating a container from the image
```
> docker create nvcr.io/nvidia/gpu-operator:v26.7.0
39cdcc0f5b6476bb5f1b0fa0c0620ce7907d91800f2f5b59e69468b41a623fb8 // this is a container id
```

- List containers need `-a` flag to list not running container
```
> docker container ls 0a
```

- Start a container 
```
> docker start 39cdcc0f5b6476bb5f1b0fa0c0620ce7907d91800f2f5b59e69468b41a623fb8
```

- See logs from container
```
> docker logs 39cdcc0f5b6476bb5f1b0fa0c0620ce7907d91800f2f5b59e69468b41a623fb8

{"level":"info","ts":"2026-09-11T00:44:39Z","msg":"version: 10ee5b36-arm64, commit: 10ee5b3"}
{"level":"error","ts":"2026-09-11T00:44:39Z","msg":"OPERATOR_NAMESPACE environment variable not set, cannot proceed"}
{"level":"info","ts":"2026-09-11T00:44:49Z","msg":"version: 10ee5b36-arm64, commit: 10ee5b3"}
{"level":"error","ts":"2026-09-11T00:44:49Z","msg":"OPERATOR_NAMESPACE environment variable not set, cannot proceed"}
```

- find a file in an image by starting a temporary container
```
> docker run --rm --entrypoint sh --user 0 ddba086bb593 -c "find / -regex '.*gpucluster.*'"
/usr/bin/cleanup-gpuclusters
/opt/gpu-operator/nvidia.com_gpuclusters.yaml
```
  - `docker run --rm --entrypoint sh`: automatically remove container after exit, start shell upon start
  - `--user 0`: in linux `UID 0 = root`
  - `-c` is argument for sh, means run these command upon start shell, effectively running this upon container start `sh -c "find / -regex '.*gpucluster.*'"`
  - a more visual exaplanation
    ```
    docker run
      --rm
      --entrypoint sh
      --user 0
      ddba086bb593
      ---------------- IMAGE boundary
      -c "find / -regex '.*gpucluster.*'"
    ```