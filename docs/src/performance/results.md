# Q35 Performance

Performance on Q35 is managed by a Rust component that writes and publishes the
[Firmware Basic Boot Performance Table (FBPT)](https://uefi.org/specs/ACPI/6.5/05_ACPI_Software_Programming_Model.html#firmware-basic-boot-performance-table).
For more information on the functionality of the performance component, see the
[performance component documentation](https://github.com/OpenDevicePartnership/patina/blob/main/components/patina_performance/README.md)
in the main `patina` repo. This document focuses on how to collect and interpret performance results.

## Collecting Performance

### 1. Enable the Performance Component

The performance component must be enabled to collect FBPT results. Example code can be found under
[Enabling Performance Measurements](https://github.com/OpenDevicePartnership/patina/blob/main/components/patina_performance/README.md#enabling-performance-measurements-during-boot)
in the main `patina` repo.

Note that
[performance collection is already enabled by default](https://github.com/OpenDevicePartnership/patina-dxe-core-qemu/blob/main/bin/q35_dxe_core.rs)
for Q35 in `patina-dxe-core-qemu`.

### 2. Boot and Collect FBPT Binary

When enabled, the performance component collects FBPT results during UEFI boot. This is done by setting
`BLD_*_PERF_TRACE_ENABLE=TRUE` as a build variable to `stuart_build`:

```bash
stuart_build -c Platforms/QemuQ35Pkg/PlatformBuild.py --flashrom BLD_*_PERF_TRACE_ENABLE=TRUE
```

For more information on the `stuart` system, see [Building the Firmware](../building/building.md).

To collect raw FBPT results from the UEFI shell, `FbptDump.efi` must be built. Add the following to your platform
`.dsc`:

```dsc
UefiTestingPkg\PerfTests\FbptDump\FbptDump.inf
```

The
[source code for `FbptDump`](https://github.com/microsoft/mu_plus/tree/dev/202502/UefiTestingPkg/PerfTests/FbptDump)
is in `mu_plus`.

### 3. Interpret Performance Results

#### Pip Modules

Two tools are necessary to convert the resulting `.bin` file into a readable HTML results file.

First, install `edk2-pytool-extensions`:

```bash
pip install edk2-pytool-extensions
```

Then, run `fpdt_parser` to retrieve the XML, and `perf_report_generator` to retrieve the final HTML page:

```bash
fpdt_parser -b path_to_bin_file.bin -x output_file_name.xml
perf_report_generator -i path_to_xml_file.xml -r output_file_name.html -s path_to_directory_with_guids/
```

#### Scripts

There are
[two scripts](https://github.com/tianocore/edk2-pytool-extensions/blob/master/edk2toolext/perf/fpdt_parser.py) to
convert the FBPT binary dump into usable results in `edk2-pytool-extensions`:

- [`fpdt_parser.py`](https://github.com/tianocore/edk2-pytool-extensions/blob/master/edk2toolext/perf/fpdt_parser.py)
  converts the `.bin` file into XML.
- [`perf_report_generator.py`](https://github.com/tianocore/edk2-pytool-extensions/blob/master/edk2toolext/perf/perf_report_generator.py)
  converts the XML into an HTML page with filtering and graphing options.

#### Sample Result

An example of these results can be found in the [Sample Q35 Performance Report](q35_report_sample.md).
