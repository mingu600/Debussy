# Agentic Music Composition Tool - Technical Implementation Plan

## Overview

An agentic music composition system that integrates with Claude Code to automate and iterate on musical composition through a feedback loop involving MusicXML generation, MuseScore integration, and audio analysis.

## Architecture Components

### 1. MCP Server for MuseScore Integration

#### Core Functionality
- **Purpose**: Bridge between Claude Code and MuseScore for file operations and rendering
- **Protocol**: Model Context Protocol (MCP) server
- **Language**: Python (recommended for MuseScore API compatibility)

#### Required Methods
```python
# File Operations
- import_musicxml(filepath: str) -> bool
- export_musicxml(filepath: str) -> bool
- export_audio(filepath: str, format: "wav"|"mp3") -> bool
- export_midi(filepath: str) -> bool
- save_project(filepath: str) -> bool

# Score Operations  
- get_score_info() -> dict  # duration, measures, instruments
- validate_notation() -> list[dict]  # technical issues
- get_measure_range(start: int, end: int) -> str  # MusicXML fragment

# Playback Control (optional)
- play_score() -> bool
- stop_playback() -> bool
- play_from_measure(measure: int) -> bool
```

#### Implementation Approach
- Use MuseScore's command-line interface for basic operations
- For advanced integration, consider MuseScore's plugin API
- Implement file watching for automated import/export
- Handle MuseScore process lifecycle management

### 2. Claude Code Extensions

#### Music Generation Module
```python
# Core classes for MusicXML generation
class MusicXMLGenerator:
    - parse_specification(spec_md: str) -> CompositionPlan
    - generate_structure() -> ScoreStructure
    - create_melodic_material() -> list[Voice]
    - harmonize() -> HarmonicProgression
    - arrange_for_instruments() -> Score
    - export_musicxml() -> str

class CompositionPlan:
    - instrumentation: list[Instrument]
    - structure: FormStructure
    - style_parameters: dict
    - constraints: list[Constraint]
    - motifs: list[MusicMotif]
```

#### Analysis Module
```python
class MusicAnalyzer:
    # Technical Analysis
    - check_playability(score: Score) -> list[Issue]
    - verify_ranges(score: Score) -> list[RangeViolation]
    - analyze_voice_leading(score: Score) -> list[VoiceLeadingIssue]
    
    # Spec Compliance
    - verify_structure(score: Score, spec: CompositionPlan) -> ComplianceReport
    - check_instrumentation(score: Score, spec: CompositionPlan) -> bool
    - analyze_motif_usage(score: Score, motifs: list[MusicMotif]) -> MotifReport
    
    # Audio Analysis
    - analyze_audio_file(filepath: str) -> AudioAnalysis
    - detect_tempo(audio: AudioData) -> float
    - measure_dynamics(audio: AudioData) -> DynamicsProfile
    - identify_silence_gaps(audio: AudioData) -> list[TimeRange]
```

### 3. Workflow Orchestration

#### State Management
```python
class CompositionProject:
    - id: str
    - spec_file: str
    - current_version: int
    - history: list[CompositionVersion]
    - feedback_log: list[FeedbackEntry]
    
class CompositionVersion:
    - version_number: int
    - musicxml_file: str
    - audio_render: str
    - analysis_report: AnalysisReport
    - timestamp: datetime
```

#### Revision Loop Controller
```python
class CompositionIterator:
    - load_project(project_path: str) -> CompositionProject
    - generate_initial() -> CompositionVersion
    - analyze_version(version: CompositionVersion) -> AnalysisReport
    - apply_feedback(feedback: str) -> RevisionPlan
    - generate_revision(plan: RevisionPlan) -> CompositionVersion
    - compare_versions(v1: CompositionVersion, v2: CompositionVersion) -> Diff
```

## File Structure

```
project-root/
├── mcp-musescore/
│   ├── server.py              # MCP server implementation
│   ├── musescore_api.py       # MuseScore interface
│   └── requirements.txt
│
├── claude-code-music/
│   ├── generator/
│   │   ├── musicxml.py        # MusicXML generation
│   │   ├── harmonizer.py      # Harmonic progression logic
│   │   └── orchestrator.py    # Instrumentation logic
│   │
│   ├── analyzer/
│   │   ├── technical.py       # Technical correctness checks
│   │   ├── compliance.py      # Spec compliance verification
│   │   └── audio.py           # Audio analysis tools
│   │
│   └── workflow/
│       ├── project.py         # Project state management
│       └── iterator.py        # Revision loop controller
│
└── compositions/              # User composition projects
    └── string-quartet-em/
        ├── spec.md
        ├── versions/
        │   ├── v1/
        │   │   ├── score.musicxml
        │   │   ├── render.wav
        │   │   └── analysis.json
        │   └── v2/
        └── feedback.md
```

## Implementation Phases

### Phase 1: Core Infrastructure (Week 1-2)
- [ ] Basic MCP server with file import/export
- [ ] Simple MusicXML generator for basic scores
- [ ] Project structure and state management
- [ ] Manual testing workflow

### Phase 2: Generation Capabilities (Week 3-4)
- [ ] Specification parser for markdown files
- [ ] Melodic generation with motif support
- [ ] Basic harmonization algorithms
- [ ] Multi-instrument arrangement

### Phase 3: Analysis Loop (Week 5-6)
- [ ] Technical correctness checker
- [ ] Spec compliance verification
- [ ] Basic audio analysis (silence detection, duration)
- [ ] Automated revision suggestions

### Phase 4: Advanced Features (Week 7-8)
- [ ] Style-aware generation (classical, jazz, etc.)
- [ ] Advanced audio analysis (tempo, dynamics)
- [ ] Comparative analysis between versions
- [ ] Human feedback integration

### Phase 5: Polish & Integration (Week 9-10)
- [ ] Claude Code integration optimizations
- [ ] Performance improvements
- [ ] Extended MuseScore operations
- [ ] Documentation and examples

## Technical Considerations

### MusicXML Complexity
- Start with single-staff instruments
- Gradually add support for:
  - Multiple voices per staff
  - Complex rhythms (tuplets, nested)
  - Articulations and dynamics
  - Tempo changes and fermatas

### Musical Knowledge Encoding
- Rule-based systems for common practices
- Style templates for different genres
- Configurable constraint systems
- Machine learning integration points (future)

### Performance Optimization
- Incremental MusicXML generation
- Cached analysis results
- Parallel processing for multi-movement works
- Efficient diff algorithms for versions

### Error Handling
- Graceful MuseScore crashes
- Partial score recovery
- Malformed MusicXML handling
- Network interruption recovery

## Integration with Claude Code

### Command Interface
```bash
# Initialize new composition
claude-code compose init "String Quartet in E Minor"

# Generate from specification
claude-code compose generate --spec spec.md

# Analyze current version
claude-code compose analyze

# Apply revision based on feedback
claude-code compose revise --feedback "Make the development section more dramatic"

# Compare versions
claude-code compose diff v1 v2
```

### Prompt Engineering
- Structured prompts for consistent generation
- Style-specific prompt templates
- Feedback interpretation guidelines
- Constraint specification format

## Success Metrics

1. **Technical Accuracy**: <5% of generated scores have notation errors
2. **Spec Compliance**: >90% of specifications correctly implemented
3. **Iteration Efficiency**: Average 3-5 iterations to acceptable result
4. **Generation Speed**: <30 seconds for 5-minute piece
5. **User Satisfaction**: Measured through feedback quality

## Future Enhancements

- **Real-time collaboration**: Multiple users editing specs
- **Style learning**: Analyze existing scores to extract style
- **Performance preparation**: Generate practice tracks, parts
- **Extended formats**: Support for Sibelius, Finale, LilyPond
- **AI accompaniment**: Generate complementary parts for existing music
- **Live coding**: Real-time composition with immediate playback