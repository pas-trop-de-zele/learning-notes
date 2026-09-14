## Virtual memory
- Each process have some fixed virtual address space (unmapped)
- As applications start using more memory, the kernel would find empty physical pages and map the unreserved space
- From the application pov, the memory pages are consecutive, however the kernel might just map them to scattered spaces