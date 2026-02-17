---
layout: post
title: "Entropy in Packet Analysis"
date: 2026-02-03
category: posts
---

Using information theory to detect network attacks through statistical signatures.

Network traffic has predictable randomness patterns. Normal traffic follows human behavior and protocol structures. Malicious traffic betrays itself through anomalous entropy characteristics.

## The Detection Mechanism

Attackers face a dilemma: use predictable patterns and get fingerprinted, or use random patterns and get flagged for high entropy. This asymmetry makes entropy an effective detection tool without requiring specific attack signatures.

### Entropy Signatures

Different traffic types have characteristic entropy levels:

| Traffic Feature | Normal | Attack |
|-----------------|--------|---------|
| Source IPs | 4-8 bits (clustered) | 32 bits (spoofed) or <3 bits (single source) |
| Destination Ports | Low (common services) | High (port scan) |
| Payload Bytes | 4-6 bits (plaintext) | 7.5-8 bits (encrypted C2) |

Shannon entropy formula:

$$H(X) = -\sum_{i=1}^{n} p(x_i) \log_2 p(x_i)$$

Implementation for network data:

```python
import math
from collections import Counter

def calculate_entropy(data):
    if not data:
        return 0.0

    counts = Counter(data)
    total = len(data)

    entropy = 0.0
    for count in counts.values():
        if count > 0:
            p = count / total
            entropy -= p * math.log2(p)

    return entropy

def byte_entropy(payload):
    # Max entropy: 8 bits (256 possible bytes)
    # Encrypted: 7.5-8.0 bits
    # Plaintext: 4-6 bits
    return calculate_entropy(list(payload))
```

## DDoS Detection

### Spoofed IP Floods

Random spoofing creates maximum entropy (normalized ~1.0). Normal traffic rarely exceeds 0.7.

```python
def detect_spoofed_flood(packets, threshold=0.85):
    src_ips = [p['src_ip'] for p in packets]
    unique_ratio = len(set(src_ips)) / len(src_ips)
    norm_entropy = calculate_entropy(src_ips) / math.log2(len(set(src_ips)))

    return norm_entropy > threshold and unique_ratio > 0.9
```

### Amplification Attacks

Distinctive asymmetric signature:
- Source IP entropy: very low (few amplifiers)
- Source port entropy: very low (service port)
- Volume: massive

```python
def detect_amplification(packets):
    src_ips = [p['src_ip'] for p in packets]
    src_ports = [p['src_port'] for p in packets]

    return (calculate_entropy(src_ips) < 3.0 and
            calculate_entropy(src_ports) < 1.0)
```

## Port Scanning

Sequential scans produce predictable sequences with near-zero entropy. Random scans show high port entropy with high failure rates.

```python
def detect_port_scan(packets):
    dst_ports = [p['dst_port'] for p in packets]
    failures = sum(1 for p in packets if p.get('tcp_flags') == 'RST')

    port_entropy = calculate_entropy(dst_ports)
    unique_ratio = len(set(dst_ports)) / len(dst_ports)
    fail_ratio = failures / len(packets)

    return (port_entropy > 8.0 and
            unique_ratio > 0.8 and
            fail_ratio > 0.7)
```

## Payload Analysis

Content type reveals itself through byte entropy:

| Content | Entropy |
|---------|---------|
| Encrypted (TLS, SSH) | 7.5-8.0 bits |
| Compressed | 7.0-7.9 bits |
| Executables | 5.0-7.0 bits |
| Plaintext | 4.0-5.0 bits |
| Base64 | 5.9-6.0 bits |

DNS tunneling detection:

```python
def detect_dns_tunnel(queries, threshold=4.5):
    suspicious = []
    for query in queries:
        subdomain = query.split('.')[0]
        char_entropy = calculate_entropy(list(subdomain))

        if char_entropy > threshold and len(subdomain) > 50:
            suspicious.append(query)

    return suspicious
```

Packed malware shows uniformly high entropy (7.0+ bits) across all sections. Normal executables have varied section entropy.

## Baseline Detection

Effective detection requires learning normal patterns:

```python
class EntropyDetector:
    def __init__(self, baseline_mean, baseline_std, z_threshold=3.0):
        self.baseline_mean = baseline_mean
        self.baseline_std = baseline_std
        self.z_threshold = z_threshold

    def analyze(self, packets):
        src_ips = [p['src_ip'] for p in packets]
        entropy = calculate_entropy(src_ips)

        z_score = (entropy - self.baseline_mean) / self.baseline_std

        if z_score > self.z_threshold:
            return 'anomaly_detected', entropy, z_score
        return 'normal', entropy, z_score
```

Sliding window for real-time detection:

```python
from collections import deque, Counter

class SlidingEntropyWindow:
    def __init__(self, window_size):
        self.window = deque(maxlen=window_size)
        self.counts = Counter()

    def add(self, value):
        if len(self.window) == self.window.maxlen:
            old = self.window[0]
            self.counts[old] -= 1
            if self.counts[old] == 0:
                del self.counts[old]

        self.window.append(value)
        self.counts[value] += 1

        return self.entropy()

    def entropy(self):
        total = len(self.window)
        if total == 0:
            return 0.0

        h = 0.0
        for count in self.counts.values():
            p = count / total
            h -= p * math.log2(p)
        return h
```

## Advanced Techniques

KL divergence measures distribution shift from baseline:

$$D_{KL}(P \| Q) = \sum_{x} P(x) \log_2 \frac{P(x)}{Q(x)}$$

Conditional entropy reveals correlation breakdown during attacks. Normal traffic: knowing source IP predicts destination port. Attack traffic: correlation breaks.

## Limitations

Entropy is not perfect:
- Flash crowds vs DDoS both show high source entropy
- CDNs aggregate users behind few IPs (looks like attacks)
- VPN/Tor exits create similar patterns
- Sophisticated attackers can craft matching entropy profiles

Use entropy as one signal among many. Combine with behavioral models, reputation data, and ML for robust detection.

## Conclusion

Entropy detects attacks through statistical fingerprints. Different attack types have distinct randomness signatures. The method works because attackers cannot hide without creating either predictable patterns or detectable randomness.

Key insight: measuring fundamental statistical properties catches attacks that evade signature-based detection. No need to know the specific attack. Measure deviation from expected behavior.

Practical application: baseline normal entropy, detect deviations via z-scores, classify attack type by signature pattern. Works for DDoS, port scans, tunneling, and malware.

---
