# AutoFleet-M2M Cognitive & Decision Subsystem (`AI-MODULE`)

> **The On-Device Non-Autoregressive Decision Engine, Probabilistic Economic Gatekeeper, and Model Context Protocol (MCP) Robotics Server for AutoFleet-M2M.**

Part of the **[AutoFleet-M2M](https://github.com/HirthikBalaji/MST-Blockchain-x-Newrro-24-Hour-Buildathon)** project for the **MST Blockchain x Newrro 24-Hour Buildathon** (BMS College of Engineering, Bengaluru).

---

## 🎯 Role in AutoFleet-M2M

In autonomous robotics operating in hazardous environments (e.g., toxic gas, fire outbreak, collision risk), relying on cloud-based generative LLMs is catastrophic:
- **High latency (1.5s–8s)** causes delayed emergency physical actuation.
- **Generative hallucination** breaks strict JSON schemas expected by microcontrollers.
- **Network dropouts** completely disable the robot in remote vaults or industrial basements.

The **`AI-MODULE`** resolves this by embedding a **non-autoregressive, calibrated decision model (LAYA)** directly on the edge. It serves as:
1. **System 1 (Reflex Decision Engine):** Evaluates multi-channel sensor telemetry in $<35$\,ms (CPU) or $<8$\,ms (GPU TileLang), triggering instant physical countermeasures (water pump, obstacle evasion).
2. **Economic Risk Firewall:** Outputs calibrated probabilities (`noul`) to mathematically verify that a transaction is economically rational before signing on-chain \$MSTC micropayments.
3. **System 2 (MCP Agent Orchestrator):** Implements the Model Context Protocol (MCP) to provide high-level agentic planners with discoverable robotic and blockchain tools.

---

## 🧠 Cognitive Architecture & Primitives

```
                             [ Multi-Channel Rover Telemetry ]
                            (MQ135 Gas, Flame, Ultrasonic, GPS)
                                             |
                                             v
               +-----------------------------------------------------------+
               |                     AI-MODULE Core                        |
               |                                                           |
               |   +---------------------------------------------------+   |
               |   |            System 1: LAYA Decision Router         |   |
               |   |        (Non-Autoregressive Transformer Encoder)   |   |
               |   +-------------------------+-------------------------+   |
               |                             |                             |
               |        +--------------------+--------------------+        |
               |        |                    |                    |        |
               |        v                    v                    v        |
               |     [choice]             [score]               [noul]     |
               |   Discrete Action    Hazard Severity     Calibrated Gate  |
               |  (e.g., Extinguish)  (e.g., Critical)    (Tx Auth > 0.85) |
               +--------+--------------------+--------------------+--------+
                        |                    |                    |
                        v                    v                    v
                 Actuate Pump/Servo    Telemetry Log       MSTC Escrow Sign
```

### 1. Typed Decision Primitives
Unlike generative models that generate tokens sequentially, LAYA processes the entire state context in a single forward pass and outputs typed answers:
* **`choice`**: Selects the optimal categorical action from discrete options based on semantic criteria.
* **`score`**: Assigns a calibrated scalar severity rank across pre-configured priority buckets.
* **`noul`**: Generates a mathematically calibrated probability ($[0.0, 1.0]$) evaluating a binary proposition (used as a trustless gate for machine-to-machine financial transactions).

### 2. Calibrated Confidence Scoring
Raw softmax outputs from neural networks are notoriously overconfident. `AI-MODULE` integrates temperature scaling and Platt calibration (`laya/confidence.py`), ensuring that when the model outputs an $85\%$ confidence score for `authorize_m2m_tx`, the decision historically holds true at that exact frequency.

### 3. Model Context Protocol (MCP) Server
`AI-MODULE` bundles a compliant MCP server (`laya.mcp`) allowing external LLMs and autonomic agent frameworks (LangChain, LlamaIndex, CrewAI) to invoke rover operations via standardized RPC:
* `mst_query_balance`: Inspect machine wallet liquidity on MST Testnet.
* `mst_sign_and_send`: Dispatch smart contract escrow payments.
* `laya_typed_decision`: Query the edge decision router with arbitrary telemetry state.
* `robot_actuate_hardware`: Trigger motor drives, fluid pump relays, and servo arms.
* `robot_rfid_handshake`: Authenticate physical contact with docking stations.

---

## 💻 Decision Loop Implementation

The primary entrypoint for on-device inference is `laya.Router`:

```python
from laya import Router

# Initialize the edge decision router (defaults to optimized weights)
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

    # Deterministic System-1 forward pass
    decision = router.predict(state_context, questions)
    
    action = decision["answers"]["physical_action"]["choice"]
    tx_confidence = decision["answers"]["authorize_m2m_tx"]["noul"]
    
    # 1. Trigger physical reflex
    if action == "extinguish":
        telemetry["pump_relay"].activate(duration_seconds=5)
    elif action == "avoid_collision":
        telemetry["motor_controller"].emergency_brake()
        
    # 2. Trigger economic transaction gated by calibrated AI confidence
    if action == "dock_for_refill" and tx_confidence > 0.85:
        telemetry["wallet"].sign_and_transfer(
            recipient=telemetry["depot_wallet"],
            amount=telemetry["depot_quote"]
        )

    return decision
```

---

## ⚡ Performance & Accelerators

| Execution Mode | Runtime Backend | Latency | Memory Footprint |
|---|---|---|---|
| **CPU Default** | PyTorch / ONNX Runtime | **28–34 ms** | ~180 MB |
| **GPU Fast Path** | TileLang Fused Kernels (`laya/fast.py`) | **6–8 ms** | ~450 MB VRAM |
| **Edge Embedded** | Quantized ONNX (INT8) | **14–19 ms** | ~65 MB |
| **Browser / Web Dashboard** | `laya-ts` (WASM / WebGL) | **22–27 ms** | In-browser sandbox |

---

## 📂 Subsystem Structure

```
AI-MODULE/
├── laya/                        # Core Python Engine
│   ├── agent.py                 # Decision model encoder and forward pass
│   ├── router.py                # Semantic question router and shortlisting
│   ├── confidence.py            # Platt scaling and temperature calibration
│   ├── hooks.py                 # Interceptors for telemetry logging & hardware events
│   ├── fast.py                  # TileLang fused GPU kernel bindings
│   ├── tl_kernels.py            # Fused attention and reduction kernels
│   ├── onnx_agent.py            # ONNX Runtime inference wrapper
│   ├── structured.py            # Pydantic schema validation & typed outputs
│   ├── serve.py                 # FastAPI REST serving endpoints
│   ├── cli.py                   # CLI benchmarking and evaluation tool
│   ├── mcp/                     # Model Context Protocol implementation
│   │   ├── server.py            # Stdio & SSE MCP server
│   │   ├── tools.py             # Tool definitions (decision, telemetry, hardware)
│   │   └── device.py            # Device abstraction and state tracking
│   └── integrations/            # Connectors for LangChain, LlamaIndex, CrewAI
├── laya-ts/                     # TypeScript client & ONNX browser bindings
├── tests/                       # Comprehensive unit and integration test suite
├── research/                    # Benchmarks, dataset generators, and evaluation logs
├── docker/                      # Multi-stage Docker build files
└── pyproject.toml               # Python package configuration
```

---

## 🛠️ Setup & Usage

### 1. Installation
From the root of this repository:

```bash
cd AI-MODULE

# Core dependencies
pip install -e .

# Full installation with MCP server, FastAPI serve, and ONNX Runtime
pip install -e ".[mcp,serve,onnx]"
```

### 2. Run Verification Tests
```bash
# Run unit tests across decision routing and calibration
pytest tests/test_router.py tests/test_criteria.py tests/test_confidence.py
```

### 3. Launch the MCP Robotics Server
```bash
laya-mcp-server
```

### 4. Launch the REST API Server
```bash
laya-serve --host 0.0.0.0 --port 8000
```
