# AutoFleet-M2M

### Autonomous Sentinel Rover with Agentic AI, LAYA On-Device Decision Engine & Decentralized Machine-to-Machine Commerce on MST Blockchain

[![MST Blockchain](https://img.shields.io/badge/Blockchain-MST%20Layer%201-1f4e79?style=flat-square)](https://mstscan.com)
[![Hardware](https://img.shields.io/badge/Hardware-NEWRRO%20NEURICK%20(ESP32--S3%20+%20STM32)-548235?style=flat-square)](https://newrro.com)
[![AI Engine](https://img.shields.io/badge/AI%20Engine-LAYA%20(<35ms%20System--1)-c00000?style=flat-square)](./AI-MODULE)
[![Protocol](https://img.shields.io/badge/Protocol-Model%20Context%20Protocol%20(MCP)-orange?style=flat-square)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue?style=flat-square)](./AI-MODULE/LICENSE)

* **Event:** [MST Blockchain x Newrro 24-Hour Buildathon](https://unstop.com/hackathons/mst-blockchain-newrro-bmsc-24-hour-buildathon-bms-college-of-engineering-1750981)
* **Venue:** BMS College of Engineering, Bengaluru
* **Date:** September 28–29, 2026
* **Track:** AI & Web3 Builders Track

---

## 📌 Executive Summary

Modern autonomous robotics in hazardous industrial environments face two fundamental bottlenecks:
1. **The Perception & Decision Latency Gap:** Fragile rule-based thresholds produce false alarms, while cloud-based generative LLMs (1.5s–8s latency, hallucinations, cloud dropouts) cannot satisfy real-time emergency reflex deadlines.
2. **The Isolated Machine Economy Bottleneck:** Autonomous rovers operate in administrative silos. They cannot autonomously negotiate, verify, and pay for fuel, charging power, or suppressant refills using sovereign, trustless micro-commerce.

**AutoFleet-M2M** is a complete cyber-physical robotic system built on the dual-controller **NEWRRO NEURICK** platform (ESP32-S3 + STM32F103). It couples the **LAYA** non-autoregressive decision engine ($<35$\,ms deterministic latency) for System-1 fast reflexes with an autonomic System-2 goal-directed agent orchestrator leveraging the **Model Context Protocol (MCP)**. Integrated directly into the Indian Layer-1 **MST Blockchain**, AutoFleet-M2M executes an autonomous closed-loop OODA cycle: sensing hazards, actuating physical fire-suppression countermeasures, and conducting peer-to-peer Machine-to-Machine (M2M) micro-commerce using \$MSTC tokens through non-custodial machine wallets.

---

## 🏛️ Three-Tier System Architecture

```
+===================================================================================================+
|                                    AUTOFLEET-M2M ARCHITECTURE                                     |
+===================================================================================================+
|                                                                                                   |
|  [ TIER 1: PHYSICAL ROBOTICS ]                                                                    |
|  NEWRRO NEURICK Platform:                                                                         |
|  * STM32F103 (Motor loop, encoder feedback, I2C 0x08)                                             |
|  * ESP32-S3 (Sensor fusion, GPIO expansion header, Wi-Fi/BLE)                                     |
|  * Multi-Modal Sensors: MQ135 Gas, Flame, HC-SR04 Ultrasonic, DHT22, DS18B20, RC522 RFID, GPS     |
|  * Physical Actuators: R385 Water Pump + Relay, MG995 Aiming Servo, SSD1306 OLED Display          |
|                                         |                                                         |
|                                         | 10ms Telemetry Stream                                   |
|                                         v                                                         |
|  [ TIER 2: COGNITIVE DECISION ENGINE (AI-MODULE/) ]                                               |
|  Dual-Process Agentic AI:                                                                         |
|  * System 1 (LAYA Engine): Non-autoregressive encoder (<35ms CPU / <8ms TileLang GPU)             |
|    - Typed Primitives: [choice] Discrete Action | [score] Hazard Rank | [noul] Tx Confidence Gate |
|  * System 2 (MCP Orchestrator): Mission planning, multi-hop navigation, dynamic tariff auction    |
|  * Tools: Model Context Protocol (MCP) server unifying local robot drivers & MST Blockchain RPC   |
|                                         |                                                         |
|                                         | Calibrated Transaction Authorization (Confidence > 0.85)|
|                                         v                                                         |
|  [ TIER 3: DISTRIBUTED LEDGER & SETTLEMENT ]                                                      |
|  MST Blockchain (Layer 1):                                                                        |
|  * Smart Contract Escrow: Autonomous machine lock, delivery challenge, and instant payout         |
|  * Sovereign Machine Wallets: BridgeKey integration with on-device private key signing            |
|  * Proof of Physical Work (PoPW): Cryptographic sensor telemetry hashing for tamper-proof audits  |
|                                                                                                   |
+===================================================================================================+
```

### Tier Comparison & Allocation

| Tier | Hardware / Software Core | Primary Responsibilities |
|---|---|---|
| **Tier 1: Physical Tier** | NEURICK (ESP32-S3 + STM32), DC Motors, MQ135, Flame, DHT22, DS18B20, HC-SR04, GPS, RC522 | Dual-core motion loop, interrupt-driven encoder tracking, multi-channel analog/digital telemetry sampling, mechanical fluid actuation. |
| **Tier 2: Cognitive Tier** | [`AI-MODULE/`](./AI-MODULE) (LAYA Engine, ONNX Runtime, MCP Robotics Server) | High-frequency inference ($<35$\,ms), typed categorical decisions (`choice`), risk scoring (`score`), and transaction confidence gating (`noul`). |
| **Tier 3: Settlement Tier** | MST Blockchain Layer 1, BridgeKey Wallet, Solidity Escrow Contracts | Autonomous machine keypair signing, task escrow locking, verified PoPW logging, and instant peer-to-peer \$MSTC micro-transfers. |

---

## 🔄 The Autonomic OODA Closed Loop

AutoFleet-M2M operates 24/7 without manual operator intervention:

1. **Observe (Multi-Modal Ingestion):** Ingests sensor streams from the NEURICK 40-pin header: toxic gas concentration (MQ135), infrared flame optical states, ambient/surface thermal data, obstacle distances, and GPS waypoints.
2. **Orient (Contextual Modeling):** Telemetry is normalized against internal rover state: battery voltage ($V_{\text{batt}}$), suppressant tank level ($L_{\text{fluid}}$), active inspection bounties, and available \$MSTC wallet balance.
3. **Decide (Hierarchical Agentic Inference):**
   - **System 1 (LAYA):** Instantly classifies imminent hazards and calculates calibrated confidence.
   - **System 2 (Planner):** If resource replenishment or mission selection is required, calculates economic utility:
     $$\mathcal{U}_{\text{agent}} = \mathbb{E}[\text{Bounty}_{\text{task}}] - \text{Cost}_{\text{refill}}(\text{tariffs}) - \text{Penalty}_{\text{delay}}$$
4. **Act (Physical Actuation):** High-level directives compile down to embedded commands dispatched to the STM32 motor controller (`nr.motor(ch, speed)`), the R385 pump relay, or the MG995 servo aiming arm.
5. **Settle (On-Chain Proof):** Signs cryptographic Proof of Physical Work (PoPW) receipts and settles payments on MST Blockchain Testnet.

---

## ⚡ M2M Micro-Commerce Protocol on MST Blockchain

```
Rover (Agent R)                                    Docking Depot (Agent D)
      |                                                      |
      |--------- 1. Proximity Handshake (RFID RC522) ------->|
      |                                                      |
      |<-------- 2. Dynamic Tariff Quote (e.g. 0.5 MSTC) ---|
      |                                                      |
      | [ LAYA Financial Gate Evaluates Utility > 0.85 ]     |
      |                                                      |
      |--------- 3. requestService() -> Escrow Locked ------>| (MST Blockchain)
      |                                                      |
      |                                 [ Fluid Dispensed via Pump ]
      |                                                      |
      |--------- 4. PoPW Telemetry Hash Confirmed ---------->|
      |                                                      |
      |<-------- 5. confirmDelivery() -> Funds Released -----| (MST Blockchain)
```

1. **Proximity Handshake:** Rover navigates within 5\,cm using ultrasonic guidance and scans the depot's passive RFID tag to authenticate vendor identity and MST address.
2. **Dynamic Tariff Bidding:** Depot broadcasts its real-time replenishment tariff based on inventory and electricity tariffs.
3. **LAYA Financial Gate:** The edge decision router evaluates whether battery/suppressant margins justify the transaction cost.
4. **Escrow Locking:** Rover signs an on-chain transaction invoking `requestService()` on `MachineCommerceEscrow.sol`.
5. **Physical Delivery & PoPW:** Depot actuates the fluid pump for 20 seconds. Upon level sensor confirmation, the rover commits a telemetry hash, releasing payment from escrow.

---

## 📂 Project Repository Organization

This repository is organized into modular subsystems:

```
.
├── README.md                    # Main Project Overview (This Document)
│
├── AI-MODULE/                   # Complete Cognitive & Decision Engine Subsystem
│   ├── README.md                # Dedicated AI-MODULE Architecture & Quickstart
│   ├── laya/                    # LAYA Decision Engine & Router Implementation
│   │   ├── agent.py             # Non-autoregressive Transformer encoder
│   │   ├── router.py            # Typed question routing & shortlist grouping
│   │   ├── confidence.py        # Temperature scaling & Platt calibration
│   │   ├── hooks.py             # Interceptors for telemetry logging & hardware
│   │   ├── fast.py              # TileLang GPU kernel optimizations (<8ms)
│   │   ├── onnx_agent.py        # ONNX Runtime embedded execution wrapper
│   │   ├── structured.py        # Typed output schemas and Pydantic helpers
│   │   ├── serve.py             # FastAPI REST endpoint server
│   │   ├── cli.py               # CLI evaluation and benchmarking utility
│   │   ├── mcp/                 # Model Context Protocol robotics & MST server
│   │   └── integrations/        # Connectors for LangChain, LlamaIndex, CrewAI
│   ├── laya-ts/                 # TypeScript client & browser ONNX bindings
│   ├── tests/                   # 75+ automated unit and integration tests
│   ├── research/                # Benchmarks, datasets, and performance reports
│   └── pyproject.toml           # Package metadata and dependencies
│
└── .github/                     # GitHub Actions CI/CD workflows
```

---

## 📋 Buildathon Mandatory Requirements Compliance

| Buildathon Requirement | Implementation in AutoFleet-M2M | Verification Status |
|---|---|:---:|
| **Meaningful MST Usage** | Non-custodial machine wallets execute automated escrow locks, dynamic service payments, and immutable PoPW telemetry hashes. Not a cosmetic login. | ✅ **PASS** |
| **Testnet Deployment** | Smart contract deployed to MST Testnet; verified transactions viewable on [mstscan.com](https://mstscan.com). | ✅ **PASS** |
| **Public GitHub Repo** | Complete open-source codebase including AI engine (`AI-MODULE/`), test suites, and documentation. | ✅ **PASS** |
| **BridgeKey Integration** | Used for human fleet operator oversight, balance deposits, mission creation, and fleet revenue sweeping. | ✅ **PASS** |
| **Working Hardware Demo** | Dual-microcontroller NEWRRO NEURICK rover reading live sensors, actuating pumps, and driving live on-chain transactions. | ✅ **PASS** |

---

## 🚀 Quickstart

### 1. Set Up the AI Subsystem
```bash
# Clone the repository
git clone https://github.com/HirthikBalaji/MST-Blockchain-x-Newrro-24-Hour-Buildathon.git
cd MST-Blockchain-x-Newrro-24-Hour-Buildathon

# Navigate to the AI Module and install
cd AI-MODULE
pip install -e ".[mcp,serve]"
```

### 2. Run Decision Verification Tests
```bash
pytest tests/test_router.py tests/test_confidence.py
```

### 3. Launch the Model Context Protocol (MCP) Robotics Server
```bash
laya-mcp-server
```

For in-depth AI engine configuration, benchmarking, and GPU acceleration, see **[`AI-MODULE/README.md`](./AI-MODULE/README.md)**.

---

## 👥 Authors & Team

* **Hirthik Balaji** ([hirthikbalaji2006@gmail.com](mailto:hirthikbalaji2006@gmail.com))
* **Anandhappriya** ([anandhappriya@gmail.com](mailto:anandhappriya@gmail.com))

*Built for the **MST Blockchain x Newrro 24-Hour Buildathon**, BMS College of Engineering, Bengaluru (September 28–29, 2026).*
