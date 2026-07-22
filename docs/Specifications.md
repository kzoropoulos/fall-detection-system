## Fall Detection System Overview

This system utilizes millimeter-wave (mmWave) radar technology to protect the elderly by detecting abrupt height changes and immobilization on the floor. Upon detecting a fall, peripheral room sensors wirelessly alert a central unit, which then immediately notifies designated contacts or health services over the internet.

### System Components

| Component | Primary Function | Power Source | Key Feature |
| --- | --- | --- | --- |
| **Peripheral Sensors** | Map 3D position and detect micro-motions/falls in specific rooms.

 | Long-lasting internal battery.

 | Discreet, ceiling-mounted, and operate independently.

 |
| **Central Unit** | Route emergency notifications and confirm fall events.

 | Mains power with a backup battery.

 | Internet-connected with LED and buzzer feedback.

 |

---

## Technical Specifications

* **mmWave Radar:** Operates optimally at 60GHz for high-resolution micro-motion detection, processing data internally to map 3D posture and filter out pets using Radar Cross Section.


* **Radar Power Management:** Utilizes a Pulsed Coherent or Low-Power FMCW radar that remains in deep sleep (<10 µA) until woken by a hardware interrupt from the PIR sensor.


* **Sensor Communication:** The embedded microcontroller transmits only simple event data (fall detected or periodic keep-alive signals) via low-power Zigbee protocols to conserve energy.


* **PIR Sensor:** Features ultra-low standby current (<3 µA), a 100° to 120° field of view, 4 to 6 meters of range, and environmental shielding against temperature, light, and radio interference.


* **Peripheral Microcontroller:** An energy-efficient 32-bit SoC (32 to 96 MHz) equipped with instant wake-up capabilities, power gating, and sufficient transmission power (>+8 dBm) to penetrate walls.


* **Peripheral Battery:** Employs a non-rechargeable, high-capacity Li-SOCl2 battery (2500mAh to 4000mAh) coupled with a supercapacitor to handle sudden power draws without voltage drops.


* **Central Microcontroller:** Utilizes a main processor alongside a dedicated Zigbee coordinator chip for constant network listening, paired with Wi-Fi or Ethernet for external communication.


* **Central Unit Safety:** Includes an uninterruptible power supply (UPS) circuit for blackout protection, a Watchdog Timer for automatic crash reboots, and non-volatile memory to retain settings without power.


* **Central Unit Interface:** Features multi-colored LED status indicators, a built-in piezo buzzer to alert the user of a trigger, and a physical button to cancel false alarms or pair new devices.
