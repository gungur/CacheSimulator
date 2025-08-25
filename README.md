# Cache Simulator (CSIM)

## Overview

This project is a cache simulator written in C that replays memory access traces from Valgrind and outputs statistics on cache hits, misses, and evictions. The simulator implements an LRU (Least Recently Used) replacement policy.

## Features

- Simulates cache behavior for load (L), store (S), and modify (M) operations
- Configurable cache parameters (set size, associativity, block size)
- Outputs hit, miss, and eviction counts
- Supports verbose mode for detailed trace output
- Handles instruction loads (ignored) and data modifications (treated as load + store)

## Project Structure

```
project/
├── csim.c          # Main cache simulator implementation
├── Makefile        # Build configuration
├── traces/         # Directory containing memory trace files
├── csim-ref        # Reference implementation
└── test-csim       # Testing script
```

## Building the Project

Compile the simulator using the provided Makefile:

```bash
make
```

## Usage

```bash
./csim [-hv] -s <num> -E <num> -b <num> -t <file>
```

### Options

- `-h`: Print help message
- `-v`: Verbose mode (print trace information)
- `-s <num>`: Number of set index bits (determines number of sets S = 2^s)
- `-E <num>`: Number of lines per set (associativity)
- `-b <num>`: Number of block offset bits (determines block size B = 2^b)
- `-t <file>`: Trace file to replay

### Examples

```bash
# Basic usage
./csim -s 4 -E 1 -b 4 -t traces/yi.trace

# Verbose mode
./csim -v -s 8 -E 2 -b 4 -t traces/yi.trace
```

## Cache Implementation Details

### Data Structures

- `cache_line_t`: Represents a single cache line with valid bit and tag
- `cache_set_t`: Array of cache lines representing a set
- `cache_t`: Array of sets representing the entire cache

### Key Functions

- `init_cache()`: Allocates and initializes the cache structure
- `free_cache()`: Frees all heap-allocated memory
- `access_data()`: Simulates data access and updates cache statistics
- `replay_trace()`: Processes trace files and simulates memory accesses

### Replacement Policy

The simulator uses LRU (Least Recently Used) replacement policy. When a cache miss occurs and all lines in a set are valid, the least recently used line is evicted.

## Trace File Format

Trace files contain memory access records in the format:
```
[operation] [address],[size]
```

Where:
- Operation: I (instruction load, ignored), L (data load), S (data store), M (data modify)
- Address: Hexadecimal memory address
- Size: Access size in bytes

## Output

The simulator outputs three statistics:
- `hits`: Number of cache hits
- `misses`: Number of cache misses
- `evictions`: Number of cache evictions

Results are printed to standard output and written to a `.csim_results` file.

## Testing

Use the provided `test-csim` script to verify your implementation against the reference simulator:

```bash
./test-csim
```

## Implementation Notes

1. Instruction loads (I) are ignored as specified
2. Data modify (M) operations are treated as a load followed by a store
3. Each load/store can cause at most one cache miss plus a possible eviction
4. The implementation tracks LRU using timestamps or similar mechanism
