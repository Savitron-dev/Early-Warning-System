# AI-Powered Early Warning System — In-Vehicle Unit (CAD & Mechanical Enclosure)

> **Repository:** [`Savitron-dev/Early-Warning-System`](https://github.com/Savitron-dev/Early-Warning-System)

## 📌 Executive Summary

Everyday drivers and vulnerable pedestrians lack a proactive safety network, leaving them exposed to unintentional traffic violations and unpredictable collisions. Existing safety infrastructure is entirely **reactive**, offering a **0-second predictive warning window**.

This repository contains the **3D CAD mechanical enclosure designs**, **exploded assembly models**, **KiCad hardware schematics**, and **prototype specifications** for the **Controlled Traffic System - Vehicle Unit**.

Our system delivers a **3 to 5-second predictive warning window** by acquiring real-time vehicle speed via OBD-II/CAN bus, processing high-precision positioning via RTK GNSS, and communicating localized risks over 4G/5G cellular mesh and cloud backends.

## 🏗️ Hardware Architecture & CAD Design

The vehicle-mounted hardware unit is designed in **Autodesk Fusion 360** as a modular, ruggedized, and heat-dissipating dashboard-mounted enclosure housing all core telemetry and computational electronics.

```
+-----------------------------------------------------------------------------------+
|                        IN-VEHICLE ENCLOSURE ASSEMBLY                              |
|                                                                                   |
|  +------------------+   +-------------------+   +------------------------------+  |
|  | Camera Module    |   | Removable Lid     |   | Display & Speaker Outputs    |  |
|  | Assembly (Top)   |   | Mounting Standoffs|   | Visual / Audio Warnings      |  |
|  +--------+---------+   +---------+---------+   +--------------+---------------+  |
|           |                       |                            |                  |
|  +--------v-----------------------v----------------------------v---------------+  |
|  |                           MAIN PCB                                         |  |
|  |  +---------------+  +-------------------+  +----------------------------+  |  |
|  |  | ESP32 Micro   |  | MCP2515 + CAN     |  | RTK GNSS Positioning       |  |  |
|  |  | Controller    |  | Transceiver       |  | Antenna & Receiver         |  |  |
|  |  +---------------+  +-------------------+  +----------------------------+  |  |
|  |  +---------------+  +-------------------+  +----------------------------+  |  |
|  |  | 4G/5G Cellular|  | Protected Power   |  | OBD-II Connector Port      |  |  |
|  |  | Modem Module  |  | Regulation Supply |  | (Read-Only CAN Access)     |  |  |
|  |  +---------------+  +-------------------+  +----------------------------+  |  |
|  +----------------------------------------------------------------------------+  |
+-----------------------------------------------------------------------------------+
```

### 🧩 Exploded Mechanical Assembly Features

1. **Main Enclosure Body:** Low-profile dashboard housing with internal mounting rails and heat vents for high-temperature automotive operation.
2. **Removable Enclosure Lid:** Top-secured lid featuring recessed mounting holes and a dedicated camera housing pedestal.
3. **Internal Mounting Deck:** Standoff-based mounting deck for rigid alignment of the Main Controller PCB, preventing vibration-induced solder degradation.
4. **OBD-II Interface Cutout:** Dedicated side slot for pass-through wiring connection to the vehicle’s CAN Bus port.
5. **Camera Module Housing:** Top-mounted directional bracket providing forward-facing line-of-sight for optical signal recognition.
6. **Acoustic Grille Port:** Integrated speaker/buzzer cutout designed for high-decibel audible driver alerts ($>85\text{ dB}$).

## 📁 File Types & Software Compatibility

This repository provides source files across standardized CAD, archive, and EDA formats:

| File Extension | File Type / Description | Compatible Software |
| :--- | :--- | :--- |
| **`.f3z`** | **Fusion 360 Distributed Archive** — Proprietary multi-component assembly file containing the full parametric CAD model and exploded views. | Can **only** be opened in **Autodesk Fusion 360**. |
| **`.step`** | **Standard for Exchange of Product Data** — Universal neutral 3D CAD exchange format preserving solid geometry and assembly topology. | Opens in almost **any 3D CAD software** (e.g., SolidWorks, FreeCAD, Onshape, CATIA, AutoCAD). |
| **`.kicad_sch`** | **KiCad Schematic File** — Native schematic design file containing circuit connection topology, component symbols, and pin mappings. | Opened and edited using **KiCad EDA** (v7.0+ / v10.0+). |

## 📋 Itemized Bill of Materials (BOM)

| Component | Specification / Part | Role & Functional Description | Qty | 
| ----- | ----- | ----- | ----- | 
| **Main Controller** | ESP32 (Dual-Core 240 MHz) | Core real-time processing, algorithm evaluation & local edge decision logic | 1 | 
| **CAN Controller** | MCP2515 (SPI) | Interfacing with automotive vehicle speed PID via CAN bus | 1 | 
| **CAN Transceiver** | SN65HVD230 / MCP2562 | High-speed physical layer CAN differential signaling interface | 1 | 
| **Positioning Module** | RTK GNSS / Multi-Constellation | Precise location, heading vector, and road segment identification | 1 | 
| **Cellular Telemetry** | 4G LTE / 5G Mobile Modem | High-speed long-range communication with cloud safety backend | 1 | 
| **Vehicle Interface** | OBD-II Standard Plug & Cable | Read-only physical connection to vehicle diagnostic port | 1 | 
| **Alert Output** | Piezo Buzzer / $8\text{ }\Omega$ Speaker | High-decibel audible driver warning system | 1 | 
| **Power Protection** | Protected Buck Converter Module | Automotive voltage regulation ($12\text{V}$ to $5\text{V} / 3.3\text{V}$) with transient suppression | 1 | 
| **Camera Module** | Optical Camera (SPI/Parallel) | Forward-facing traffic light state and sign identification | 1 | 
| **Enclosure** | Fusion 360 Custom Enclosure | Dashboard PETG/ABS heat & UV resistant 3D-printed enclosure | 1 | 

## 🔌 Electronic Schematics (`.kicad_sch`)

The circuit schematic is located at `schematics/Controlled_Traffic_System_Wired.kicad_sch`.

### Key Interface Topology:

* **ESP32** $\leftrightarrow$ **MCP2515:** SPI Interface (`SCK`, `MOSI`, `MISO`, `CS`) + `INT` (Interrupt line).
* **MCP2515** $\leftrightarrow$ **SN65HVD230 Transceiver:** `TXCAN`, `RXCAN`.
* **Transceiver** $\leftrightarrow$ **OBD-II Port:** `CAN_H` and `CAN_L` differential data bus lines (Read-Only mode).
* **ESP32** $\leftrightarrow$ **RTK GNSS:** High-speed UART (`TXD`, `RXD`, `PPS` pulse per second).
* **ESP32** $\leftrightarrow$ **4G/5G Module:** UART AT Command Interface (`TX`, `RX`, `RESET`, `STATUS`).
* **Power Protection Unit:** $V_{\text{BAT}}\text{ (12V Input)}$ $\rightarrow$ Automotive TVS Diode + Reverse Polarity Protection $\rightarrow$ Step-down Buck Converter $\rightarrow$ $3.3\text{V} / 5.0\text{V}$ Regulated Rails.

## 🛡️ Safety, Standards & Fail-Safe Design

1. **Read-Only Vehicle Interface:**
   * Hardware & software interfaces are strictly configured to query standard vehicle PIDs (`0x0D` for Vehicle Speed). No write or control commands are transmitted to the vehicle Engine Control Unit (ECU).
2. **Fail-Safe Edge Processing:**
   * In the event of cellular or network dropouts, local speed evaluation and overspeed alerts operate independently on-device via edge computing logic.
3. **Automotive Standards Alignment:**
   * **ISO 26262:** Functional Safety considerations for automotive electronic systems.
   * **ISO/SAE 21434:** Cybersecurity alignment for connected vehicle telemetry.
   * **UN Regulation No. 10:** Electromagnetic Compatibility (EMC) compliance for vehicle accessories.

## 💻 How to Open & Render Project Files

### 1. Opening `.f3z` CAD Files (Autodesk Fusion 360)
* `.f3z` is an archived Fusion 360 assembly.
* **To open:** Launch **Autodesk Fusion 360** $\rightarrow$ **File** $\rightarrow$ **Open...** $\rightarrow$ **Open from my computer** and select `Controlled Traffic System - Vehicle Unit.f3z`.

### 2. Opening `.step` CAD Files (Universal 3D CAD)
* `.step` (ISO 10303) allows seamless import across any engineering software.
* **To open:** Import `Controlled Traffic System - Vehicle Unit.step` into **SolidWorks**, **FreeCAD**, **Onshape**, **Autodesk Inventor**, or **Rhino**.
* **3D Printing Recommendation:** Print enclosure using **PETG** or **ABS** for UV resistance and heat tolerance inside the vehicle dashboard.

### 3. Opening `.kicad_sch` Files (KiCad EDA)
* `.kicad_sch` contains the hardware schematics and module pinouts.
* **To open:** Download and install [KiCad EDA](https://www.kicad.org/), open **KiCad**, click **Open Schematic**, and browse to `Controlled_Traffic_System_Wired.kicad_sch`.

## 👥 Development Team & Acknowledgments

* **Team ID:** `VK26-IITH-SC-0359`
* **Institution:** Indian Institute of Technology Hyderabad (IIT Hyderabad)
* **Competition:** Vishwakarma Awards 2026–27 (Stage 2)
* **Organized By:** Maker Bhavan Foundation
* **Track:** Sustainable Cities — Smart Urban Infrastructure & Asset Tracking

---
*For software code, backend APIs, and pedestrian mobile application code, please refer to the primary repository directories.*
