<h1 align="center">Namaste 🙏, I'm Rohit Gupta</h1>
<h3 align="center">Embedded Firmware & Hardware Security · C/C++ on MCUs · Real-Time Signal Pipelines · Python Test Automation</h3>

<p align="center">
  <a href="https://linkedin.com/in/rohit-gupta126"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="mailto:rjgupta2116@gmail.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white" /></a>
  <a href="https://github.com/Rohit-Gupta-126"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" /></a>
  <img src="https://komarev.com/ghpvc/?username=Rohit-Gupta-126&label=Profile%20views&color=0055FF&style=for-the-badge" />
</p>

<hr>

<table>
<tr>
<td width="62%" valign="top">

### About Me

I work at the layer where **analog physics becomes digital code** — projects that start at a diode
and end at an API: design the front end, fabricate the board, write the register-level firmware,
then build the Python harness that proves the numbers are real.

- 🔧 **Bare-metal C/C++ on 32-bit MCUs** — ADC/DMA capture paths, FreeRTOS task design, I²C/UART drivers, custom binary wire protocols.
- 📡 **Real-time signal pipelines** — microsecond-budget scoring, fixed-bandwidth decision layers, hardware telemetry health monitoring.
- 🔬 **Measurement discipline** — every performance claim is tied to a measurement at that exact configuration. If it isn't measured, it isn't a result.
- 🔐 **Hardware security** — TRNG design, NIST SP 800-90B entropy assessment, post-quantum cryptography.
- 🎓 B.Tech Electronics & Communication, **SOA University (2027)** · Research Intern, **Samsung R&D Institute**

</td>
<td width="38%" valign="top">

### Currently

**Z.E.K v2** — running the SP 800-90B measurement
campaign across three ADC sample rates to replace
provisional firmware defaults with measured ones.

**PHAROS** — closing the simulation-to-real domain
gap on CMS Open Data.

**Learning** — ARM Cortex-M bring-up (STM32 HAL and
register level), Layer 1/2 networking internals,
CI/CD for embedded build pipelines.

**Open to** firmware, embedded systems, and
hardware security roles. If you build close to the
silicon, I'd like to hear about it.

</td>
</tr>
</table>

<hr>

<h2>⚡ Z.E.K — ZenTropy Key</h2>
<p><em>Hardware True Random Number Generator · Entropy-as-a-Service</em><br>
<code>C/C++</code> <code>ESP32</code> <code>FreeRTOS</code> <code>ADC-DMA</code> <code>PCB Design</code> <code>mbedTLS</code> <code>FastAPI</code> <code>Redis</code></p>

<p>Randomness sourced from physics, not arithmetic. A reverse-biased Zener diode is driven into
<strong>avalanche breakdown</strong>; the broadband noise is amplified, sampled, conditioned in firmware,
and served as destroy-on-read entropy tokens. I designed the analog chain, laid out the board, and
wrote every line of firmware and service code.</p>

<table>
<tr><td width="50%" valign="top">

**Analog chain & measured performance**

| Stage | Part |
|---|---|
| Boost rail | MT3608 → ~14 V |
| Entropy source | 1N4742A Zener, avalanche breakdown |
| Bias / DC block | 10k/10k virtual ground · 100 nF |
| Gain | LM358 @ ~1000× |
| Capture | ESP32 ADC1_CH6, 12-bit, 11 dB atten. |

| Metric | Value |
|---|---|
| Shannon entropy | 7.9998 bits/byte |
| Min-entropy (H<sub>min</sub>) | 6.415 bits/byte |
| Standard | NIST SP 800-90B / SP 800-22 |
| v1 baseline latency | ~830 ms per 4096-bit key |

</td><td width="50%" valign="top">

**Firmware v2 — engineering highlights**

| Technique | Implementation |
|---|---|
| DMA capture | ADC continuous mode at 83 kS/s replaces a 50 µs busy-wait — CPU free during conversion |
| RTOS design | Harvester task pinned to core 0 keeps a live pool charged; core 1 serves requests |
| Bit extraction | Configurable N-LSB folding (4 bits/sample) instead of discarding 11 of 12 bits |
| Conditioning | 512-byte sponge + counter-appended SHA-256 on the ESP32 hardware SHA engine |
| Dual mode | `MODE_FULL_PHYSICAL` (raw-backed) and `MODE_90C_EXPANDED` (physical seed → CTR-DRBG) |
| Wire protocol | Binary framing @ 921600 baud, runtime-tunable (`MODE`, `OSR`, `LSB`, `RATE`, `STAT`) |
| Test hook | `RAW <count>` streams 12-bit samples into NIST `ea_non_iid` — assessment cut from hours to minutes |
| Noise hygiene | Radios off during harvest (RF modulates the ADC reference); I²C at 400 kHz |

</td></tr>
</table>

<blockquote>
<strong>What I am not claiming.</strong> v2 targets ~25 ms by design arithmetic, but that figure stays
unpublished until <code>ea_non_iid</code> confirms per-sample min-entropy at 20 / 40 / 83 kS/s. The
LM358 sits at roughly <strong>1 kHz closed-loop bandwidth</strong> at 1000× single-stage gain, so samples
taken faster than that may be correlated — a real analog constraint. The measurement sets the spec.
</blockquote>

<p><strong>Service layer:</strong> FastAPI + Redis (atomic <code>LPOP</code> = destroyed on serve) + Postgres,
Argon2id-hardened hardware-seeded API keys, AES-256-GCM WebSocket telemetry with 10-minute key
rotation, Next.js dashboard and admin console.</p>

<hr>

<h2>🔭 PHAROS — Real-Time Anomaly Recognition in Online Streams</h2>
<p><em>Streaming trigger/DAQ pipeline for physics events and accelerator hardware telemetry</em><br>
<code>Python</code> <code>Kafka/Redpanda</code> <code>PyTorch</code> <code>ONNX Runtime</code> <code>ROOT</code> <code>hls4ml</code> <code>Docker</code> <code>Makefile</code></p>

<p>One pipeline, two live streams, one problem: <strong>learn what normal looks like, then notice in real
time when something isn't</strong> — no labels, hard latency budget. Stream A scores particle-collision
events the way an LHC trigger would; Stream B watches real electrical waveforms from accelerator
power hardware and flags equipment that has stopped looking healthy. Public data, one laptop.</p>

<table>
<tr><td width="50%" valign="top">

**Pipeline**

| Stage | Implementation |
|---|---|
| Ingest | Datasets replayed as rate-controlled streams onto one Redpanda backbone; sim and real detector data share a wire format |
| Score | Autoencoder at **µs/event**, ONNX Runtime, PyTorch-vs-ORT parity as a build target |
| Decide | Fixed-budget top-N per window, not a static threshold — a burst can't blow the recording budget |
| Monitor | PSI/KS drift against frozen references, with alerting |
| Retrain | Closed loop on confirmed drift; new model gated behind a correctness check vs the incumbent |
| Operate | Live dashboard: throughput, keep/discard ratio, score distributions, drift alerts |

</td><td width="50%" valign="top">

**Results, including the ones that don't flatter**

| Finding | Measurement |
|---|---|
| Data reduction | **115×**, top-scoring events retained |
| Detector accuracy | AUC **0.889** vs 0.775 baseline |
| FPGA fixed-point | `ap_fixed<24,8>` for **100%** decision agreement; `<16,6>` gave **91%** |
| Sim-to-real gap | **KS = 0.80** vs real CMS Open Data — measured, not fixed |
| Hardware stream | Statistical baseline **beat** the deep model (0.805 vs 0.711); cause: preprocessing smoothed away the sharp fault transients |
| Drift monitor | **Negative result:** benign calibration skew not separable from genuine shift |

**Engineering practice** — multi-phase `Makefile` (`make phase0` → `phase4`), Docker Compose
orchestration, pytest smoke suite, single-command reproducible setup, and a design log recording
the decisions that failed alongside the ones that worked.

</td></tr>
</table>

<hr>

<h2>📡 A.R.E.S — Autonomous Radio Evasion System</h2>
<p><em>Hybrid AI–cryptographic anti-jamming across a 32-channel spectrum</em><br>
<code>Python</code> <code>GRU/DQN</code> <code>Next.js</code></p>

<p>Sequence models predict which channels are about to be jammed (<strong>98.5%</strong> prediction accuracy,
<strong>97.2%</strong> packet delivery ratio); Z.E.K then supplies the hardware entropy that picks the final
channel from the AI-ranked safe set, so the hop pattern carries no algorithmic structure to learn.
<strong>0.52 ms</strong> end-to-end selection. The hardware project and the signal-layer project feed each other.</p>

<hr>

<h2>🛠️ Capability Map</h2>

<table>
<tr><td valign="top" width="50%">

**Embedded & Firmware**
```
C · C++ · bare-metal register configuration
FreeRTOS · dual-core task pinning · ISR discipline
ADC continuous/DMA · I²C · UART · SPI
Custom binary wire protocols · runtime tuning
PCB design & fabrication · hardware bring-up
Oscilloscope / logic-analyser debug
Analog front ends · gain-bandwidth analysis
EMI & noise-coupling mitigation
```

**Test Automation & Build**
```
Python: pyserial, pandas, NumPy
Automated validation harnesses & fixed seeds
NIST SP 800-90B (ea_non_iid) · SP 800-22
Makefiles · Docker & Compose · pytest
Linux / WSL2 · shell scripting · Git
```

</td><td valign="top" width="50%">

**Signals & Systems**
```
DSP · communication systems · spectrum analysis
Probability & statistics · KS / PSI testing
Real-time latency budgeting
Streaming telemetry & health monitoring
ONNX / ONNX Runtime · hls4ml (FPGA estimation)
```

**Security & Cryptography**
```
TRNG design · entropy assessment
mbedTLS · SHA-256 · CTR-DRBG · Argon2id
AES-256-GCM · post-quantum cryptography
Zero-trust key lifecycle · forward secrecy
```

**Software**
```
FastAPI · Redis · Postgres · Kafka
PyTorch · MATLAB · Next.js · React
```

</td></tr>
</table>

<hr>

<h2>🏅 Leadership & Certifications</h2>

<ul>
<li><strong>Campus Lead</strong>, Coding Ninjas 10X on Campus, SOA University — ran programs for <strong>1,000+</strong> students; co-founded <em>Nexus</em>, a student-led open-source group, defining contributor onboarding and release process.</li>
<li><strong>Team Lead</strong>, Smart India Hackathon 2025 — split a real-time transit build across backend, ML and frontend tracks for a six-member team; owned integration and final testing to deadline.</li>
<li><strong>Project Management Foundations</strong>, LinkedIn Learning (PMI) · <strong>NCC 'A' Certificate</strong>, Unit 37 Jharkhand Battalion</li>
</ul>

<hr>

<h2>📊 GitHub</h2>

<p align="center">
  <img height="165" src="https://github-readme-stats.vercel.app/api?username=Rohit-Gupta-126&show_icons=true&locale=en&theme=tokyonight&hide_border=true&hide=issues" alt="stats" />
  <img height="165" src="https://streak-stats.demolab.com/?user=Rohit-Gupta-126&theme=tokyonight&hide_border=true" alt="streak" />
</p>
<p align="center">
  <img height="165" src="https://github-readme-stats.vercel.app/api/top-langs/?username=Rohit-Gupta-126&layout=compact&theme=tokyonight&hide_border=true&langs_count=8" alt="languages" />
</p>

<p align="center"><em>Come curious. Build for real.</em></p>
