# Circuit-design and measurement artifacts

This repository contains six machine-readable measurement tables and two independent transistor-level circuit descriptions. It is intentionally self-contained and does not identify a manuscript, figure, author, institution, foundry, or fabrication process.

## Contents

### Measurement data

The `data/` directory contains comma-separated values with a header row:

- `Fin_SNDR.csv` and `Fin_SFDR.csv`: SNDR and SFDR versus input frequency. The frequency column is in MHz; performance values are in dB.
- `TEMP_SNDR.csv` and `TEMP_SFDR.csv`: SNDR and SFDR versus temperature. Temperature is in degrees Celsius; performance values are in dB. Each sample is reported at 50 MHz, 250 MHz, and 1000 MHz input frequency.
- `VDD_SNDR.csv` and `VDD_SFDR.csv`: SNDR and SFDR versus relative supply-voltage change. The sweep column is the percentage change from the nominal supply; performance values are in dB.

Columns named `chip1`, `chip2`, and `chip3` are independent anonymized measured samples. No missing-value sentinel is used.

These are processed measurement results rather than raw waveforms. For the frequency datasets, the input frequency was swept across the values named in the columns. For the temperature datasets, environmental temperature was swept at the three listed input frequencies. For the supply datasets, the supply was swept by the listed percentage relative to its nominal value at the three listed input frequencies.

The files contain the numerical values directly. No proprietary software is required to read them. The repository does not include the private acquisition files or the local MATLAB export script because those files contain environment-specific paths and acquisition metadata and are not needed to use the released CSV data.

### Circuit descriptions

The `design_a/` and `design_b/` directories contain independent hierarchical netlists in Cadence Spectre syntax:

- `design_a/design_a.scs`, with top-level subcircuit `design_a_top`.
- `design_b/design_b.scs`, with top-level subcircuit `design_b_top`.
- Each directory includes its own `generic_models.scs`, referenced by a relative include.

The netlists preserve connectivity, transistor dimensions, device multiplicity, threshold-class distinctions, and nominal ideal-capacitor values. Foundry paths, proprietary model and library identifiers, process corners, PCell metadata, and layout-dependent parameters are not included. Custom hierarchy names have been replaced with neutral identifiers.

The generic transistor, capacitor, and digital-cell definitions are illustrative stand-ins for structural inspection and experimentation. They are not calibrated to a fabrication process, and they are not expected to reproduce measured or sign-off performance.

## Using the circuit descriptions

Run from the selected design directory so that `generic_models.scs` resolves locally, or configure the simulator include path accordingly.

For design A, the positional top-level interface is:

```text
design_a_top CA0 CAB0 IBIAS1 IBIAS2 VCMB VCMC VDD VDDC VDDN VDDP VIN VIP VOUTN VOUTP VSS VSSC VSSN VSSP
```

Example instantiation:

```spectre
simulator lang=spectre
include "design_a.scs"
XDUT (CA0 CAB0 IBIAS1 IBIAS2 VCMB VCMC VDD VDDC VDDN VDDP VIN VIP VOUTN VOUTP VSS VSSC VSSN VSSP) design_a_top
```

For design B, the positional top-level interface is:

```text
design_b_top EN0 IBIAS1 IBIAS2 RST0 VCM2 VCMD VDD VDDC VDDN VDDP VIN VIP VOUTN VOUTP VSS VSSC VSSN VSSP
```

Example instantiation:

```spectre
simulator lang=spectre
include "design_b.scs"
XDUT (EN0 IBIAS1 IBIAS2 RST0 VCM2 VCMD VDD VDDC VDDN VDDP VIN VIP VOUTN VOUTP VSS VSSC VSSN VSSP) design_b_top
```

The user must provide supplies, biases, controls, input stimuli, output loads, and analyses. The hierarchy and dependency resolution have been checked statically; no functional, operating-point, timing, noise, stability, PVT, mismatch, or measured-performance equivalence is claimed.

## License

Unless a file states otherwise, all data, source, and documentation in this repository are licensed under the CERN Open Hardware Licence Version 2 - Permissive, SPDX identifier `CERN-OHL-P-2.0`. See `LICENSE` for the complete terms. The material is provided without warranty.
