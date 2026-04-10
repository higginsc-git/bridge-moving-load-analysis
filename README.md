# Bridge Moving Load Analysis

A browser-based structural analysis tool for simply-supported beams with optional cantilevers on both ends. It performs moving load analysis for standard AASHTO truck configurations, computes HL-93 design envelopes, and integrates influence lines for distributed load effects.

## Author

Developed by Christopher Higgins, PhD, PE  
School of Civil and Construction Engineering  
Oregon State University, 2026

Based on original Fortran analysis program by C. Higgins (2004)

## What It Does

This program moves truck axle loads across a beam and computes shear and moment at user-defined points of interest. It handles:

- **Simply-supported spans** with optional left and/or right cantilevers
- **Moving point loads** from any axle configuration — standard AASHTO trucks are built in, and custom trucks can be defined
- **HL-93 design envelopes** per AASHTO LRFD, combining the governing result of the Design Truck (rear axle spacing swept 14' to 30') or Tandem, multiplied by the dynamic impact factor (IM), plus lane load (no IM)
- **HL-93 Fatigue Truck** evaluated independently (14'/30' fixed spacing, no lane load, moment only) with its own IM factor
- **Dynamic impact factors (IM)** applied per AASHTO LRFD: 1.33 for strength (truck/tandem), 1.15 for fatigue, 1.0 for lane load. IM values are user-adjustable.
- **All trucks run forward and reverse** to capture the governing direction at each point
- **Influence line integration** using the unit load response and trapezoidal rule, with positive and negative areas loaded independently for maximum effect
- **Dead load and lane load effects** computed from the true unit-load influence line
- **Distribution factors** for moment and shear applied to lane load results

## How to Use It

1. **Open `beam-analysis_2026.html` in any modern web browser** (Chrome, Edge, Firefox, Safari). No installation required.
2. **Define the beam geometry**: enter the main span length and optional left/right cantilever lengths in feet.
3. **Set points of interest**: enter comma-separated distances from the left end of each segment where you want results (e.g., `15, 30, 45` for quarter points on a 60' span).
4. **Select truck loads**: click the truck chips to toggle them on/off. The HL-93 Design Truck and Tandem are always used for the HL-93 design envelope regardless of selection. Additional trucks appear in the Truck Envelopes tab.
5. **Set distributed load and impact parameters**: lane load intensity (default 0.64 k/ft for HL-93), dead load, moment/shear distribution factors, and dynamic impact factors (IM Strength = 1.33, IM Fatigue = 1.15).
6. **Click Run Analysis**.

## Results Tabs

- **HL-93 Design Envelope** — Moment and shear envelope plots along the full bridge at 2 ft spacing, plus a summary table at user points. Each entry shows which loading governs: `Truck-14'-F` means the Design Truck with 14' rear axle spacing running forward; `Tandem` means the tandem governs. The HL-93 total = governing truck/tandem × IM + lane load (IM=1.0). Click any point of interest for a detailed breakdown showing the full calculation chain: raw values → × IM → + lane → total.
- **Influence Lines** — True unit-load influence lines (moment and shear) at each analysis point. These are the ordinates integrated for lane and dead load effects.
- **Response History** — Moment and shear at a selected point as a selected truck traverses the bridge. A truck selector lets you view any truck in your selection. Direction (F/R) and governing spacing are reported. Response values are without IM.
- **Truck Envelopes** — Max/min moment and shear envelope tables for all selected trucks, with governing direction (F = forward, R = reverse). Values are without IM except for the HL-93 Fatigue Truck, which reports moment × IM Fatigue (1.15).
- **Distributed Loads** — Lane and dead load effects at each point from influence line integration, with positive and negative areas reported separately.

## Sign Convention

- **Positive moment**: deck in compression (sagging)
- **Positive shear**: standard beam convention

## Dynamic Impact Factors (IM)

Per AASHTO LRFD, dynamic impact factors are applied as follows:

| Load Case | Default IM | Applied To |
|-----------|-----------|------------|
| Strength (Truck/Tandem) | 1.33 | HL-93 Design Envelope only |
| Fatigue Truck | 1.15 | Fatigue Truck envelope only |
| Lane Load | 1.00 | No dynamic impact |
| Dead Load | 1.00 | No dynamic impact |
| Other Trucks (individual) | 1.00 | Truck Envelopes tab (no IM) |

In the HL-93 Design Envelope, the combination is:

**HL-93 = max(Design Truck, Tandem) × IM + Lane Load × 1.0**

Both IM values are user-adjustable in the input parameters. Individual truck results in the Truck Envelopes and Response History tabs are reported without IM (except the Fatigue Truck, which includes its IM in the reported moments).

## Built-In Truck Models

| Truck | Axle Weights (kips) | Axle Spacings (ft) | Notes |
|-------|-------------------|-------------------|-------|
| HL-93 Design Truck | 8, 32, 32 | 14, 14–30 (swept) | IM=1.33 in HL-93 envelope |
| HL-93 Tandem | 25, 25 | 4 | IM=1.33 in HL-93 envelope |
| HL-93 Fatigue Truck | 8, 32, 32 | 14, 30 (fixed) | Moment only, IM=1.15 |
| HS-20 | 8, 32, 32 | 14, 14 | |
| Type 3 | 16, 17, 17 | 15, 4 | |
| Type 3S2 | 10, 15.5, 15.5, 15.5, 15.5 | 11, 4, 22, 4 | |
| Type 3-3 | 12, 12, 12, 16, 14, 14 | 15, 4, 15, 16, 4 | |
| SU4 | 8, 14, 14, 14 | 10, 4, 4 | |
| Unit Load | 1 | — | |

Custom trucks with any number of axles can be added through the interface.

## Technical Details

- The analysis engine uses 1000 increments to move the truck across the bridge
- The HL-93 Design Truck sweeps 17 rear axle spacings (14' to 30') in both forward and reverse directions (34 configurations total)
- All multi-axle trucks are run in both forward and reverse to capture the governing direction
- Lane and dead load effects are computed by integrating the unit-load influence line using the trapezoidal rule, with zero-crossing detection to separate positive and negative areas
- Analysis points near span boundaries use a small offset (0.0001 ft) to avoid numerical singularities
- The dense envelope table uses exact 2 ft spacing along the bridge

## Requirements

- A modern web browser (Chrome, Edge, Firefox, or Safari)
- An internet connection is needed the first time you open the file to load fonts and the React library from CDN. After that, your browser cache handles it.

## License

MIT License — see [LICENSE](LICENSE) for details.

## Disclaimer

This software is provided as-is for educational and reference purposes. Engineers should independently verify all results before using them for design. The author assumes no liability for the use of this software in engineering practice.
