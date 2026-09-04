# 🛡️ UNFRAGMENT ARCHITECTURE & WORKFLOW SUITE
## Secure Multi-Vendor DVR/NVR Forensic Analysis Platform
### AI-Augmented Acquisition, Recovery, Verification & Analysis of Surveillance Evidence

---

```
  ██╗   ██╗███╗   ██╗███████╗██████╗  █████╗  ██████╗ ███╗   ███╗███████╗███╗   ██╗████████╗
  ██║   ██║████╗  ██║██╔════╝██╔══██╗██╔══██╗██╔════╝ ████╗ ████║██╔════╝████╗  ██║╚══██╔══╝
  ██║   ██║██╔██╗ ██║█████╗  ██████╔╝███████║██║  ███╗██╔████╔██║█████╗  ██╔██╗ ██║   ██║   
  ██║   ██║██║╚██╗██║██╔══╝  ██╔══██╗██╔══██║██║   ██║██║╚██╔╝██║██╔══╝  ██║╚██╗██║   ██║   
  ╚██████╔╝██║ ╚████║██║     ██║  ██║██║  ██║╚██████╔╝██║ ╚═╝ ██║███████╗██║ ╚████║   ██║   
   ╚═════╝ ╚═╝  ╚═══╝╚═╝     ╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚═╝     ╚═╝╚══════╝╚═╝  ╚═══╝   ╚═╝   
   ===================================================================================
     SECURE FORENSIC ENGINEERING ARCHITECTURE • LAW ENFORCEMENT & JUDICIAL INTEGRITY
```

---

## 🧭 Welcome to the UNFRAGMENT Documentation Suite
Welcome to the modular High-Level Architecture, System Design, and Workflow Specification for **UNFRAGMENT**. 

This documentation is divided into **thematic, standalone topic guides**. Every guide has been engineered with a **dual-track presentation**:
1. **For Non-Technical Stakeholders & Evaluators:** Clear visual diagrams, ASCII schematics, real-world analogies, and plain-English overviews explaining *what we are building and why it matters*.
2. **For Forensic Engineers & Developers:** Precise technical specifications, byte patterns, NAL unit carving heuristics, cryptographic formulas, and legal compliance benchmarks (**ISO/IEC 27037**, **NIST SP 800-86**, **FRE 901/902**, and **BSA 2023 Section 63**).

---

## ⚡ UNFRAGMENT in 60 Seconds: What Are We Building?

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                              THE 60-SECOND SUMMARY                                     │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ 1. THE PROBLEM: Commercial CCTV units (Hikvision, Dahua, CP Plus) save video on hard   │
│    drives using weird, secret formats instead of normal files. When police seize a     │
│    drive, Windows says "Drive is unformatted. Format now?" (Accidental click = ruined  │
│    evidence). Traditional recovery tools output garbled, unplayable video.             │
│                                                                                        │
│ 2. OUR SOLUTION: UNFRAGMENT connects via hardware write-blockers (100% read-only),     │
│    cracks open proprietary DVR filesystems, glues fragmented video back together into  │
│    crystal-clear MP4 video, and syncs cameras to the same clock.                       │
│                                                                                        │
│ 3. THE AI REVOLUTION: A smart, local AI assistant flags people, cars, and suspects     │
│    across hundreds of hours of video in minutes — without modifying original evidence. │
│                                                                                        │
│ 4. THE COURTROOM PROOF: An unhackable cryptographic Merkle Tree (digital wax seal)     │
│    proves in court that nobody tampered with a single pixel.                           │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📚 Topic Documentation Sitemap

Navigate directly to any topic below:

| # | Topic Document | Core Focus | Key Visual Included |
| :-: | :--- | :--- | :--- |
| **01** | [**01 — Executive Summary & Problem**](./01_EXECUTIVE_SUMMARY_AND_PROBLEM.md) | The 4-part CCTV forensics crisis and measurable platform objectives | *Normal PC HDD vs CCTV Continuous Ring Buffer* |
| **02** | [**02 — System Context & Ecosystem**](./02_SYSTEM_CONTEXT_AND_ECOSYSTEM.md) | Crime scene seizure to courtroom submission pipeline & external actors | *Crime-Scene-to-Courtroom Storyboard* |
| **03** | [**03 — Four-Layer Architecture**](./03_FOUR_LAYER_ARCHITECTURE.md) | Strict four-tier separation of concerns across forensic disciplines | *The 4-Floor Forensic Skyscraper* |
| **04** | [**04 — Components & Plugin Engine**](./04_COMPONENTS_AND_PLUGIN_ENGINE.md) | 13-microservice pipeline, reverse engineering, Hikvision & Dahua stubs | *The Universal DVR Adapter Concept* |
| **05** | [**05 — Workflow & Evidence Data Flow**](./05_WORKFLOW_AND_EVIDENCE_DATA_FLOW.md) | 16-stage end-to-end investigation & strict Class A/B/C/D segregation | *The Glass Vault Evidence Isolation Model* |
| **06** | [**06 — AI Integration & Human Oversight**](./06_AI_INTEGRATION_AND_HUMAN_OVERSIGHT.md) | On-premises computer vision triage & the Confirm/Edit/Reject review gate | *Detective's UI Mockup with Action Buttons* |
| **07** | [**07 — Integrity, Security & Chain of Custody**](./07_INTEGRITY_SECURITY_AND_CHAIN_OF_CUSTODY.md) | Dual SHA-256/MD5 hashing, Merkle audit trees, and legal compliance | *The Digital Tamper-Proof Wax Seal* |
| **08** | [**08 — Deployment, Hardware & Tech Stack**](./08_DEPLOYMENT_HARDWARE_AND_TECH_STACK.md) | Air-gapped workstation hardware setup, sequence call flow & stack | *Forensic Laboratory Workstation Blueprint* |
| **09** | [**09 — Operations Matrix & Failure Handling**](./09_OPERATIONS_MATRIX_AND_FAILURE_HANDLING.md) | Subsystem responsibilities, data tiers, corrupted drive recovery | *What Happens When a Disk is Damaged?* |
| **10** | [**10 — Future Roadmap & Architecture Summary**](./10_FUTURE_ROADMAP_AND_SUMMARY.md) | Future vendor & AI plugins, hackathon scope disclosure, final synthesis | *Modular Extensibility Blueprint* |

---

## 🎯 Recommended Reading Pathways

### 👔 Track A: For Judges, Police Officers, Faculty & Non-Tech Evaluators
If you want to understand what the system does without getting bogged down in low-level code:
1. Start with [**01 — Executive Summary & Problem**](./01_EXECUTIVE_SUMMARY_AND_PROBLEM.md) to understand why police can't view CCTV easily.
2. Read [**02 — System Context & Ecosystem**](./02_SYSTEM_CONTEXT_AND_ECOSYSTEM.md) to see how evidence flows from a crime scene to court.
3. Check [**06 — AI Integration & Human Oversight**](./06_AI_INTEGRATION_AND_HUMAN_OVERSIGHT.md) to see why our AI is legally safe and never replaces human detectives.
4. Review [**07 — Integrity, Security & Chain of Custody**](./07_INTEGRITY_SECURITY_AND_CHAIN_OF_CUSTODY.md) to see the tamper-proof mathematical proof.

### 💻 Track B: For Digital Forensics Architects & Software Engineers
If you are evaluating the technical design, reverse-engineering architecture, or algorithms:
1. Inspect [**03 — Four-Layer Architecture**](./03_FOUR_LAYER_ARCHITECTURE.md) and [**04 — Components & Plugin Engine**](./04_COMPONENTS_AND_PLUGIN_ENGINE.md).
2. Deep-dive into [**05 — Workflow & Evidence Data Flow**](./05_WORKFLOW_AND_EVIDENCE_DATA_FLOW.md) and [**08 — Deployment, Hardware & Tech Stack**](./08_DEPLOYMENT_HARDWARE_AND_TECH_STACK.md).
3. Review edge-case recovery in [**09 — Operations Matrix & Failure Handling**](./09_OPERATIONS_MATRIX_AND_FAILURE_HANDLING.md).
