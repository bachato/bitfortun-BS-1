# Bitfortun BS-1

Firmware for the Bitfortun BS-1 home Bitcoin miner (ESP32-S3 + BM1373).

|                  |                        |
|------------------|------------------------|
| **MCU**          | ESP32-S3               |
| **ASIC**         | Bitmain BM1373         |
| **Framework**    | ESP-IDF ≥ 5.3          |
| **License**      | GPL-3.0                |

---

## Overview

Bitfortun BS-1 is a compact, quiet single-chip Bitcoin miner designed for home use. This repository contains the open-source firmware that drives the device, providing a web dashboard, stratum mining support, and full control over frequency, voltage, and cooling.

The code is derived from the broader open-source mining community and has been adapted specifically for the BS-1 hardware.
BS-1 is like a newborn. It has many imperfections, and our team keeps working hard. We will resolve some of the issues via future firmware updates.
---

## Quick Start – Flashing

### 1. Get the binary
Download the latest factory image from the [Releases](https://github.com/liaojinguu/bitfortun-BS-1/releases) page.

### 2. Prepare configuration
```bash
git clone https://github.com/liaojinguu/bitfortun-BS-1.git
cd bitfortun-BS-1
cp config.cvs.example config.cvs
```
Open `config.cvs` and fill in your pool details, Wi-Fi credentials, and preferred frequency/voltage settings.

### 3. Enter bootloader mode
Hold the **BOOT** button, press **RESET**, then release BOOT.

### 4. Flash
```bash
bitaxetool --config ./config.cvs --firmware <downloaded-factory.bin> -p /dev/ttyACM0
```
*(Use the correct serial port for your OS.)*

---

## Building from Source

### Docker (easiest)

```bash
# one-time setup
cd docker && ./build_docker.sh && cd ..

export BOARD="BITFORTUN_BS1"
./docker/idf.sh set-target esp32s3
./docker/idf.sh build
```

Resulting files appear in `build/`:
- `esp-miner.bin`
- `www.bin`

For a full interactive environment:
```bash
./docker/idf-shell.sh
```

### Manual build (inside Docker shell or local ESP-IDF)

```bash
export BOARD="BITFORTUN_BS1"
idf.py set-target esp32s3
idf.py build

nvs_partition_gen.py generate config.cvs config.bin 12288
./merge_bin_with_config.sh bitfortun-bs1.bin

esptool.py --chip esp32s3 -p /dev/ttyACM0 -b 460800 \
  --before=default_reset --after=hard_reset \
  write_flash --flash_mode dio --flash_freq 80m --flash_size 16MB \
  0x0 bitfortun-bs1.bin
```

---

## Configuration Notes

All runtime settings live in `config.cvs`. Important keys include:

- Pool connection (`stratumurl`, `stratumport`, `stratumuser`, `stratumpass`)
- Network (Wi-Fi SSID / password or Ethernet)
- ASIC parameters (frequency, core voltage)
- Fan behaviour and temperature limits

After editing, regenerate the NVS partition or use `bitaxetool` to apply the new config.

---

## Community & Support

Join the discussion on Discord:  
[https://discord.gg/3E8ca2dkcC](https://discord.gg/3E8ca2dkcC)

---

## Acknowledgements

Special thanks to these open-source projects for their generous contributions:

1. **Nerd** – https://github.com/shufps/ESP-Miner-NerdQAxePlus  
2. **ngx-admin** – https://github.com/akveo/ngx-admin  

This firmware would not exist without the work of the Bitaxe / NerdAxe community and the original authors.

---

*Use at your own risk. Cryptocurrency mining consumes electricity and may involve hardware wear.*
