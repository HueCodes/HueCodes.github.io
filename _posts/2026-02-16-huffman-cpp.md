---
layout: post
title: "Huffman-Cpp: Modern C++ Implementation of Huffman Coding"
date: 2026-02-16
category: projects
---

A C++17 implementation of Huffman coding for lossless data compression, demonstrating priority queue-based tree construction, smart pointer memory management, and comprehensive error handling.

![Huffman Tree](/assets/images/projects/huffman-tree.svg)

Huffman coding solves the lossless compression problem by assigning variable-length binary codes to characters based on their frequency. Characters that appear more often get shorter codes, creating optimal prefix-free encoding. The algorithm matters because it underpins common compression formats like GZIP and JPEG. For a concrete example, "hello world" compresses from 88 bits to 38 bits, a 56.82% reduction. Understanding the implementation details reveals important patterns in algorithm design, memory management, and edge case handling that apply broadly to systems programming.

![Compression Comparison](/assets/images/projects/huffman-compression.svg)

The implementation centers on three phases: frequency analysis, tree construction, and code generation. Frequency analysis counts character occurrences in a single linear pass through the input. Tree construction uses a min-heap priority queue to greedily merge the two lowest-frequency nodes repeatedly until only the root remains. Code generation traverses the tree depth-first, assigning '0' for left edges and '1' for right edges. Each phase demonstrates different algorithmic patterns. Frequency counting shows hash map usage for O(1) lookups. Priority queue operations demonstrate heap-based sorting with O(log k) insertions and deletions. Tree traversal shows recursive depth-first search with backtracking.

![Algorithm Flow](/assets/images/projects/huffman-algorithm-flow.svg)

Memory management uses C++17 smart pointers throughout. Each Node contains two std::unique_ptr children, making ownership explicit and automatic. When nodes merge during tree construction, std::move transfers ownership without copying. The tree destructor becomes trivial because unique_ptr handles recursive cleanup automatically. This is RAII in practice. No manual delete calls, no memory leaks, no double-free bugs. The tradeoff is handling std::priority_queue's const-correctness. The priority queue returns const references to prevent modifications that would violate heap invariants. Extracting elements requires const_cast because we immediately call pop(), making the const qualification overly restrictive. The alternative would be using std::make_heap with manual heap operations on a vector. The current approach is idiomatic and safe given the immediate pop().

Edge cases required careful handling. Single character inputs create degenerate trees with only one leaf. The algorithm handles this by creating an artificial parent node to maintain the binary tree structure. Without this special case, code generation would produce an empty string instead of "0" repeated. Two character inputs create the minimal tree with one parent and two leaves. All unique characters produce a balanced tree. Empty input throws std::invalid_argument immediately. These edge cases appear in testing, demonstrating why comprehensive unit tests matter.

The test suite includes 17 test cases covering round-trip encoding, compression ratio validation, prefix-free property verification, and error handling. Testing the prefix-free property explicitly checks that no code is a prefix of another by comparing all pairs. This catches implementation bugs that would create ambiguous encodings. Compression ratio tests verify that skewed frequency distributions actually compress. For example, "aaaaaaaaaaaaaaaaaaaabbbbbccd" should compress to fewer bits than the original 8 bits per character. Error handling tests verify that encoding before building the tree throws runtime_error, empty input throws invalid_argument, and truncated encoded sequences throw during decoding. These tests document the API contract and catch regressions.

Performance optimizations focus on minimizing allocations. The code generation function pre-allocates 32 bytes for the code string buffer using reserve(). This avoids repeated reallocations during tree traversal. Move semantics throughout tree construction eliminate copies of node pointers. String view parameters for encode() and decode() avoid copying input text. Hash maps provide O(1) lookups for both encoding and decoding. The overall complexity is O(n log k) for tree construction where n is text length and k is unique characters, O(n) for encoding, and O(m) for decoding where m is encoded length.

## Architecture

The project separates library implementation from CLI tool. The huffman.h header defines the public API with HuffmanTree class and Node structure. The huffman.cpp file contains the implementation. The main.cpp file provides a command-line interface for file compression. This separation allows using the Huffman tree as a library without depending on file I/O code. Tests link against the library directly, not the CLI executable. The CMakeLists.txt configuration produces both a static library and the CLI tool.

The encoding representation uses strings of '0' and '1' characters instead of packed bits. This is intentional for educational clarity. Real compression would pack eight characters into each byte, reducing "01010111" from 8 bytes to 1 byte. The current representation makes testing and debugging straightforward because you can inspect encoded strings visually. The tradeoff is storage efficiency versus implementation simplicity. For demonstrating the algorithm and verifying correctness, string representation wins. For production compression, bit packing would be necessary.

Working on Huffman-Cpp reinforced several lessons about algorithm implementation. Smart pointers eliminate entire categories of memory bugs and make code more maintainable. Edge cases like single character inputs require explicit handling early in design. Performance optimization should focus on algorithmic complexity first, then micro-optimizations like pre-allocation. Comprehensive testing catches edge cases that code review might miss. The const_cast issue demonstrates that library interfaces sometimes force pragmatic choices. These patterns apply beyond Huffman coding to any tree-based algorithm or compression system.

---

[View on GitHub](https://github.com/HueCodes/Huffman-Cpp)
