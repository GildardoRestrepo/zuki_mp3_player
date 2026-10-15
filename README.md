---
type: readme
created: 2026-10-13
---
# Reproductor MP3 — Mark 1

> [!success] Resumen
> Repositorio del desarrollo por etapas de un reproductor MP3 portátil basado en ESP32. La etapa **Mark 1** busca un prototipo funcional, soldado y con carcasa impresa en 3D para el **2026-11-05**.

## 1. Visión del proyecto

| Horizonte                  | Objetivo                                                                                                       |
| -------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Producto (largo plazo)** | Un reproductor portátil con prestaciones y calidad de audio comparables a reproductores comerciales tipo iPod. |
| **Aprendizaje**            | Diseño electrónico, diseño 3D en Fusion 360, firmware para sistemas embebidos y electrónica de audio.          |
| **Mark 1 (este hito)**     | Prototipo funcional: reproduce MP3 desde microSD por audífonos, con batería, controles y carcasa.              |

El proyecto avanza por versiones (*marks*). Cada mark cierra con una etiqueta de git (`mark1`, `mark2`, …) que congela firmware, electrónica y mecánica en un estado que funciona.

---
## 2. Alcance de Mark 1

### 2.1 Qué incluye

- Reproducción de archivos `.mp3` desde una tarjeta microSD.
- Salida analógica por DAC externo I2S con jack de 3,5 mm para audífonos.
- Alimentación por batería LiPo de una celda con carga por USB.
- Interfaz simple: botones (reproducir/pausa, siguiente, anterior, volumen) y, opcionalmente, una pantalla pequeña.
- Montaje soldado (placa perforada o PCB sencilla).
- Carcasa diseñada en Fusion 360 e impresa en 3D.

### 2.2 Qué queda fuera (para marks posteriores)

- Formatos sin pérdida (FLAC/WAV) como objetivo principal, base de datos de biblioteca, navegación por artista/álbum.
- Bluetooth, gestión avanzada de energía y medición de autonomía.
- PCB propia de 4 capas, etapa de amplificación dedicada, optimización de ruido.

---
## 3. Arquitectura (borrador)

```text
             ┌──────────────┐  SPI / SDMMC  ┌──────────┐
             │              │◄─────────────►│ microSD  │
 Botones ───►│    ESP32     │               └──────────┘
 Pantalla ◄─►│  (decodif.   │   I2S   ┌───────┐   ┌──────────────┐
  (I2C/SPI)  │    MP3)      │────────►│  DAC  │──►│ Jack 3,5 mm  │
             └──────▲───────┘         └───────┘   └──────────────┘
                    │ 3V3
             ┌──────┴───────┐   ┌────────────┐
             │  Regulador   │◄──│ LiPo 1S +  │◄── USB 5 V
             │   3,3 V      │   │ cargador   │
             └──────────────┘   └────────────┘
```

---
## 4. Lista de materiales (preliminar)

> La selección final se acuerda en un ADR (`_docs/adr/`). Los candidatos son referencias de partida.

| Bloque | Componente | Estado | Notas |
| ------ | ---------- | ------ | ----- |
| MCU | ESP32 (variante por definir) | por definir | La librería `ESP32-audioI2S` recomienda chips multinúcleo como el ESP32-S3; el ESP32 original es solo parcialmente adecuado. |
| DAC + jack | Referencia por confirmar | por definir | Candidatos comunes: PCM5102A, UDA1334A. |
| Almacenamiento | Lector microSD | por definir | SPI o SDMMC de 1 bit. |
| Batería | LiPo 1S 3,7 V | por definir | Capacidad según volumen de la carcasa. |
| Carga | Módulo cargador LiPo (p. ej. TP4056 con protección) | por definir | |
| Regulación | LDO o buck-boost a 3,3 V | por definir | |
| Interfaz | Botones táctiles / pantalla OLED o TFT | por definir | |
| Carcasa | Impresión 3D (PLA/PETG) | por definir | Diseño en Fusion 360. |

---
## 5. Estructura del repositorio

```text
02_mp3/
├── README.md              ← este documento
├── LICENSE                ← MIT
├── _docs/                 ← documentación (guía de estilo, roadmap, ADRs, pruebas)
├── datasheets/            ← hojas de datos de los componentes
├── electronics_design/    ← esquemáticos y PCB
├── mechanical_3d/         ← carcasa (Fusion 360, STEP, STL)
└── src/                   ← firmware del ESP32
```

---
## 6. Hoja de ruta de Mark 1

| Fase | Entregable | Área | Fecha objetivo |
| ---- | ---------- | ---- | -------------- |
| 0 | Repositorio organizado, BOM y componentes elegidos (ADR) | gestion | 2026-10-17 |
| 1 | Prueba en protoboard: ESP32 + microSD + DAC suenan por el jack | audio / firmware | 2026-10-22 |
| 2 | Firmware básico: lista de reproducción, botones, volumen, pantalla | firmware | 2026-10-27 |
| 3 | Alimentación: LiPo, carga USB, encendido/apagado | electronica | 2026-10-29 |
| 4 | Montaje soldado y pruebas del circuito final | electronica | 2026-11-01 |
| 5 | Carcasa 3D, integración y etiqueta `mark1` | 3d | 2026-11-05 |

---
## 7. Cómo empezar

### 7.1 Firmware

*Pendiente: se definirá con la elección del entorno (PlatformIO recomendado).*

```bash
# Ejemplo previsto con PlatformIO
pio run -t upload
pio device monitor
```

### 7.2 Preparar la tarjeta microSD

1. Formatear en FAT32.
2. Copiar los archivos `.mp3` (estructura de carpetas por definir).

---
## 8. Proyectos de referencia

| Proyecto                                                                           | Por qué sirve                                                                                                     |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| [Tangara (Cool Tech Zone)](https://sr.ht/~jacqueline/tangara/)                     | Reproductor ESP32 abierto de nivel producto: DAC WM8523, rueda táctil, batería 2200 mAh. Referencia para escalar. |
| [JPL.mp3](https://github.com/JPLabsystems/JPL.mp3)                                 | Estilo iPod con ESP32-S3, PCM5102, KiCad 4 capas, PlatformIO y carcasa impresa.                                   |
| [ESP32_MP3_Player](https://github.com/Anand-Dev7/ESP32_MP3_Player)                 | Versión mínima con UDA1334A, OLED y 3 botones. Buen punto de partida para la Fase 1.                              |
| [ESP32-audioI2S](https://github.com/schreibfaul1/ESP32-audioI2S)                   | Librería de decodificación (MP3, AAC, FLAC…) con salida I2S.                                                      |
| [arduino-audio-tools](https://github.com/pschatzmann/arduino-audio-tools)          | Librería alternativa de procesamiento de audio, códecs y ecualizador.                                             |

---
## 9. Licencia

Distribuido bajo licencia MIT. Ver `LICENSE`.

---
## Enlaces
[[mp3|Reproductor MP3]]
