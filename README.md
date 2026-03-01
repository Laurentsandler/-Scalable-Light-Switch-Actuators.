# Scalable Light Switch Actuators

Small battery-powered automatic light switch actuators powered by ESP32.

## Description

A solution for renters who cannot install smart light switches. These small, battery-powered actuators mount on the outside of existing light switches and physically toggle them using a servo motor. They connect together over ESP-NOW (no Wi-Fi needed per module) via a central base station that handles Blynk/HomeKit integration. Up to 5 modules can be controlled from one base station.

## My Motivation

I live in a rented house, so I cannot install smart light switches. The only solutions on the market are bulky actuators that use a proprietary remote and don't support smart home platforms like Amazon Alexa, Home Assistant, Zigbee, or Apple HomeKit. So I decided to build my own using a custom PCB.

## Hardware Design

The custom PCB was designed using EasyEDA. The PCB dimensions are 32 × 26 mm — fitting perfectly on the back of an SG90 servo.

### PCB Layout & 3D Renders

*(Images to be added to the PCB/ folder)*

### Case

The case was designed in Onshape — as small as possible while fitting all components, with a snap-on lid and space for wires.

*(Images to be added to the Images/ folder)*

## Features

- **Servo Control**: Physically toggles light switches ±20° via SG90 servo
- **ESP-NOW**: Low-power mesh-style communication between modules and base station — no per-module Wi-Fi needed
- **Blynk Integration**: Control up to 5 switches via Blynk virtual pins V0–V4
- **Apple HomeKit / BLE**: Supported via ESPHome/Matter on the ESP32-C3
- **Long Battery Life**: ~2 months per module using deep sleep + ESP-NOW architecture
- **USB-C Charging**: On-board battery charging and power path management
- **Physical Button**: User can still manually toggle the switch via button on the device
- **Compact PCB**: 32 × 26 mm, fits on the back of an SG90 servo
- **Snap-fit 3D Printed Case**: Designed in Onshape, easily printable

## System Architecture

```
[Blynk App] → [Base ESP32 (WiFi)] --ESP-NOW--> [Module 1 (ESP32-C3)] → Servo 1
                                             --ESP-NOW--> [Module 2 (ESP32-C3)] → Servo 2
                                             --ESP-NOW--> [Module 3 (ESP32-C3)] → Servo 3
                                             --ESP-NOW--> [Module 4 (ESP32-C3)] → Servo 4
                                             --ESP-NOW--> [Module 5 (ESP32-C3)] → Servo 5
```

## Hardware Requirements

| Component | Specification | Notes |
|-----------|---------------|-------|
| Module MCU | ESP32-C3-WROOM-02 | ~18×20mm, ~5µA deep sleep, Wi-Fi + BLE |
| Base MCU | ESP32 Dev Board | Always-on, handles Blynk & ESP-NOW relay |
| Servo | SG90 | 3 wires soldered to PCB bottom |
| Battery | LiPo (fits SG90 footprint) | High capacity, fits behind PCB+servo |
| Charging IC | On-board (USB-C) | Power path management, 5V→3.3V step-down |
| USB-Serial | CH340 | On-board for programming |
| Button | Tactile | Physical switch toggle |

## Software Dependencies

Install these libraries via Arduino IDE Library Manager:
- WiFi (built-in)
- ESP-NOW (built-in)
- BlynkSimpleEsp32 (Blynk library)
- ESP32Servo

## Configuration

### Module Setup
1. Flash each ESP32-C3 module with the Module firmware
2. Note the MAC address printed on Serial for each module
3. Paste the MAC addresses into the Base firmware

### Base Setup
Edit the following in `Firmware/base/base.ino`:

```cpp
const char* WIFI_SSID = "YOUR_WIFI_SSID";
const char* WIFI_PASS = "YOUR_WIFI_PASSWORD";
const char* BLYNK_AUTH = "YOUR_BLYNK_AUTH_TOKEN";

uint8_t module1MAC[] = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00}; // Replace with actual MACs
uint8_t module2MAC[] = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
// ... etc
```

## Usage

### Blynk App Setup
1. Create a new Blynk project
2. Add 5 Button widgets on virtual pins **V0–V4**
3. Set each button to **Push mode**, Integer **0/1**
4. Each V0–V4 button controls Module 1–5 respectively

### Physical Button
- Press the button on any module to manually toggle the light switch

### LED Status Indicators
- **Solid**: Connected and idle
- **Blinking**: Receiving command / moving servo

## API Services Used

- **Blynk**: Remote switch control (V0–V4)
- **ESP-NOW**: Local wireless communication between base and modules

## Bill of Materials (BOM)

Final Actuator — March 2026

| # | Name | Designator | Footprint | Qty | Manufacturer Part | Manufacturer | Supplier | Supplier Part | Price | JLCPCB Part Class | Value |
|---|------|-----------|-----------|-----|------------------|-------------|---------|--------------|-------|------------------|-------|
| 1 | 10uF | C1,C5,C8,C9 | C0603 | 4 | CL10A106KP8NNNC | SAMSUNG | LCSC | C19702 | $0.007 | Basic Part | |
| 2 | 100nF | C4,C6 | C0603 | 2 | CC0603KRX7R9BB104 | YAGEO | LCSC | C14663 | $0.002 | Basic Part | |
| 3 | 1uF | C7 | C0805 | 1 | CL21B105KBFNNNE | SAMSUNG | LCSC | C28323 | $0.010 | Basic Part | |
| 4 | B2B-PH-K-S(LF)(SN) | CN1 | CONN-TH_B2B-PH-K-S | 1 | B2B-PH-K-S(LF)(SN) | JST | LCSC | C131337 | $0.031 | Extended Part | |
| 5 | B5819W_C8598 | D1 | SOD-123 | 1 | B5819W SL | CJ | LCSC | C8598 | $0.024 | Basic Part | |
| 6 | AO3401A | Q1 | SOT-23 | 1 | AO3401A | AOS | LCSC | C15127 | $0.057 | Basic Part | |
| 7 | 5.1kΩ | R1,R2 | R0603 | 2 | 0603WAF5101T5E | UNI-ROYAL | LCSC | C23186 | $0.001 | Basic Part | |
| 8 | 100kΩ | R3 | R0603 | 1 | 0603WAF1003T5E | UNI-ROYAL | LCSC | C25803 | $0.001 | Basic Part | |
| 9 | 2kΩ | R4 | R0603 | 1 | 0603WAF2001T5E | UNI-ROYAL | LCSC | C22975 | $0.001 | Basic Part | |
| 10 | 10kΩ | R5,R6,R7,R8 | R0603 | 4 | 0603WAF1002T5E | UNI-ROYAL | LCSC | C25804 | $0.001 | Basic Part | |
| 11 | TS-1088-AR02016 | SW1,SW2 | SW-SMD | 2 | TS-1088-AR02016 | XUNPU | LCSC | C720477 | $0.048 | Basic Part | |
| 12 | TP4054A | U1 | SOT-23-5 | 1 | TP4054A | YONGYUTAI | LCSC | C33551022 | $0.031 | Extended Part | |
| 13 | ESP32-C3-WROOM-02-N4 (4MB) | U5 | Module | 1 | ESP32-C3-WROOM-02-N4 | ESPRESSIF | LCSC | C2934560 | $3.067 | Extended Part | |
| 14 | CH340N | U6 | SOP-8 | 1 | CH340N | WCH | LCSC | C2977777 | $0.561 | Extended Part | |
| 15 | TPS73633DBVR | U7 | SOT-23-5 | 1 | TPS73633DBVR | TI | LCSC | C28038 | $0.422 | Extended Part | |
| 16 | TYPE-C-31-M-12 | USBC1 | USB-C SMD | 1 | TYPE-C-31-M-12 | 韩国韩荣 | LCSC | C165948 | $0.183 | Extended Part | |
| 17 | 1kΩ | R9 | R0603 | 1 | 0603WAD1001T5E | UNI-ROYAL | LCSC | C51218 | $0.007 | Extended Part | 1kΩ |
| 18 | LED (Yellow) | LED2 | LED0805 | 1 | 17-215UYC/S530-A3/TR8 | EVERLIGHT | LCSC | C72036 | $0.054 | Extended Part | |

## License

MIT License
