# Quick Reference Guide

## Project Structure

```
ML_platform_MVP/
├── README.md                                    # Main project README
├── QUICK_REFERENCE.md                           # This file
└── docs/                                        # All documentation
    ├── README.md                                # Documentation index
    ├── project-overview/                        # High-level overview
    │   ├── SUMMARY.md                           # Quick summary (start here!)
    │   └── project-summary.md                   # Detailed overview
    ├── technical/                               # Technical specs
    │   ├── design.md                            # Architecture & design
    │   └── requirements.md                      # Requirements
    ├── planning/                                # Project planning
    │   └── tasks.md                             # Task breakdown & timeline
    └── research/                                # Research notes
        ├── chatgpt-conversation.md              # Design discussions
        ├── chatgpt-conversation.txt             # (text format)
        └── chatgpt-conversation.pdf             # (PDF format)
```

## Where to Start

### I'm new to the project
→ Read `docs/project-overview/SUMMARY.md`

### I need to understand the architecture
→ Read `docs/technical/design.md`

### I want to know what to build
→ Read `docs/technical/requirements.md`

### I need the implementation plan
→ Read `docs/planning/tasks.md`

### I want to understand the design decisions
→ Read `docs/research/chatgpt-conversation.md`

## Key Information at a Glance

### Tech Stack
- **Backend**: Go (API server)
- **Training**: Python (GA optimization)
- **Inference**: C++ (TensorRT, ONNX Runtime)
- **Storage**: PostgreSQL + Redis
- **Deployment**: Docker + docker-compose

### Timeline
- **MVP**: 6 weeks (160 hours)
- **Full Platform**: 9 months

### Performance Goals
- Latency: <100ms
- Throughput: >10 FPS
- Concurrent Users: 100

### GPU Constraints
- Max 2 concurrent GPU jobs
- RTX 4060 target hardware

## Development Phases

1. **Week 1**: Containerized inference (Docker + API)
2. **Week 2**: TensorRT integration
3. **Week 3**: Multi-container setup (Redis + PostgreSQL)
4. **Week 4**: Training pipeline
5. **Week 5**: Genetic algorithm optimization
6. **Week 6**: Auto inference engine selection

## Important Files

| File | Purpose |
|------|---------|
| `docs/project-overview/SUMMARY.md` | Quick project overview |
| `docs/technical/design.md` | System architecture |
| `docs/technical/requirements.md` | What to build |
| `docs/planning/tasks.md` | How to build it |
| `docs/research/chatgpt-conversation.md` | Why we made these choices |

## Common Questions

**Q: What models are supported?**
A: YOLO models (YOLOv8n, YOLOv8s) for detection, segmentation, and classification

**Q: What inference engines?**
A: PyTorch (baseline), ONNX Runtime, TensorRT FP16

**Q: How does optimization work?**
A: 5-generation genetic algorithm optimizes hyperparameters, system auto-selects best inference engine

**Q: What's the deployment model?**
A: Docker containers (inference service + Redis + PostgreSQL) via docker-compose

**Q: Is this production-ready?**
A: No, this is an MVP for demonstration and learning

## Next Steps

1. Read `docs/project-overview/SUMMARY.md`
2. Review `docs/technical/design.md`
3. Check `docs/planning/tasks.md` for current phase
4. Start building!

---

**Last Updated**: [Current Date]
**Project Status**: MVP Development Phase
