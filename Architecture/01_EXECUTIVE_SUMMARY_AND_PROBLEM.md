# 01 — Executive Summary & Problem Statement
## Understanding the CCTV Digital Forensics Crisis & UNFRAGMENT Vision

---

## 💡 In Plain English / For Non-Tech Readers

Imagine you buy a music cassette tape, but instead of standard songs recorded one after another, the manufacturer created their own secret tape player that records 16 songs simultaneously by cutting each second of music into tiny shreds and weaving them together like a braid. 

If you put that tape into a normal cassette player, it makes harsh static noise. If you plug that tape into a computer, Windows says: **"This tape is blank. Do you want to erase it?"**

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                  NORMAL COMPUTER DISK vs CCTV SURVEILLANCE DISK                        │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ NORMAL WINDOWS PC DISK (NTFS / FAT32)        CCTV SURVEILLANCE DISK (PROPRIETARY)      │
│                                                                                        │
│ ┌────────────────────────────────────────┐   ┌────────────────────────────────────────┐│
│ │ [Partition Table] [Folder: Videos]     │   │ [NO PARTITION TABLE] [NO FOLDERS]      ││
│ │  ├── clip1.mp4 (Continuous file)       │   │  Continuous Raw Ring Buffer:           ││
│ │  ├── clip2.mp4 (Continuous file)       │   │  [Cam1 slice][Cam2 slice][Cam3 slice]..││
│ │  └── index.txt (Standard table)        │   │  Repeats and overwrites itself forever ││
│ └────────────────────────────────────────┘   └────────────────────────────────────────┘│
│  Result: Double-click and it plays!          Result: Windows says "Disk is RAW/Blank"  │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

Even worse:
* **The Overwrite Trap:** When the tape gets full, the camera doesn't ask permission — it starts recording right over the oldest video in a continuous loop.
* **The Triage Nightmare:** A 32-camera system recorded for a month has over **20,000 hours of video**. A human detective would need over two years of non-stop watching just to find the five seconds where a thief broke in!
* **The Courtroom Trap:** If police use the camera maker's free viewing tool downloaded from the internet, defense lawyers in court can argue: *"That app isn't certified! How do we know it didn't fake this video?"*

**UNFRAGMENT fixes all three problems at once.**

---

## 🚨 The Four Operational Gaps

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                             THE 4-PART CCTV CRISIS GAP                                 │
└────────────────────────────────────────────────────────────────────────────────────────┘
  1. ACQUISITION GAP           2. EXTRACTION GAP          3. TRIAGE GAP          4. ADMISSIBILITY GAP
 ┌──────────────────────┐    ┌─────────────────────┐    ┌─────────────────┐    ┌──────────────────────┐
 │ • Raw Ring Buffers   │    │ • Proprietary FS    │    │ • 1000s of Hours│    │ • Unvetted OEM Tools │
 │ • OS Flags as Unalloc│    │ • Multiplex Slices  │    │ • Multi-Camera  │    │ • Cloud Activation   │
 │ • Accidental Writes  │ ──►│ • Cyclic Overwrites │ ──►│ • Manual Scrub  │ ──►│ • No Hash Proofs     │
 │ • Corrupt Geometry   │    │ • Broken Carving    │    │ • Backlog Delay │    │ • Evidence Thrown Out│
 └──────────────────────┘    └─────────────────────┘    └─────────────────┘    └──────────────────────┘
```

### 1. The Acquisition Gap (Risk of Evidence Destruction)
Commercial DVRs/NVRs bypass standard operating system partition schemes (MBR/GPT) and standard file systems (NTFS, EXT4) to write continuous video streams at maximum speed. When connected to an investigator's PC:
* The host operating system flags the drive as **Unallocated** or **RAW**.
* The OS may prompt the user to initialize the disk, or automatically write volume serial numbers or metadata flags.
* **Result:** Evidence spoliation before the forensic analysis even begins.

### 2. The Extraction Gap (Proprietary Filesystems)
Every vendor invents their own storage layout:
* **Hikvision:** Master Sector Map, 256 MB data partitions, proprietary `HKMI` frame wrappers.
* **Dahua:** Dahua Hard Disk File System (`DHFS 4.1`), inode-less block indexing, `DHAV` container wrappers.
* **Jovision & Xiongmai:** `WFS 0.4` and `ZHFS` continuous ring buffers.

When traditional file recovery tools (like PhotoRec or Scalpel) try to recover deleted video, they fail because they expect continuous files. Because CCTV units interleave slices from 4 to 32 cameras across adjacent sectors, generic carvers output corrupted video where camera angles rapidly switch back and forth.

### 3. The Triage Gap (Volume Overload)
Investigators face an impossible volume of video. A single case with 16 cameras across two weeks produces over **5,000 hours of video**. Manual scrubbing causes severe mental fatigue, missed leads, and investigation backlogs.

### 4. The Legal Admissibility Gap (Courtroom Scrutiny)
To view footage, officers often download free software from camera manufacturers. These tools:
* Often demand cloud account login, violating air-gapped lab rules.
* Apply undocumented filters or frame rate adjustments.
* Provide zero cryptographic chain of custody, causing evidence to be rejected under **FRE 901**, **ISO/IEC 27037**, and **BSA 2023 Section 63**.

---

## 🎯 UNFRAGMENT Measurable Objectives

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        UNFRAGMENT SOLUTION ARCHITECTURE                                │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│  [SEIZED CCTV DRIVE]                                                                   │
│          │                                                                             │
│          ▼                                                                             │
│  1. HARDWARE WRITE BLOCKER   ──► 100% Read-Only, zero byte modification guaranteed     │
│          │                                                                             │
│          ▼                                                                             │
│  2. VENDOR REVERSE ENGINE    ──► Native decoding for Hikvision, Dahua, Jovision        │
│          │                                                                             │
│          ▼                                                                             │
│  3. DEEP NAL FRAME CARVER    ──► Glues broken H.264/H.265 slices into playable MP4     │
│          │                                                                             │
│          ▼                                                                             │
│  4. ON-PREMISES AI TRIAGE    ──► Finds suspects/vehicles across all channels in mins   │
│          │                                                                             │
│          ▼                                                                             │
│  5. HUMAN-IN-THE-LOOP GATE   ──► Detective Confirms, Edits, or Rejects every finding   │
│          │                                                                             │
│          ▼                                                                             │
│  6. MERKLE TREE AUDIT LOG    ──► Court-ready PDF with unhackable cryptographic proofs  │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

| ID | Engineering Objective | Measurable Forensic Metric |
| :--- | :--- | :--- |
| **OBJ-01** | **Zero Modification** | 100% read-only acquisition via hardware write-blockers; dual SHA-256 and MD5 hash baseline. |
| **OBJ-02** | **Multi-Vendor Decoding** | Reverse-engineered parsers for Hikvision and Dahua with automatic vendor detection. |
| **OBJ-03** | **Deep Frame Carving** | Direct NAL unit recovery (`0x000001` start codes) from unallocated space without index tables. |
| **OBJ-04** | **Timecode Sync** | Extraction of hardware Real-Time Clock (RTC) timestamps from proprietary slice headers. |
| **OBJ-05** | **Air-Gapped AI Triage** | 100% local, on-premises computer vision flagging persons and vehicles across all cameras. |
| **OBJ-06** | **Human Authority Gate** | Confirm / Edit / Reject workflow; zero silent automation; AI outputs logged as separate metadata. |
| **OBJ-07** | **Tamper-Proof Audit** | Merkle-tree event logging of every operator action, creating a mathematically provable ledger. |
| **OBJ-08** | **Court-Ready Reports** | Automated generation of forensic reports compliant with ISO/IEC 27037 and Section 63 BSA 2023. |

---

[← Return to Master Index](./README.md) | [Next: 02 — System Context & Ecosystem →](./02_SYSTEM_CONTEXT_AND_ECOSYSTEM.md)
