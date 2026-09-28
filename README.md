# MST Blockchain x Newrro 24-Hour Buildathon

> **AutoFleet-M2M**: Autonomous Sentinel Rover with Agentic AI, LAYA On-Device Decision Engine & Decentralized Machine-to-Machine Commerce on MST Blockchain.
> 
> *Venue:* BMS College of Engineering, Bengaluru  
> *Track:* AI & Web3 Builders Track  
> *Date:* September 28–29, 2026  
> 
> **Note: This repository contains the AI Cognitive Core, Edge Decision Engine, and MCP Agent Orchestrator.**

---

## 🚀 Overview: The AI Subsystem

Autonomous cyber-physical rovers in industrial facilities and hazard zones face two fundamental challenges:
1. **The Perception & Latency Gap:** Generative cloud LLMs take 1.5 to 8+ seconds per request, hallucinatory generative text breaks embedded parsers, and cloud drops blind the robot.
2. **The Isolated Machine Economy:** Mobile agents cannot autonomously assess economic risk, verify depot authenticity, or pay for power/recharging using trustless on-chain currency.

This repository provides the **complete AI subsystem** for **AutoFleet-M2M**. Powered by **LAYA** (a lightweight, non-autoregressive decision model) and a hierarchical **Model Context Protocol (MCP)** agent architecture, it bridges physical rover telemetry (NEWRRO NEURICK) with decentralized transaction execution on the **MST Blockchain (Layer 1)**.

```
                      +------------------------------------------+
                      |        NEWRRO NEURICK ROBOT PLATFORM     |
                      |   (ESP32-S3 + STM32F103 Dual-Controller) |
                      |    Sensors: MQ135, Flame, HC-SR04, GPS    |
                      +--------------------+---------------------+
                                           | Telemetry Feed (<10ms)
                                           v
+-----------------------------------------------------------------------------------------+
|                                THIS REPOSITORY: AI COGNITIVE CORE                       |
|                                                                                         |
|   +------------------------------------+       +------------------------------------+   |
|   |         SYSTEM 1 REFLEX CORE       |       |        SYSTEM 2 AGENTIC CORE       |   |
|   |          (LAYA Decision Engine)    | <---> |      (Model Context Protocol)      |   |
|   |                                    |       |                                    |   |
|   |  - Non-autoregressive encoder      |       |  - Mission & waypoint planner      |   |
|   |  - Sub-35ms deterministic inference|       |  - Long-horizon spatial logic      |   |
|   |  - Typed decisions: choice, score  |       |  - Autonomous tool invocation      |   |
|   |  - Calibrated transaction gate     |       |  - Multi-agent fleet coordination  |   |
|   +-----------------+------------------+       +-----------------+------------------+   |
+---------------------|--------------------------------------------|----------------------+
                      | Verified Action & Gate                     | Proof of Work & Tx
                      v                                            v
         +--------------------------+                 +--------------------------+
         |   PHYSICAL ACTUATION     |                 |  MST BLOCKCHAIN (L1)     |
         | - Fluid suppression pump |                 | - BridgeKey machine auth |
         | - Servo aim / obstacle   |                 | - M2M $MSTC micropayment |
         | - Emergency halt relay   |                 | - PoPW inspection audit  |
         +--------------------------+                 +--------------------------+
```

---

## 🧠 Dual-Process AI Architecture

The system mimics dual-process cognitive psychology to ensure both sub-second robotic reflexes and strategic planning:

### 1. System 1: Ultra-Fast Reflexes (LAYA Engine)
* **Latency:** $<35$\,ms on standard CPU; $<8$\,ms with TileLang GPU kernels.
* **Non-Autoregressive:** Evaluates multiple predefined typed queries in a single deterministic forward pass without token-by-token generation overhead.
* **Primitives:**
  - `choice`: Discrete action selection (e.g., `extinguish`, `avoid_collision`, `dock_for_refill`, `patrol_sweep`).
  - `score`: Calibrated threat/severity ranking (`nominal`, `moderate_anomaly`, `critical_hazard`).
  - `noul`: Calibrated probability verification (used as an economic firewall to authorize or reject on-chain transaction execution).

### 2. System 2: Deliberative Goal Orchestration (MCP Agent)
* Coordinates complex, multi-stage missions (e.g., facility perimeter sweeps, waypoint navigation, automated refueling negotiations).
* Exposes standardized Model Context Protocol (MCP) tools for telemetry polling, actuator triggering, and cryptographic signing.

---

## 🛠️ Hardware & Sensor Telemetry Integration

The AI engine processes multi-modal telemetry streams sourced from the NEWRRO NEURICK robotics platform:

| Sensor / Actuator | Interface | Hardware Pin | AI Decision Role |
|---|---|---|---|
| **MQ135 Gas Sensor** | Analog ADC | GPIO4 (Header Pin 2) | Toxic & flammable gas anomaly detection |
| **Infrared Flame Sensor** | Digital | GPIO5 (Header Pin 4) | Fire detection trigger for suppression |
| **HC-SR04 Ultrasonic** | GPIO (Trig/Echo) | GPIO6 / GPIO7 | Proximity & emergency obstacle avoidance |
| **DHT22 & DS18B20** | 1-Wire Digital | GPIO10 / GPIO11 | Thermal runaway & ambient climate analysis |
| **RC522 RFID Module** | SPI | GPIO12–GPIO15 | Charging pad authentication & physical proximity proof |
| **Ublox NEO-6M GPS** | UART | GPIO17 / GPIO18 | Geofencing & Proof of Physical Work (PoPW) |
| **R385 Pump & Relay** | Digital Relay | GPIO13 | Fire suppression fluid actuation |
| **MG995 Servo** | PWM | STM32 Servo Ch 1 | Precision nozzle aiming and mechanical latching |

---

## 💻 AI Decision Loop & Code Walkthrough

Below is the production decision loop executed by the rover's on-device cognitive core:

```python
from laya import Router

# Initialize the lightweight on-device decision router
router = Router()

def evaluate_rover_state(telemetry: dict) -> dict:
    """
    Evaluates real-time rover telemetry and produces typed physical
    and economic actuation decisions in <35ms.
    """
    state_context = f"""
    Rover Status:
    - Gas Concentration: {telemetry['mq135_ppm']} ppm
    - Flame Sensor Triggered: {telemetry['flame_detected']}
    - Ambient Temp: {telemetry['temp_c']} C | Surface Temp: {telemetry['surface_temp_c']} C
    - Obstacle Distance: {telemetry['distance_cm']} cm
    - Battery Voltage: {telemetry['battery_v']} V
    - Fire Suppressant Reservoir: {telemetry['tank_pct']}%
    - Quoted Depot Rate: {telemetry['depot_quote']} MSTC
    - Scanned RFID Tag: {telemetry['rfid_tag']}
    - GPS Coordinates: ({telemetry['lat']}, {telemetry['lon']})
    """

    questions = {
        # Immediate physical reflex actuation
        "physical_action": {
            "type": "choice",
            "instructions": "Determine immediate physical reflex action.",
            "criteria": {
                "extinguish": "Flame detected or dangerous combustible gas levels detected",
                "avoid_collision": "Obstacle closer than 15 cm ahead",
                "dock_for_refill": "Battery critically low (<11.2V) or tank depleted near authorized depot",
                "patrol_sweep": "All environmental readings nominal; continue route"
            }
        },
        # Environmental risk assessment
        "hazard_severity": {
            "type": "score",
            "instructions": "Assess overall environmental danger rating.",
            "criteria": ["nominal", "moderate_anomaly", "critical_hazard"]
        },
        # Economic transaction firewall
        "authorize_m2m_tx": {
            "type": "noul",
            "instructions": "Authorize trustless on-chain MSTC micro-payment for resource replenishment?"
        }
    }

    # Execute deterministic System-1 forward pass
    decision = router.predict(state_context, questions)
    
    action = decision["answers"]["physical_action"]["choice"]
    tx_confidence = decision["answers"]["authorize_m2m_tx"]["noul"]
    
    # 1. Execute physical reflex
    if action == "extinguish":
        trigger_fluid_pump(duration_seconds=5)
    elif action == "avoid_collision":
        trigger_emergency_evasion()
        
    # 2. Execute economic transaction gated by calibrated AI confidence
    if action == "dock_for_refill" and tx_confidence > 0.85:
        execute_mst_micropayment(
            depot_address=telemetry["depot_wallet"],
            amount_mstc=telemetry["depot_quote"]
        )

    return decision
```

---

## ⚡ Key Technical Features in this Repository

* **`laya/` Core Decision Engine**: PyTorch & ONNX non-autoregressive encoder architecture.
* **Calibrated Confidence Gating (`laya/confidence.py`)**: Platt and temperature scaling for probabilistic gating of Web3 payments.
* **Model Context Protocol (`laya/mcp/`)**: Full MCP server exposing robotics tools, GPS telemetry, and MST transaction helpers to LLM reasoning agents.
* **Fast TileLang GPU Kernels (`laya/fast.py`, `laya/tl_kernels.py`)**: Fused attention kernels for ultra-low latency inference on edge accelerators (Jetson / RTX).
* **Cross-Language TypeScript Bindings (`laya-ts/`)**: Browser & Node.js ONNX runtime bindings for dashboard telemetry and remote rover control.
* **REST & Streaming API (`laya/serve.py`)**: FastAPI server for remote telemetry ingestion and live fleet inspection.

---

## 📦 Quickstart

### 1. Installation

```bash
# Clone the repository
git clone https://github.com/HirthikBalaji/MST-Blockchain-x-Newrro-24-Hour-Buildathon.git
cd MST-Blockchain-x-Newrro-24-Hour-Buildathon

# Install core dependencies
pip install -e .

# Optional: Install MCP server and FastAPI serving support
pip install -e ".[mcp,serve]"
```

### 2. Run Local Decision Tests

```bash
# Run core decision model and router verification
python tests/test_router.py
python tests/test_criteria.py
python tests/test_confidence.py
```

### 3. Launch the MCP Robotics Server

```bash
# Expose rover AI capabilities over Model Context Protocol
laya-mcp-server
```

---

## 👥 Authors & Team

* **Hirthik Balaji** ([hirthikbalaji2006@gmail.com](mailto:hirthikbalaji2006@gmail.com))
* **Anandhappriya** ([anandhappriya@gmail.com](mailto:anandhappriya@gmail.com))

*Submitted for the **MST Blockchain x Newrro 24-Hour Buildathon**, BMS College of Engineering, Bengaluru.*
