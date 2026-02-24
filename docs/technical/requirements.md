# Requirements Document: CV Model Optimization & Deployment Platform (MVP)

## Functional Requirements

### FR1: Model Management

**FR1.1 Model Upload**
- Users with Admin/Engineer roles can upload CV models
- Supported formats: PyTorch (.pt, .pth), TensorFlow (.pb, .h5), ONNX (.onnx)
- System validates model architecture and input/output shapes
- Models stored with metadata (name, description, framework, task type)
- Maximum model size: 1 GB

**FR1.2 Preset Models**
- System provides preset models for common tasks
- Preset models: YOLOv8 (detection), ResNet50 (classification), EfficientNet (classification)
- Preset models pre-loaded and ready for training/benchmarking
- Users can view preset model details and specifications

**FR1.3 Model Listing & Details**
- Users can list all available models (preset + custom)
- Users can view model details: name, type, framework, task type, size, input/output shapes
- Admin users can delete models
- Models display creation timestamp and creator

**FR1.4 Model Conversion to ONNX**
- All models automatically converted to ONNX format after upload
- Conversion happens asynchronously (CPU task)
- System validates ONNX model after conversion
- ONNX model stored alongside original model

### FR2: Dataset Management

**FR2.1 Dataset Upload**
- Users with Admin/Engineer roles can upload datasets
- Supported formats: ZIP (containing images/videos), CSV (metadata)
- System validates dataset structure and task compatibility
- Datasets stored with metadata (name, task type, num_samples, num_classes)
- Maximum dataset size: 10 GB

**FR2.2 Dataset Preprocessing**
- System automatically preprocesses datasets (CPU task)
- Preprocessing: normalization, resizing, augmentation
- Train/validation/test split configurable (default: 80/10/10)
- Preprocessing results cached for reuse

**FR2.3 Dataset Listing & Details**
- Users can list all available datasets
- Users can view dataset details: name, task type, num_samples, num_classes, size
- Datasets display creation timestamp and creator

### FR3: Training & Optimization

**FR3.1 Model Training**
- Users with Admin/Engineer roles can start training jobs
- Training accepts: model, dataset, hyperparameters, max epochs, validation split
- Training runs on GPU (queued if GPU unavailable)
- Training progress tracked and displayed in real-time
- Training results: final accuracy, final loss, best epoch, trained model

**FR3.2 Genetic Algorithm Optimization**
- Optional GA-based hyperparameter optimization
- GA configuration: population size, generations, mutation rate, crossover rate
- GA optimizes: learning rate, batch size, optimizer, weight decay, dropout rate
- GA fitness function: validation accuracy
- GA results: best hyperparameters, fitness history

**FR3.3 Training Monitoring**
- Real-time training progress display (epoch, loss, accuracy)
- Training can be cancelled by Admin users
- Training status: queued, running, completed, failed, cancelled
- Training results stored in database

**FR3.4 Training History**
- Users can view training history for each model
- Training history includes: hyperparameters, results, duration, timestamp
- Training history exportable as CSV/JSON

### FR4: Benchmarking

**FR4.1 Multi-Engine Benchmarking**
- Users with Admin/Engineer roles can benchmark models
- Supported engines: ONNX Runtime, TensorRT FP16
- Benchmarking configurable: batch sizes, input shapes, warmup iterations, measurement iterations
- Benchmarking runs on GPU (queued if GPU unavailable)

**FR4.2 Benchmark Metrics**
- System collects: latency (mean, P95, P99), throughput (FPS), GPU utilization (%), CPU utilization (%), VRAM usage (MB)
- Metrics collected for each engine and batch size combination
- Metrics stored in database for historical analysis

**FR4.3 Benchmark Monitoring**
- Real-time benchmark progress display
- Benchmark status: queued, running, completed, failed
- Benchmark results displayed after completion

**FR4.4 Constraint Evaluation**
- Users can specify performance constraints (e.g., latency < 50ms, throughput > 20 FPS)
- System evaluates constraints against benchmark results
- Constraint evaluation results displayed in report

### FR5: Report Generation

**FR5.1 Deterministic Report Generation**
- Reports generated deterministically (no LLM, no randomness)
- Reports include: metadata, raw metrics table, delta analysis, constraint evaluation, engine selection reasoning
- Reports stored in database with unique ID

**FR5.2 Dual-Level Reporting**
- Technical view: detailed metrics, delta analysis, constraint evaluation, recommendations
- Simple view: selected engine, key metrics, why this engine, deployment steps
- Both views generated for each benchmark

**FR5.3 Report Viewing & Export**
- Users can view reports in technical or simple view
- Reports exportable as PDF, HTML, JSON, CSV
- Reports include timestamp and model information

**FR5.4 Engine Selection**
- System automatically selects optimal engine based on:
  1. Constraint satisfaction (engines that satisfy all constraints)
  2. Latency/throughput ranking (if multiple engines satisfy constraints)
  3. Memory efficiency (if latency/throughput tied)
- Selection reasoning documented in report

### FR6: Inference Service

**FR6.1 Model Inference**
- Users with Engineer/Viewer roles can run inference
- Inference accepts: model ID, input data, batch size
- Inference returns: output data, latency, confidence scores (if applicable)
- Inference runs on bounded worker pool (default: 10 workers)

**FR6.2 Inference Concurrency**
- System handles 100 concurrent HTTP users
- Inference requests queued if workers busy
- Queue timeout: 30 seconds
- Inference timeout: 30 seconds

**FR6.3 Inference Metrics**
- System tracks: request count, latency distribution, error count, GPU/CPU utilization, VRAM usage
- Metrics aggregated and displayed in system dashboard

**FR6.4 Inference Health Check**
- Health check endpoint returns: model status, worker pool status, queue depth, GPU status
- Health check used for monitoring and load balancing

### FR7: Package Generation

**FR7.1 Deployment Package Creation**
- Users with Admin role can generate deployment packages
- Package includes: model, inference runtime, configuration, documentation
- Package format: Docker image
- Package versioning: semantic versioning (v1.0.0)

**FR7.2 Package Configuration**
- Package configuration includes: model ID, selected engine, inference workers, max concurrency, GPU memory limit, CPU threads
- Configuration stored in package metadata
- Configuration customizable before package generation

**FR7.3 Package Artifacts**
- Package includes: ONNX model, inference runtime (C++), configuration files, deployment guide, health check script
- Package artifacts checksummed for integrity verification
- Package size tracked and displayed

**FR7.4 Package Deployment**
- Package deployable via Docker: docker run -d cv-platform:v1.0.0
- Package includes docker-compose.yml for multi-container deployment
- Package includes deployment guide with step-by-step instructions

### FR8: Role-Based Access Control (RBAC)

**FR8.1 User Roles**
- Admin: Full access (train, modify hyperparams, run GA, benchmark, generate package, view reports)
- Engineer: Train, run inference, view reports, inspect configuration
- Viewer: Access prediction endpoint only

**FR8.2 Authentication**
- Users authenticate with username/password
- System generates API key for programmatic access
- API key stored securely (hashed)
- Session timeout: 24 hours

**FR8.3 Authorization**
- System enforces role-based authorization on all endpoints
- Unauthorized requests return 403 Forbidden
- Authorization checked before request processing

**FR8.4 User Management**
- Admin users can create, list, delete users
- Admin users can modify user roles
- User creation requires username, password, role
- Passwords stored as bcrypt hashes

### FR9: System Monitoring & Health

**FR9.1 System Health Check**
- Health check endpoint returns: API status, database status, GPU status, inference service status
- Health check used for monitoring and alerting

**FR9.2 System Metrics**
- System tracks: active jobs, queue depth, GPU utilization, CPU utilization, memory usage, request rate, error rate
- Metrics displayed in admin dashboard
- Metrics exportable as JSON

**FR9.3 Logging**
- System logs all requests, errors, and important events
- Logs include: timestamp, request ID, user ID, action, status, duration
- Logs stored in files (rotating logs)
- Log level configurable: DEBUG, INFO, WARN, ERROR, FATAL

**FR9.4 Alerting**
- System alerts on: GPU queue full, inference queue full, high error rate, database errors
- Alerts displayed in admin dashboard
- Alerts sent via email (optional)

## Non-Functional Requirements

### NFR1: Performance

**NFR1.1 Inference Latency**
- Single inference latency: < 100ms (for typical models)
- P95 latency: < 200ms
- P99 latency: < 500ms

**NFR1.2 Throughput**
- System handles 100 concurrent HTTP users
- Inference throughput: > 10 FPS (for typical models)
- Benchmark throughput: > 5 FPS

**NFR1.3 GPU Resource Utilization**
- GPU workers: maximum 2 concurrent
- GPU memory: configurable limit (default: 16 GB)
- GPU utilization: > 80% during training/benchmarking

**NFR1.4 Response Time**
- API response time: < 1 second (for non-GPU tasks)
- API response time: < 5 seconds (for GPU task submission)

### NFR2: Scalability

**NFR2.1 Concurrent Users**
- System supports 100 concurrent HTTP users
- Inference worker pool: configurable (default: 10)
- GPU job queue: bounded (max: 2 concurrent)

**NFR2.2 Data Volume**
- Model size: up to 1 GB
- Dataset size: up to 10 GB
- Database size: up to 100 GB (SQLite)

**NFR2.3 Job Queue**
- GPU job queue: bounded (max: 2 concurrent)
- Inference request queue: bounded (max: 1000 requests)
- CPU task queue: unbounded (parallel execution)

### NFR3: Reliability

**NFR3.1 Availability**
- System uptime: > 99% (excluding maintenance)
- Health check: every 30 seconds
- Automatic recovery: restart failed components

**NFR3.2 Data Persistence**
- All data persisted to SQLite database
- Database backups: daily (manual)
- Data recovery: restore from backup

**NFR3.3 Error Handling**
- All errors logged with context (request ID, user ID, timestamp)
- Errors returned to client with error code and message
- Graceful degradation: system continues operating if non-critical component fails

**NFR3.4 Fault Tolerance**
- GPU job failure: job marked as failed, error logged, next job started
- Inference failure: request marked as failed, error returned to client
- Database failure: system returns 503 Service Unavailable

### NFR4: Security

**NFR4.1 Authentication**
- Username/password authentication with bcrypt hashing
- API key authentication for programmatic access
- Session management: 24-hour timeout

**NFR4.2 Authorization**
- Role-based access control (Admin, Engineer, Viewer)
- All endpoints enforce authorization
- Unauthorized requests return 403 Forbidden

**NFR4.3 Data Protection**
- Sensitive data (passwords, API keys) hashed
- TLS/HTTPS support (optional, configurable)
- Input validation on all endpoints

**NFR4.4 Audit Logging**
- All user actions logged (create, read, update, delete)
- Logs include: user ID, action, resource, timestamp, result
- Logs retained for 90 days

### NFR5: Maintainability

**NFR5.1 Code Quality**
- Code follows language-specific style guides (Go, Python, C++)
- Code reviewed before merge
- Test coverage: > 80%

**NFR5.2 Documentation**
- API documentation: OpenAPI/Swagger
- Architecture documentation: design document
- Deployment documentation: deployment guide
- Code comments: inline comments for complex logic

**NFR5.3 Monitoring & Observability**
- Structured logging: JSON format
- Metrics collection: Prometheus-compatible
- Tracing: request ID propagation
- Health checks: liveness and readiness probes

**NFR5.4 Deployment**
- Single Docker image deployment
- Environment variable configuration
- Health check: HTTP endpoint
- Graceful shutdown: 30-second timeout

### NFR6: Usability

**NFR6.1 User Interface**
- TUI interface: primary interface
- HTMX demo UI: optional, minimal
- Intuitive navigation and clear error messages

**NFR6.2 API Usability**
- RESTful API design
- Consistent error responses
- Comprehensive API documentation
- Example requests and responses

**NFR6.3 Reporting**
- Dual-level reporting: technical and simple views
- Clear visualizations and tables
- Exportable reports (PDF, HTML, JSON, CSV)

## Constraints & Assumptions

### Constraints

1. **No LLM**: Report generation is deterministic, no LLM integration
2. **No Redis**: No external caching layer, in-memory caching only
3. **No Cloud-Native Orchestration**: No Kubernetes, Docker Swarm, or similar
4. **SQLite Only**: No external database, SQLite for persistence
5. **GPU Limit**: Maximum 2 concurrent GPU jobs
6. **Model Size**: Maximum 1 GB per model
7. **Dataset Size**: Maximum 10 GB per dataset
8. **Concurrent Users**: 100 concurrent HTTP users
9. **No Multi-Node**: Single-node deployment only

### Assumptions

1. **GPU Availability**: System assumes GPU is available (NVIDIA GPU with CUDA)
2. **Model Format**: Models provided in PyTorch, TensorFlow, or ONNX format
3. **Dataset Format**: Datasets provided as ZIP or CSV
4. **Task Types**: Only detection (video) and classification (image) tasks supported
5. **Inference Engines**: ONNX Runtime and TensorRT FP16 available
6. **Docker**: Docker available for packaging and deployment
7. **Python 3.10+**: Python 3.10 or later available
8. **Go 1.20+**: Go 1.20 or later available
9. **C++ 17**: C++ 17 or later available

## Acceptance Criteria

### AC1: Model Management
- [ ] Users can upload models in PyTorch, TensorFlow, ONNX formats
- [ ] System validates model architecture and shapes
- [ ] Models automatically converted to ONNX
- [ ] Preset models available and ready to use
- [ ] Users can list and view model details
- [ ] Admin users can delete models

### AC2: Dataset Management
- [ ] Users can upload datasets as ZIP or CSV
- [ ] System validates dataset structure
- [ ] Datasets automatically preprocessed
- [ ] Train/validation/test split configurable
- [ ] Users can list and view dataset details

### AC3: Training & Optimization
- [ ] Users can start training jobs with hyperparameters
- [ ] GA optimization optional and configurable
- [ ] Training progress displayed in real-time
- [ ] Training can be cancelled
- [ ] Training results stored and retrievable

### AC4: Benchmarking
- [ ] Users can benchmark models on multiple engines
- [ ] Metrics collected: latency, throughput, GPU/CPU utilization, VRAM
- [ ] Benchmark progress displayed in real-time
- [ ] Constraints can be specified and evaluated
- [ ] Benchmark results stored and retrievable

### AC5: Report Generation
- [ ] Reports generated deterministically
- [ ] Dual-level reporting: technical and simple views
- [ ] Reports include metadata, metrics, delta analysis, constraint evaluation
- [ ] Engine selection reasoning documented
- [ ] Reports exportable as PDF, HTML, JSON, CSV

### AC6: Inference Service
- [ ] Users can run inference on models
- [ ] System handles 100 concurrent users
- [ ] Inference latency < 100ms (typical models)
- [ ] Inference metrics tracked and displayed
- [ ] Health check endpoint available

### AC7: Package Generation
- [ ] Admin users can generate deployment packages
- [ ] Packages include model, runtime, configuration, documentation
- [ ] Packages deployable via Docker
- [ ] Package configuration customizable
- [ ] Package artifacts checksummed

### AC8: RBAC
- [ ] Admin users have full access
- [ ] Engineer users can train and run inference
- [ ] Viewer users can only access inference endpoint
- [ ] Authentication with username/password
- [ ] API key authentication supported

### AC9: System Monitoring
- [ ] Health check endpoint available
- [ ] System metrics tracked and displayed
- [ ] Logging implemented with configurable level
- [ ] Alerts on critical events

### AC10: GPU Resource Management
- [ ] Maximum 2 concurrent GPU jobs enforced
- [ ] GPU job queue bounded and managed
- [ ] GPU memory limit configurable
- [ ] GPU utilization monitored

### AC11: Inference Concurrency
- [ ] Inference worker pool configurable
- [ ] Inference request queue bounded
- [ ] Queue timeout: 30 seconds
- [ ] Inference timeout: 30 seconds

### AC12: Deterministic Benchmarking
- [ ] Same benchmark config produces same results
- [ ] Warmup phase before measurement
- [ ] Multiple iterations for statistical accuracy
- [ ] Metrics aggregated deterministically

