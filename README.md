# Fall Detection System

## Abstract
A comprehensive fall detection system designed for elderly support. It utilizes millimeter-wave (mmWave) radar technology for continuous, contactless tracking of position and micro-motions (vital signs), with an emphasis on low power consumption and privacy preservation.

## System Architecture
The system is divided into two primary subsystems:

*   **Sensor Nodes:** Autonomous ceiling-mounted units. They integrate a mmWave radar (60GHz/24GHz) for point cloud processing, a PIR sensor for hardware wake-up, and a low-power microcontroller. Powered by Lithium Thionyl Chloride (Li-SOCl2) batteries.
*   **Central Hub:** Data collection and processing unit. It operates as a Zigbee Coordinator, verifies fall events, and routes emergency notifications via Wi-Fi/Ethernet. Supported by an uninterruptible power supply system (UPS/Backup battery).

## Repository Structure
This repository is organized into the following directories:

*   `/hardware`: Schematic diagrams, Printed Circuit Board (PCB) design files, and Bill of Materials (BOM) for the sensors and the central hub.
*   `/firmware`: Microcontroller source code, detection algorithms, power management routines, and communication stacks (Zigbee/Wi-Fi).
*   `/docs`: Detailed documentation, system specifications, and user manuals.

## License
Due to the mixed nature of the project, a dual-licensing model is applied:
*   Source code and software (within the `/firmware` directory) are distributed under the **Apache License 2.0**.
*   Hardware design files (within the `/hardware` directory) are distributed under the **CERN Open Hardware Licence Version 2 - Permissive (CERN-OHL-P)**.

For further details, refer to the respective `LICENSE` files within the individual directories.
