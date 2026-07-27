# SUNFLEX-HC Manufacturing Files

## Assembly Options

### Option 1: JLCPCB Assembly (Recommended)

1. Upload Gerber files from `hardware/gerbers/sunflex_hc_rev_a.zip`
2. Select: 4-layer, 2 oz inner Cu, ENIG, green solder mask
3. Upload BOM + CPL files from `pick-and-place/`
4. JLCPCB will source and assemble all SMT components
5. Hand-solder through-hole parts (inductor, terminal blocks)

### Option 2: Hand Assembly

Full DIY assembly guide in `assembly-notes/assembly-guide.md`.

**Difficulty:** Intermediate (0402 passives, QFN FPGA, TDSON-8 MOSFETs)
**Estimated time:** 3–4 hours for an experienced builder

## Files

| File | Description |
|------|-------------|
| `pick-and-place/sunflex_hc_cpl.csv` | Component placement list (JLCPCB format) |
| `pick-and-place/sunflex_hc_bom.csv` | JLCPCB-format BOM |
| `assembly-notes/assembly-guide.md` | Step-by-step hand assembly guide |
| `assembly-notes/testing-after-assembly.md` | Bring-up and validation procedure |

## Assembly Notes

- **ESD precautions:** MOSFETs and FPGA are ESD-sensitive. Use grounded wrist strap.
- **Thermal paste:** Apply between MOSFETs and heatsink before final mounting.
- **First power-up:** Use current-limited bench supply (set to 12V, 100 mA) for initial testing — do NOT connect a battery first.

