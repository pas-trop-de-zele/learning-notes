## Basics
- Drivers are required for linux to communicate to hardware
- Drivers could be available as kernel starts through `initramfs` for things like `nvme.ko` or loadable modules (i.e. nvidia drivers)

## Common commands to run from low -> hi
- `lspci` list all device connected to the pci bus
- `lspci -k` also list the driver associated to the device