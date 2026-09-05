# 08 — Deployment, Hardware & Tech Stack
## Laboratory Topology, Component Interaction Sequence & Engineering Stack

---

## 💡 In Plain English / For Non-Tech Readers: The Offline Forensic Lab

In spy movies, high-tech labs are always connected to satellites and the cloud. In real forensic science, **that is illegal**. 

If an evidence computer is connected to the internet:
* A hacker could delete the video remotely.
* Cloud services might upload confidential crime footage to external servers.
* Defense lawyers will ask: *"Did someone hack into your system while you were working?"*

UNFRAGMENT operates in an **Air-Gapped Workstation** (completely disconnected from Wi-Fi, Ethernet, and Bluetooth).

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                     THE PHYSICAL FORENSIC WORKSTATION SETUP                            │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ DUAL FORENSIC MONITORS                                                           │  │
│  │ ┌──────────────────────────────────────┐  ┌───────────────────────────────────┐  │  │
│  │ │ [16-Channel Video Player Canvas]     │  │ [Timeline Scrubber & AI Triage]   │  │  │
│  │ │ Cam 1, Cam 2, Cam 3... In lockstep!  │  │ Click timestamp to jump video!    │  │  │
│  │ └──────────────────────────────────────┘  └───────────────────────────────────┘  │  │
│  └────────────────────────────────────────┬─────────────────────────────────────────┘  │
│                                           │ DisplayPort                                │
│                                           ▼                                            │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ HIGH-PERFORMANCE FORENSIC WORKSTATION                                            │  │
│  │ • 16-Core CPU (Deep Frame Carving)                                               │  │
│  │ • 64 GB ECC RAM (High-Speed Memory Buffers)                                      │  │
│  │ • NVIDIA RTX GPU (Local AI Vision Inference)                                     │  │
│  │ • 4 TB NVMe Scratch Drive (Super-fast temporary video storage)                   │  │
│  │ • 🚫 NETWORK CARD DISABLED (AIR-GAPPED - ZERO CLOUD TELEMETRY!)                  │  │
│  └────────────────────────────────────────▲─────────────────────────────────────────┘  │
│                                           │ USB 3.0 / PCIe                             │
│                                           │ Direct Read-Only Channel                   │
│                                           ▼                                            │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ PHYSICAL HARDWARE WRITE BLOCKER (Tableau / CRU Bridge)                           │  │
│  │ [RED LIGHT: WRITE COMMANDS BLOCKED]   [GREEN LIGHT: POWER ON / READ-ONLY OK]     │  │
│  └────────────────────────────────────────▲─────────────────────────────────────────┘  │
│                                           │ SATA / SAS / NVMe Cable                    │
│                                           ▼                                            │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ SEIZED CRIME SCENE EVIDENCE (Proprietary Hikvision / Dahua Surveillance Drive)   │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🖥️ Air-Gapped Deployment Architecture (Mermaid)

```mermaid
flowchart TD
    %% Styling Classes
    classDef nodeWorkstation fill:#0f172a,stroke:#38bdf8,stroke-width:3px,color:#f8fafc;
    classDef nodeStorage fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;
    classDef nodeGPU fill:#0f172a,stroke:#a855f7,stroke-width:2px,color:#f8fafc;
    classDef nodeOutput fill:#0f172a,stroke:#f59e0b,stroke-width:2px,color:#f8fafc;

    subgraph AIRGAP_BOUNDARY ["AIR-GAPPED FORENSIC LABORATORY ENVIRONMENT (ZERO EXTERNAL NETWORK)"]
        direction TB

        subgraph STORAGE_TIER ["1. EVIDENCE STORAGE SUBSYSTEM"]
            EVD_HDD["Physical DVR HDD / SSD<br/>(Seized Media)"]:::nodeStorage
            EVD_IMG["Forensic Image Repository<br/>(.RAW / .E01 / .AFF4)"]:::nodeStorage
            EVD_REPO["Working Evidence Repository<br/>(Scratch Fast NVMe Storage)"]:::nodeStorage
        end

        subgraph WORKSTATION ["2. FORENSIC WORKSTATION (HOST NODE)"]
            UI_CORE["UNFRAGMENT Examiner UI<br/>(React / Electron / PyQt GUI)"]:::nodeWorkstation
            ENG_ANALYSIS["Analysis & Carving Engine<br/>(C++ / Rust / Python)"]:::nodeWorkstation
            ENG_AUDIT["Audit & Merkle Engine<br/>(OpenSSL / SQLite Ledger)"]:::nodeWorkstation
        end

        subgraph ACCEL_TIER ["3. HARDWARE ACCELERATION (OPTIONAL)"]
            GPU_NODE["NVIDIA CUDA GPU<br/>• Parallel Frame Carving<br/>• YOLOv8 CV Inference<br/>• Local LLM Narrative Engine"]:::nodeGPU
        end

        subgraph OUTPUT_TIER ["4. EXPORT & REPORT SUBSYSTEM"]
            OUT_VID["Recovered Normalized Video<br/>(.MP4 / .MKV Playable Footage)"]:::nodeOutput
            OUT_META["Evidence Metadata Manifest<br/>(JSON-LD / Sector Trace Map)"]:::nodeOutput
            OUT_LOG["Merkle-Chained Audit Logs<br/>(Cryptographic Proof Files)"]:::nodeOutput
            OUT_PDF["Court-Ready Forensic Report<br/>(Digitally Signed PDF Document)"]:::nodeOutput
        end
    end

    EVD_HDD ==>|"Hardware Write Blocker (Tableau / CRU)"| ENG_ANALYSIS
    EVD_IMG -->|"Direct Block Ingestion"| ENG_ANALYSIS
    ENG_ANALYSIS -->|"Staged Carved Slices"| EVD_REPO

    ENG_ANALYSIS <==>|"PCIe Gen4 x16 Direct Memory Access"| GPU_NODE
    ENG_ANALYSIS -->|"Verified Streams & Annotations"| UI_CORE
    UI_CORE -->|"Examiner Adjudications"| ENG_AUDIT

    ENG_ANALYSIS --> OUT_VID
    ENG_ANALYSIS --> OUT_META
    ENG_AUDIT --> OUT_LOG
    ENG_AUDIT --> OUT_PDF

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

## ⏱️ UML Sequence Diagram (System Call Flow)

```mermaid
sequenceDiagram
    autonumber
    actor Examiner as Forensic Examiner
    participant Source as Evidence Source
    participant AcqEng as Acquisition Engine
    participant HashEng as Hash Engine
    participant Classifier as Vendor Classifier
    participant Parser as Parser Engine
    participant RecEng as Recovery Engine
    participant AIEng as AI Engine
    participant AuditEng as Audit Engine
    participant RepEng as Report Engine

    Note over Examiner,RepEng: PHASE I: INGESTION & INTEGRITY BASELINE
    Examiner ->> Source: 1. Select and mount physical evidence (Write-Blocked)
    Source -->> AcqEng: 2. Transmit raw bitstream via zero-write DMA
    AcqEng ->> HashEng: 3. Stream sectors for cryptographic hashing
    HashEng -->> AcqEng: 4. Return dual hashes (SHA-256 & MD5)
    AcqEng ->> AuditEng: 5. Record acquisition baseline & source hashes

    Note over Examiner,RepEng: PHASE II: CLASSIFICATION & PARSING
    AcqEng ->> Classifier: 6. Transmit master boot sectors (LBA 0..64)
    Classifier -->> Parser: 7. Deliver identified profile (e.g., Hikvision HIK / HKMI)
    Parser ->> Source: 8. Read Master Sector Maps & Allocation Tables
    Parser ->> RecEng: 9. Dispatch partition chunk bounds & channel descriptors

    Note over Examiner,RepEng: PHASE III: DEEP CARVING & TIME SYNC
    RecEng ->> RecEng: 10. Scan sectors, carve NAL units & assemble GOPs
    RecEng ->> RecEng: 11. Extract embedded RTC timestamps & demux channels
    RecEng ->> AIEng: 12. Supply normalized, playable derivative MP4 stream

    Note over Examiner,RepEng: PHASE IV: AI TRIAGE & EXAMINER ADJUDICATION
    AIEng ->> AIEng: 13. Execute CV object detection & draft timeline narrative
    AIEng -->> Examiner: 14. Present flagged candidate events in Triage UI
    Examiner ->> AuditEng: 15. Submit adjudication (Confirm / Edit / Reject)

    Note over Examiner,RepEng: PHASE V: ATTESTATION & REPORT GENERATION
    AuditEng ->> AuditEng: 16. Compile verified transactions into Merkle Tree
    AuditEng ->> RepEng: 17. Dispatch verified case record & Merkle root proof
    RepEng ->> RepEng: 18. Generate digitally signed ISO 27037 / BSA Sec 63 report
    RepEng -->> Examiner: 19. Deliver certified court-ready forensic report PDF
```

---

## 🛠️ Technology Stack Architecture (Mermaid)

```mermaid
flowchart TD
    %% Styling Classes
    classDef stackUI fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;
    classDef stackAI fill:#0f172a,stroke:#a855f7,stroke-width:2px,color:#f8fafc;
    classDef stackCore fill:#0f172a,stroke:#06b6d4,stroke-width:2px,color:#f8fafc;
    classDef stackSec fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;
    classDef stackAcq fill:#0f172a,stroke:#f59e0b,stroke-width:2px,color:#f8fafc;

    subgraph STACK_UI ["USER INTERFACE & PRESENTATION TIER"]
        UI_FE["Modern Desktop / Web GUI<br/>(React 18 / TypeScript / Tailwind CSS / Electron)"]:::stackUI
        UI_VID["Multi-Channel Video Canvas<br/>(Video.js / HTML5 WebGL Hardware Canvas)"]:::stackUI
        UI_HEX["Forensic Hex & Sector Inspector<br/>(Custom High-Performance Virtualized Grid)"]:::stackUI
    end

    subgraph STACK_AI ["AI & COMPUTER VISION TIER"]
        AI_CV["Computer Vision Triage Engine<br/>(PyTorch / ONNX Runtime / YOLOv8-nano)"]:::stackAI
        AI_ML["Vendor / Codec ML Classifier<br/>(Scikit-learn / LightGBM Byte-Entropy Model)"]:::stackAI
        AI_LLM["Assisted Report Drafting<br/>(Local Quantized Llama-3-8B via llama.cpp)"]:::stackAI
    end

    subgraph STACK_CORE ["PARSING & RECONSTRUCTION TIER"]
        CORE_RUST["High-Performance Carving Engine<br/>(Rust / C++20 Memory-Safe Binary Parsers)"]:::stackCore
        CORE_PY["Format Discovery & Prototyping<br/>(Python 3.12 / Kaitai Struct / Construct)"]:::stackCore
        CORE_FF["Stream Remuxing & Video Transcoding<br/>(FFmpeg 6.x / libavcodec / libavformat)"]:::stackCore
    end

    subgraph STACK_SEC ["SECURITY & CRYPTOGRAPHIC TIER"]
        SEC_HASH["Dual Hashing & Verification<br/>(OpenSSL 3.x / Crypto++ SHA-256 & MD5)"]:::stackSec
        SEC_MERKLE["Tamper-Evident Event Ledger<br/>(Custom RFC 6962 Compliant Merkle Tree)"]:::stackSec
        SEC_SIG["Digital Signatures & Legal Packaging<br/>(libsodium Ed25519 / ReportLab PDF Engine)"]:::stackSec
    end

    subgraph STACK_ACQ ["ACQUISITION & LOW-LEVEL I/O TIER"]
        ACQ_RAW["Direct Sector Block I/O<br/>(Direct Win32 CreateFile / Linux O_DIRECT)"]:::stackAcq
        ACQ_IMG["Forensic Image Container Support<br/>(libewf for .E01 / libaff4 for .AFF4)"]:::stackAcq
        ACQ_HW["Hardware Write-Blocker Interface<br/>(Tableau / CRU / USB3-to-SATA Firmware Lock)"]:::stackAcq
    end

    STACK_UI --> STACK_AI
    STACK_AI --> STACK_CORE
    STACK_CORE --> STACK_SEC
    STACK_SEC --> STACK_ACQ

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

[← Previous: 07 — Integrity & Security](./07_INTEGRITY_SECURITY_AND_CHAIN_OF_CUSTODY.md) | [Master Index](./README.md) | [Next: 09 — Operations Matrix & Failure Handling →](./09_OPERATIONS_MATRIX_AND_FAILURE_HANDLING.md)
