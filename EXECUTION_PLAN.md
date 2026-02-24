# MVP Execution Plan

**Timeline:** 6 weeks (160 hours)  
**Target Hardware:** RTX 4060 Laptop GPU  
**Goal:** End-to-end working MVP with demo capability

## Week-by-Week Breakdown

### Week 1: Foundation & API (Days 1-7)
**Focus:** Core infrastructure, Go HTTP server, database setup

**Daily Tasks:**

**Day 1-2: Project Setup**
- Initialize Go project structure
- Set up Go modules and dependencies
- Create project directory structure
- Set up Git repository
- Configure development environment (Docker, CUDA)

**Day 3-4: HTTP Server & Database**
- Implement HTTP server (port 8080)
- Set up SQLite database connection
- Create database schema (users, models, datasets, jobs)
- Implement basic middleware (logging, error handling, CORS)
- Set up health check endpoint

**Day 5-7: Authentication & RBAC**
- Implement user model and schema
- Implement password hashing (bcrypt)
- Implement JWT authentication
- Create RBAC middleware (Admin, Engineer, Viewer)
- Implement login/logout endpoints
- Test authentication flow

**Week 1 Deliverables:**
- [ ] Go HTTP server running on port 8080
- [ ] SQLite database with schema
- [ ] Working authentication system
- [ ] Health check endpoint functional

---

### Week 2: Model & Dataset Management (Days 8-14)
**Focus:** Upload, storage, and validation of models and datasets

**Daily Tasks:**

**Day 8-9: Model Management API**
- Implement model upload endpoint
- Create model storage structure
- Implement model validation logic
- Create model listing and details endpoints

**Day 10-11: Dataset Management API**
- Implement dataset upload endpoint
- Create dataset storage structure
- Implement dataset validation (YOLO format only)
- Create dataset listing and details endpoints

**Day 12-13: ONNX Conversion Pipeline**
- Implement ONNX converter (PyTorch → ONNX)
- Create ONNX validation logic
- Implement async conversion queue
- Set up model storage and retrieval

**Day 14: Integration & Testing**
- Test model/dataset upload flow
- Verify ONNX conversion
- Test API endpoints with curl/Postman
- Fix bugs and edge cases

**Week 2 Deliverables:**
- [ ] Models can be uploaded and converted to ONNX
- [ ] Datasets can be uploaded and validated
- [ ] API endpoints for model/dataset management working
- [ ] Basic file storage system operational

---

### Week 3: Training Pipeline (Days 15-21)
**Focus:** Model training with Python, integration with Go API

**Daily Tasks:**

**Day 15-16: Python Training Module**
- Set up Python project structure
- Implement model loader (PyTorch)
- Implement dataset loader (YOLO format)
- Create training loop with metrics collection

**Day 17-18: Training API Integration**
- Create training request handler in Go
- Implement training job submission
- Set up GPU job queue (max 2 concurrent)
- Implement training progress tracking

**Day 19-20: Preset Models & Validation**
- Implement YOLOv8 preset model loading
- Create model validation pipeline
- Implement training result storage
- Test training workflow end-to-end

**Day 21: Buffer & Refinement**
- Fix training pipeline bugs
- Optimize training performance
- Test with sample dataset
- Document training API usage

**Week 3 Deliverables:**
- [ ] Training pipeline functional
- [ ] GPU job queue working (max 2 concurrent)
- [ ] Training progress tracking implemented
- [ ] End-to-end training flow tested

---

### Week 4: Inference Runtime (Days 22-28)
**Focus:** C++ inference runtime, ONNX Runtime integration

**Daily Tasks:**

**Day 22-23: C++ Inference Setup**
- Set up C++ project structure
- Integrate ONNX Runtime library
- Create inference engine interface
- Implement model loading/unloading

**Day 24-25: Inference Service**
- Implement single inference request
- Implement batch inference
- Create inference request queue
- Implement worker thread pool (default: 10 workers)

**Day 26-27: Inference API**
- Create inference endpoint in Go
- Implement inference health check
- Add latency and metrics tracking
- Test inference performance

**Day 28: Integration & Optimization**
- Integrate inference with main API
- Test concurrent inference requests
- Optimize inference latency
- Verify <100ms latency target

**Week 4 Deliverables:**
- [ ] C++ inference runtime working
- [ ] Inference API functional
- [ ] Worker pool operational
- [ ] Latency <100ms for typical models

---

### Week 5: Benchmarking & GA (Days 29-35)
**Focus:** Multi-engine benchmarking, genetic algorithm optimization

**Daily Tasks:**

**Day 29-30: Benchmarking Module**
- Implement benchmark configuration
- Create benchmarking pipeline (warmup, measurement)
- Implement metrics collection (latency, FPS, GPU%)
- Add statistical aggregation (mean, P95, P99)

**Day 31-32: Engine Selection**
- Implement multi-engine benchmarking
- Add TensorRT support (FP16)
- Create engine selection logic
- Implement constraint evaluation

**Day 33-34: Genetic Algorithm**
- Implement GA framework (5 generations)
- Create genome representation (hyperparameters)
- Implement selection, crossover, mutation
- Add elitism (carry forward best)

**Day 35: Integration & Testing**
- Integrate GA with training pipeline
- Test GA optimization workflow
- Verify engine selection works
- Test end-to-end flow

**Week 5 Deliverables:**
- [ ] Benchmarking module working
- [ ] Multi-engine support (ONNX Runtime, TensorRT)
- [ ] Engine selection automatic
- [ ] GA optimization functional (5 generations)

---

### Week 6: Reports, Packaging & Polish (Days 36-42)
**Focus:** Report generation, Docker packaging, final testing

**Daily Tasks:**

**Day 36-37: Report Generation**
- Implement report metadata collection
- Create raw metrics table formatting
- Implement delta analysis
- Generate technical and simple views

**Day 38-39: Docker Packaging**
- Create Dockerfile for inference service
- Set up docker-compose.yml
- Configure NVIDIA Docker runtime
- Test containerized deployment

**Day 40-41: Final Integration**
- End-to-end testing of all features
- Load testing (100 concurrent users)
- Performance optimization
- Bug fixes and refinements

**Day 42: Demo Preparation**
- Create demo script
- Prepare sample dataset and model
- Document usage instructions
- Final polish and cleanup

**Week 6 Deliverables:**
- [ ] Report generation working
- [ ] Docker packaging functional
- [ ] All features integrated and tested
- [ ] Demo-ready MVP

---

## Critical Path (What Must Work First)

```
Week 1: Foundation
├── HTTP Server ← CRITICAL (everything depends on this)
├── Database ← CRITICAL
└── Authentication ← CRITICAL

Week 2: Data Management
├── Model Upload ← CRITICAL (needed for training)
└── Dataset Upload ← CRITICAL (needed for training)

Week 3: Training
└── Training Pipeline ← CRITICAL (core feature)

Week 4: Inference
└── Inference Runtime ← CRITICAL (core feature)

Week 5: Optimization
├── Benchmarking ← Needed for engine selection
└── GA ← Key differentiator

Week 6: Polish
└── Packaging ← Final deliverable
```

## Daily Schedule Recommendation

**For 4-6 hours daily:**

| Time Block | Task |
|-----------|------|
| Morning (2h) | Implement new features |
| Afternoon (2h) | Test and fix issues |
| Evening (1-2h) | Documentation, planning |

**For 6-8 hours daily:**

| Time Block | Task |
|-----------|------|
| Morning (3h) | Implement new features |
| Afternoon (3h) | Testing and integration |
| Evening (2h) | Refinement and documentation |

## Milestone Checkpoints

### End of Week 1
- [ ] Can start HTTP server
- [ ] Can create user account
- [ ] Can authenticate
- [ ] Database storing data

### End of Week 2
- [ ] Can upload model
- [ ] Model converts to ONNX
- [ ] Can upload dataset
- [ ] Dataset validates correctly

### End of Week 3
- [ ] Can start training job
- [ ] Training completes
- [ ] GPU queue limits enforced
- [ ] Training results stored

### End of Week 4
- [ ] Can run inference
- [ ] Latency < 100ms
- [ ] Concurrent requests work
- [ ] Inference metrics tracked

### End of Week 5
- [ ] Can run benchmark
- [ ] Multiple engines tested
- [ ] Auto engine selection works
- [ ] GA optimization works

### End of Week 6
- [ ] Can generate package
- [ ] Docker container runs
- [ ] Demo works smoothly
- [ ] All bugs fixed

## Risk Mitigation

### High-Risk Items (Address Early)

**1. TensorRT Compatibility**
- Risk: TensorRT conversion may fail on some models
- Mitigation: Test early with YOLOv8 models
- Fallback: Use ONNX Runtime only if needed

**2. GPU Memory Limits**
- Risk: RTX 4060 has limited VRAM
- Mitigation: Test with small models first (YOLOv8n)
- Fallback: Use CPU inference for large models

**3. Docker + GPU Access**
- Risk: NVIDIA Docker setup can be tricky
- Mitigation: Set up Day 1, test immediately
- Reference: NVIDIA Docker documentation

**4. Dataset Validation**
- Risk: Users may upload invalid datasets
- Mitigation: Strict validation, clear error messages
- Fallback: Provide sample dataset for testing

### Buffer Time

- **Week 1-2:** 2 days buffer for environment setup issues
- **Week 3:** 1 day buffer for training pipeline debugging
- **Week 4:** 1 day buffer for inference optimization
- **Week 5:** 1 day buffer for GA tuning
- **Week 6:** 2 days buffer for integration and testing

## Testing Strategy

### Unit Tests (Ongoing)
- Test each component in isolation
- Use table-driven tests in Go
- Use pytest in Python
- Target: 70% coverage

### Integration Tests (Week 3+)
- Test API endpoints with database
- Test training pipeline end-to-end
- Test inference with real models

### Load Testing (Week 6)
- Test 100 concurrent requests
- Verify latency under load
- Check for memory leaks

### Demo Testing (Week 6)
- Run full workflow from start to finish
- Time each step
- Prepare for common issues

## Development Environment Setup

### Required Software
- Go 1.20+
- Python 3.10+
- C++ 17 compiler
- Docker
- NVIDIA Docker runtime
- CUDA 12.2
- Git

### Recommended IDEs
- Go: GoLand or VS Code with Go extension
- Python: PyCharm or VS Code with Python extension
- C++: CLion or VS Code with C++ extension

### Hardware Requirements
- NVIDIA GPU (RTX 4060 or similar)
- 16 GB RAM minimum
- 50 GB storage minimum

## Success Criteria

### Must Have (MVP)
- [ ] Model upload and ONNX conversion
- [ ] Dataset upload and validation
- [ ] Training pipeline with GA
- [ ] Inference API with <100ms latency
- [ ] Multi-engine benchmarking
- [ ] Auto engine selection
- [ ] Docker packaging
- [ ] Working demo

### Nice to Have (If Time Permits)
- [ ] TUI interface
- [ ] Report export (JSON, CSV)
- [ ] Advanced metrics collection
- [ ] User management UI

## Common Issues & Solutions

### Issue: CUDA Out of Memory
**Solution:** Reduce batch size, use smaller models (YOLOv8n)

### Issue: ONNX Conversion Fails
**Solution:** Use matching PyTorch/ONNX versions, simplify model

### Issue: Docker GPU Access Denied
**Solution:** Install NVIDIA Docker runtime, use nvidia-docker command

### Issue: Training Too Slow
**Solution:** Use smaller dataset, reduce epochs, use YOLOv8n

### Issue: API Not Responding
**Solution:** Check logs, verify database connection, check GPU availability

## Next Steps After MVP

1. **Week 7-8:** Add TUI interface
2. **Week 9-10:** Add TensorRT INT8 support
3. **Week 11-12:** Add advanced monitoring
4. **Month 4+:** Add distributed training support

## References

- [Design Document](docs/technical/design.md)
- [Requirements](docs/technical/requirements.md)
- [Tasks Breakdown](docs/planning/tasks.md)
- [Research Notes](docs/research/chatgpt-conversation.md)

---

**Plan Version:** 1.0  
**Created:** February 2026  
**Status:** Ready for Execution