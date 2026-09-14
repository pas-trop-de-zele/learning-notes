## BLUF
- `containerd`is the container manager - handles image pulling, unpack image, preparing container file system, etc
- `runc` is the underlying container which creates new process, cgroup, etc
  ```
  kubelet
     |
     | "run this Pod"
     v
  containerd
     |
     +--> pull image
     +--> unpack image
     +--> prepare container filesystem
     +--> track container lifecycle
     |
     v
  runc
     |
     +--> create namespaces
     +--> create/configure cgroups
     +--> set up mounts
     +--> start the actual process
     |
     v
  Linux kernel
  ```
- `cri-o` is an alternative to containerd by Redhat

## Flow
- In a usual when GPU is plugged into a linux machine, nvidia driver must be installed before the kernel can communicate with the device. When a container needs a gpu it does roughly this
  ```
  process
     |
     | open("/dev/nvidia0")
     v
  NVIDIA kernel driver
     |
     v
  GPU
  ```
- In a container world however, there is nothing to tell the kernel to expose certain device `/dev/nvidia0` to the container, something must tell the kernel to do so. Nvidia container toolkit is what modifies the container definition so `/dev/nvidia0` get exposed on the container
  ```
  HOST

  /dev/nvidia0
        |
        | exposed into container
        v

  CONTAINER

  /dev/nvidia0
        |
        v
  container process
        |
        v
  NVIDIA kernel driver
        |
        v
  GPU
  ```
- Now each application needs different application like PyTorch, nvidia container toolkit does not enable those but more so generic nvidia specific software i.e. `libcuda`
  ```
  Container image
      = application's GPU software stack

  NVIDIA Container Toolkit
      = connects that software stack to
        the host NVIDIA driver + GPU

  NVIDIA driver
      = actually controls the GPU
  ```
- Concretely, `runc` create the container, then via a prestart hook, `nvidia-container-runtime`would inject the selected GPU devices and supporting software
  ```
  containerd
     ↓
  nvidia-container-runtime
     |
     | take OCI config.json
     | add NVIDIA-specific setup
     v
  runc
     ↓
  container process
  ```

## What is Container Device Interface `CDI`?
- In the old flow, `nvidia-container-runtime` would need to know that for a certain device we need to inject these supporting software
- In CDI world, nvidia container toolkit would expose device cdi spec i.e. `/var/run/cdi/nvidia.yaml`. New flow would look like 
  ```
  kubelet
     ↓
  containerd
     |
     | sees container requests:
     | nvidia.com/gpu=0
     |
     | reads CDI spec:
     | /var/run/cdi/nvidia.yaml
     |
     | applies the device's "containerEdits"
     | - device nodes
     | - mounts
     | - hooks
     | - env/config
     v
  runc
     ↓
  container process
  ```