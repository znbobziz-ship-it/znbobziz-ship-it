| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Eugene Park (@znbobziz-ship-it)

> Systementwickler mit Schwerpunkt auf extrem latenzarmem Netzwerk-Audio, echtzeitfähigen Linux-Kerneln und deterministischer DSP-Infrastruktur.

Ich entwerfe und implementiere Software an der direkten Schnittstelle zwischen Hardware-Takten und physischen Netzwerkkarten. Meine tägliche Arbeit konzentriert sich auf sperrfreie Nebenläufigkeit (*Lock-Free*), speichereffiziente Paket-Serialisierung ohne Zwischenkopien (*Zero-Copy*) und Audio-Graph-Engines, die sich nicht einen einzigen verworfenen Abtastwert erlauben können.

---

## 🧭 Hintergrund & Arbeitsschwerpunkte

- **Fachgebiete**: Verteilte Audioübertragung mit minimaler Latenz, Firmware für eingebettete DSPs, harte Echtzeitsystem-Programmierung.
- **Kernel & Betriebssystem**: Linux mit `PREEMPT_RT`-Patch, PipeWire- und JACK-Graphen-Subsysteme, Treiberarchitektur von ALSA.
- **Netzwerk & Synchronisation**: IEEE 1588 PTPv2 Präzisions-Zeitsynchronisationsprotokoll, AES67, RTP/RTSP, Multicasting über Raw Sockets.
- **Aktuelle Forschungsbereiche**: Vektorisierte DSP-Algorithmen auf RISC-V RVV 1.0 und ARM NEON, sperr- und wartefreie SPMC-Ringspeicherarchitekturen.

---

## 🛠 Technischer Werkzeugkasten

| Kategorie | Bevorzugte Technologien und Werkzeuge |
| :--- | :--- |
| **Hauptsprachen** | Rust, C++20, ISO C99, Go, Zig, Python |
| **Systemnah & DSP** | SIMD (AVX2, AVX-512, NEON), Festkomma-Arithmetik, FFTW, libsndfile, CLAP / VST3 SDK |
| **Audio-Subsysteme** | PipeWire SPA Plugins, JACK Audio Connection Kit, ALSA Kernel-API, WebAudio |
| **Echtzeit & Parallelität** | Lock-freie Ringpuffer, atomare Operationen, Cache-Line-Ausrichtung, Tuning für `PREEMPT_RT` |
| **Profiling & Diagnose** | `perf`, `ebpf` (bpftrace), Valgrind / Massif, Wireshark (Audio over IP), Oszilloskope |
| **Build-Systeme & CI** | Cargo, CMake, Ninja, Make, GCC, Clang/LLVM, GitHub Actions CI |

---

## 🚀 Wichtige Open-Source-Projekte

```
+-----------------------------------------------------------------------------+
| PROJEKT       | SPRACHE   | FUNKTION                                        |
+-----------------------------------------------------------------------------+
| wave-mesh     | Rust      | Zero-Copy Netzwerk-Audio-Routing-Daemon         |
| synth-core    | C++20     | Polyphone modulare DSP-Klangerzeugungs-Engine   |
| jitter-guard  | Rust / C  | CLI-Monitor für PTPv2-Takt-Drift im Mikrosek-Bereich |
| dsp-bench     | Zig / C   | Plattformübergreifendes IIR/FIR-Benchmark-Tool  |
+-----------------------------------------------------------------------------+
```

### 1. `wave-mesh`
- **Architektur**: Asynchroner Zero-Copy-Audio-Daemon zur verlustfreien Übertragung von 64 Kanälen bei 24-Bit/96kHz über Standard-Gigabit-Ethernet-Netzwerke.
- **Kernmerkmale**: Ringpuffer-basierter Shared-Memory-Transport, Mikrosekunden-genaue Jitter-Kompensation und native Brückenmodule für PipeWire und JACK.
- **Technologie-Stack**: Rust, Linux `AF_PACKET`, `epoll`, PipeWire SPA.

### 2. `synth-core`
- **Architektur**: Modulare Echtzeit-DSP-Bibliothek für Audio-Plugin-Hosts und physikalische Eurorack-Synthesizermodule.
- **Kernmerkmale**: Bandbegrenzte Oszillatorwellenformen (BLIT/BLEP), überabgetastete rückkopplungsfreie Filter (ZDF) und die strikte Garantie von null dynamischen Heap-Allokationen im Audio-Thread.
- **Technologie-Stack**: C++20, AVX2 / ARM NEON SIMD-Intrinsics, CMake.

### 3. `jitter-guard`
- **Architektur**: Passives Telemetriewerkzeug zur Überwachung von IEEE 1588 PTP-Paketen und Hardware-Zeitstempeln (`SO_TIMESTAMPING`) zur Analyse der Laufzeitvarianz (PDV).
- **Kernmerkmale**: TUI-Visualisierung im Terminal, strukturierter JSONL-Export für langfristige Trendanalysen und konfigurierbare Frühwarnungen bei Synchronisationsverlust.
- **Technologie-Stack**: Rust, `crossterm`, Linux Socket Timestamping API.

### 4. `dsp-bench`
- **Architektur**: Schlankes CLI-Werkzeug zur Bestimmung der verbrauchten Taktzyklen pro Abtastwert (Cycles-per-sample) klassischer Filterstrukturen auf diversen Prozessorarchitekturen.
- **Kernmerkmale**: Unterstützt x86_64, aarch64 und riscv64 mit direkter Auslesung von Hardware-Performance-Countern.
- **Technologie-Stack**: Zig, C99, GNU Make.

---

## 📐 Ingenieurphilosophie

1. **Deterministische Ausführung steht an erster Stelle**: In der Echtzeit-Audiosignalverarbeitung sind verspätete Daten wertlose Daten. Dynamische Speicherallokationen, blockierende Mutex-Sperren und E/A-Systemaufrufe haben im Signalverarbeitungs-Thread nichts verloren.
2. **Explizites Design vor impliziten Annahmen**: Höchstleistung entsteht nicht durch nachträgliche Korrekturen. Cache-bewusste Speicherstrukturen, gezieltes Daten-Packing und klar abgegrenzte Besitzverhältnisse bilden das Fundament.
3. **Messen statt Mutmaßen**: Kein Refactoring stützt sich auf unbewiesene Vermutungen. Exakte Hardware-Timer, Taktzähler und eBPF-Traces liefern die Entscheidungsgrundlage.
4. **Resilienz durch Einfachheit**: Überladene Protokolle versagen zuerst bei instabilen Netzwerken oder Stromschwankungen. Nur minimalistische, modulare Paketstrukturen widerstehen widrigen Einsatzbedingungen.

---

## 📈 System-Architektur & Ausführungszyklus

```
[System-Ereignisschleife]
  │
  ├── 1. Audio-Ingress & Netzwerkpaket-Empfang
  │      └── Lock-freie Single-Producer Multi-Consumer (SPMC) Queue
  │
  ├── 2. Vektorisierte DSP-Berechnung (SIMD / Allokationsfrei)
  │      └── Feste Blockpuffer-Pipeline (16..128 Abtastrahmen)
  │
  └── 3. Egress zum Hardware-Bus / Soundkarten-DMA-Puffer
         └── Nachweislich null Pufferabrisse (Zero Xruns) unter Linux PREEMPT_RT
```

---

## 📬 Kontakt & Technischer Austausch

- **GitHub Issues**: Diskussionsbeiträge, Fehlerberichte und Vorschläge können jederzeit in den Repositories eingereicht werden.
- **Fachlicher Austausch**: Sehr gern offen für technische Dialoge zu Audio over IP (AoIP), Linux-Kernel-Echtzeitoptimierung und Treiberentwicklung.
- **GPG Key ID**: `0x3F8B2E17A4D091C5` (Signaturprüfung aller Git-Commits)
