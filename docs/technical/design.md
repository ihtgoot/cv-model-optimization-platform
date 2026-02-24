# Design Document: CV Model Optimization & Deployment Platform (MVP)

## Overview

Backend-first Computer Vision platform enabling model optimization through genetic algorithm hyperparameter tuning, ONNX conversion, multi-engine benchmarking, and scalable inference deployment. Supports multi-user role-based access with GPU resource constraints (2 parallel executions) and 100 concurrent HTTP users.

## System Architecture

### High-Level System Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CV Model Platform MVP                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  User Interfaces                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐  │
│  │ TUI Terminal │  │ HTMX Web UI  │  │ HTTP API Clients         │  │
│  │ (Primary)    │  │ (Optional)   │  │ (100 concurrent users)   │  │
│  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────────┘  │
│         │                 │                     │                   │
│         └─────────────────┼─────────────────────┘                   │
│                           │                                         │
│                  ┌────────▼────────┐                                │
│                  │  Go HTTP API    │                                │
│                  │  (Port 8080)    │                                │
│                  └────────┬────────┘                                │
│                           │                                         │
│        ┌──────────────────┼──────────────────┐                      │
│        │                  │                  │                      │
│   ┌────▼─────┐    ┌──────▼──────┐   ┌──────▼──────┐               │
│   │ Auth &   │    │ Model &     │   │ Benchmark  │               │
│   │ RBAC     │    │ Training    │   │ & Inference│               │
│   │ Module   │    │ Orchestrator │   │ Module     │               │
│   └──────────┘    └──────┬──────┘   └──────┬──────┘               │
│                          │                  │                      │
│         ┌────────────────┼──────────────────┘                      │
│         │                │                                         │
│    ┌────▼────┐    ┌─────▼──────┐    ┌──────────────┐             │
│    │ Python  │    │ GPU Job    │    │ C++ Inference│             │
│    │ Training│    │ Queue      │    │ Runtime      │             │
│    │ + GA    │    │ (MAX=2)    │    │              │             │
│    └─────────┘    └────────────┘    └──────────────┘             │
│                                                                    │
│         ┌────────────────────────────────────────┐                │
│         │                                        │                │
│         │      SQLite Database                   │                │
│         │  (Models, Datasets, Jobs, Reports)    │                │
│         │                                        │                │
│         └────────────────────────────────────────┘                │
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ Docker Container (Single Deployment Unit)                   │ │
│  │ - Go API + TUI                                              │ │
│  │ - Python training worker (subprocess)                       │ │
│  │ - C++ inference runtime (linked library)                    │ │
│  │ - SQLite database (volume mount)                            │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Data Flow Architecture

```
User Request (TUI/HTTP)
    │
    ├─► Authentication & RBAC Check
    │
    ├─► Route to Handler
    │   ├─ Model Upload/Selection
    │   ├─ Dataset Upload
    │   ├─ Training Request
    │   ├─ Benchmark Request
    │   └─ Inference Request
    │
    ├─► Orchestration Layer
    │   ├─ GPU Job Queue (MAX_GPU_WORKERS=2)
    │   ├─ CPU Task Executor
    │   └─ Resource Monitoring
    │
    ├─► Execution
    │   ├─ Python: Training + GA Optimization
    │   ├─ ONNX Conversion
    │   ├─ C++ Inference Runtime
    │   └─ Benchmark Metrics Collection
    │
    ├─► Persistence
    │   ├─ SQLite: Models, Benchmarks, Reports
    │   └─ File Storage: Model artifacts, datasets
    │
    └─► Response
        ├─ Report Generation (Deterministic)
        ├─ Dual-level Views (Technical/Simple)
        └─ Return to Client
```

## Module Architecture

### 1. Go HTTP API Server (Port 8080)

**Responsibilities:**
- HTTP request routing and middleware
- Authentication and RBAC enforcement
- Request validation and serialization
- Response formatting and error handling
- TUI server integration
- Orchestration coordination

**Key Endpoints:**
- Authentication: `/api/v1/auth/login`, `/api/v1/auth/logout`, `/api/v1/auth/verify`
- Models: `/api/v1/models` (upload, list, get, delete)
- Datasets: `/api/v1/datasets` (upload, list, get)
- Training: `/api/v1/training` (start, status, cancel)
- Benchmarking: `/api/v1/benchmark` (start, status, report)
- Inference: `/api/v1/inference/predict`, `/api/v1/inference/health`
- Packages: `/api/v1/packages` (generate, list, download)
- Reports: `/api/v1/reports` (list, get, view, export)
- System: `/api/v1/health`, `/api/v1/metrics`

### 2. Python Training & GA Module

**Responsibilities:**
- Model loading (PyTorch, TensorFlow, ONNX)
- Dataset preprocessing and loading
- Training loop execution
- Genetic Algorithm hyperparameter optimization
- ONNX model conversion
- Metrics collection

**Key Functions:**
- Load preset models (YOLOv8, ResNet50, EfficientNet)
- Load custom models with validation
- Preprocess datasets (normalization, resizing, augmentation)
- Execute training with configurable hyperparameters
- Run GA optimization (population, fitness, selection, crossover, mutation)
- Convert trained models to ONNX format
- Validate ONNX models

### 3. C++ Inference Runtime

**Responsibilities:**
- ONNX model loading and management
- Inference execution (ONNX Runtime & TensorRT FP16)
- Batch processing
- GPU memory management
- Metrics collection (latency, throughput, GPU/CPU utilization)

**Key Functions:**
- Load ONNX models into inference engines
- Execute single and batch inference
- Collect performance metrics
- Manage GPU memory allocation
- Handle concurrent inference requests

### 4. Benchmarking Module

**Responsibilities:**
- Multi-engine benchmarking (ONNX Runtime, TensorRT FP16)
- Deterministic metric collection
- Constraint evaluation
- Engine selection logic
- Report generation

**Benchmarking Process:**

```
Benchmark Request
    │
    ├─► FOR each engine [ONNX Runtime, TensorRT FP16]:
    │   ├─ Load model in engine
    │   ├─ FOR each batch size:
    │   │  ├─ Warmup phase (discard results)
    │   │  ├─ Measurement phase (collect metrics)
    │   │  │  ├─ Latency (mean, P95, P99)
    │   │  │  ├─ Throughput (FPS)
    │   │  │  ├─ GPU utilization (%)
    │   │  │  ├─ CPU utilization (%)
    │   │  │  └─ VRAM usage (MB)
    │   │  └─ Store results
    │   └─ Unload model
    │
    ├─► Evaluate Constraints
    │   ├─ Check latency constraints
    │   ├─ Check throughput constraints
    │   └─ Check memory constraints
    │
    ├─► Select Optimal Engine
    │   ├─ Filter by constraint satisfaction
    │   ├─ Rank by performance
    │   └─ Select best engine
    │
    └─► Generate Report
        ├─ Metadata (model, timestamp, config)
        ├─ Raw metrics table
        ├─ Delta analysis (engine comparison)
        ├─ Constraint evaluation
        └─ Engine selection reasoning
```

### 5. Report Generation Module

**Responsibilities:**
- Deterministic report generation (no LLM)
- Dual-level reporting (Technical & Simple views)
- Metadata aggregation
- Delta analysis
- Constraint evaluation
- Report persistence

**Report Structure:**
- Metadata: Model info, dataset, benchmark config, system info, timestamp
- Raw Metrics Table: Engine, batch size, latency, P95, P99, FPS, GPU%, VRAM
- Delta Analysis: Engine comparisons, performance deltas
- Constraint Evaluation: Pass/fail for each constraint
- Engine Selection: Reasoning and alternatives
- Technical View: Detailed metrics and analysis
- Simple View: Key metrics and deployment recommendation

### 6. Package Generation Module

**Responsibilities:**
- Docker image creation
- Model artifact bundling
- Configuration generation
- Deployment documentation
- Package versioning

**Package Contents:**
- ONNX model
- Inference runtime (C++)
- Configuration files
- Deployment guide
- Health check script
- Docker Compose file

### 7. TUI Interface

**Responsibilities:**
- Interactive terminal UI
- User authentication
- Model/dataset management
- Training/benchmark monitoring
- Report viewing
- Package generation

**TUI Screens:**

```
┌─────────────────────────────────────────────────────────────┐
│  CV Model Optimization Platform - TUI                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  [1] Models    [2] Datasets    [3] Training    [4] Reports  │
│  [5] Benchmark [6] Inference   [7] Packages    [8] Settings │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Models                                                      │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ID    │ Name          │ Type    │ Status   │ Size    │  │
│  ├──────────────────────────────────────────────────────┤  │
│  │ m001  │ YOLOv8-nano   │ preset  │ ready    │ 6.2 MB  │  │
│  │ m002  │ ResNet50      │ custom  │ training │ 98 MB   │  │
│  │ m003  │ EfficientNet  │ preset  │ ready    │ 27 MB   │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  [U]pload  [D]elete  [V]iew  [T]rain  [B]enchmark  [Q]uit  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Resource Management

### GPU Job Queue Management

```
GPU Job Queue (MAX_GPU_WORKERS = 2)

Active GPU Jobs (2 max):
┌─────────────────────────────────────────┐
│ Job 1: Training (model_m001)            │
│ Status: Running (45% complete)          │
│ GPU Memory: 8.2 GB / 16 GB              │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Job 2: Benchmark (model_m002)           │
│ Status: Running (ONNX Runtime)          │
│ GPU Memory: 4.1 GB / 16 GB              │
└─────────────────────────────────────────┘

Queued GPU Jobs (waiting):
┌─────────────────────────────────────────┐
│ Job 3: Training (model_m003) - Prio: 5  │
│ Job 4: Benchmark (model_m004) - Prio: 3 │
│ Job 5: Training (model_m005) - Prio: 2  │
└─────────────────────────────────────────┘
```

### Inference Concurrency Model

```
HTTP Inference Requests (100 concurrent users)
    │
    ├─► Request Router (Go HTTP Server)
    │   ├─ Accept request
    │   ├─ Validate authentication
    │   ├─ Validate input shape
    │   └─ Enqueue to inference queue
    │
    ├─► Inference Request Queue (bounded, max 1000)
    │   ├─ FIFO ordering
    │   └─ Timeout: 30 seconds
    │
    ├─► Worker Thread Pool (C++ Inference Runtime)
    │   ├─ Default: 10 workers
    │   ├─ Configurable via env var
    │   ├─ Each worker:
    │   │  ├─ Dequeue request
    │   │  ├─ Execute inference
    │   │  ├─ Collect metrics
    │   │  └─ Enqueue result
    │   └─ Synchronization: mutex + condition variable
    │
    ├─► Response Handler
    │   ├─ Format output
    │   ├─ Include latency
    │   └─ Return to client
    │
    └─► Metrics Aggregation
        ├─ Track latency distribution
        ├─ Monitor queue depth
        └─ Update system metrics
```

## Database Schema Overview

### Core Tables

```
Users Table
├─ id (PRIMARY KEY)
├─ username (UNIQUE)
├─ password_hash
├─ role (admin, engineer, viewer)
├─ api_key
└─ timestamps

Models Table
├─ id (PRIMARY KEY)
├─ name, description
├─ model_type (preset, custom)
├─ framework (pytorch, tensorflow, onnx)
├─ task_type (detection, classification)
├─ file_path, file_size
├─ onnx_path, onnx_size
├─ input_shape, output_shape
├─ metadata (JSON)
└─ created_by (FOREIGN KEY)

Datasets Table
├─ id (PRIMARY KEY)
├─ name, description
├─ task_type
├─ file_path, file_size
├─ num_samples, num_classes
├─ split_config (JSON)
└─ created_by (FOREIGN KEY)

Training Jobs Table
├─ id (PRIMARY KEY)
├─ model_id, dataset_id, user_id
├─ status (queued, running, completed, failed)
├─ hyperparams (JSON)
├─ ga_config (JSON)
├─ max_epochs, validation_split
├─ start_time, end_time, duration
├─ final_accuracy, final_loss, best_epoch
└─ result_model_id

Benchmark Jobs Table
├─ id (PRIMARY KEY)
├─ model_id, user_id
├─ status
├─ engines (JSON)
├─ batch_sizes (JSON)
├─ input_shapes (JSON)
├─ num_warmup, num_iterations
└─ timestamps

Benchmark Results Table
├─ id (PRIMARY KEY)
├─ benchmark_job_id
├─ engine, batch_size
├─ latency_ms, latency_p95_ms, latency_p99_ms
├─ throughput_fps
├─ gpu_util_pct, cpu_util_pct
├─ vram_usage_mb
└─ power_draw_w

Reports Table
├─ id (PRIMARY KEY)
├─ benchmark_job_id
├─ selected_engine
├─ technical_view, simple_view
├─ metadata (JSON)
├─ delta_analysis (JSON)
└─ constraint_eval (JSON)

Deployment Packages Table
├─ id (PRIMARY KEY)
├─ model_id, version
├─ selected_engine
├─ config (JSON)
├─ docker_image_tag
├─ artifacts (JSON)
├─ package_size, checksum
└─ created_by
```

## Deployment Architecture

### Docker Deployment

```
Docker Container Structure

┌─────────────────────────────────────────────────────┐
│ cv-platform:v1.0.0                                  │
├─────────────────────────────────────────────────────┤
│                                                     │
│ Base Image: nvidia/cuda:12.2.0-runtime-ubuntu22.04 │
│                                                     │
│ ┌─────────────────────────────────────────────────┐ │
│ │ Go HTTP API Server (Port 8080)                  │ │
│ │ - Authentication & RBAC                         │ │
│ │ - Request routing                               │ │
│ │ - TUI server                                    │ │
│ └─────────────────────────────────────────────────┘ │
│                                                     │
│ ┌─────────────────────────────────────────────────┐ │
│ │ Python Training Worker (subprocess)             │ │
│ │ - Model training                                │ │
│ │ - GA optimization                               │ │
│ │ - ONNX conversion                               │ │
│ └─────────────────────────────────────────────────┘ │
│                                                     │
│ ┌─────────────────────────────────────────────────┐ │
│ │ C++ Inference Runtime (linked library)          │ │
│ │ - ONNX Runtime engine                           │ │
│ │ - TensorRT FP16 engine                          │ │
│ │ - Inference execution                          │ │
│ └─────────────────────────────────────────────────┘ │
│                                                     │
│ ┌─────────────────────────────────────────────────┐ │
│ │ SQLite Database (volume mount)                  │ │
│ │ - Models, datasets, jobs, reports               │ │
│ └─────────────────────────────────────────────────┘ │
│                                                     │
│ Environment Variables:                              │
│ - MAX_GPU_WORKERS=2                                │
│ - INFERENCE_WORKERS=10                             │
│ - DB_PATH=/data/cv-platform.db                     │
│ - MODEL_PATH=/data/models                          │
│ - DATASET_PATH=/data/datasets                      │
│ - REPORT_PATH=/data/reports                        │
│ - LOG_LEVEL=info                                   │
│ - TLS_ENABLED=false                                │
│                                                     │
│ Volumes:                                            │
│ - /data/models (model artifacts)                   │
│ - /data/datasets (dataset files)                   │
│ - /data/reports (generated reports)                │
│ - /data/db (SQLite database)                       │
│                                                     │
│ Health Check:                                       │
│ - Endpoint: /api/v1/health                         │
│ - Interval: 30 seconds                             │
│ - Timeout: 10 seconds                              │
│                                                     │
└─────────────────────────────────────────────────────┘
```

## System Invariants

1. **GPU Worker Invariant**: Active GPU workers ≤ 2 at all times
2. **Model Consistency**: All models have ONNX conversion
3. **Benchmark Determinism**: Same config produces same results
4. **RBAC Enforcement**: All endpoints enforce role-based access
5. **Inference Latency**: All inferences have latency > 0
6. **Report Completeness**: All reports have technical and simple views
7. **Package Integrity**: Package checksums verified
8. **User Uniqueness**: Username uniqueness enforced

## Performance Targets

| Metric | Target |
|--------|--------|
| Inference Latency | < 100ms (typical models) |
| P95 Latency | < 200ms |
| P99 Latency | < 500ms |
| Throughput | > 10 FPS |
| Concurrent Users | 100 HTTP users |
| System Uptime | > 99% |
| Test Coverage | > 80% |
| GPU Workers | ≤ 2 concurrent |
| Inference Workers | 10 (configurable) |

## Key Design Decisions

1. **Backend-First**: Go HTTP API as central orchestrator
2. **GPU Resource Constraints**: Hard limit of 2 concurrent GPU jobs
3. **Deterministic Reporting**: No LLM, purely algorithmic
4. **ONNX as Runtime Format**: All models converted to ONNX
5. **Dual-Level Reporting**: Technical and simple views
6. **SQLite for Persistence**: Lightweight, no external dependencies
7. **Docker Packaging**: Single deployment unit
8. **TUI as Primary Interface**: Direct terminal interaction
9. **Bounded Concurrency**: Explicit limits on workers and threads
10. **Deterministic Benchmarking**: Warmup, multiple iterations, statistical aggregation
