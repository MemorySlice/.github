# Memory Slice (.msl) Format

### Process-Centric. Self-Describing. Forensically Sound.

Welcome to the official organization for the **Memory Slice (.msl)** process memory dump format. 

Memory forensics has traditionally centered on a system-centric paradigm: acquire the full physical address space, apply an operating system profile, and traverse kernel data structures to reconstruct state. This approach increasingly breaks down in containerized environments, embedded systems, mobile platforms, and time-critical incident response triage.

We introduce **Memory Slice**, a modern, block-based, append-only forensic capture format designed to solve these challenges.

### Core Problems Solved

Memory Slice shifts the paradigm from system-level acquisition to efficient, high-integrity process-centric capture. It addresses the common limitations of standard process dumps (e.g., ELF cores, standard `.dmp`) in live forensics:

* **Self-Describing Context:** MSL captures not just the virtual address space, but also critical OS-queryable metadata that exists only at acquisition time—module lists with precise version information, file descriptors, memory region permissions, and network connections—embedding them as typed data blocks.
* **"Epistemic Honesty" & TOCTOU Handling:** Live memory acquisition is subject to Time-Of-Check to Time-Of-Use (TOCTOU) races. MSL introduces a 2-bit `PageStateMap` per memory region block, explicitly distinguishing between pages that were **Captured** (read successfully), **Failed/Paged** (read failed, but were present in metadata), or **Unmapped** (not present in any metadata). This ensures analysis tools can distinguish genuine data from acquisition-time failures without ambiguity.
* **Cryptographic Chain of Custody:** To ensure forensic integrity in append-only scenarios, MSL employs a strict **BLAKE3 hash chain**. Every block forward-hashes into the next, mathematically guaranteeing that original acquisition blocks cannot be altered without invalidating the entire dump. The high performance of BLAKE3 ensures that hashing never bottlenecks gigabit-speed acquisition.
* **Efficiency and Alignment:** Every variable-length payload in MSL (e.g., `PageStateMap`, strings) is strictly padded to 8-byte boundaries using `pad8`, allowing fast, zero-copy `mmap()` parsing in high-performance C, C++, and Rust tooling.

### Organization Repositories

This organization hosts the essential specifications, reference implementations, and analysis tools for the Memory Slice ecosystem:
* **[`memslicer`](Link to Acquirer):** The reference acquisition tool. A lightweight, Python implementation for capturing process memory in the MSL format on Linux, Windows, and macOS, utilizing BLAKE3 for integrity and fast UUIDv4 generation via thread-local PRNGs.
* **[`memslicer-rs`](Link to Acquirer):** The reference acquisition tool. A lightweight, high-speed Rust implementation for capturing process memory in the MSL format on Linux, Windows, and macOS, utilizing BLAKE3 for integrity and fast UUIDv4 generation via thread-local PRNGs.
* **[`memslice-spec`](Link to Specification):** The definitive, formal specification (PDF) of the Memory Slice binary format (current draft-2026-03).
* **[`libmsl`](Link to C/C++/Rust Library):** A reference parsing library to facilitate the adoption of MSL into third-party analysis tools.
* **[`memslice-samples`](Link to Samples):** Small, validated dummy MSL captures with various states (compressed, failed pages, multi-region) for testing new parser implementations.

### Research & Publications

The Memory Slice format and its associated analysis techniques (e.g., Differential Analysis and candidate data structure discovery) will be presented in an upcoming research paper.

> *Citation placeholder (Zenodo DOI will be here):*
>
> **[Our Paper Title Here]**, [Your Names], [Conference Name] (e.g., DFRWS/IMF 2026). DOI: [Link].

For detailed technical review, please refer to the formal specification housed in the `memslice-spec` repository and the permanent archive cited above.

### Contributing

We welcome contributions from the forensic research and development community. If you find errors in the specification, have suggestions for new Block Types (Semantic or Structural namespaces), or wish to contribute to the acquisition or analysis tools, please open an issue or pull request in the respective repository.

---

This organization is dedicated to advancing the state of process memory forensics through open engineering standards.
