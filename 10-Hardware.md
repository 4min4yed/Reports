### PCI - The Peripheral Component Interconnect Bus
standard that describes how to electrically connect the peripheral components of a system together in a structured and controlled way
**PCI bus**: connects components
**PCI-PCI Brdg**: connects PCI buses
active hw on linux: ```/sys/bus/pci/``` OR ```lspci```

```App calls → Guest OS Kernel → Hypervisor Kernel (VMkernel) → physical drivers → Physical HW```

`dd` performs a sector-for-sector copy

A **UEFI** bootable USB contains
```
EFI/
└── BOOT/
    ├── BOOTX64.EFI
    ├── boot.cfg
    └── crypto64.efi
```
The firmware searches specifically for `EFI/BOOT/BOOTX64.EFI` which launches the ESXi bootloader.

