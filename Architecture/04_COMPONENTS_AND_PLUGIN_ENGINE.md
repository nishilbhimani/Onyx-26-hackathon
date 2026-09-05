# 04 — Components & Plugin Engine
## The 13 Subsystems & The Universal Multi-Vendor Adapter

---

## 💡 In Plain English / For Non-Tech Readers: The Universal Travel Adapter

Imagine you travel across Europe, the UK, the US, and India. Every country has a completely different wall outlet. If you try to force a UK plug into a US socket, you break the socket or cause a short circuit.

CCTV DVRs are the same. A Hikvision hard drive uses one secret format, a Dahua hard drive uses another, and Jovision uses a third. 

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        THE UNIVERSAL DVR ADAPTER CONCEPT                               │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│   DIFFERENT CCTV VENDORS                  UNFRAGMENT AUTO-DETECTION & PLUGINS          │
│                                                                                        │
│  ┌───────────────────────┐                 ┌────────────────────────────────────────┐  │
│  │ HIKVISION HARD DRIVE  │───► [Socket A]──►│ HIKVISION PARSER PLUGIN (WORKING DEMO) │  │
│  │ (Master Sector Maps)  │                 │  Reads proprietary HKMI frame wrappers │  │
│  └───────────────────────┘                 └───────────────────┬────────────────────┘  │
│                                                                │                       │
│  ┌───────────────────────┐                 ┌───────────────────┴────────────────────┐  │
│  │ DAHUA HARD DRIVE      │───► [Socket B]──►│ DAHUA PARSER PLUGIN (DEMO STUB)       │  │
│  │ (DHFS 4.1 Superblock) │                 │  Recognizes DHAV video containers      │  │
│  └───────────────────────┘                 └───────────────────┬────────────────────┘  │
│                                                                │                       │
│  ┌───────────────────────┐                 ┌───────────────────┴────────────────────┐  │
│  │ OTHER / UNKNOWN CCTV  │───► [Unknown ]──►│ RAW NAL STREAM CARVER (FALLBACK)       │  │
│  │ (Zeroed or Broken MBR)│                 │  Direct search for 0x000001 byte codes │  │
│  └───────────────────────┘                 └───────────────────┬────────────────────┘  │
│                                                                │                       │
│                                                                ▼                       │
│                                            ┌────────────────────────────────────────┐  │
│                                            │ NORMALIZED, CRYSTAL-CLEAR MP4 VIDEO    │  │
│                                            │ Plays in any media player immediately! │  │
│                                            └────────────────────────────────────────┘  │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

UNFRAGMENT acts as a **Universal Forensic Adapter**. It scans the first few sectors of the disk, identifies the manufacturer's unique "fingerprint," and automatically clicks the right parser plugin into place. If the format is completely unknown or the disk was formatted to hide a crime, UNFRAGMENT falls back to **Raw NAL Stream Carving**, extracting video frames directly from raw sectors.

---

## ⚙️ Component Architecture Pipeline (Mermaid)

```mermaid
flowchart TD
    %% Styling Classes
    classDef srvAcq fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;
    classDef srvPars fill:#0f172a,stroke:#06b6d4,stroke-width:2px,color:#f8fafc;
    classDef srvAI fill:#0f172a,stroke:#a855f7,stroke-width:2px,color:#f8fafc;
    classDef srvAudit fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;
    classDef repoStore fill:#1e293b,stroke:#f59e0b,stroke-width:2px,color:#f8fafc;

    ACQ_SRV["1. Evidence Acquisition Service<br/>(Raw I/O & Block Imager)"]:::srvAcq
    INT_SRV["2. Evidence Integrity Service<br/>(Dual SHA-256 / MD5 Hash Engine)"]:::srvAcq
    DET_ENG["3. Vendor Detection Engine<br/>(Heuristic & Byte-Entropy Classifier)"]:::srvPars
    ORCH_LAY["4. Parser Orchestration Layer<br/>(Dynamic Plugin Router)"]:::srvPars

    subgraph PLUGINS ["5. VENDOR PARSER PLUGINS"]
        P_HIK["Hikvision Plugin<br/>(Primary Working Demo)"]:::srvPars
        P_DAH["Dahua Plugin<br/>(Demo / Stub Implemented)"]:::srvPars
        P_JOV["Jovision Plugin<br/>(Modular Stub)"]:::srvPars
        P_OTH["Other / Custom DVR<br/>(Extensible Stub)"]:::srvPars
    end

    REC_ENG["6. Recovery Engine<br/>(Unallocated Sector Scanning)"]:::srvPars
    NAL_ENG["7. NAL / GOP Reconstruction Engine<br/>(Slice Disambiguation & SPS/PPS)"]:::srvPars
    SYNC_ENG["8. Time Synchronization Engine<br/>(Embedded RTC & OSD Metadata)"]:::srvPars
    EVID_REPO[("9. Recovered Evidence Repository<br/>(Class B Normalized Media)")]:::repoStore
    AI_LAY["10. AI Intelligence Layer<br/>(Local CV Triage & LLM Drafter)"]:::srvAI
    REV_LAY["11. Examiner Review Layer<br/>(Confirm / Edit / Reject Gate)"]:::srvAudit
    AUD_ENG["12. Audit / Chain-of-Custody Engine<br/>(Merkle-Tree Event Ledger)"]:::srvAudit
    REP_ENG["13. Report Generation Engine<br/>(ISO 27037 & BSA Sec 63 Signer)"]:::srvAudit

    ACQ_SRV -->|"Raw Bitstream"| INT_SRV
    INT_SRV -->|"Cryptographic Baseline"| AUD_ENG
    INT_SRV -->|"Verified Sectors"| DET_ENG
    DET_ENG -->|"Identified Vendor Profile"| ORCH_LAY

    ORCH_LAY -->|"Route by Signature"| P_HIK
    ORCH_LAY -->|"Route by Signature"| P_DAH
    ORCH_LAY -->|"Route by Signature"| P_JOV
    ORCH_LAY -->|"Fallback Unknown"| P_OTH

    P_HIK -->|"Extracted Extents"| REC_ENG
    P_DAH -->|"Extracted Extents"| REC_ENG
    P_JOV -->|"Extracted Extents"| REC_ENG
    P_OTH -->|"Raw Sectors"| REC_ENG

    REC_ENG -->|"Carved NAL Stream"| NAL_ENG
    NAL_ENG -->|"Assembled GOPs"| SYNC_ENG
    SYNC_ENG -->|"Normalized Video + Metadata"| EVID_REPO

    EVID_REPO -->|"Read-Only Video Copy"| AI_LAY
    EVID_REPO -->|"Playable Stream"| REV_LAY

    AI_LAY -->|"Suggested Triage Annotations"| REV_LAY
    REV_LAY -->|"Attested Examiner Decisions"| AUD_ENG
    AUD_ENG -->|"Chained Audit Receipts"| REP_ENG
    REP_ENG -->|"Court-Ready Report PDF"| REV_LAY

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

## 🔌 Multi-Vendor Plugin Architecture (Mermaid)

```mermaid
flowchart TD
    %% Styling Classes
    classDef rawEvidence fill:#1e293b,stroke:#f59e0b,stroke-width:2px,color:#f8fafc;
    classDef fingerEngine fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#f8fafc;
    classDef orchCore fill:#0f172a,stroke:#a855f7,stroke-width:3px,color:#f8fafc;
    classDef prodPlugin fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#f8fafc;
    classDef stubPlugin fill:#0f172a,stroke:#f59e0b,stroke-dasharray: 5 5,stroke-width:2px,color:#f8fafc;
    classDef futurePlugin fill:#0f172a,stroke:#64748b,stroke-dasharray: 3 3,stroke-width:1.5px,color:#94a3b8;
    classDef normOutput fill:#0f172a,stroke:#06b6d4,stroke-width:2px,color:#f8fafc;

    RAW_EV["RAW FORENSIC EVIDENCE<br/>(Bitstream / Sectors / Disk Image)"]:::rawEvidence
    FINGER_ENG["VENDOR FINGERPRINT ENGINE<br/>• Magic Byte Header Analysis<br/>• Master Sector Map Scanner<br/>• Entropy & Structural Heuristics"]:::fingerEngine
    ORCH_CORE["PARSER ORCHESTRATOR CORE<br/>• Plugin Registry & Dynamic Loader<br/>• Confidence-Based Parser Dispatcher<br/>• Fallback Carving Coordinator"]:::orchCore

    subgraph PLUGIN_REGISTRY ["VENDOR PLUGIN IMPLEMENTATION MATRIX"]
        PLUG_HIK["HIKVISION PARSER PLUGIN<br/>(STATUS: PRIMARY WORKING / DEMO)<br/>• Master Sector Map Parsing<br/>• Data Partition Chunk Traversal<br/>• HKMI Slice Extraction & RTC Sync"]:::prodPlugin

        PLUG_DAH["DAHUA PARSER PLUGIN<br/>(STATUS: DEMO / PLUGIN STUB)<br/>• DHFS 4.1 Superblock Detection<br/>• Inode-less Block Indexing Stub<br/>• DHAV Container Wrapper Demuxer"]:::stubPlugin

        PLUG_JOV["JOVISION PARSER PLUGIN<br/>(STATUS: PLUGIN STUB)<br/>• WFS 0.4 Superblock Identification<br/>• Contiguous Extent Mapper Stub"]:::stubPlugin

        PLUG_CUST["CUSTOM / OTHER DVR PARSER<br/>(STATUS: EXTENSIBLE FALLBACK)<br/>• Generic Ring-Buffer Carver<br/>• Raw NAL Start-Code Scanner"]:::stubPlugin

        PLUG_FUT["FUTURE VENDOR SDK PLUGINS<br/>(STATUS: ROADMAP EXTENSION)<br/>• Uniview (U-FS) • CP Plus • TVT • Tiandy"]:::futurePlugin
    end

    NORM_OUT["NORMALIZED FORENSIC OUTPUT<br/>• Standard Playable MP4 / MKV Elementary Streams<br/>• Extracted Hardware RTC Frame Timestamps<br/>• Master LBA Sector Mapping Manifest"]:::normOutput

    RAW_EV ==>|"1. Stream Inspection"| FINGER_ENG
    FINGER_ENG -->|"2. Vendor Signature & Confidence"| ORCH_CORE

    ORCH_CORE -->|"Match: Hikvision Signature"| PLUG_HIK
    ORCH_CORE -->|"Match: DHFS / DHAV Signature"| PLUG_DAH
    ORCH_CORE -->|"Match: WFS0.4 Signature"| PLUG_JOV
    ORCH_CORE -->|"Match: Unknown / Wiped MBR"| PLUG_CUST
    ORCH_CORE -.->|"Future Dynamic Link"| PLUG_FUT

    PLUG_HIK ==>|"Demuxed Channels & Frames"| NORM_OUT
    PLUG_DAH ==>|"Simulated / Stubbed Frames"| NORM_OUT
    PLUG_JOV -.->|"Extents"| NORM_OUT
    PLUG_CUST ==>|"Heuristically Carved Frames"| NORM_OUT

    linkStyle default stroke:#64748b,stroke-width:1.5px;
```

---

## 💻 The Plugin Interface Contract

Every vendor parser implements the clean `BaseParserPlugin` programming interface:

```python
class BaseParserPlugin(ABC):
    """Standard interface that all DVR vendor plugins implement."""

    @abstractmethod
    def identify(self, sector_buffer: bytes) -> IdentificationResult:
        """Checks sector bytes for vendor magic signatures and returns confidence (0.0 to 1.0)."""
        pass

    @abstractmethod
    def parse_filesystem_geometry(self, disk_handle: BinaryIO) -> FilesystemGeometry:
        """Decodes master allocation tables, partition offsets, and ring-buffer extents."""
        pass

    @abstractmethod
    def enumerate_channels(self) -> List[ChannelDescriptor]:
        """Finds all multiplexed cameras (e.g. Camera 1 through Camera 16)."""
        pass

    @abstractmethod
    def extract_stream(self, channel_id: int, time_range: TimeRange) -> Iterator[ForensicFrame]:
        """Yields reconstructed video frames with physical sector offsets and real-time clock timestamps."""
        pass
```

### Forensic Implementation Status
* **Hikvision (Primary Working Demo):** Full reverse-engineering completed. Reads Master Sector Maps, unpacks 256 MB data blocks, extracts `HKMI` frame slices, pulls embedded hardware timestamps, and produces clean MP4 files.
* **Dahua (Demo / Plugin Stub):** Fully structured stub. Detects `DHFS 4.1` superblocks and recognizes `DHAV` container wrappers. Demonstrates how new vendor plugins plug into the orchestrator without touching core code.
* **Jovision & Generic Carving (Extensible Fallback):** When headers are completely erased or unknown, the engine scans for raw H.264/H.265 byte markers (`00 00 01`), reassembling frames even without filesystem tables!

---

[← Previous: 03 — Four-Layer Architecture](./03_FOUR_LAYER_ARCHITECTURE.md) | [Master Index](./README.md) | [Next: 05 — Workflow & Evidence Data Flow →](./05_WORKFLOW_AND_EVIDENCE_DATA_FLOW.md)
