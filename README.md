# RDMA_PROGRAMMING-
# RDMA Programming Learning Guide

## Introduction

Remote Direct Memory Access (RDMA) is a networking technology that enables a computer to access the memory of another computer directly without involving the remote CPU, operating system, or intermediate memory copies.

RDMA is designed for:

- High throughput
- Low latency
- Reduced CPU utilization
- High-performance computing (HPC)
- AI/ML workloads
- Storage systems
- Distributed databases
- Cloud infrastructure

Popular RDMA technologies include:

- InfiniBand
- RoCE (RDMA over Converged Ethernet)
- iWARP

---

# Prerequisites

Before learning RDMA, you should be comfortable with:

## Programming

- C Programming
- Linux Programming
- Memory Management
- Pointers
- Data Structures

## Networking

- TCP/IP
- Ethernet
- UDP
- Network Interfaces
- MTU

## Linux

- Command Line
- Device Drivers
- Kernel vs User Space
- Memory Mapping

---

# RDMA Architecture

Traditional Networking:

```
Application
    |
Socket API
    |
Kernel
    |
NIC
```

RDMA Networking:

```
Application
    |
RDMA Verbs API
    |
RNIC (RDMA Network Interface Card)
```

The data path bypasses most of the operating system.

Benefits:

- Lower latency
- Zero-copy transfers
- Reduced CPU overhead

---

# Important RDMA Components

## RNIC

Remote Direct Memory Access capable network adapter.

Examples:

- NVIDIA ConnectX
- Intel E810
- Broadcom RDMA adapters

---

## Queue Pair (QP)

The most important RDMA object.

A Queue Pair consists of:

### Send Queue (SQ)

Contains send requests.

### Receive Queue (RQ)

Contains receive requests.

---

## Completion Queue (CQ)

Stores operation completion events.

Applications poll the CQ using:

```c
ibv_poll_cq()
```

---

## Work Request (WR)

A request submitted to a queue.

Examples:

- Send
- Receive
- RDMA Read
- RDMA Write

---

## Work Completion (WC)

Represents the completion status of a Work Request.

---

## Memory Region (MR)

Memory must be registered before RDMA operations.

Example:

```c
ibv_reg_mr()
```

RDMA can only access registered memory.

---

# RDMA Communication Models

## Two-Sided Communication

Similar to sending messages.

Sender:

```text
SEND
```

Receiver:

```text
RECEIVE
```

Requirements:

- Sender posts SEND WR
- Receiver posts RECEIVE WR

---

## One-Sided Communication

The remote side is not actively involved.

### RDMA READ

Read remote memory directly.

### RDMA WRITE

Write directly into remote memory.

Benefits:

- Lower latency
- Less CPU usage

---

# Transport Types

## RC (Reliable Connection)

Comparable to TCP.

Features:

- Reliable
- Ordered delivery
- Retransmissions

Most common transport type.

---

## UD (Unreliable Datagram)

Comparable to UDP.

Features:

- No guaranteed delivery
- Connectionless
- Highly scalable

---

# RDMA Connection Setup

Typical connection process:

1. Create Context
2. Allocate Protection Domain (PD)
3. Create Completion Queue (CQ)
4. Create Queue Pair (QP)
5. Register Memory Region (MR)
6. Exchange Connection Information
7. Move QP through states

QP State Flow:

```text
RESET
  |
INIT
  |
RTR (Ready To Receive)
  |
RTS (Ready To Send)
```

---

# Typical Send Workflow

Sender:

```text
Create WR
     |
Post WR to Send Queue
     |
Hardware Processes Request
     |
Completion Generated
     |
Poll Completion Queue
```

Receiver:

```text
Allocate Buffers
     |
Register Memory
     |
Post Receive WR
     |
Receive Data
     |
Poll Completion Queue
```

---

# RDMA Reliability

Reliable transports (RC) use:

- Sequence Numbers
- Acknowledgements
- Retransmissions
- CRC Error Checking

---

# Useful libibverbs Functions

## Open Device

```c
ibv_open_device()
```

## Allocate Protection Domain

```c
ibv_alloc_pd()
```

## Create Completion Queue

```c
ibv_create_cq()
```

## Create Queue Pair

```c
ibv_create_qp()
```

## Register Memory

```c
ibv_reg_mr()
```

## Post Send Request

```c
ibv_post_send()
```

## Post Receive Request

```c
ibv_post_recv()
```

## Poll Completion Queue

```c
ibv_poll_cq()
```

---

# Resource Cleanup Order

Always clean up resources in reverse order.

```text
Destroy QP
    ↓
Destroy CQ
    ↓
Deregister Memory Region
    ↓
Destroy Completion Channel
    ↓
Free Allocated Memory
```

Common Functions:

```c
ibv_destroy_qp()
ibv_destroy_cq()
ibv_dereg_mr()
free()
```

---

# RDMA CM

RDMA Connection Manager (RDMA CM) simplifies connection setup.

Advantages:

- Similar to sockets
- Automatic address resolution
- Event-driven connection management

Common RDMA CM Functions:

```c
rdma_create_event_channel()
rdma_create_id()
rdma_resolve_addr()
rdma_connect()
```

---

# Performance Concepts

## Zero Copy

Data is transferred directly between memory regions.

Benefits:

- Lower CPU utilization
- Better throughput

---

## Latency

Time required to transfer a message.

RDMA provides:

- Microsecond-level latency
- Faster communication than traditional sockets

---

## Throughput

Amount of data transferred per second.

RDMA achieves very high throughput because:

- Fewer memory copies
- Less kernel involvement
- Hardware offloading

---

# Useful Commands

Check RDMA devices:

```bash
ibv_devices
```

Show device information:

```bash
ibv_devinfo
```

Display GIDs:

```bash
show_gids
```

Run bandwidth test:

```bash
ib_write_bw
```

Run latency test:

```bash
ib_send_lat
```

---

# Recommended Learning Path

## Phase 1: Fundamentals

- RDMA Concepts
- Networking Basics
- InfiniBand Architecture
- RoCE

## Phase 2: Verbs Programming

- Context
- PD
- CQ
- QP
- MR

## Phase 3: Data Transfers

- SEND/RECV
- RDMA READ
- RDMA WRITE

## Phase 4: Advanced Topics

- RDMA CM
- Completion Channels
- Multi-threaded RDMA
- Performance Tuning

## Phase 5: Real-World Applications

- NVMe-oF
- Ceph
- MPI
- Kubernetes RDMA
- AI Clusters

---

# Recommended Resources

Official Documentation:

- https://github.com/linux-rdma/rdma-core
- https://www.infinibandta.org
- https://docs.nvidia.com/networking/

Books:

- RDMA Aware Networks Programming User Manual (NVIDIA)
- InfiniBand Network Architecture

---

# Summary

RDMA allows applications to transfer data directly between hosts with minimal CPU and operating system involvement. To become productive in RDMA programming, focus on understanding:

- Queue Pairs (QP)
- Completion Queues (CQ)
- Memory Registration (MR)
- Work Requests (WR)
- SEND/RECV
- RDMA READ/WRITE
- Reliable Connections (RC)

Mastering these concepts will provide a strong foundation for developing high-performance distributed applications.
