<h1 align="center">Namaste 🙏, I'm Rohit Gupta</h1>

<p align="center">
  <em>I work at the layer where <b>analog physics becomes digital code</b>.</em><br>
  <em>My projects start at a diode and end at an API.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Embedded_Firmware-0055FF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Hardware_Security-6E00FF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Real--Time_Signals-00A3FF?style=for-the-badge" />
</p>

<p align="center">
  <a href="https://linkedin.com/in/rohit-gupta126"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="mailto:rjgupta2116@gmail.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white" /></a>
  <a href="https://github.com/Rohit-Gupta-126"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" /></a>
  <img src="https://komarev.com/ghpvc/?username=Rohit-Gupta-126&label=Views&color=0055FF&style=for-the-badge" />
</p>

---

<table align="center">
<tr>
<td align="center" width="25%"><h3>🔧</h3><b>Bare-metal C/C++</b><br><sub>ADC/DMA · FreeRTOS<br>I²C · UART · SPI</sub></td>
<td align="center" width="25%"><h3>📡</h3><b>Real-time pipelines</b><br><sub>µs scoring budgets<br>streaming telemetry</sub></td>
<td align="center" width="25%"><h3>🔐</h3><b>Hardware security</b><br><sub>TRNG · SP 800-90B<br>post-quantum crypto</sub></td>
<td align="center" width="25%"><h3>🔬</h3><b>Measured, not claimed</b><br><sub>if it isn't measured,<br>it isn't a result</sub></td>
</tr>
</table>

<p align="center">
🎓 B.Tech ECE, <b>SOA University 2027</b> &nbsp;·&nbsp; 🏢 Research Intern, <b>Samsung R&D Institute</b><br>
<sub>Currently: SP 800-90B campaign on Z.E.K v2 · closing the sim-to-real gap on PHAROS · ARM Cortex-M bring-up</sub>
</p>

---

<h2 align="center">⚡ Z.E.K — ZenTropy Key</h2>
<p align="center"><b>Randomness sourced from physics, not arithmetic.</b><br>
<sub>A Zener diode in avalanche breakdown → amplified → sampled → conditioned in firmware → served as destroy-on-read tokens.<br>
I designed the analog chain, laid out the board, and wrote every line of firmware and service code.</sub></p>

<p align="center">
  <img src="https://img.shields.io/badge/C%2FC%2B%2B-00599C?style=flat-square&logo=c%2B%2B&logoColor=white" />
  <img src="https://img.shields.io/badge/ESP32-E7352C?style=flat-square&logo=espressif&logoColor=white" />
  <img src="https://img.shields.io/badge/FreeRTOS-2ea44f?style=flat-square" />
  <img src="https://img.shields.io/badge/ADC--DMA-555?style=flat-square" />
  <img src="https://img.shields.io/badge/PCB_Design-teal?style=flat-square" />
  <img src="https://img.shields.io/badge/mbedTLS-9cf?style=flat-square" />
  <img src="https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white" />
</p>

```mermaid
flowchart LR
    A["⚡ 14 V rail<br/>MT3608"] --> B["🎲 Zener 1N4742A<br/>avalanche noise"]
    B --> C["🔊 LM358<br/>gain 1000x"]
    C --> D["📈 ESP32 ADC<br/>12-bit at 83 kS/s<br/>continuous DMA"]
    D --> E["🧮 LSB fold<br/>4 bits per sample"]
    E --> F["🔒 Sponge + SHA-256<br/>hardware engine"]
    F --> G["🔑 Entropy tokens<br/>destroyed on read"]

    style B fill:#6E00FF,color:#fff,stroke:none
    style D fill:#0055FF,color:#fff,stroke:none
    style F fill:#00A3FF,color:#fff,stroke:none
    style G fill:#2ea44f,color:#fff,stroke:none
```

<p align="center">
  <img src="https://img.shields.io/badge/Shannon_entropy-7.9998_bits%2Fbyte-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Min--entropy-6.415_bits%2Fbyte-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/NIST-SP_800--90B_%2F_22-0055FF?style=for-the-badge" />
</p>

> **What I am not claiming.** v2 targets ~25 ms per 4096-bit key by design arithmetic — but that number stays unpublished until `ea_non_iid` confirms min-entropy at 20 / 40 / 83 kS/s. The LM358 gives roughly **1 kHz closed-loop bandwidth** at 1000× gain, so faster samples may be correlated. The measurement sets the spec, not the other way round.

<details>
<summary><b>🔍 Firmware v2 — engineering detail</b></summary>

<br>

| Technique | Implementation |
|---|---|
| **DMA capture** | ADC continuous mode at 83 kS/s replaces a 50 µs busy-wait — CPU free during conversion |
| **RTOS design** | Harvester task pinned to core 0 keeps a live pool charged; core 1 serves requests |
| **Bit extraction** | Configurable N-LSB folding instead of discarding 11 of every 12 converted bits |
| **Dual mode** | `MODE_FULL_PHYSICAL` (raw-backed) and `MODE_90C_EXPANDED` (physical seed → CTR-DRBG) |
| **Wire protocol** | Binary framing @ 921600 baud, runtime-tunable (`MODE`, `OSR`, `LSB`, `RATE`, `STAT`) |
| **Test hook** | `RAW <count>` streams samples into NIST `ea_non_iid` — assessment cut from hours to minutes |
| **Noise hygiene** | Radios off during harvest (RF modulates the ADC reference); I²C raised to 400 kHz |

**Service layer** — FastAPI + Redis (atomic `LPOP` = destroyed on serve) + Postgres, Argon2id-hardened hardware-seeded API keys, AES-256-GCM WebSocket telemetry with 10-minute key rotation, Next.js dashboard and admin console.

</details>

---

<h2 align="center">🔭 PHAROS — Real-Time Anomaly Recognition</h2>
<p align="center"><b>Learn what normal looks like, then notice in real time when something isn't.</b><br>
<sub>No labels, hard latency budget. Stream A scores collision events the way an LHC trigger would.<br>
Stream B watches real accelerator power-hardware waveforms for equipment that stopped looking healthy.<br>
Public data. One laptop.</sub></p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/Kafka_%2F_Redpanda-231F20?style=flat-square&logo=apachekafka" />
  <img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=flat-square&logo=pytorch&logoColor=white" />
  <img src="https://img.shields.io/badge/ONNX_Runtime-005CED?style=flat-square&logo=onnx&logoColor=white" />
  <img src="https://img.shields.io/badge/ROOT-CERN-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/hls4ml-FPGA-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white" />
</p>

```mermaid
flowchart LR
    S1["🌌 Collision events"] --> K
    S2["⚙️ Hardware telemetry"] --> K
    K["📨 Redpanda<br/>one wire format"] --> SC["🧠 Autoencoder<br/>ONNX Runtime<br/>microseconds per event"]
    SC --> DE{"⚖️ Fixed budget<br/>top-N per window"}
    DE -->|"1 in 115"| KEEP["💾 Recorded"]
    DE -->|"rest"| DROP["🗑️ Discarded"]
    SC --> MON["📊 PSI / KS drift"]
    MON --> RT["🔁 Retrain<br/>gated on beating<br/>the incumbent"]
    RT -.-> SC

    style K fill:#0055FF,color:#fff,stroke:none
    style SC fill:#6E00FF,color:#fff,stroke:none
    style DE fill:#00A3FF,color:#fff,stroke:none
    style KEEP fill:#2ea44f,color:#fff,stroke:none
```

<p align="center">
  <img src="https://img.shields.io/badge/Data_reduction-115x-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Detector_AUC-0.889_vs_0.775-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Sim--to--real_KS-0.80-critical?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Drift_monitor-negative_result-lightgrey?style=for-the-badge" />
</p>

<details>
<summary><b>🔍 The results that don't flatter</b></summary>

<br>

| Finding | Measurement |
|---|---|
| **FPGA fixed-point** | `ap_fixed<24,8>` needed for 100% trigger-decision agreement; `<16,6>` gave only 91% |
| **Sim-to-real gap** | KS = 0.80 against real CMS Open Data — measured, not fixed |
| **Hardware stream** | A statistical baseline *beat* the deep model (0.805 vs 0.711); root cause: preprocessing smoothed away the sharp transients the faults consist of |
| **Drift monitor** | Cannot separate benign calibration skew from genuine distribution shift |

**Engineering practice** — multi-phase `Makefile` (`make phase0` → `phase4`), Docker Compose orchestration, pytest smoke suite, single-command reproducible setup, and a design log recording the decisions that failed alongside the ones that worked.

</details>

---

<h2 align="center">📡 A.R.E.S — Autonomous Radio Evasion System</h2>
<p align="center"><b>AI predicts the jam. Physics picks the channel.</b><br>
<sub>Sequence models rank the safe channels; Z.E.K's hardware entropy makes the final pick —<br>
so the hop pattern carries no algorithmic structure for an adversary to learn.</sub></p>

<p align="center">
  <img src="https://img.shields.io/badge/Prediction_accuracy-98.5%25-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Packet_delivery-97.2%25-2ea44f?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Selection_time-0.52_ms-0055FF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Spectrum-32_channels-6E00FF?style=for-the-badge" />
</p>

---

<h2 align="center">🛠️ Capability Map</h2>

<table>
<tr>
<td valign="top" width="50%">

**⚙️ Embedded & Firmware**
```
C · C++ · bare-metal register config
FreeRTOS · core pinning · ISR discipline
ADC continuous/DMA · I²C · UART · SPI
Custom binary wire protocols
PCB design · fabrication · bring-up
Scope / logic-analyser debug
Analog front ends · GBW analysis
EMI & noise-coupling mitigation
```

**🧪 Test Automation & Build**
```
Python: pyserial, pandas, NumPy
Validation harnesses · fixed seeds
NIST SP 800-90B · SP 800-22
Makefiles · Docker Compose · pytest
Linux / WSL2 · shell · Git
```

</td>
<td valign="top" width="50%">

**📶 Signals & Systems**
```
DSP · comms · spectrum analysis
Probability & stats · KS / PSI
Real-time latency budgeting
Streaming telemetry & health
ONNX Runtime · hls4ml
```

**🔐 Security & Cryptography**
```
TRNG design · entropy assessment
mbedTLS · SHA-256 · CTR-DRBG
Argon2id · AES-256-GCM · PQC
Zero-trust keys · forward secrecy
```

**💻 Software**
```
FastAPI · Redis · Postgres · Kafka
PyTorch · MATLAB · Next.js · React
```

</td>
</tr>
</table>

---

<h2 align="center">🏅 Leadership</h2>

<p align="center">
<b>Campus Lead</b>, Coding Ninjas 10X — programs for <b>1,000+</b> students; co-founded <em>Nexus</em>, a student-led open-source group<br>
<b>Team Lead</b>, Smart India Hackathon 2025 — six-member team across backend/ML/frontend; owned integration and final testing<br>
<sub>Project Management Foundations (PMI, LinkedIn Learning) · NCC 'A' Certificate, Unit 37 Jharkhand Battalion</sub>
</p>

---

<p align="center">
  <img height="150" src="https://github-readme-stats.vercel.app/api?username=Rohit-Gupta-126&show_icons=true&locale=en&theme=tokyonight&hide_border=true&hide=issues" alt="stats" />
  <img height="150" src="https://streak-stats.demolab.com/?user=Rohit-Gupta-126&theme=tokyonight&hide_border=true" alt="streak" />
</p>
<p align="center">
  <img height="150" src="https://github-readme-stats.vercel.app/api/top-langs/?username=Rohit-Gupta-126&layout=compact&theme=tokyonight&hide_border=true&langs_count=8" alt="languages" />
</p>

<p align="center"><b>Come curious. Build for real.</b><br>
<sub>Open to firmware, embedded systems, and hardware security roles. If you build close to the silicon, I'd like to hear about it.</sub></p>
