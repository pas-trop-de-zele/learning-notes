## Glossary
- MIG Profile: size and capabilities of 1 MIG instance 1g.23gb/2g.45gb/4g.90gb (B200 profiles)
- MIG geometry: the combination of MIG Profile which represent a node
    - Ex: 2 × 1g.23gb + 1 × 2g.45gb + 1 × 3g.90gb
    - Ex: 1 × 1g.23gb + 1 × 2g.45gb + 1 × 4g.90gb

## Usage:
- ClusterPolicy set cluster mig node: 
    - `none`: no GPU can use MIG 
    - `single`: each GPU MIG geometry could only be homogeneous 
    - `mixed`: GPU MIG geometry could be homogeneous or heterogenous
    ```
    apiVersion: nvidia.com/v1
    kind: ClusterPolicy
    metadata:
      name: cluster-policy

    spec:
      # ...
      mig:
        strategy: mixed
    ```
- Each node than have `mig-config: foo-mig-config-name` with `mig-config: all-balanced` being the default manufacture specification.
    ```
    // B200 all-balanced configuration
    2 × 1g.23gb
    1 × 2g.45gb
    1 × 3g.90gb
    ```
- Full example of custom mig config flow
    ```
    # ClusterPolicy
    apiVersion: nvidia.com/v1
    kind: ClusterPolicy
    metadata:
      name: cluster-policy
    spec:
      # ...
      mig:
        strategy: mixed
      migManager:
        enabled: true
        config:
          name: custom-mig-config
      # ...
    ```
    ```
    # Custom MIG ConfigMap
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: custom-mig-config
      namespace: gpu-operator
    data:
      config.yaml: |
        version: v1
        mig-configs:
          all-disabled:
            - devices: all
              mig-enabled: false

          b200-all-small:
            - devices: all
              mig-enabled: true
              mig-devices:
                "1g.23gb": 7

          b200-all-medium:
            - devices: all
              mig-enabled: true
              mig-devices:
                "2g.45gb": 3

          b200-mixed:
            - devices: all
              mig-enabled: true
              mig-devices:
                "4g.90gb": 1
                "2g.45gb": 1
                "1g.23gb": 1
    ```
    ```
    apiVersion: v1
    kind: Node
    metadata:
      name: gpu-node-01
      labels:
        # ...
        nvidia.com/mig.capable: "true"
        nvidia.com/mig.strategy: mixed
        nvidia.com/mig.config: b200-mixed
        nvidia.com/mig.config.state: success

        nvidia.com/mig-1g.23gb.count: "1"
        nvidia.com/mig-2g.45gb.count: "1"
        nvidia.com/mig-4g.90gb.count: "1"

    status:
      capacity:
        # ...
        nvidia.com/mig-1g.23gb: "1"
        nvidia.com/mig-2g.45gb: "1"
        nvidia.com/mig-4g.90gb: "1"

      allocatable:
        # ...
        nvidia.com/mig-1g.23gb: "1"
        nvidia.com/mig-2g.45gb: "1"
        nvidia.com/mig-4g.90gb: "1"
    ```

## Allocatable resource single `single` mig strategy
- `single` mig strategy dictates allocatable resource not as the MIG profiles but merely `nvidia.com/gpu`
    ```
    # B200 → 7 × 1g.23gb MIG instances

    # single mig
    allocatable:
      nvidia.com/gpu: "7"

    # mixed mig
    allocatable:
      nvidia.com/mig-1g.23gb: "7"
    ```
- Note if the GPU cannot be fully partitioned using the selected MIG profile, the remaining resource will be left unused
    ```
    # B200 → 1 × 4g.90gb MIG instance
    # 3 compute slice wasted

		# single mig
    allocatable:
      nvidia.com/gpu: "1"

		# mixed mig
    allocatable:
      nvidia.com/mig-4g-90gb: "1"
    ```
## Dynamic mig
- Instead of having to manually set the MIG geometry per node, dynamic MIG lets pod request certain MIG partition, then dynamic MIG will allocate a correpsonding partition on a GPU
- Note: as MIG split a GPU into hardware isolated partition, one partition going down does not affect other partition

## Concern
- If user can set 
- How to prevent user from switching node(s) MIG geometry as that would cause breakage existing GPU workloads -> rbac?
- We have to ensure the profiles are mixed otherwise you might have single mode with pod requesting 4g.90gb and waste the other possible 3 slices -> This is going to affect scheduling