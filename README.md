| [KR 한국어](./docs/locales/ko.md) | [US English](./README.md) | [JP 日本語](./docs/locales/ja.md) | [CN 简体中文](./docs/locales/zh-CN.md) | [ES Español](./docs/locales/es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./docs/locales/fr.md) | [DE Deutsch](./docs/locales/de.md) | [RU Русский](./docs/locales/ru.md) | [SA العربية](./docs/locales/ar.md) | [IN हिन्दी](./docs/locales/hi.md) |

---

# Eugene Park (@znbobziz-ship-it)

> Systems programmer specializing in low-latency networked audio, real-time Linux kernels, and deterministic DSP infrastructure.

I design and implement software that lives close to hardware clocks and wire speeds. My daily focus revolves around lock-free concurrency, memory-efficient packet serialization, and audio graph engines that cannot afford a single dropped sample.

---

## 🧭 Background & Focus

- **Domain**: Low-Latency Distributed Audio, Embedded DSP Firmware, Real-Time Systems Programming
- **Kernel & OS**: Linux `PREEMPT_RT`, PipeWire / JACK Graph Subsystems, ALSA driver internals
- **Network & Clocking**: IEEE 1588 PTPv2 Precision Time Protocol, AES67, RTP/RTSP, raw socket multicasting
- **Current Curiosity**: Vectorized DSP routines across RISC-V RVV 1.0 and ARM NEON, lock-free wait-free SPMC queues

---

## 🛠 Technical Toolkit

| Category | Primary Technologies & Tooling |
| :--- | :--- |
| **Core Languages** | Rust, C++20, ISO C99, Go, Zig, Python |
| **Low-Level & DSP** | SIMD (AVX2, AVX-512, NEON), Fixed-Point Math, FFTW, libsndfile, CLAP / VST3 SDK |
| **Audio Subsystems** | PipeWire SPA Plugins, JACK Audio Connection Kit, ALSA Kernel API, WebAudio |
| **Real-Time & Concurrency** | Lock-Free Ring Buffers, Atomic Primitives, Cache Line Alignment, PREEMPT_RT Tuning |
| **Profiling & Diagnostics** | `perf`, `ebpf` (bpftrace), Valgrind / Massif, Wireshark (Audio over IP), Oscilloscopes |
| **Toolchains & Build** | Cargo, CMake, Ninja, Make, GCC, Clang/LLVM, GitHub Actions CI |

---

## 🚀 Key Open-Source Projects

```
+-----------------------------------------------------------------------------+
| PROJECT       | LANGUAGE  | PURPOSE                                         |
+-----------------------------------------------------------------------------+
| wave-mesh     | Rust      | Zero-copy multicast networked audio routing bus |
| synth-core    | C++20     | Polyphonic modular synthesizer engine with SIMD |
| jitter-guard  | Rust / C  | Microsecond-level PTPv2 drift monitor CLI       |
| dsp-bench     | Zig / C   | Cross-platform benchmarker for IIR/FIR filters  |
+-----------------------------------------------------------------------------+
```

### 1. `wave-mesh`
- **Architecture**: Asynchronous zero-copy audio daemon transporting 64-channel 24-bit/96kHz streams across standard local Ethernet networks.
- **Key Features**: Ring-buffer shared memory transport, jitter-buffer adaptation with microsecond drift correction, and native PipeWire/JACK bridge modules.
- **Stack**: Rust, Linux `AF_PACKET`, `epoll`, PipeWire SPA.

### 2. `synth-core`
- **Architecture**: Real-time modular DSP library designed for plugin hosts and embedded Eurorack modules.
- **Key Features**: Band-limited oscillator generation (BLIT/BLEP), oversampled zero-delay feedback (ZDF) filters, and zero dynamic heap allocations in audio threads.
- **Stack**: C++20, AVX2 / ARM NEON SIMD intrinsics, CMake.

### 3. `jitter-guard`
- **Architecture**: Telemetry tool that passively listens to IEEE 1588 PTP packets and hardware timestamps (SO_TIMESTAMPING) to pinpoint packet delay variation (PDV).
- **Key Features**: TUI visualization, JSONL export for long-term jitter tracking, and alert triggers for clock synchronization failures.
- **Stack**: Rust, `crossterm`, Linux Socket Timestamping API.

### 4. `dsp-bench`
- **Architecture**: Lightweight CLI toolkit evaluating cycles-per-sample efficiency of common audio DSP filters across diverse CPU architectures.
- **Key Features**: Supports x86_64, aarch64, and riscv64 targets with cycle-accurate hardware counters.
- **Stack**: Zig, C99, GNU Make.

---

## 📐 Engineering Principles

1. **Deterministic Execution First**: In real-time audio, late data is corrupted data. Memory allocations, locks, and blocking syscalls never enter the audio execution thread.
2. **Explicit Over Implicit**: Performance is not an afterthought; cache friendliness, struct layout packing, and explicit data ownership dictate architectural choices.
3. **Measure, Don't Guess**: Hardware timers, cycle counters, and eBPF tracing guide every refactoring decision rather than compiler assumptions.
4. **Resilient Simplicity**: Complex protocols fail in unexpected environments. Minimal, modular protocol envelopes survive dirty network segments and power interruptions.

---

## 📈 Activity & Development Workflow

```
[System Event Loop]
  │
  ├── 1. Ingress Audio / Network Packet
  │      └── Lock-free Single-Producer Multi-Consumer (SPMC) Queue
  │
  ├── 2. Vectorized DSP Processing (SIMD / Non-allocating)
  │      └── Fixed-block buffer pipeline (16..128 frames)
  │
  └── 3. Egress Hardware Bus / Soundcard DMA Buffer
         └── Verified Zero Xruns under Linux PREEMPT_RT
```

---

## 📬 Connect & Collaborate

- **GitHub Issues**: Open a discussion or report issues directly on any repository.
- **Audio Over IP & Real-Time Inquiries**: Available for technical design discussions on low-latency audio transmission and driver architectures.
- **GPG Key ID**: `0x3F8B2E17A4D091C5` (commit signatures and verification)
