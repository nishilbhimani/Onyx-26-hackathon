# 03 — Four-Layer Architecture
## The Core Architectural Separation of Concerns

---

## 💡 In Plain English / For Non-Tech Readers: The 4-Floor Forensic Skyscraper

Imagine UNFRAGMENT as a **secure 4-story forensic laboratory building**. Each floor has one job, and people are strictly prohibited from mixing duties:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                     THE 4-FLOOR FORENSIC SKYSCRAPER ANALOGY                            │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│  FLOOR 4: THE COURTROOM & EXAMINER OFFICE                                              │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ The Detective and Lawyer lounge. Video playback on 16 screens, timeline scrubber,│  │
│  │ hex inspector, and official report generator.                                    │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                    ▲                                                   │
│                                    │ Only verified human decisions pass up             │
│  FLOOR 3: THE AI INTELLIGENCE LAB  │                                                   │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ A smart, air-gapped assistant scans the video for people, cars, and events.      │  │
│  │ Everything is watermarked "AI-Generated — Unverified" until approved upstairs.   │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                    ▲                                                   │
│                                    │ Reads playable MP4 video copies                   │
│  FLOOR 2: THE RECONSTRUCTION WORKSHOP                                                  │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ Master technicians reverse-engineer Hikvision and Dahua secret formats.          │  │
│  │ They carve raw video slices, assemble broken frames, and sync camera clocks.     │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                    ▲                                                   │
│                                    │ Reads raw byte sectors through a one-way mirror   │
│  FLOOR 1: THE HIGH-SECURITY EVIDENCE VAULT                                             │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │ Hardware Write-Blockers lock the drive in 100% read-only mode. Dual SHA-256 and  │  │
│  │ MD5 hashes are generated immediately. Not a single bit can be altered!          │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
│                                    ▲                                                   │
│                                    │ Connected via physical write-blocker cable        │
│  BASEMENT: THE SEIZED CRIME SCENE CCTV HARD DRIVE                                      │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🏛️ Four-Layer Architecture Diagram (Mermaid)

```mermaid
flowchart TD
    %% Global Styling Definitions
    classDef l4Style fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;
    classDef l3Style fill:#0f172a,stroke:#a855f7,stroke-width:2px,color:#f8fafc;
    classDef l2Style fill:#0f172a,stroke:#06b6d4,stroke-width:2px,color:#f8fafc;
    classDef l1Style fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;
    classDef physStyle fill:#1e293b,stroke:#f59e0b,stroke-width:3px,color:#f8fafc;

    %% Layer 4: Examiner UI & Auditing
    subgraph L4 ["LAYER 4 — EXAMINER UI & AUDITING (CLASS D ATTESTED DOMAIN)"]
        direction TB
        L4_DASH["Forensic Case Dashboard"]:::l4Style
        L4_VIEW["Evidence Viewer & Multi-Channel Player"]:::l4Style
        L4_TIME["Timeline & Event Review Scrubbing"]:::l4Style
        L4_HEX["Integrated Hex & Sector Inspector"]:::l4Style
        L4_CUST["Chain-of-Custody Manager"]:::l4Style
        L4_AUD["Tamper-Evident Audit Dashboard"]:::l4Style
        L4_REP["Court-Ready Report Generator (PDF / Merkle)"]:::l4Style
    end

    %% Layer 3: AI Intelligence
    subgraph L3 ["LAYER 3 — AI INTELLIGENCE (CLASS C DERIVATIVE DOMAIN)"]
        direction TB
        L3_CLASS["Vendor / Codec Classifier (ML Fingerprinter)"]:::l3Style
        L3_CV["Computer Vision Triage Engine"]:::l3Style
        L3_OBJ["Object Detection & Person / Vehicle Classifier"]:::l3Style
        L3_EVT["Temporal Event Classification"]:::l3Style
        L3_TIME["Multi-Camera Timeline Intelligence"]:::l3Style
        L3_REP["Assisted Report Drafting (LLM Narrative)"]:::l3Style
        L3_CONF["Confidence Scoring & Uncertainty Metric"]:::l3Style
        L3_GATE["Examiner Confirmation Layer (Confirm / Edit / Reject)"]:::l3Style
    end

    %% Layer 2: Parsing & Recovery
    subgraph L2 ["LAYER 2 — PARSING & RECOVERY (CLASS B RECOVERED DOMAIN)"]
        direction TB
        L2_FS["Vendor Filesystem Parser (HIK / DHFS / WFS / ZHFS)"]:::l2Style
        L2_DET["Format & Superblock Detection Engine"]:::l2Style
        L2_SCAN["Unallocated Raw Sector Scanner"]:::l2Style
        L2_CARV["NAL Unit Carver (0x000001 / 0x00000001)"]:::l2Style
        L2_SPS["SPS / PPS Validator & Profile Constraint Check"]:::l2Style
        L2_FRM["I / P / B Frame & Slice Classifier"]:::l2Style
        L2_DEINT["Channel De-Interleaving Demuxer"]:::l2Style
        L2_GOP["GOP Assembler & Sequence Reconstructor"]:::l2Style
        L2_RTC["RTC / Embedded Timecode Extractor (OSD / Slice)"]:::l2Style
        L2_MUX["Standardized MP4 / MKV Lossless Remuxer"]:::l2Style
    end

    %% Layer 1: Acquisition & I/O
    subgraph L1 ["LAYER 1 — ACQUISITION & I/O (CLASS A ORIGINAL EVIDENCE)"]
        direction TB
        L1_BLOCK["Hardware Write Blocker (Physical Isolation)"]:::l1Style
        L1_BUS["SATA / SAS / NVMe Physical Storage Interface"]:::l1Style
        L1_IMG["Raw Bit-Stream Imager (Direct DMA Access)"]:::l1Style
        L1_SEC["Sector-Level Acquisition Controller"]:::l1Style
        L1_FMT["Forensic Image Support (.DD / .RAW / .E01 / .AFF4)"]:::l1Style
        L1_RO["Read-Only Loopback Virtual Disk Access"]:::l1Style
        L1_HASH["Hardware-Accelerated Dual Source Hashing (SHA-256 / MD5)"]:::l1Style
    end

    %% Physical Evidence Substrate
    subgraph PHYS ["PHYSICAL SURVEILLANCE EVIDENCE"]
        STORAGE["PHYSICAL EVIDENCE SUBSTRATE<br/>DVR / NVR Appliance • Surveillance HDD / SSD • Seized Media"]:::physStyle
    end

    STORAGE ==>|"Direct Physical Tap / Cable"| L1_BLOCK
    L1_BLOCK --> L1_BUS --> L1_IMG --> L1_SEC --> L1_HASH
    L1_FMT -.-> L1_RO

    L1_HASH ==>|"Read-Only Raw Bitstream (Class A)"| L2_DET
    L1_RO ==>|"Unallocated Block Stream"| L2_SCAN

    L2_DET --> L2_FS
    L2_SCAN --> L2_CARV --> L2_SPS --> L2_FRM --> L2_DEINT --> L2_GOP --> L2_RTC --> L2_MUX

    L2_MUX ==>|"Verified Remuxed Streams (Class B)"| L3_CV
    L2_DET -.->|"Byte Patterns"| L3_CLASS

    L3_CV --> L3_OBJ --> L3_EVT --> L3_TIME --> L3_CONF --> L3_REP --> L3_GATE

    L3_GATE ==>|"Examiner Attested Findings (Class D)"| L4_DASH
    L2_MUX ==>|"Lossless Video Streams"| L4_VIEW
    L2_RTC ==>|"Synchronized Timestamp Tracks"| L4_TIME
    L1_RO -.->|"Direct Sector Inspection"| L4_HEX
    L3_GATE --> L4_CUST --> L4_AUD --> L4_REP

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

## 🔍 Layer-by-Layer Technical Specification

### Layer 1: Acquisition & I/O
* **Mission:** Guaranteed read-only physical access. Zero byte modifications under any circumstance.
* **Key Components:**
  * **Hardware Write Blocker:** Physical isolation bridging SATA/NVMe connections. Drops write commands at the hardware register level.
  * **Direct DMA Imager:** Sector-by-sector copying (`O_DIRECT`) bypassing operating system cache.
  * **Dual Hashing Engine:** Parallel generation of SHA-256 and MD5 hashes at the moment of acquisition.

### Layer 2: Parsing & Recovery
* **Mission:** Crack open vendor formats and assemble broken video slices into playable MP4/MKV files.
* **Key Components:**
  * **Vendor Filesystem Parsers:** Native decoding for Hikvision (HIK), Dahua (DHFS 4.1), and Jovision (WFS 0.4).
  * **Deep NAL Unit Carver:** Hunts for H.264/H.265 byte markers (`00 00 01` and `00 00 00 01`) across unallocated space.
  * **Channel De-Interleaver:** Disentangles multiplexed camera slices so Camera 1 video does not show up in Camera 2.
  * **RTC Time Synchronizer:** Reads camera hardware clock timestamps from slice headers to eliminate time drift.

### Layer 3: AI Intelligence
* **Mission:** Fast-forward investigations by finding people, cars, and events in minutes.
* **Key Components:**
  * **Local YOLOv8 Computer Vision:** Quantized on-device object detection running 100% offline.
  * **Multi-Camera Timeline Intelligence:** Correlates suspect movements across multiple cameras.
  * **Local LLM Case Drafter:** Generates an objective draft timeline narrative for the detective to review.
  * **Safety Isolation:** Everything produced here is strictly labeled *"AI-Generated — Unverified"*.

### Layer 4: Examiner UI & Auditing
* **Mission:** The command center for the licensed detective and judicial certification.
* **Key Components:**
  * **16-Channel Video Player:** Smooth, synchronized playback of multiple camera angles.
  * **Hex Inspector:** Click any video frame and see the exact physical disk sector where it lives.
  * **Confirm / Edit / Reject Gate:** The detective makes every final decision.
  * **Merkle Tree Audit Ledger:** Produces court-ready, signed PDF certificates compliant with ISO/IEC 27037 and Section 63 BSA 2023.

---

[← Previous: 02 — System Context](./02_SYSTEM_CONTEXT_AND_ECOSYSTEM.md) | [Master Index](./README.md) | [Next: 04 — Components & Plugin Engine →](./04_COMPONENTS_AND_PLUGIN_ENGINE.md)
