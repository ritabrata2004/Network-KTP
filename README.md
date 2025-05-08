# KTP (KGP Transport Protocol)

A reliable transport protocol implementation built on top of unreliable communication channels with sliding window flow control.

## Overview

KTP is a custom transport protocol that implements end-to-end reliable data transfer over unreliable channels (UDP). This implementation demonstrates key networking concepts including:

- **Reliability Mechanisms**: Sequence numbers, acknowledgments, and timeouts
- **Flow Control**: Window-based flow control with dynamic window sizing
- **Error Recovery**: Automatic retransmission and duplicate detection
- **Concurrency Management**: Thread-safe operations with semaphore-based synchronization

This project serves as an excellent demonstration of transport layer protocol implementation, showcasing fundamental concepts in computer networking and distributed systems.

## Architecture

The implementation consists of three main components:

### 1. Socket Library (`ksocket.c` and `ksocket.h`)

Core library providing the KTP socket interface:

- **k_socket()**: Creates new KTP sockets
- **k_bind()**: Associates addresses with sockets
- **k_sendto()**: Handles message transmission
- **k_recvfrom()**: Manages message reception
- **k_close()**: Cleans up socket resources

### 2. Initialization Process (`initksocket.c`)

Service that manages the KTP protocol's operation:

- Creates and initializes IPC resources
- Manages thread operations:
  * **Receiver (R)**: Handles all incoming messages
  * **Sender (S)**: Manages timeouts and retransmissions
  * **Garbage Collector (GC)**: Cleans up orphaned sockets

### 3. Test Applications (`user1.c` and `user2.c`)

Sample applications demonstrating reliable file transfer:

- **user1.c**: Sender application that transmits a file
- **user2.c**: Receiver application that receives and stores the file

## Key Features

### Reliable Data Transfer

- **Sequence Numbering**: 8-bit sequence numbers ensure proper message ordering
- **Cumulative Acknowledgments**: Efficiently confirm received messages
- **Timeout-Based Retransmission**: Automatic retransmission after timeout period (T seconds)

### Flow Control

- **Sliding Window**: Dynamic window management based on receiver capacity
- **Buffer Management**: Fixed-size buffers (512 bytes per message)
- **Window Updates**: Receiver window size piggybacked on acknowledgments

### Robustness

- **Duplicate Detection**: Tracking and discarding of duplicate messages
- **Loss Recovery**: Reliable detection and recovery from packet loss
- **Error Reporting**: Custom error codes for common failure scenarios

### Implementation Highlights

- **Thread Safety**: Careful synchronization of shared resources
- **Memory Management**: Efficient buffer usage and resource tracking
- **Zero-Copy Design**: Direct buffer access where possible
- **Smart Retransmission**: Only retransmitting unacknowledged packets

## Performance Analysis

The protocol maintains excellent reliability even under adverse network conditions. Tests with varying packet loss probabilities demonstrate robust performance:

| Packet Loss (p) | Average Transmissions per Message |
|-----------------|----------------------------------|
| 0.05            | 1.21                             |
| 0.25            | 1.90                             |
| 0.50            | 3.28                             |

Even with 50% packet loss, the protocol requires only 3.28 transmissions per message on average, showing near-optimal efficiency.

## Building and Usage

### Compilation

```bash
make all
```

This builds:
- The KTP socket library (`libksocket.a`)
- The initialization process (`initksocket`)
- Test applications (`user1` and `user2`)

### Running

1. Start the initialization process:
   ```bash
   ./initksocket
   ```

2. Start the receiver:
   ```bash
   ./user2 <receiver_ip> <receiver_port> <sender_ip> <sender_port>
   ```

3. Start the sender:
   ```bash
   ./user1 <sender_ip> <sender_port> <receiver_ip> <receiver_port>
   ```

### Example

```bash
# Start the initialization process
./initksocket

# In another terminal, start the receiver
./user2 127.0.0.1 5076 127.0.0.1 8081

# In a third terminal, start the sender
./user1 127.0.0.1 8081 127.0.0.1 5076
```

## Advanced Features

- **Multiple Simultaneous Connections**: Support for N active KTP sockets
- **Garbage Collection**: Automatic cleanup of orphaned sockets
- **Simulated Network Conditions**: Configurable packet loss probability
- **Window-Based Flow Control**: Dynamic adjustment of transmission rate

## Technical Implementation

The implementation leverages several system concepts:

- **IPC Mechanisms**: Shared memory and semaphores for cross-process communication
- **Thread Management**: Multiple threads handling different protocol aspects
- **Socket Programming**: Low-level socket operations for network communication
- **Concurrency Control**: Careful synchronization of shared resources

## Demonstration Value

This project effectively demonstrates:

1. **Protocol Design**: Understanding of transport layer protocols
2. **Concurrent Programming**: Thread-safe operation in a complex system
3. **Error Handling**: Robust recovery from network failures
4. **System Programming**: Effective use of IPC mechanisms
5. **Performance Optimization**: Efficient resource utilization

---
*Developed by: Aritra Maji (Roll: 22CS30011)*