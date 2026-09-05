# 09 — Operations Matrix & Failure Handling
## Subsystem Responsibilities, Data Tiers & Edge-Case Failure Protocols

---

## 💡 In Plain English / For Non-Tech Readers: What Happens When a Disk is Damaged?

In real police investigations, evidence is rarely pristine. Criminals deliberately:
* Pull the plug while the DVR is recording.
* Press "Factory Reset" or "Format Disk".
* Drop the recorder in water or damage the disk partition table.

UNFRAGMENT has an **Automated Safety Valve System**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                 WHAT HAPPENS WHEN EVIDENCE IS DAMAGED OR WIPED?                        │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│                      [UNFRAGMENT INGESTS SEIZED DISK]                                  │
│                                     │                                                  │
│                                     ▼                                                  │
│                       IS DISK HEADER / INDEX INTACT?                                   │
│                        /                         \                                     │
│                  YES  /                           \  NO (Wiped / Formatted / Corrupt) │
│                      ▼                             ▼                                   │
│            [NORMAL FAST PATH]             [HEROIC CARVING FALLBACK]                    │
│            Read master index table        Search raw sectors for H.264/H.265           │
│            Instant video recovery!        magic codes (00 00 01). Assemble broken      │
│                                           frames into video slices.                    │
│                                                    │                                   │
│                                                    ▼                                   │
│                                           FLAG AS LOW-CONFIDENCE                       │
│                                           Notify detective: "Recovered from unallocated│
│                                           space without index."                        │
│                                                                                        │
│  ────────────────────────────────────────────────────────────────────────────────────  │
│                                                                                        │
│  WHAT IF SOMEONE TRIES TO TAMPER WITH OR ALTER EVIDENCE?                               │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ 🚨 HASH MISMATCH DETECTED!                                                       │  │
│  │ Calculated hash does not match crime scene baseline!                             │  │
│  │ 🛑 SYSTEM ACTION: Immediately halt all processes. Sound alarm. Lock interface.  │  │
│  │ 📋 AUDIT ACTION: Record tamper event in Merkle ledger. Require human explanation.│  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## ⚠️ Failure Recovery Workflow Diagram (Mermaid)

```mermaid
flowchart TD
    %% Styling Classes
    classDef normProc fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;
    classDef failProc fill:#0f172a,stroke:#ef4444,stroke-width:3px,color:#f8fafc;
    classDef altProc fill:#0f172a,stroke:#f59e0b,stroke-width:2px,color:#f8fafc;
    classDef recovProc fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;

    START["INPUT EVIDENCE INGESTION"]:::normProc
    DEC_HDR{"Filesystem Header / Index Intact?"}:::altProc

    FS_PARSE["Vendor Filesystem Parsing<br/>(HIK / DHFS Index Traversal)"]:::normProc
    FS_EXTR["Direct Clip Extraction"]:::normProc

    SCAN_RAW["RAW SECTOR SCANNING<br/>(Unallocated LBA Block Scan)"]:::altProc
    NAL_DET["NAL SIGNATURE DETECTION<br/>(0x000001 / 0x00000001 Search)"]:::altProc
    FRM_VAL{"Valid SPS/PPS & Macroblock Syntax?"}:::altProc
    DROP_NOISE["Discard Sector Noise"]:::failProc
    GOP_REC["GOP RECONSTRUCTION<br/>(Assemble I/P/B Frame Slices)"]:::recovProc
    PART_REC["PARTIAL STREAM RECOVERY"]:::recovProc
    FLAG_LOW["FLAG AS LOW-CONFIDENCE RESULT<br/>(Audit Note: Corrupted Index Carve)"]:::altProc

    DEC_HASH{"Verification Hash Matches Baseline?"}:::altProc
    HALT_PIPE["STOP PROCESSING IMMEDIATELY"]:::failProc
    FLAG_EVID["FLAG EVIDENCE CORRUPTION"]:::failProc
    AUD_FAIL["CREATE AUDIT CRITICAL EVENT"]:::failProc
    REQ_EXAM["REQUIRE EXAMINER INTERVENTION & OVERRIDE"]:::failProc

    PROCEED_NORM["PROCEED TO VERIFIED EXPORT"]:::recovProc

    START --> DEC_HDR
    DEC_HDR -- "YES (Normal)" --> FS_PARSE --> FS_EXTR --> DEC_HASH
    DEC_HDR -- "NO (Damaged / Wiped)" --> SCAN_RAW

    SCAN_RAW --> NAL_DET --> FRM_VAL
    FRM_VAL -- "NO (Invalid)" --> DROP_NOISE
    FRM_VAL -- "YES (Valid Slices)" --> GOP_REC --> PART_REC --> FLAG_LOW --> DEC_HASH

    DEC_HASH -- "YES (Match)" --> PROCEED_NORM
    DEC_HASH -- "NO (Mismatch / Alteration)" --> HALT_PIPE --> FLAG_EVID --> AUD_FAIL --> REQ_EXAM

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

## 📊 Component Responsibility Matrix

| Subsystem | Core Mission | What Goes In (Input) | What Comes Out (Output) | Failure & Safety Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **Acquisition Engine** | Read-only physical drive imaging | Physical DVR HDD/SSD | Raw byte bitstream (.RAW / .E01) | Aborts instantly if write signal detected. |
| **Hash Engine** | Cryptographic integrity | Raw bitstream | SHA-256 & MD5 values | Raises critical alarm on hash mismatch. |
| **Vendor Classifier** | Identifies CCTV brand | Boot sectors (LBA 0..64) | Vendor profile & confidence score | Prompts examiner if confidence is low (< 70%). |
| **Parser Engine** | Decodes proprietary structures | Raw disk image & vendor profile | Master sector map & channel list | Falls back to raw carving if tables are wiped. |
| **Recovery Engine** | Hunts video in unallocated space | Unallocated disk sectors | Raw H.264/H.265 frame slices | Drops corrupted noise bytes automatically. |
| **Time Sync Engine** | Syncs multi-camera clocks | Slice headers (HKMI/DHAV) | Master UTC timecode track | Interpolates timestamps if clock header corrupt. |
| **AI Engine** | Local video triage & drafting | Recovered MP4 copy | Clickable detections & draft report | Watermarks everything as provisional. |
| **Examiner UI** | Command center & verification | Playable video & AI annotations | Human Confirm/Edit/Reject decisions | Blocks unreviewed AI from case reports. |
| **Audit Engine** | Unhackable event recording | All user & system events | Merkle-tree chained receipts | Rejects backdated or unsigned entries. |
| **Report Engine** | Court-ready certification | Verified findings & Merkle root | Signed legal PDF certificate | Validates all signatures before rendering. |

---

## 🏷️ The Four Data Classification Tiers

```mermaid
flowchart TD
    %% Styling Classes
    classDef clA fill:#0f172a,stroke:#10b981,stroke-width:3px,color:#f8fafc;
    classDef clB fill:#0f172a,stroke:#06b6d4,stroke-width:2px,color:#f8fafc;
    classDef clC fill:#0f172a,stroke:#a855f7,stroke-width:2px,color:#f8fafc;
    classDef clD fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;

    subgraph CLASS_A ["CLASS A: ORIGINAL EVIDENCE (INVIOLATE EVIDENCE TIER)"]
        direction TB
        CA_RAW["Physical DVR / NVR Storage Media"]:::clA
        CA_IMG["Bit-Stream Forensic Images (.RAW / .E01 / .AFF4)"]:::clA
        CA_HSH["Master Acquisition Hashes (SHA-256 / MD5)"]:::clA
        CA_SEC["Sector-Level Block Maps (LBA 0 .. N)"]:::clA
    end

    subgraph CLASS_B ["CLASS B: RECOVERED EVIDENCE (OBJECTIVE MEDIA TIER)"]
        direction TB
        CB_VID["Reconstructed Playable Video (.MP4 / .MKV)"]:::clB
        CB_STR["Carved Elementary Streams (H.264 / H.265 NAL)"]:::clB
        CB_MET["Extracted Hardware Timecodes (RTC / OSD)"]:::clB
        CB_MAP["Physical Sector Traceability Index"]:::clB
    end

    subgraph CLASS_C ["CLASS C: DERIVATIVE ANALYSIS (ASSISTIVE AI TIER)"]
        direction TB
        CC_DET["AI Object / Person / Vehicle Bounding Boxes"]:::clC
        CC_EVT["Temporal Event Classifications"]:::clC
        CC_NAR["LLM Draft Timeline Narratives"]:::clC
        CC_PRO["Model Provenance & Confidence Weights"]:::clC
    end

    subgraph CLASS_D ["CLASS D: AUDIT RECORDS (ATTESTED LEGAL TIER)"]
        direction TB
        CD_DEC["Examiner Adjudication Log (Confirm/Edit/Reject)"]:::clD
        CD_SIG["Examiner Ed25519 Digital Signatures"]:::clD
        CD_MRK["Cryptographic Merkle Tree Event Ledger"]:::clD
        CD_REP["Signed Court-Ready Forensic Certificate (ISO 27037)"]:::clD
    end

    CLASS_A ==>|"Read-Only Extraction"| CLASS_B
    CLASS_B -.->|"Sandboxed Derivative Pipe"| CLASS_C
    CLASS_C ==>|"Human Verification Gate"| CLASS_D
    CLASS_B ==>|"Direct Adjudication"| CLASS_D

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

[← Previous: 08 — Deployment & Hardware](./08_DEPLOYMENT_HARDWARE_AND_TECH_STACK.md) | [Master Index](./README.md) | [Next: 10 — Future Roadmap & Summary →](./10_FUTURE_ROADMAP_AND_SUMMARY.md)
