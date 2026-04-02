---
layout: hardware
title: Underwater Acoustic Modem
status: updates soon
github:
---

Short-range underwater acoustic communication using **ESP32** and BFSK modulation. Intended for UUV (unmanned underwater vehicle) command links.

## Architecture

```
                TRANSMITTER                              RECEIVER
 +----------+    +----------+    +-----------+    +-------+    +----------+
 | ESP32 TX |--->| TB6612FNG|--->| Transducer|~~~~| Sensor|--->| ESP32 RX |
 | (PWM)    |    | (Amp)    |    | /Speaker  |    | /Hydro|    | (ADC)    |
 +----------+    +----------+    +-----------+    +-------+    +----------+
```

## Modulation

**BFSK** -- binary frequency-shift keying. Two tones:

| Symbol | Frequency |
|--------|-----------|
| 0 | 10 kHz |
| 1 | 12 kHz |

Raw rate: ~1000 bps (1 ms/symbol). ~500 bps effective with FEC overhead.

## Demodulation

Goertzel algorithm -- a single-bin DFT that computes the energy at exactly two target frequencies per sample window. Much cheaper than a full FFT when you only care about two bins.

## Frame Format

```
[Preamble 8 sym][Length 1B][Payload 0-255B][CRC-16 2B]
```

FEC: Hamming(7,4) initially, Reed-Solomon for higher BER environments.

## Hardware

| Component | Status |
|-----------|--------|
| ESP32 DevKitC-32 (3x) | Owned |
| TB6612FNG motor driver (2x) | Owned |
| Sound sensor module | Owned |
| Logic analyzer | Owned |
| Piezoelectric transducer | Needed (~$15) |

## Milestones

1. Air loopback -- speaker to sound sensor, verify tone generation and Goertzel detection
2. Air framing -- complete frames through air with preamble sync, CRC, Hamming decode
3. Bathtub test -- waterproof sensor, characterize SNR and multipath
4. Pool test -- 1-5m range, measure BER vs distance
5. FEC upgrade -- Reed-Solomon if BER warrants it
6. Bidirectional half-duplex link
