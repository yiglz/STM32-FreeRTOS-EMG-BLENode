# STM32-FreeRTOS-EMG-BLENode
A low-power BLE node built with STM32 and FreeRTOS, featuring real-time EMG signal processing and gesture recognition via DMA-driven ADC

Raw muscle signals are noisy and microvolt-level. This project acts as a real-time biometric translation engine. It is a custom-built embedded device that captures raw biopotentials, filters out electromagnetic noise in the analog domain, analyzes muscle power via RTOS, and transmits hand gestures over Bluetooth Low Energy (BLE).

<img width="1724" height="938" alt="STM32_BLEEMG_PCB" src="https://github.com/user-attachments/assets/5adfa135-3215-407c-8960-afa7618c4d0a" />

---
## Used ICs
* **MCU & BLE Radio:** STM32WB55RG (Dual-Core)
* **Precision Instrumentation Amplifier:** INA333
* **Zero-Drift CMOS Op-Amps:** OPA2333
* **Ultra-Low Noise LDO:** LP5907MFX-3.3
* **Buck Converter:** AP63205WU
* **2.4 GHz Chip Antenna & Balun:** 2450AT18A100E (Antenna) & DLF162500LT-5028A1 (Balun)
---

## Analog Front-End
* The human body acts as an antenna for 50/60Hz mains noise. This design utilizes a **Right Leg Driven (RLD)** electrode network to cancel out common-mode interference.
* The raw differential signals are amplified with a **precise gain of 200** using INA333 instrumentation amplifiers.
* Before entering the ADC, the amplified signal passes through an active **2nd-Order Sallen-Key Low-Pass Filter** built with OPA2333 op-amps. Tuned to a cut-off frequency of exactly **~479 Hz**.
* The 2.4 GHz RF trace is **impedance-matched** using a DLF162500LT balun into a 2450AT18A100E chip antenna. To prevent digital switching noise from corrupting the microvolt analog signals, the analog front-end is powered by a dedicated LP5907 3.3V **ultra-low-noise LDO**.

---

## Firmware
To save energy, the system defaults to a **deep hardware sleep mode**.

* The ADC continuously captures 3 analog channels (EMG 1, EMG 2, Battery Sense). The DMA streams this incoming data into a **120-item memory buffer** without CPU intervention.
* The `vSignalTask` executes a **absolute-value averaging algorithm**. This extracts muscle power in microseconds.
* The `vGestureTask` evaluates the signals against thresholds to classify states utilizing **precise RTOS tick timing**. The system updates the BLE characteristics *only* when a definitive gesture state change is detected. By leveraging the BLE **Slave Latency** parameter, the underlying RF radio skips unnecessary connection intervals and **remains in deep sleep** during idle periods.

---

## This Repository Contains
* **Firmware (`Core/` & `STM32_WPAN/`)
* **`.ioc` File
* **Schematic PDF
* **Gerber and Drill Files
