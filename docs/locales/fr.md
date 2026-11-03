| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Eugene Park (@znbobziz-ship-it)

> Ingénieur systèmes spécialisé dans l'audio réseau à faible latence, le noyau Linux temps réel et les architectures DSP déterministes.

Je conçois et développe des logiciels fonctionnant au plus près des horloges matérielles et des couches physiques de transmission. Mon quotidien s'articule autour de la concurrence sans verrou (*lock-free*), de la sérialisation de paquets sans copie inutile (*zero-copy*) et de moteurs de graphes audio où la perte d'un seul échantillon est inacceptable.

---

## 🧭 Contexte et Domaines d'Expertise

- **Spécialité**: Audio distribué ultra-faible latence, microprogrammes DSP embarqués, programmation système temps réel dur.
- **Noyau & OS**: Linux avec patch `PREEMPT_RT`, sous-systèmes de graphes PipeWire / JACK, architecture interne des pilotes ALSA.
- **Réseau & Synchronisation**: Protocole de synchronisation d'horloge IEEE 1588 PTPv2, AES67, RTP/RTSP, multidiffusion sur sockets bruts (*raw sockets*).
- **Recherches Actuelles**: Routines DSP vectorisées sur RISC-V RVV 1.0 et ARM NEON, files SPMC sans attente ni verrou (*wait-free*).

---

## 🛠 Boîte à Outils Technologique

| Domaine | Technologies et Outils Privilégiés |
| :--- | :--- |
| **Langages Principaux** | Rust, C++20, ISO C99, Go, Zig, Python |
| **Bas Niveau & DSP** | SIMD (AVX2, AVX-512, NEON), Arithmétique en virgule fixe, FFTW, libsndfile, CLAP / VST3 SDK |
| **Sous-Systèmes Audio** | Plugins PipeWire SPA, JACK Audio Connection Kit, API noyau ALSA, WebAudio |
| **Temps Réel & Concurrence** | Bipasseurs annulaires *lock-free*, primitives atomiques, alignement de lignes de cache, réglages `PREEMPT_RT` |
| **Profilage & Diagnostic** | `perf`, `ebpf` (bpftrace), Valgrind / Massif, Wireshark (Audio over IP), Oscilloscopes |
| **Chaîne de Compilation** | Cargo, CMake, Ninja, Make, GCC, Clang/LLVM, GDB/LLDB, ASan/UBSan, Clang-Tidy |

---

## 🚀 Principaux Projets Open-Source

```
+-----------------------------------------------------------------------------+
| PROJET        | LANGAGE   | OBJECTIF                                        |
+-----------------------------------------------------------------------------+
| wave-mesh     | Rust      | Bus de routage audio réseau multipoint zéro-copie|
| synth-core    | C++20     | Moteur de synthèse modulaire polyphonique SIMD  |
| jitter-guard  | Rust / C  | Moniteur en ligne de commande de gigue PTPv2    |
| dsp-bench     | Zig / C   | Banc d'essai multiplateforme de filtres IIR/FIR |
+-----------------------------------------------------------------------------+
```

### 1. `wave-mesh`
- **Architecture**: Démon audio asynchrone *zero-copy* transportant 64 canaux à 24-bit/96kHz sur des réseaux Ethernet locaux sans dégradation d'échantillons.
- **Points forts**: Transport en mémoire partagée via tampons circulaires, compensation de dérive temporelle à l'échelle de la microseconde et passerelles natives PipeWire/JACK.
- **Technologies**: Rust, Linux `AF_PACKET`, `epoll`, PipeWire SPA.

### 2. `synth-core`
- **Architecture**: Bibliothèque DSP modulaire temps réel destinée aux hôtes de plugins audionumériques et modules physiques Eurorack.
- **Points forts**: Oscillateurs à bande limitée (BLIT/BLEP), filtres à rétroaction sans délai (ZDF) avec suréchantillonnage, et garantie formelle de zéro allocation dynamique sur le fil audio.
- **Technologies**: C++20, intrinsèques SIMD AVX2 / ARM NEON, CMake.

### 3. `jitter-guard`
- **Architecture**: Outil d'analyse télémétrique passif écoutant les trames IEEE 1588 PTP et les horodatages matériels (`SO_TIMESTAMPING`) pour quantifier la gigue de transmission (PDV).
- **Points forts**: Visualisation interactive en terminal (TUI), export continu au format JSONL et alertes d'écart de synchronisation d'horloge.
- **Technologies**: Rust, `crossterm`, API Linux Socket Timestamping.

### 4. `dsp-bench`
- **Architecture**: Outil en ligne de commande évaluant l'efficacité en cycles par échantillon d'algorithmes de filtrage DSP sur diverses architectures CPU.
- **Points forts**: Prise en charge native de x86_64, aarch64 et riscv64 avec lecture directe des compteurs de performance matériels.
- **Technologies**: Zig, C99, GNU Make.

---

## 📐 Philosophie d'Ingénierie

1. **Priorité absolue au déterminisme**: Dans le traitement audio en temps réel, une donnée en retard est une donnée corrompue. Les allocations dynamiques, les verrous d'exclusion mutuelle et les appels système bloquants sont formellement exclus de la boucle de rendu audio.
2. **L'explicite prévaut sur l'implicite**: La haute performance ne se greffe pas a posteriori. L'alignement mémoire adapté aux mémoires caches, le compactage de structures et l'isolation stricte de propriété des données structurent le logiciel.
3. **Mesurer concrètement, ne jamais spéculer**: Aucune décision d'optimisation ne repose sur des conjectures. Seules les horloges matérielles, les compteurs de cycles et les traces eBPF valident les choix de conception.
4. **La simplicité crée la robustesse**: Les protocoles surchargés cèdent les premiers lors d'aléas réseau ou de micro-coupures de courant. Seules les structures modulaires et épurées survivent aux conditions de production exigeantes.

---

## 📈 Pipeline d'Exécution Système

```
[Boucle Événementielle du Système]
  │
  ├── 1. Entrée Audio et Réception des Trames Réseau
  │      └── File d'Attente sans Verrou Simple Producteur / Multi-Consommateurs (SPMC)
  │
  ├── 2. Traitement DSP Vectorisé (Instructions SIMD / Mémoire Statique)
  │      └── Pipeline par blocs de taille constante (16..128 échantillons)
  │
  └── 3. Émission vers le Bus Matériel / Tampon DMA de la Carte Son
         └── Zéro Décrochage Audio (Zero Xruns) validé sous Linux PREEMPT_RT
```

---

## 📬 Contacts & Échanges Techniques

- **GitHub Issues**: Les contributions, signalements de bogues et discussions architecturales sont les bienvenus sur les dépôts respectifs.
- **Échanges Professionnels**: Disponible pour débattre de technologies Audio sur IP (AoIP), de réglages de latence sous Linux et d'ingénierie de pilotes temps réel.
- **Identifiant Clé GPG**: `0x3F8B2E17A4D091C5` (vérification d'authenticité des commits)
