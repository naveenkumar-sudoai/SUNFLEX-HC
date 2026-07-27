## Description
<!-- Describe your changes in detail -->

## Type of Change
<!-- Check all that apply -->

- [ ] 🐛 Bug fix (non-breaking change addressing an issue)
- [ ] ✨ New feature (non-breaking change adding functionality)
- [ ] 💔 Breaking change (fix or feature that changes existing behavior)
- [ ] 📖 Documentation update
- [ ] 🔧 Hardware design change (schematic, PCB, BOM)
- [ ] ⚡ FPGA logic change (VHDL/Verilog)
- [ ] 🧪 Test addition or improvement
- [ ] 🏭 Manufacturing file update
- [ ] 🧹 Code cleanup / refactor

## Scope
<!-- Which part(s) of the project does this affect? -->

- [ ] Hardware
- [ ] Firmware (ESP32-S3)
- [ ] FPGA
- [ ] Software (Desktop/Web)
- [ ] Documentation
- [ ] Testing
- [ ] Manufacturing

## Related Issues
<!-- Reference issues using "Closes #123" or "Related to #456" -->

Closes #

## Testing Performed
<!-- Describe the tests you ran and their results -->

- [ ] Firmware unit tests pass (`platformio test -e native`)
- [ ] FPGA testbenches pass (`make check` in fpga/simulation)
- [ ] Bench test with resistive load
- [ ] Tested with battery (type: _______, voltage: _______)
- [ ] Tested with solar panel (specs: _______)
- [ ] 1-hour stability test at ______ W

## Hardware Validation (if applicable)
<!-- Only for hardware/FPGA changes -->

- [ ] Schematic DRC passes (0 errors, 0 warnings)
- [ ] PCB DRC passes (0 errors, 0 warnings)
- [ ] All components in stock at LCSC/Digi-Key/Mouser
- [ ] Thermal analysis updated in docs
- [ ] BOM updated with new/changed components

## Documentation
<!-- Check if documentation is updated -->

- [ ] README updated (if user-facing change)
- [ ] Relevant doc in `docs/` updated
- [ ] Comments added to code for complex logic
- [ ] Example configs updated (if applicable)

## Screenshots / Logs
<!-- Attach relevant screenshots, scope traces, serial output -->

```
(paste logs here if applicable)
```

## Checklist
- [ ] My code follows the project's [style guidelines](community/CONTRIBUTING.md)
- [ ] I have reviewed my own changes
- [ ] I have commented my code where non-obvious
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix/feature works
- [ ] All existing tests pass
- [ ] I have updated the documentation accordingly
