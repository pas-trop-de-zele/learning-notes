## BLUF
This document collects different helm tibits I have found over the years

## Basics
- When looking in a helm chart, the outer most `Chart.yaml` file name dictates the outer key. Let say the outer most `Chart.yaml` name is `gpu-operator`.
  ```
  # gpu-operator/Chart.yaml
  name: gpu-operator

  dependencies:
    - name: node-feature-discovery
      version: ...


  # gpu-operator/values.yaml
  node-feature-discovery:
    worker:
      enable: true
  ```

- Now say the `gpu-operator` has a dependency call `node-feature-discovery` (nfd), nfd will be the subchart. So to access the subchart, if you are parent of gpu-opreator you will do `gpu-operator.node-feature-discovery...`
  ```
  gpu-operator/
  ├── Chart.yaml
  │     name: gpu-operator
  │
  ├── values.yaml
  │     node-feature-discovery:
  │       worker:
  │         ...
  │
  └── charts/
        └── node-feature-discovery/
              ├── Chart.yaml
              └── values.yaml
  ```

## `--take-ownership`
- Used to take ownership of another resource as long as these attribute matches:
  - Kind
  - Namespace
  - Name
- Example: OLM deployed gpu-operator deploys daemonset node-feature-discovery-worker in namespace foo. If I deploy another helm-chart gpu-operator which deploys another daemonset with the same name in the same namespace, the node-feature-discovery-worker will be now managed by my helm-chart gpu-operator
- Note: As helm only check `Kind`, I would put any new Daemonset with any images and as long as the name + namespace matches helm will allow me to take over.
- Metadata is how helm used to keep track of resource ownership, not to be mistaken with kubernetes `ownerReference` which is used for garbage collection tracking
  ```
  metadata:
  labels:
    app.kubernetes.io/managed-by: Helm

  annotations:
    meta.helm.sh/release-name: <helm-release-name>
    meta.helm.sh/release-namespace: <helm-release-namespace>
  ```