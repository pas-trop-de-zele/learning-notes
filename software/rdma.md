## Application flow without DMA
- CPU copies application memory into kernel buffer
- Most NIC nowadays DMA in the kernel memory

## With DMA
- Application bypass the first copy by the CPU from application memory to kernel buffer (kernel bypass)
- Application establish a queue pair (QP) to tell NIC:
  - Send data from certain memory space
  - Receive data into certain memory space
- `lkey`: local key which application hold to confirm with NIC that it owns certain memory spaces
- `rkey`: remote key grant access to certain memory spaces on the remote computer
