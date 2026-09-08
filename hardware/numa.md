## BLUF
- In a multi-socket system, each cpu socket has their own memory slot
  ```
  CPU socket 0
  └─ local memory slots

  CPU socket 1
    └─ local memory slots
  ```
- All the cpu sockets are connected via a high-speed bus so any cpu can read from any memory slot regardless of whether the memory slot is the cpu socket's local memory slot
- This however does not mean memory access performance is uniform. Accessing local memory slot generally has lower latency and higher bandwidth

## How this relates to k8s