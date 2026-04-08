---
layout: post
title: "Dredge: AI-Powered Binary Vulnerability Discovery"
---

Automated firmware vulnerability scanner. Point it at a binary or firmware image — it decompiles with Ghidra, triages with fast heuristics, and sends the most interesting functions to Claude for deep analysis.

![Pipeline](/assets/images/projects/dredge-demo.svg)

The core problem is scale. A typical firmware image contains dozens of binaries with thousands of functions. Sending every function to an LLM is prohibitively expensive and slow. Dredge solves this with a 6-stage pipeline that progressively filters: extract the filesystem, discover ELF binaries, decompile with Ghidra headless, score every function with fast heuristics, analyze only the high-scoring functions with Claude, then correlate findings into attack chains.

## Heuristic Triage

The triage stage scores each function 0-100 before any LLM call. Universal dangerous sinks like `system()` and `gets()` add 20-25 points. Input handling patterns (HTTP headers, network reads, CGI) add 15. Hardcoded credentials or format string patterns add 15-20. Profile-specific sinks and domain keywords contribute additional points tuned to the target domain. Functions below the threshold (default 30) are skipped entirely. This filters out ~95% of functions — library code, compiler stubs, and low-risk helpers — before the expensive analysis stage.

Penalties matter too. Functions with more than 20 callers lose 5 points because high fan-in suggests library code. Very small functions (under 50 characters of decompiled output) score zero. Compiler-generated functions (`__*`, `_INIT`, `_FINI`) are excluded outright.

## Domain Profiles

Four profiles customize every pipeline stage for different firmware targets. The drone profile adds MAVLink handler detection, failsafe bypass patterns, and GPS spoofing vulnerability classes. The satellite profile watches for telecommand spoofing, safe mode bypass, and CCSDS protocol handling. Each profile contributes high-priority binary names for discovery scoring, domain-specific dangerous sinks for triage, input pattern regexes, and a tuned system prompt that guides Claude's analysis toward domain-relevant threats.

The profile system means adding a new domain requires no pipeline changes — just a new dataclass with the appropriate heuristics, patterns, and prompt context.

## Attack Chain Correlation

Individual findings are useful, but correlated findings are critical. The correlator groups findings by binary, deduplicates by vulnerability class, and detects attack chains. When hardcoded credentials and an auth bypass appear in the same binary, both findings get a severity boost because together they represent a complete exploitation path. High-confidence command injection findings receive an additional boost. The result is a prioritized list where the most dangerous combinations surface first.

## Architecture Decisions

Ghidra runs in headless mode as subprocesses with configurable parallelism and a 300-second timeout per binary. The Claude API calls are async with a semaphore capping concurrency at 5 requests, exponential backoff for rate limits, and per-function result caching. The entire pipeline is resumable — Ctrl+C triggers a graceful save, and `dredge rescan` picks up where it left off. State lives in SQLite with WAL mode for concurrent reads during analysis.

---

[View on GitHub](https://github.com/HueCodes/Dredge)
