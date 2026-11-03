| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Eugene Park (@znbobziz-ship-it)

> Engenheiro de sistemas focado em áudio em rede de baixíssima latência, kernels Linux de tempo real e infraestrutura DSP determinística.

Projeto e implemento software que opera nos limites entre os relógios de hardware e as interfaces físicas de rede. Minha dedicação diária concentra-se em concorrência sem travas (*lock-free*), serialização de pacotes sem cópias redundantes (*zero-copy*) e motores de grafos de áudio que não toleram o descarte de uma única amostra sequer.

---

## 🧭 Trajetória e Áreas de Atuação

- **Especialidade**: Áudio distribuído de baixa latência, firmware para DSPs embarcados, programação de sistemas de tempo real rígido (*hard real-time*).
- **Kernel e Sistema Operacional**: Linux com patch `PREEMPT_RT`, subsistemas de grafos PipeWire / JACK, arquitetura interna de drivers ALSA.
- **Rede e Sincronização**: Protocolo de temporização precisa IEEE 1588 PTPv2, AES67, RTP/RTSP, difusão seletiva (*multicast*) sobre sockets brutos.
- **Investigações Atuais**: Rotinas DSP vetorizadas em RISC-V RVV 1.0 e ARM NEON, filas SPMC sem travas e sem espera (*wait-free*).

---

## 🛠 Conjunto de Ferramentas Técnicas

| Categoria | Tecnologias Principais e Ferramental |
| :--- | :--- |
| **Linguagens Centrais** | Rust, C++20, ISO C99, Go, Zig, Python |
| **Baixo Nível e DSP** | SIMD (AVX2, AVX-512, NEON), Aritmética de ponto fixo, FFTW, libsndfile, CLAP / VST3 SDK |
| **Subsistemas de Áudio** | Plugins PipeWire SPA, JACK Audio Connection Kit, API de Kernel ALSA, WebAudio |
| **Tempo Real e Concorrência** | Buffers circulares *lock-free*, primitivas atômicas, alinhamento de linha de cache, ajustes finos de `PREEMPT_RT` |
| **Perfilamento e Diagnóstico** | `perf`, `ebpf` (bpftrace), Valgrind / Massif, Wireshark (Audio over IP), Osciloscópios |
| **Sistemas de Build e CI** | Cargo, CMake, Ninja, Make, GCC, Clang/LLVM, GitHub Actions CI |

---

## 🚀 Principais Projetos de Código Aberto

```
+-----------------------------------------------------------------------------+
| PROJETO       | LINGUAGEM | FINALIDADE                                      |
+-----------------------------------------------------------------------------+
| wave-mesh     | Rust      | Barramento de roteamento de áudio em rede zero-copy |
| synth-core    | C++20     | Sintetizador modular polifônico acelerado por SIMD  |
| jitter-guard  | Rust / C  | Monitor CLI de jitter e desvio de relógio PTPv2 (µs)|
| dsp-bench     | Zig / C   | Benchmark multiplataforma para filtros IIR / FIR    |
+-----------------------------------------------------------------------------+
```

### 1. `wave-mesh`
- **Arquitetura**: Daemon assíncrono de áudio *zero-copy* para transporte contínuo de 64 canais a 24-bit/96kHz em redes locais Ethernet convencionais sem falhas de amostragem.
- **Destaques**: Transporte via memória compartilhada baseada em anéis de buffer, buffer de jitter com correção micrométrica de derivação e adaptadores nativos para PipeWire e JACK.
- **Tecnologias**: Rust, Linux `AF_PACKET`, `epoll`, PipeWire SPA.

### 2. `synth-core`
- **Arquitetura**: Biblioteca modular DSP em tempo real desenvolvida para ambientes de plugins e módulos físicos Eurorack.
- **Destaques**: Osciladores de banda limitada (BLIT/BLEP), filtros com realimentação sem atraso (ZDF) sobreamostrados e garantia absoluta de zero alocações dinâmicas de heap na thread de áudio.
- **Tecnologias**: C++20, intrínsecos SIMD AVX2 / ARM NEON, CMake.

### 3. `jitter-guard`
- **Arquitetura**: Ferramenta de telemetria passiva que monitora pacotes IEEE 1588 PTP e registros temporais de hardware da placa de rede (`SO_TIMESTAMPING`) para avaliar variações de latência (PDV).
- **Destaques**: Interface TUI gráfica em terminal, exportação estruturada em JSONL para estudos cronológicos e alertas antecipados de perda de sincronia.
- **Tecnologias**: Rust, `crossterm`, API Linux Socket Timestamping.

### 4. `dsp-bench`
- **Arquitetura**: Utilitário enxuto em linha de comando que mensura a quantidade de ciclos de processador consumidos por amostra calculada em diferentes arquiteturas CPU.
- **Destaques**: Suporte abrangente a x86_64, aarch64 e riscv64 com leitura direta de registradores de desempenho do processador.
- **Tecnologias**: Zig, C99, GNU Make.

---

## 📐 Diretrizes de Engenharia

1. **Determinisno de execução acima de tudo**: Em processamento de áudio em tempo real, dado atrasado é dado corrompido. Alocações de memória dinâmica, travas do tipo mutex e chamadas bloqueantes de sistema jamais devem penetrar o loop de renderização sonora.
2. **Design explícito sobre suposições implícitas**: Eficiência não é adicionada como um ajuste tardio. A disposição de memória favorável à memória cache, a união compacta de estruturas e a posse estrita dos dados guiam a arquitetura de software.
3. **Medir concretamente em vez de especular**: Nenhuma decisão de refatoração se apoia em palpites. Medidores precisos de hardware, contadores de ciclos de máquina e rastros de eBPF validam cada etapa do código.
4. **Resiliência construída pela simplicidade**: Protocolos superdimensionados ruem primeiro quando submetidos a redes ruidosas ou cortes de energia. Pacotes limpos e minimalistas sustentam a estabilidade nos cenários reais mais severos.

---

## 📈 Fluxo Operacional do Sistema

```
[Loop Principal de Eventos]
  │
  ├── 1. Ingress de Áudio e Recepção de Pacotes da Interface de Rede
  │      └── Fila Lock-Free de Produtor Único e Múltiplos Consumidores (SPMC)
  │
  ├── 2. Computação DSP Vetorizada (Instruções SIMD / Sem Alocação Dinâmica)
  │      └── Pipeline de processamento em blocos fixos (16..128 amostras)
  │
  └── 3. Transmissão ao Barramento de Hardware / Buffer DMA da Placa de Som
         └── Homologação de Zero Estalos (Zero Xruns) sob Linux PREEMPT_RT
```

---

## 📬 Conexões e Diálogo Técnico

- **GitHub Issues**: Sinta-se à vontade para registrar ideias, relatórios de anomalias ou debates técnicos nos respectivos repositórios.
- **Colaboração em Engenharia**: Aberto a discussões aprofundadas sobre Audio over IP (AoIP), sintonia fina de kernels Linux para baixa latência e engenharia de drivers de áudio.
- **Identificador de Chave GPG**: `0x3F8B2E17A4D091C5` (validação de autenticidade de commits)
