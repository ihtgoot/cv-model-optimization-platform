# CV Model Optimization & Deployment Platform

Backend-first computer vision optimization and deployment system with genetic algorithm hyperparameter tuning, multi-engine inference benchmarking, and containerized API packaging.

## Overview

This platform provides an end-to-end workflow:
- Accept CV models (preset or user-provided)
- Accept structured datasets
- Train and optimize using Genetic Algorithms
- Convert models to ONNX (standardized runtime contract)
- Benchmark multiple inference engines
- Automatically select optimal runtime
- Package a scalable inference service
- Deploy locally via Docker
- Support multi-user RBAC
- Handle high concurrency (100 users target)

## Supported Scope (MVP)

### Tasks
- Object Detection (video)
- Image Classification
- (Tracking optional extension)

### Model Inputs
- YOLOv8 preset models
- User-provided PyTorch .pkl / .pt
- User-provided ONNX models

### Dataset
- YOLO format only (MVP restriction)

### Runtime Contract
All models must convert to **ONNX** for inference standardization.

## Architecture Overview

```
USER (CLI / TUI)
        │
        ▼
Go API + RBAC Layer
        │
        ├── Training Engine ────► ONNX Conversion ────► Benchmark Engine
        │     (Python + GA)                              (ONNX / TensorRT)
        │
        ▼
Packaging Module
      (Docker Build)
```

## Layered Architecture

### Layer 1 — Interface Layer
**Technology:** CLI / TUI (MVP)

**Responsibilities:**
- Accept model, dataset, performance constraints
- Display benchmark reports
- Trigger packaging

No business logic here.

### Layer 2 — API & Control Layer (Go)
**Responsibilities:**
- RBAC enforcement
- Request routing
- Job scheduling
- GPU task queue control
- Concurrency management
- Orchestration between components

Stateless except metadata storage.

### Layer 3 — Training Layer (Python)
**Responsibilities:**
- Model loading
- Dataset loading
- YOLO training
- Genetic Algorithm optimization
- Output optimized model artifact

**GA Variants (5 Strategies):**
- Tournament GA
- Elitist GA
- Adaptive Mutation GA
- Constraint-aware GA
- Multi-objective GA

Generations configurable (default: 5 for MVP)

Example fitness:
```
fitness = 0.7 * mAP50 + 0.3 * mAP50-95 - latency_penalty
```

### Layer 4 — Conversion Layer
**Responsibilities:**
- Convert PyTorch → ONNX
- Validate ONNX compatibility
- Fail fast on unsupported operators

ONNX is mandatory runtime format.

### Layer 5 — Inference Optimization Layer
**Supported runtimes:**
- PyTorch
- ONNX Runtime
- TensorRT FP16
- TensorRT INT8

**Benchmarked metrics:**
- Single image latency
- FPS (video stream)
- CPU usage
- GPU usage
- RAM usage

**Engine selection logic:**
Select engine that satisfies:
- Accuracy threshold
- Target latency
- Resource constraints

Choose fastest valid candidate. Deterministic. No hidden heuristics.

### Layer 6 — Packaging Layer
Generates deployable Docker artifact including:
- Optimized model (.engine / .onnx)
- Go API binary
- Runtime libraries
- Config file
- docker-compose setup
- Redis (optional)
- PostgreSQL (optional)

Does NOT include:
- Dataset
- Training code
- GA code

Strict separation between training and inference.

### Layer 7 — Runtime Service Layer
The generated inference service supports:
- 100 concurrent HTTP users
- Bounded worker pool
- Max 2 parallel GPU tasks
- Shared model instance (loaded once)

**Runtime architecture:**
```
HTTP Server
      │
      ▼ Bounded Queue
      │
      ▼ Worker Pool (N)
      │
      ▼ Shared Runtime
      │
      ▼ GPU
```

Backpressure applied under overload.

## Concurrency Design

**Platform-level:**
- 100 concurrent API users
- 2 parallel GPU tasks
- Separate queues for: Training, Inference, Packaging

GPU access controlled by worker controller. No uncontrolled VRAM allocation.

## RBAC Model

### Roles

**Admin**
- Train models
- Run GA
- Benchmark engines
- Generate deployment artifact

**Engineer**
- Run inference
- View metrics
- Inspect configurations

**Viewer**
- Prediction endpoint only

Project creator defines access.

## Performance Targets (MVP)

| Metric | Target |
|--------|--------|
| Single Image Latency | <100ms |
| P95 Latency | <200ms |
| Concurrent Users | 100 |
| GPU Parallel Tasks | 2 |
| No OOM | Required |

## Deployment

### Local Deployment (MVP)
```bash
docker-compose up
```

### Requirements
- Linux (Ubuntu / PopOS recommended)
- NVIDIA GPU (RTX 4060+ recommended)
- CUDA 12+
- Docker + NVIDIA Container Toolkit

## Technology Stack

| Layer | Technology |
|-------|-----------|
| API & Orchestration | Go |
| Training | Python |
| GA Optimization | Python (vectorized) |
| Inference Runtime | C++ |
| ONNX Runtime | 1.15+ |
| TensorRT | 8.x |
| Database | PostgreSQL |
| Cache | Redis |
| Deployment | Docker |
| GPU | NVIDIA CUDA 12+ |

## Design Principles

1. ONNX as runtime contract
2. Deterministic engine selection
3. Bounded concurrency
4. Clear separation of concerns
5. Infrastructure-first design
6. No silent GPU overcommit
7. Explicit constraint evaluation

## Future Extensions (Post-MVP)

- Kubernetes deployment
- Horizontal scaling
- Dynamic model registry
- Multi-node training
- Web UI
- Monitoring & observability

## Project Status

🚧 MVP in active development

Focus:
- Core orchestration layer
- Training + GA
- Runtime benchmarking
- Containerized packaging

## Why This Project Exists

Most ML projects stop at training. This platform focuses on:
- Deployment discipline
- Runtime optimization
- Concurrency control
- Scalable inference packaging

It is an ML infrastructure project, not a notebook demo.

---

**Note:** This is an MVP focused on demonstrating end-to-end ML infrastructure capabilities. See `docs/` for detailed technical documentation.