# Elderly Fall Detection System

## Core Description
A comprehensive fall detection system for the elderly, utilizing millimeter-wave (mmWave) radar technology. The system's architecture is based on the combination of a central data processing unit and peripheral sensors (transceivers) placed in the areas of interest.

## Basic Operation
The peripheral transceivers emit and receive millimeter-wavelength electromagnetic signals for the continuous tracking of the position, posture, and micro-motion of individuals in the space. This data is transmitted to the central unit via low-power wireless protocols. The central unit analyzes the information in real-time and maps the 3D position of the user. If, based on the embedded algorithm, an abrupt change in height and subsequent immobilization on the floor (indicating a fall) is detected, the system activates automatically. Subsequently, it sends an emergency notification over the internet to preselected contact persons or health services.

## Sensors / Transceivers (Peripheral Units)
Each mmWave sensor is intended to cover a specific room. To ensure autonomy and stability, the sensors communicate exclusively with the central unit and not with each other. The millimeter-wave radar is integrated with a microcontroller for data processing and transmission. It is a discreet, thin, white device mounted on the room's ceiling, offering an optimal field of view without requiring complex synchronization or continuous micro-adjustments. Power is ideally supplied via mains electricity, with the capability of internal battery backup.

## Central Unit
Collects, synchronizes, and processes data from multiple sensors, running the fall detection algorithm. It is responsible for confirming the event and securely routing the notification message. This medium-sized, discreetly designed device remains permanently connected to a power supply, while featuring a built-in backup battery for uninterrupted operation during power outages. It also features LED indicators for immediate user feedback regarding the system's operational status.

## Technical Specifications of mmWave Transmitter
*   **Operating Frequency:** 60GHz (ideally) or 24GHz. The 60GHz band offers significantly higher resolution and sensitivity in detecting micro-motions (like breathing) compared to 24GHz.
*   **Radar Type:** Pulsed Coherent Radar (PCR) or Low-Power FMCW (Frequency-Modulated Continuous Wave). Simple Doppler sensors are insufficient, as they only detect speed/motion and not distance/posture.
*   **Deep Sleep Current:** Must be in the micro-Ampere range (e.g., $<10 \mu A$) when the sensor is waiting for a wake-up command from the PIR.
*   **Duty Cycling Support:** The chip's ability to wake up instantly, scan the room within a few milliseconds (ms), and immediately return to sleep mode.
*   **Hardware Wake-up Pins (Interrupts):** Specific GPIO pins that allow the PIR sensor to send an electrical signal (interrupt) to wake the radar from deep sleep, without the microcontroller needing to continuously poll.
*   **On-chip DSP/MCU:** Mandatory feature. The heavy mathematical processing of the point cloud must be done internally within the mmWave chip.
*   **Binary/State Output:** The sensor must communicate (e.g., via UART, I2C, or SPI) the final result in a simple format and NOT stream raw data to the central MCU, as this would cause energy consumption to skyrocket.
*   **Micro-motion / Vital Signs Detection:** Ability to detect displacements on the order of 1-2 millimeters (reflections from the chest due to breathing or heartbeat). This ensures the system won't shut down when the elderly person is sitting or lying completely still.
*   **3D Posture / Height Mapping:** Ability to measure the target's distance from the sensor, allowing the algorithm to recognize a sudden change in position (from standing to horizontal on the floor).
*   **Pet Immunity:** Support for filtering based on Radar Cross Section (RCS), so the algorithm ignores dogs or cats moving near the floor.
*   **Event-Driven Communication:** The network microcontroller (MCU) accompanying the sensor does not transmit continuously. It sends RF data packets to the Central Unit only in two cases:
    *   When a fall is detected (Alarm).
    *   At regular intervals (e.g., every 1 hour) to send a "Keep-alive" signal and battery level update.
*   **Low-Power Wireless Protocol:** Compatibility of the accompanying MCU (not the radar itself) with protocols like Zigbee, Z-Wave, or Bluetooth Low Energy (BLE) (via chips like ESP32-H2 or nRF52840).

## Technical Specifications of PIR Transmitter
*   **Quiescent Current / Standby Current:** Must be in the nano-Ampere (nA) or minimal micro-Ampere range. Ideally under $3 \mu A$. This ensures the battery won't drain from the PIR's standby state.
*   **Operating Voltage:** Must be compatible with the battery and MCU voltage without requiring a voltage converter (which would consume power). Ideal operating range: 1.8V to 3.3V.
*   **Field of View (FoV):** Since the device will be ceiling-mounted, the PIR needs a Fresnel lens that creates a "cone" of coverage. An angle of at least $100^{\circ}$ to $120^{\circ}$ is required (to match the mmWave radar's field of view).
*   **Detection Distance:** Must cover the distance from the ceiling to the floor and slightly beyond for movements at the edges of the room. A detection range of 4 to 6 meters is optimal.
*   **Digital Output / Interrupt:** The PIR must provide a simple digital signal (HIGH or LOW) when motion is detected. This signal (Interrupt Pin) connects directly to the MCU to wake it instantly from deep sleep (Hardware Wake-up).
*   **On-board Delay Timer:** Ability to adjust the time the output remains active (HIGH) after detecting motion, giving the MCU enough time to initialize the mmWave.
*   **Temperature Compensation:** Since the PIR detects temperature differences, its sensitivity decreases when the room is very warm (e.g., $35^{\circ}C$ in summer, close to human body temperature). A good PIR must automatically adjust its sensitivity based on ambient temperature.
*   **White Light Immunity:** Must have a filter so it is not accidentally triggered by a sudden light turning on or sunbeams entering from a window.
*   **EMI/RFI Shielding:** Because it will be on the same small board as the mmWave radar and Zigbee transmitter (which emit radio waves), the PIR must be shielded to prevent false detections caused by antenna interference.

## Technical Specifications of Microcontroller
*   **Deep Sleep / System-Off Current:** Must be extremely low, ideally $<2 \mu A$ (with RAM retention and the interrupt controller active).
*   **Wake-up Time:** The time required to transition from deep sleep to full operation must be instantaneous (in the range of a few microseconds - $\mu s$) to avoid losing valuable time when the PIR sends the activation signal.
*   **Power Gating:** The MCU must be able to completely cut off the power supply (VCC) to the mmWave circuit via a simple GPIO pin or a MOSFET, ensuring the radar consumes no leakage current when the room is empty.
*   **Built-in Radio (SoC):** The antenna and transmission circuit must be inside the same chip (SoC) to save space and energy.
*   **Mesh Protocol Support:** Must natively support low-power, long-range protocols, primarily Zigbee 3.0 or Thread. (Bluetooth Low Energy - BLE is useful for initial device setup via smartphone, but not for communicating with the central unit across a large house).
*   **TX Power:** Ability to transmit at least +8 dBm so the signal reliably passes through walls to reach the Central Unit.
*   **Hardware Interrupts:** At least one pin must support waking the system (Wake-on-Interrupt) to connect with the PIR sensor.
*   **Communication Ports (UART / I2C / SPI):** Support for communication protocols (mainly UART or I2C) to reliably receive the final results (e.g., "Fall_Detected = 1") from the embedded mmWave processor.
*   **Built-in ADC (Analog-to-Digital Converter):** Necessary for continuously measuring battery voltage (V) so the system can send a low battery warning to the Central Unit in time.
*   **Core:** An ARM Cortex-M4 or RISC-V 32-bit processor is more than sufficient. (Families like Nordic Semiconductor's nRF52840 or Espressif's ESP32-H2 are highly recommended).
*   **Clock Speed:** 32 MHz to 96 MHz. High speeds are unnecessary, as the MCU does not process complex math, and a lower frequency helps drastically reduce consumption when the chip is active (Active Mode).
*   **Memory (RAM / Flash):** Sufficient memory (e.g., 256KB RAM / 1MB Flash) to smoothly run a real-time operating system (RTOS) and the Zigbee network stack without crashing.

## Technical Specifications of Battery
*   **Non-Rechargeable Lithium (Li-SOCl2 - Lithium Thionyl Chloride):** The ultimate choice for industrial IoT sensors. They have a nominal voltage of 3.6V, enormous energy density, and an extremely low self-discharge rate (losing less than 1% of their energy per year). Such a battery (e.g., AA size - ER14505) can keep the device alive for years.
*   **Capacity:** To achieve the desired autonomy, the battery should have a capacity of at least 2500mAh to 4000mAh.
*   **Pulse Discharge Current:** A very critical feature. When the PIR wakes the system, the mmWave sensor and Zigbee transmitter turn on simultaneously. At that moment, the system momentarily "pulls" a large current (e.g., 100mA - 150mA for a few milliseconds). If a Li-SOCl2 battery is chosen (which typically cannot handle large sudden discharges), it will be necessary to place a parallel smoothing capacitor (Supercapacitor / HPC) on the board to provide immediate energy to the radar without the battery voltage dropping.
*   **Ultra-low Iq LDO (Low Dropout Regulator):** The battery voltage (3.6V or 3.7V) must be reduced to the 3.3V required by the chips. The voltage regulator (LDO) placed between the battery and the electronics must have a Quiescent Current (Iq) of less than $1 \mu A$. A cheap regulator would drain the battery on its own (due to conversion losses) even when the entire system is asleep.
*   **System Connection:** Ideally, the battery connects to the board via a secure JST connector (e.g., JST-PH 2.0mm). This prevents incorrect connections (reverse polarity) during assembly or replacement.
*   **Operating Temperature:** A house ceiling can accumulate high temperatures in the summer (e.g., $40^{\circ}C - 45^{\circ}C$). The battery must be certified to operate safely in a range of at least $-10^{\circ}C$ to $+60^{\circ}C$ without risk of swelling or leaking.
*   **Weight & Size:** Must be light enough (usually around 20-50 grams) so as not to burden the sensor's mounting on the ceiling (e.g., with double-sided tape or magnets).

## Technical Specifications of Central Microcontroller
*   **Main MCU:** A dual-core processor like Espressif's ESP32-S3 (at 240MHz). It has plenty of memory (RAM/Flash) to run a web server, manage encryption protocols (SSL/TLS), and perform secondary data confirmation.
*   **Radio Co-processor:** A separate chip (like the CC2652 by Texas Instruments or the nRF52840) that exclusively handles the Zigbee network operation.
*   **To Sensors (Local Network):** Must act as a Zigbee 3.0 Coordinator. Unlike sleeping sensors (End Devices), the Coordinator has its radio receiver continuously open (Always-On Receiver) to immediately catch the fall signal whenever it occurs.
*   **To Internet (External Network):** Built-in Wi-Fi (802.11 b/g/n). Ideally, for medical alert systems, an Ethernet port is also recommended (e.g., via the LAN8720 chip) for a wired, 100% uninterrupted connection to the home router.
*   **Main Power Supply:** 5V DC (at least 1.5A-2A) via a modern USB-C port or a classic wall adapter (Barrel Jack).
*   **Emergency Battery (Backup):** A rechargeable Lithium-Ion battery (e.g., a single 18650 cell of 2500mAh-3000mAh capacity). This can keep the Central Unit alive for over 10-15 hours without power.
*   **Uninterruptible Power Supply Circuit (Power Path Management):** A specialized chip (like the IP5306 or BQ24074) is required, which charges the battery while power is available, and in the event of a blackout, transfers the load to the battery without any interruption (zero-transfer time), ensuring the MCU does not reboot.
*   **LED Indicators:** At least 3 different LEDs (or an RGB LED) will be needed:
    *   **Green/Blue:** Normal operation & Internet connection.
    *   **Yellow/Orange:** Problem (e.g., a sensor disconnected, low sensor battery, Internet down).
    *   **Red (flashing):** Alarm / Fall detected.
*   **Buzzer:** A built-in Piezo Buzzer. When a fall is detected, the hub must produce a sound so that if the elderly person simply stumbled but is fine, they know the system was triggered.
*   **Cancel / Pairing Button:** A physical push button on the hub. If pressed briefly during the alarm, it cancels the notification message (False alarm cancellation). If held down, it puts the hub into Pairing mode to connect new sensors.
*   **Watchdog Timer (WDT):** A critical safety feature. If the software crashes for any reason (e.g., due to a bad Wi-Fi connection), the Watchdog automatically reboots the device within seconds.
*   **OTA Support (Over-The-Air Updates):** Ability to upgrade software remotely (via the Internet), so algorithms can be improved without needing to connect a cable to the device.
*   **Non-Volatile Memory (EEPROM / NVS):** To store Wi-Fi credentials (SSID, Password) and sensor IDs. If power is lost and the backup battery is depleted, the device must remember all its settings when power is restored.

## Bibliography
*   mmWave sensing - Wikipedia
*   Motion sensors: Why mmWave? - Grobotronics
*   Millimeter Wave Sensors: Complete Guide to Tech, Benefits & Applications
*   (All technical specifications were written with the help of Gemini)
