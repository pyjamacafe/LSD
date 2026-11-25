# iCE40HX1K-TQ144 USB Breadboard Core (Pico-Style 1.1″ Carrier, KiCad 10)

A breadboardable, Raspberry Pi Pico-style 2-layer **Lattice iCE40HX1K-TQ144** (1,280 LUTs) FPGA core board designed in **KiCad 10**, programmable directly over a top-edge **USB Micro-B** port without an FTDI bridge chip by running the open-source **TinyFPGA RTL USB Bootloader** in the FPGA fabric.

## Key Specifications
- **Form Factor:** `31.2 mm × 64.0 mm` 2-layer FR-4 PCB (1.6 mm), solid `B.Cu` ground plane
- **Breadboard Headers:** Two 1×20 2.54 mm headers (`J3` left, `J4` right) spaced **`27.94 mm` (1.100″ / 11 × 2.54 mm)** apart center-to-center — 36× 3.3V GPIOs plus `+3V3` (top-left), `+5V` VBUS (top-right, Pico Pin 40 style), and `GND` (bottom-left & bottom-right)
- **FPGA:** Lattice `iCE40HX1K-TQ144` (1,280 LUTs, 64 kbit EBR, 1× PLL, 144-pin 0.5 mm TQFP) centered between the breadboard headers
- **USB Programming (No FTDI):** Top-center USB Micro-B (`J1`, Molex 47346-0001) + `USBLC6-2SC6` ESD protection (`U5`) + 68 Ω series resistors (`R1`, `R2`) + software-controlled 1.5 kΩ `D+` pull-up (`R3`) wired directly to Bank 0 pins `129` (`USB_DN`), `128` (`USB_DP`), and `122` (`USB_PU`)
- **Clock:** 16 MHz 3.3V CMOS oscillator (`Y1`, Abracon ASE) wired to `GBIN6` (Pin `21`), multiplied to 48 MHz via `SB_PLL40_CORE` for Full-Speed USB
- **Configuration Memory:** `W25Q16JVSS` (16 Mbit / 2 MB SOIC-8 SPI Flash, `U2`) wired in Master SPI mode with 10 kΩ pull-ups on `FLASH_CS_N`, `CRESET_B`, `CDONE`, `~WP`, and `~HOLD`
- **One-Time SPI Bootstrap Header:** Unpopulated 2×4 `J2` (`SPI_BOOT_HDR`) centered along the bottom edge (just like the Raspberry Pi Pico's bottom SWD port)
- **Power Supply:** Cascaded low-noise LDOs from USB 5V: `AP2112K-3.3` (`U3`) → `AP2112K-1.2` (`U4`), RC-filtered `VCCPLL`, and `1N4148W` drop diode (`D1`) for `VPP_2V5` per the Lattice iCE40 Hardware Checklist

## Verification Results (KiCad `10.0.3` CLI)
- **Schematic ERC (`kicad-cli sch erc`):** `0` errors, `0` warnings
- **PCB DRC (`kicad-cli pcb drc --refill-zones`):** `0` errors, `0` unconnected items

## First-Time Bootloader Bring-Up
Because a blank SPI Flash has no bitstream on it yet, flash the TinyFPGA USB Bootloader **once** via the bottom 2×4 `J2` (`SPI_BOOT_HDR`) header:
1. Hold `CRESET_B` (`J2` Pin 7) **LOW** to tri-state the FPGA SPI pins.
2. Flash the compiled `iCE40HX1K` TinyFPGA USB Bootloader image to `U2` (`W25Q16JVSS`) using `J2` Pins 1–6 (`+3V3`, `GND`, `FLASH_CS_N`, `FLASH_SCK`, `FLASH_DI`, `FLASH_DO`) with any 3.3V SPI programmer (e.g., Raspberry Pi, RP2040/Pico, or 3.3V CH341A).
3. Release `CRESET_B` and plug in USB Mini-B (`J1`) — the board enumerates directly over USB and can be programmed with `tinyprog` from then on.

## Project Files
- `ice40hx1k_usb_core.kicad_pro` — KiCad 10 project & 2-layer design rules
- `ice40hx1k_usb_core.kicad_sch` — Complete schematic (0 ERC violations)
- `ice40hx1k_usb_core.kicad_pcb` — Routed `31.2 × 64.0 mm` 2-layer PCB (0 DRC errors, 0 unconnected)
- `pins.pcf` — IceStorm / `nextpnr-ice40` pin constraint file
- `fab/gerbers/` — Fabrication-ready Gerber & Excellon drill files
- `fab/bom.csv` — Bill of Materials
- `renders/` — Schematic PDF and 3D top/bottom PCB renders
