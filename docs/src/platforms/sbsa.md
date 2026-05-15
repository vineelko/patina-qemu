# QEMU SBSA

`QemuSbsaPkg`...

- Is a derivative of OvmfPkg based on the EDK II QEMU-SBSA ARM machine type.
- Will not support Legacy BIOS or CSM.
- Will not support S3 sleep functionality.
- Has 64-bit PEI and DXE phases.
- Targets a tightly constrained virtual platform based on the QEMU ARM CPUs.

By focusing solely on the ARM chipset, this package is allowed to break compatibility with other QEMU supported
chipsets. The ARM chipset can be paired with an AArch64 processor to emulate ARM-based hardware with industry standard
features like TrustZone and PCI-E. Although `QemuSbsaPkg` leverages the SBSA machine type provided by QEMU, the
features enabled in this package are not server-class platform centric.

## SBSA Machine Type

SBSA is an ARM-based machine type that QEMU emulates. It provides better ARM-based platform-level support (ACPI, etc.)
than the generic `virt` ARM machine and includes an integrated AHCI controller.

## Building and Running

`QemuSbsaPkg` uses the Patina repositories and EDK II PyTools for its build operations. See
[Building the Firmware](../building/building.md) for full instructions.
