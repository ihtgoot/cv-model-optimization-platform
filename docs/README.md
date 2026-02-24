# CV Model Optimization & Deployment Platform - Documentation

This directory contains all project documentation organized by purpose and audience.

## Directory Structure

```
docs/
├── README.md                           # This file
├── project-overview/                   # High-level project information
│   ├── SUMMARY.md                      # Quick project summary
│   └── project-summary.md              # Detailed project overview
├── technical/                          # Technical specifications
│   ├── design.md                       # System architecture and design
│   └── requirements.md                 # Functional and non-functional requirements
├── planning/                           # Project planning and tasks
│   └── tasks.md                        # Detailed task breakdown and timeline
└── research/                           # Research notes and conversations
    ├── chatgpt-conversation.md         # ChatGPT discussion (markdown)
    ├── chatgpt-conversation.txt        # ChatGPT discussion (text)
    └── chatgpt-conversation.pdf        # ChatGPT discussion (PDF)
```

## Quick Start

### For New Team Members
1. Start with `project-overview/SUMMARY.md` for a quick overview
2. Read `technical/design.md` to understand the architecture
3. Review `technical/requirements.md` for detailed specifications
4. Check `planning/tasks.md` for implementation roadmap

### For Developers
- **Architecture**: `technical/design.md`
- **Requirements**: `technical/requirements.md`
- **Tasks**: `planning/tasks.md`

### For Project Managers
- **Overview**: `project-overview/SUMMARY.md`
- **Timeline**: `planning/tasks.md`
- **Scope**: `technical/requirements.md`

## Document Descriptions

### Project Overview
- **SUMMARY.md**: Concise project summary with key deliverables, tech stack, and timeline
- **project-summary.md**: Comprehensive project overview with all specifications

### Technical Documentation
- **design.md**: Complete system architecture including:
  - High-level system diagrams
  - Module architecture
  - Data models and database schema
  - Resource management
  - Deployment architecture
  
- **requirements.md**: Detailed requirements including:
  - 9 functional requirement categories
  - 6 non-functional requirement categories
  - Acceptance criteria
  - Constraints and assumptions

### Planning
- **tasks.md**: Implementation roadmap with:
  - 8 phases of development
  - 160 hours of estimated effort
  - Task dependencies
  - Success criteria

### Research
- **chatgpt-conversation.***: Original research conversations that informed the project design
  - Contains discussions on GA optimization, inference optimization, and MVP scoping
  - Available in multiple formats (markdown, text, PDF)

## Key Project Information

### Technology Stack
- **Backend**: Go 1.20+ (API & orchestration)
- **Training**: Python 3.10+ (GA optimization)
- **Inference**: C++ 17 (ONNX Runtime, TensorRT)
- **Database**: SQLite
- **Deployment**: Docker, docker-compose

### Timeline
- **MVP**: 160 hours (~6 weeks focused development)
- **Full Platform**: 9 months post-MVP

### Performance Targets
- Inference Latency: < 100ms
- P95 Latency: < 200ms
- Throughput: > 10 FPS
- Concurrent Users: 100

## Contributing

When adding new documentation:
1. Place it in the appropriate directory based on its purpose
2. Update this README if adding new categories
3. Keep documentation synchronized with implementation
4. Use clear, concise language
5. Include diagrams where helpful

## Version History

- **v1.0** (Current): Initial documentation structure
  - Complete design, requirements, and task breakdown
  - Research notes from planning phase
