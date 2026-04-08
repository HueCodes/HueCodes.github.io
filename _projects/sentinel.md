---
layout: post
title: "Sentinel: Embedded SIEM Log Pipeline"
---

Lightweight SIEM that ingests security events from multiple sources, normalizes them into a common schema, enriches with GeoIP and threat intelligence, detects patterns with TOML-driven rules, and correlates across sources to reconstruct kill chains. Single binary, no external dependencies.

![Pipeline](/assets/images/projects/sentinel-pipeline.svg)

## Normalization and Enrichment

Events arrive from seven sources — Suricata EVE alerts, Cowrie honeypot logs, Network-Beacon C2 detection, syslog (RFC 5424/3164), Windows Event Log, CEF, and arbitrary JSON — via file watching, UDP syslog, HTTP POST, or stdin. Each source has a dedicated parser that maps fields into a unified schema: timestamps, source/destination IPs, severity, category, action, outcome, MITRE ATT&CK references, and the original raw event preserved for forensics.

Enrichment runs in four stages. GeoIP lookups populate country, city, coordinates, and ASN for non-private IPs using MaxMindDB. Threat intelligence feeds (IP lists with CIDR support, domain lists, hash lists) tag matching events with `known_bad_ip`, `known_c2`, or `known_malware_hash`. MITRE ATT&CK mapping infers technique IDs from category and action heuristics when no explicit reference exists. Risk scoring combines severity weight (0.4), threat intel boost (+25), MITRE tactic score (up to 20), and indicator count (3 points each, capped at 15) into a composite 0-100 score with exponential decay over time.

## Detection Rules

Rules are defined in TOML with threshold, time window, grouping key, and field conditions. The default ruleset detects brute force (10 failed logins in 300s from the same IP), port scans (50 network events in 60s), and malware downloads on the honeypot. Custom rules follow the same format — specify category, action, outcome conditions with a threshold and window. Rules hot-reload on SIGHUP without restart. Each rule tracks its own sliding window state, pruned every 5 minutes.

## Kill Chain Correlation

Three correlation patterns run in parallel. Cross-source correlation flags when the same IP appears in both C2 beacon data and honeypot authentication attempts. Kill chain detection watches MITRE tactics progression per source IP — when three or more tactics appear in temporal sequence (e.g., initial access, execution, discovery), it fires a critical incident. Credential stuffing detection tracks username/password pairs across events and fires when the same credentials appear from three or more distinct IPs within the correlation window.

## Storage and Output

Events batch-write to an embedded DuckDB database (1000 events per batch, 5-second flush interval) with indexes on timestamp, severity, category, source, and IPs. Retention defaults to 90 days with prune-on-startup. The REST API exposes filtered queries, raw SQL, CSV/JSON export, and database backup. Alerts deliver via HMAC-signed webhooks with exponential backoff retries, syslog forwarding, or file output. The optional ratatui TUI dashboard shows a live event stream, severity distribution, top source IPs, and active incidents with keyboard navigation and severity filtering.

Prometheus metrics instrument everything: ingestion rates by source, processing latency histograms, enrichment cache hit rates, per-rule evaluation and match counts, DuckDB write latency, and webhook delivery status.

---

[View on GitHub](https://github.com/HueCodes/sentinel)
