| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Eugene Park (@znbobziz-ship-it)

> Ingeniero de sistemas especializado en audio en red de baja latencia, kernels Linux de tiempo real e infraestructura DSP determinista.

Diseño e implemento software que opera en la frontera directa entre los relojes de hardware y las interfaces físicas de red. Mi labor diaria se centra en la concurrencia sin bloqueos (*lock-free*), la serialización de paquetes sin copias redundantes (*zero-copy*) y los motores de grafos de audio donde no se puede tolerar la pérdida de una sola muestra.

---

## 🧭 Trayectoria y Enfoque

- **Especialidad**: Audio distribuido de baja latencia, firmware DSP embebido, programación de sistemas de tiempo real estricto.
- **Kernel y SO**: Linux con parche `PREEMPT_RT`, subsistemas de grafos PipeWire / JACK, arquitectura interna de controladores ALSA.
- **Redes y Sincronización**: Protocolo de precisión temporal IEEE 1588 PTPv2, AES67, RTP/RTSP, multidifusión sobre sockets crudos (*raw sockets*).
- **Intereses Actuales**: Rutinas DSP vectorizadas en arquitecturas RISC-V RVV 1.0 y ARM NEON, colas SPMC sin bloqueos y sin esperas (*wait-free*).

---

## 🛠 Pila Tecnológica

| Categoría | Tecnologías Principales y Herramientas |
| :--- | :--- |
| **Lenguajes Clave** | Rust, C++20, ISO C99, Go, Zig, Python |
| **Bajo Nivel y DSP** | SIMD (AVX2, AVX-512, NEON), Aritmética de punto fijo, FFTW, libsndfile, CLAP / VST3 SDK |
| **Subsistemas de Audio** | Plugins PipeWire SPA, JACK Audio Connection Kit, API de Kernel ALSA, WebAudio |
| **Tiempo Real y Concurrencia** | Búferes circulares *lock-free*, primitivas atómicas, alineación de líneas de caché, optimización de `PREEMPT_RT` |
| **Perfilado y Diagnóstico** | `perf`, `ebpf` (bpftrace), Valgrind / Massif, Wireshark (Audio sobre IP), Osciloscopios |
| **Compilación y CI** | Cargo, CMake, Ninja, Make, GCC, Clang/LLVM, GitHub Actions CI |

---

## 🚀 Proyectos Destacados de Código Abierto

```
+-----------------------------------------------------------------------------+
| PROYECTO      | LENGUAJE  | PROPÓSITO                                       |
+-----------------------------------------------------------------------------+
| wave-mesh     | Rust      | Bus de enrutamiento de audio en red sin copias  |
| synth-core    | C++20     | Motor sintetizador modular polifónico con SIMD  |
| jitter-guard  | Rust / C  | Monitor CLI de deriva de reloj PTPv2 (µs)       |
| dsp-bench     | Zig / C   | Comparador multiplataforma de filtros IIR / FIR |
+-----------------------------------------------------------------------------+
```

### 1. `wave-mesh`
- **Arquitectura**: Demonio asíncrono de audio *zero-copy* para transportar 64 canales a 24-bit/96kHz a través de redes Ethernet locales estándar sin pérdidas.
- **Características**: Transporte mediante memoria compartida con búferes circulares, búfer de fluctuación con compensación de microsegundos y puentes nativos para PipeWire y JACK.
- **Pila**: Rust, Linux `AF_PACKET`, `epoll`, PipeWire SPA.

### 2. `synth-core`
- **Arquitectura**: Biblioteca DSP modular en tiempo real diseñada para anfitriones de plugins y módulos físicos Eurorack.
- **Características**: Osciladores de banda limitada (BLIT/BLEP), filtros de realimentación sin retardo (ZDF) con sobremuestreo y garantía de cero asignaciones dinámicas de memoria en el hilo de audio.
- **Pila**: C++20, intrínsecos SIMD AVX2 / ARM NEON, CMake.

### 3. `jitter-guard`
- **Arquitectura**: Herramienta de telemetría que escucha pasivamente paquetes IEEE 1588 PTP y marcas temporales de hardware (`SO_TIMESTAMPING`) para medir la variación del retardo de paquetes (PDV).
- **Características**: Interfaz TUI en terminal en tiempo real, exportación a formato JSONL para análisis de tendencias y disparadores de alerta ante desincronización de reloj.
- **Pila**: Rust, `crossterm`, Linux Socket Timestamping API.

### 4. `dsp-bench`
- **Arquitectura**: Herramienta de línea de comandos para evaluar la eficiencia en ciclos por muestra de algoritmos DSP de audio sobre diversas arquitecturas de microprocesador.
- **Características**: Compatible con x86_64, aarch64 y riscv64 con lectura directa de contadores de ciclos de hardware.
- **Pila**: Zig, C99, GNU Make.

---

## 📐 Principios de Ingeniería

1. **La ejecución determinista es primordial**: En el procesamiento de audio en tiempo real, un dato tardío es un dato corrupto. Las reservas de memoria dinámica, los bloqueos por mutex y las llamadas al sistema bloqueantes nunca deben entrar en el hilo de renderizado de audio.
2. **Claridad explícita sobre magia implícita**: El rendimiento óptimo no se improvisa al final. La disposición de memoria favorable a la caché, el empaquetado de estructuras y la titularidad unívoca de los datos definen la arquitectura.
3. **Medir sistemáticamente en lugar de suponer**: Cada decisión de optimización y refactorización se valida mediante temporizadores de hardware, contadores de ciclos y trazas eBPF, nunca por mera intuición.
4. **Simplicidad para ganar resiliencia**: Los protocolos hipercomplejos colapsan rápidamente en entornos reales imprevistos. Las estructuras de paquetes limpias y modulares resisten cortes de energía y redes degradadas.

---

## 📈 Flujo de Procesamiento del Sistema

```
[Bucle Principal de Eventos]
  │
  ├── 1. Ingesta de Audio y Recepción de Paquetes de Red
  │      └── Cola Lock-Free de Productor Único y Múltiples Consumidores (SPMC)
  │
  ├── 2. Procesamiento Vectorizado DSP (Instrucciones SIMD / Sin asignaciones)
  │      └── Canalización por bloques fijos (16..128 tramas)
  │
  └── 3. Transmisión a Bus de Hardware / Búfer DMA de Tarjeta de Sonido
         └── Comprobación de Cero Pérdidas de Muestras (Zero Xruns) en Linux PREEMPT_RT
```

---

## 📬 Contacto y Colaboración

- **GitHub Issues**: Se invita a abrir hilos de debate y reportes técnicos en los repositorios correspondientes.
- **Intercambio Técnico**: Abierto a conversaciones sobre Audio sobre IP (AoIP), ajuste de latencia en Linux y desarrollo de controladores de dispositivos.
- **Clave GPG**: `0x3F8B2E17A4D091C5` (para verificación de firmas en commits)
