# Memory Slice (.msl)

**Process-centric. Self-describing. Forensically sound.**

A modern, block-based, append-only forensic capture format for process memory — and the open-source ecosystem built around it.

---

## Why Memory Slice?

Classical memory forensics is system-centric: acquire the full physical address space, apply an OS profile, walk kernel structures. That model breaks down in containers, embedded targets, mobile platforms, and time-critical triage — exactly where modern incident response increasingly lives.

Memory Slice flips the paradigm to **process-centric** acquisition. A `.msl` capture is not just a virtual address space — it is a self-describing record of *what was true about the process at acquisition time*: module list with versions, file descriptors, region permissions, and network connections, all embedded as typed blocks.

Three properties make MSL useful for forensics rather than just convenient for dumping:

- **Epistemic honesty about TOCTOU.** A 2-bit `PageStateMap` per region distinguishes *Captured*, *Failed/Paged*, and *Unmapped* pages — analysis tools never have to guess whether a zero page is real or an acquisition artifact.
- **BLAKE3 hash chain.** Each block forward-hashes into the next, so any tampering with original acquisition data invalidates the chain. Fast enough to never bottleneck gigabit acquisition.
- **Zero-copy friendly.** Strict 8-byte alignment throughout, so C/C++/Rust parsers can `mmap()` and go.

The full design rationale lives in [`memslice-spec`](#).

---

## The ecosystem

```
   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
   │  memslicer   │───▶│   .msl file  │───▶│   MemDiver   │
   │  (acquire)   │    │  (libmsl)    │    │  (analyze)   │
   └──────────────┘    └──────────────┘    └──────────────┘
```

| Repository | Role | Language |
|---|---|---|
| [**`memslice-spec`**](#) | Definitive binary format specification (current draft: `2026-03`) | — |
| [**`memslicer`**](#) | Reference acquirer — Python, cross-platform (Linux/Windows/macOS) | Python |
| [**`memslicer-rs`**](#) | Reference acquirer — high-throughput Rust port | Rust |
| [**`libmsl`**](#) | Parsing library for embedding MSL support in third-party tools | C / C++ / Rust |
| [**`MemDiver`**](#) | Interactive workbench for identifying and analyzing data structures in `.msl` (and other) memory dumps — FastAPI + React, with an MCP server for AI-assisted analysis | Python / TypeScript |
| [**`memslice-samples`**](#) | Validated sample captures (compressed, partial-failure, multi-region) for parser conformance testing | — |

---

## Getting started

- **Just want to look at memory?** → start with [`MemDiver`](#) — `pip install memdiver` and load a sample.
- **Need to capture a process?** → [`memslicer`](#) (Python) or [`memslicer-rs`](#) (Rust).
- **Building a tool that reads `.msl`?** → [`libmsl`](#) and the spec.
- **Implementing your own parser?** → [`memslice-spec`](#) plus [`memslice-samples`](#) for round-trip tests.

---

## Research

Memory Slice and the differential / candidate-discovery techniques built on top of it are described in research currently under submission at IMF 2026. Citation will be updated post-review; an archival DOI on Zenodo will follow camera-ready.

## Contributing

We welcome issues, PRs, and proposals for new Block Types in either the *Semantic* or *Structural* namespace. File against the relevant repo; cross-cutting design discussion belongs in [`memslice-spec`](#).

## License

Each repository ships under its own license — see individual `LICENSE` files. Most are Apache-2.0.
