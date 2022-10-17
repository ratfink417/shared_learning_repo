We all know that [Proxmox is Hard](Proxmox%20is%20Hard.md) . Their documentation doesn't quite touch on how to do certain things with qemu or KVM or any of the underlying technologies they employ. This data is handy for working with USB devices, especially if you want to create virtual ones

Good Reference info for working with virtual USB devices https://qemu-project.gitlab.io/qemu/system/devices/usb.ht

`hostbus=<nr>`

Specifies the bus number the device must be attached to

`hostaddr=<nr>`

Specifies the device address the device got assigned by the guest os

`hostport=<str>`

Specifies the physical port the device is attached to

`vendorid=<hexnr>`

Specifies the vendor ID of the device

`productid=<hexnr>`

Specifies the product ID of the device.
#USB
