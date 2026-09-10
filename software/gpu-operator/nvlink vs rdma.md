## NVLink
- Ultra fast fabric that allows NVLink-capable GPUs connected through the appropriate NVLink/NVSwitch technology to communicate directly

## IMEX
- IMEX is the authorization layer on top of nvlink fabric dictating which processes can share/access exported GPU memory across the NVLink fabric
- Ex: Process A on GPU A can only ready memory A on GPU B but not memory B as that is belonging to another process

```
IMEX
Process A ---- authorization ---- Process B
   |                              |
 GPU A <======= NVLink ========> GPU B
```

## RDMA
- Without RDMA, the data flow goes like this
```
Node A

GPU VRAM
   |
   | copy
   v
System RAM          <-- "bounce buffer"
   |
   | copy/DMA
   v
NIC
   |
 network
   v
NIC
   |
   v
System RAM          <-- another bounce
   |
   v
GPU VRAM
```
- GPUDirect RDMA enables us to drop the copy from gpu VRAM into system memory
```
GPU VRAM
   |
   | DMA
   v
NIC
   |
 network
   |
   v
NIC
   |
   | DMA
   v
GPU VRAM
```