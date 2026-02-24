# Quick Reference Guide

## Project Status
✅ GitHub repo created and pushed  
✅ Documentation organized  
✅ Execution plan ready  
🚧 Ready to start implementation

## Next Steps (Week 1 - Days 1-7)

### Day 1-2: Project Setup
```bash
# Initialize Go project
go mod init github.com/yourusername/cv-platform
go get -u github.com/gorilla/mux
go get -u github.com/mattn/go-sqlite3
go get -u golang.org/x/crypto/bcrypt
go get -u github.com/golang-jwt/jwt/v5
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

| Metric | Target |
|--------|--------|
| Inference Latency | < 100ms |
| P95 Latency | < 200ms |
| Concurrent Users | 100 |
| GPU Workers | ≤ 2 |
| Test Coverage | > 80% |

## Documentation Links

- [README.md](README.md) - Project overview
- [EXECUTION_PLAN.md](EXECUTION_PLAN.md) - Detailed 6-week plan
- [docs/technical/requirements.md](docs/technical/requirements.md) - Functional requirements
- [docs/technical/design.md](docs/technical/design.md) - System architecture
- [docs/planning/tasks.md](docs/planning/tasks.md) - Task breakdown

## Getting Started

1. Review the execution plan: `EXECUTION_PLAN.md`
2. Set up development environment (Go, Python, C++, Docker, CUDA)
3. Start with Week 1, Day 1 tasks
4. Follow the critical path in the execution plan
5. Test each component before moving to the next

## Risk Mitigation

**High-Risk Items:**
1. TensorRT compatibility - Test early with YOLOv8
2. GPU memory limits - Use small models (YOLOv8n)
3. Docker + GPU access - Set up Day 1
4. Dataset validation - Strict validation, clear errors

## Support Resources

- Go Documentation: https://go.dev/doc/
- PyTorch Documentation: https://pytorch.org/docs/
- ONNX Runtime: https://onnxruntime.ai/docs/
- TensorRT: https://docs.nvidia.com/deeplearning/tensorrt/
- Docker + GPU: https://docs.nvidia.com/datacenter/cloud-native/

---

**Last Updated**: February 24, 2026  
**Status**: Ready for implementation
