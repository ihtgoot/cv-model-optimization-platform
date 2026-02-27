# Quick Reference Guide

## Project Status
✅ GitHub repo created and pushed  
✅ Documentation organized  
✅ Execution plan ready  
✅ Workflow documented  
✅ Optimization protocol defined  
🚧 Ready to start implementation

## Documentation Index

### Core Documents
- [README.md](README.md) - Project overview and architecture
- [EXECUTION_PLAN.md](EXECUTION_PLAN.md) - 6-week implementation plan
- [WORKFLOW.md](WORKFLOW.md) - Development workflow and best practices
- [QUICK_REFERENCE.md](QUICK_REFERENCE.md) - This file

### Technical Specifications
- [docs/technical/requirements.md](docs/technical/requirements.md) - Functional & non-functional requirements
- [docs/technical/design.md](docs/technical/design.md) - System architecture and design
- [docs/technical/optimization-protocol.md](docs/technical/optimization-protocol.md) - Inference optimization protocol

### Planning & Tasks
- [docs/planning/tasks.md](docs/planning/tasks.md) - Detailed task breakdown (160 hours)

### Project Overview
- [docs/project-overview/SUMMARY.md](docs/project-overview/SUMMARY.md) - Quick project summary
- [docs/project-overview/project-summary.md](docs/project-overview/project-summary.md) - Comprehensive overview

## Next Steps (Week 1 - Days 1-7)

### Day 1-2: Project Setup
```bash
# Initialize Go project
go mod init github.com/yourusername/cv-platform
go get -u github.com/gorilla/mux
go get -u github.com/mattn/go-sqlite3
go get -u golang.org/x/crypto/bcrypt
go get -u github.com/golang-jwt/jwt/v5

# Create project structure
mkdir -p cmd/server
mkdir -p internal/{api,auth,db,models,training,benchmark,inference}
mkdir -p pkg/{python,cpp}
mkdir -p scripts tests docker
```

### Day 3-4: HTTP Server & Database
- Create `cmd/server/main.go`
- Create `internal/api/` for HTTP handlers
- Create `internal/db/` for database operations
- Set up SQLite schema

### Day 5-7: Authentication & RBAC
- Implement user model
- Implement JWT authentication
- Create RBAC middleware
- Test auth flow

## Project Structure (Recommended)

```
cv-platform/
├── cmd/
│   ├── server/          # Main HTTP server
│   └── cli/             # TUI interface
├── internal/
│   ├── api/             # HTTP handlers
│   ├── auth/            # Authentication & RBAC
│   ├── db/              # Database operations
│   ├── models/          # Data models
│   ├── training/        # Training orchestration
│   ├── benchmark/       # Benchmarking logic
│   └── inference/       # Inference service
├── pkg/
│   ├── python/          # Python training module
│   └── cpp/             # C++ inference runtime
├── scripts/             # Build and deployment scripts
├── tests/               # Integration tests
├── docs/                # Documentation (already exists)
├── docker/              # Dockerfile and compose
├── .gitignore
├── README.md
├── EXECUTION_PLAN.md
├── QUICK_REFERENCE.md
└── go.mod
```

## Key Technical Decisions

1. **Backend**: Go (HTTP API + orchestration)
2. **Training**: Python (PyTorch + GA)
3. **Inference**: C++ (ONNX Runtime + TensorRT)
4. **Database**: SQLite (no external dependencies)
5. **UI**: TUI (primary), HTMX (optional)
6. **Deployment**: Docker (single container)

## Critical Constraints

- **GPU Workers**: Maximum 2 concurrent
- **Concurrent Users**: 100 HTTP users
- **Inference Latency**: < 100ms target
- **Model Size**: Max 1 GB
- **Dataset Size**: Max 10 GB

## MVP Timeline

- **Week 1**: Foundation & API (40h)
- **Week 2**: Model & Dataset Management (40h)
- **Week 3**: Training Pipeline (40h)
- **Week 4**: Inference Runtime (50h)
- **Week 5**: Benchmarking & GA (20h)
- **Week 6**: Reports, Packaging & Polish (10h)

**Total**: 160 hours / 6 weeks

## Essential Commands

### Development
```bash
# Run server
go run cmd/server/main.go

# Run tests
go test ./...

# Build
go build -o bin/cv-platform cmd/server/main.go

# Docker build
docker build -t cv-platform:latest .

# Docker run
docker run -p 8080:8080 --gpus all cv-platform:latest
```

### API Endpoints (Port 8080)

**Authentication**
- POST `/api/v1/auth/login`
- POST `/api/v1/auth/logout`

**Models**
- POST `/api/v1/models` (upload)
- GET `/api/v1/models` (list)
- GET `/api/v1/models/:id` (details)
- DELETE `/api/v1/models/:id` (admin only)

**Datasets**
- POST `/api/v1/datasets` (upload)
- GET `/api/v1/datasets` (list)
- GET `/api/v1/datasets/:id` (details)

**Training**
- POST `/api/v1/training` (start)
- GET `/api/v1/training/:id` (status)
- DELETE `/api/v1/training/:id` (cancel)

**Benchmarking**
- POST `/api/v1/benchmark` (start)
- GET `/api/v1/benchmark/:id` (status)
- GET `/api/v1/benchmark/:id/report` (results)

**Inference**
- POST `/api/v1/inference/predict`
- GET `/api/v1/inference/health`

**System**
- GET `/api/v1/health`
- GET `/api/v1/metrics`

## Roles & Permissions

| Role | Permissions |
|------|-------------|
| Admin | Full access (train, GA, benchmark, package) |
| Engineer | Train, inference, view reports |
| Viewer | Inference endpoint only |

## Performance Targets

| Metric | Target | Notes |
|--------|--------|-------|
| Inference Latency | < 100ms | Typical models, batch 1 |
| P95 Latency | < 200ms | 95th percentile |
| P99 Latency | < 500ms | 99th percentile |
| Concurrent Users | 100 | HTTP API users |
| GPU Workers | ≤ 2 | Hard limit, enforced |
| Inference Workers | 10 (configurable) | Default worker pool |
| Test Coverage | > 80% | Unit + integration |
| System Uptime | > 99% | Excluding maintenance |

## Optimization Protocol Summary

### Step-by-Step Process
1. **Baseline (ORT FP32)** - Establish ground truth
2. **Graph Optimization** - Enable ORT optimizations
3. **FP16 ONNX** - Convert to half precision
4. **TensorRT FP16** - Build optimized engine
5. **Batch Tuning** - Test batch 1 vs 2
6. **CPU Profiling** - Check preprocessing bottlenecks
7. **VRAM Monitoring** - Stay under 6.5GB peak
8. **Concurrency Test** - Verify under load
9. **Stability Check** - 100 concurrent users, no errors

### Stopping Criteria
- Improvement < 10-15% from last step
- Variance < 5% across runs
- No OOM errors
- Stable under 100 concurrent HTTP requests
- Latency target achieved (<100ms)

### Expected Results (YOLOv8n on RTX 4060)
- ORT FP32: ~100ms baseline
- ORT FP16: 75ms (25% faster)
- TensorRT FP16: 60ms (40% faster)
- Final target: 50-90ms range

**See [docs/technical/optimization-protocol.md](docs/technical/optimization-protocol.md) for full details.**

## Quick Start Checklist

### Before You Begin
- [ ] Review [WORKFLOW.md](WORKFLOW.md) for development process
- [ ] Read [docs/technical/optimization-protocol.md](docs/technical/optimization-protocol.md) for benchmarking
- [ ] Check hardware requirements below
- [ ] Set up development environment

### Week 1 - Day 1 (Start Here)
```bash
# Initialize Go project
go mod init github.com/yourusername/cv-platform
go get -u github.com/gorilla/mux
go get -u github.com/mattn/go-sqlite3
go get -u golang.org/x/crypto/bcrypt
go get -u github.com/golang-jwt/jwt/v5

# Create project structure
mkdir -p cmd/server
mkdir -p internal/{api,auth,db,models,training,benchmark,inference}
mkdir -p pkg/{python,cpp}
mkdir -p scripts tests docker
```

## Getting Started

### 1. Review Documentation
- Start with [README.md](README.md) for project overview
- Read [WORKFLOW.md](WORKFLOW.md) for development process
- Check [EXECUTION_PLAN.md](EXECUTION_PLAN.md) for 6-week timeline
- Review [docs/technical/design.md](docs/technical/design.md) for architecture

### 2. Set Up Development Environment
- Install Go 1.20+, Python 3.10+, C++ 17 compiler
- Install Docker and NVIDIA Docker runtime
- Install CUDA 12.2+
- Verify GPU access: `nvidia-smi`

### 3. Start Implementation
- Begin with Week 1, Day 1 tasks (see above)
- Follow the critical path in execution plan
- Test each component before moving to next
- Use [docs/technical/optimization-protocol.md](docs/technical/optimization-protocol.md) for benchmarking

### 4. Track Progress
- Update task status in [docs/planning/tasks.md](docs/planning/tasks.md)
- Check milestone checkpoints weekly
- Document any deviations from plan

## Risk Mitigation

### High-Risk Items
1. **TensorRT Compatibility** - Test early with YOLOv8, fallback to ONNX Runtime
2. **GPU Memory Limits** - Use small models (YOLOv8n), stay under 6.5GB peak
3. **Docker + GPU Access** - Set up Day 1, test immediately
4. **Dataset Validation** - Strict validation, provide sample dataset

### Common Issues & Solutions

**CUDA Out of Memory**
```bash
# Solution: Reduce batch size, use smaller models
# Check VRAM: nvidia-smi
# Target: < 6.5GB peak usage
```

**ONNX Conversion Fails**
```bash
# Solution: Match PyTorch/ONNX versions
pip install torch==2.0.0 onnx==1.14.0 onnxruntime-gpu==1.15.0
```

**Docker GPU Access Denied**
```bash
# Solution: Install NVIDIA Docker runtime
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update && sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```

**Training Too Slow**
```bash
# Solution: Use smaller dataset, reduce epochs
# For testing: Use YOLOv8n with 100 images, 10 epochs
```

**API Not Responding**
```bash
# Check logs
docker logs cv-platform

# Verify database
sqlite3 /data/cv-platform.db ".tables"

# Check GPU
nvidia-smi
```

## Support Resources

### Official Documentation
- [Go Documentation](https://go.dev/doc/)
- [PyTorch Documentation](https://pytorch.org/docs/)
- [ONNX Runtime](https://onnxruntime.ai/docs/)
- [TensorRT](https://docs.nvidia.com/deeplearning/tensorrt/)
- [Docker + GPU](https://docs.nvidia.com/datacenter/cloud-native/)

### Project Documentation
- [README.md](README.md) - Project overview
- [WORKFLOW.md](WORKFLOW.md) - Development workflow
- [EXECUTION_PLAN.md](EXECUTION_PLAN.md) - 6-week plan
- [docs/technical/requirements.md](docs/technical/requirements.md) - Requirements
- [docs/technical/design.md](docs/technical/design.md) - Architecture
- [docs/technical/optimization-protocol.md](docs/technical/optimization-protocol.md) - Optimization
- [docs/planning/tasks.md](docs/planning/tasks.md) - Task breakdown

---

## Final Checklist Before Starting

- [ ] All documentation reviewed
- [ ] Development environment set up
- [ ] Hardware requirements verified (RTX 4060, CUDA 12+)
- [ ] Git repository initialized
- [ ] Week 1 tasks understood
- [ ] Optimization protocol reviewed
- [ ] Risk mitigation strategies noted

**Ready to build? Start with Week 1, Day 1 in [EXECUTION_PLAN.md](EXECUTION_PLAN.md)**

---

**Last Updated**: February 2026  
**Status**: Ready for Implementation  
**Version**: 1.0
