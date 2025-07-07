# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an agentic music composition system that integrates with Claude Code to automate musical composition through MusicXML generation, MuseScore integration, and iterative refinement based on analysis feedback.

## Architecture

The system consists of three main components:
1. **MCP Server** (`mcp-musescore/`): Python-based Model Context Protocol server for MuseScore integration
2. **Music Engine** (`claude-code-music/`): Core composition, analysis, and workflow management
3. **Compositions** (`compositions/`): User projects with versioned scores and feedback

## Development Commands

### MCP Server
```bash
# Install dependencies
cd mcp-musescore && pip install -r requirements.txt

# Start MCP server
python server.py

# Test MuseScore integration
python -m pytest tests/test_musescore_api.py
```

### Music Engine
```bash
# Install dependencies
cd claude-code-music && pip install -r requirements.txt

# Run tests
python -m pytest tests/

# Run specific test suite
python -m pytest tests/test_generator.py
python -m pytest tests/test_analyzer.py
```

### User Interface Commands
```bash
# Initialize new composition project
/compose init "Project Name"

# Generate from specification
/compose generate --spec spec.md

# Analyze current version
/compose analyze

# Apply revision with feedback
/compose revise --feedback "Make the development section more dramatic"

# Compare versions
/compose diff v1 v2
```

## Key Implementation Details

### MusicXML Generation
- Start with single-staff instruments for Phase 1
- Use CLI interface for MuseScore operations (file import/export, rendering)
- Implement incremental generation for complex scores
- Support constraint-based composition through specification parsing

### Project State Management
- File-based JSON storage in `compositions/project-name/`
- Version control through numbered directories (`v1/`, `v2/`, etc.)
- Each version contains: `score.musicxml`, `render.wav`, `analysis.json`
- Project metadata in `project.json` at root level

### Analysis Pipeline
- Technical correctness: playability, range violations, voice leading
- Spec compliance: structure, instrumentation, motif usage verification
- Audio analysis: tempo detection, dynamics measurement, silence gaps
- Use librosa for audio processing tasks

### Revision Loop
- Parse natural language feedback into structured revision plans
- Apply targeted modifications rather than full regeneration
- Maintain composition history for comparative analysis
- Support both automated and human-guided iterations

## User Experience Design

### For Non-Coding Composers
- Natural language specification in markdown format
- Conversational interface through Claude Code chat
- Automatic handling of technical music notation details
- Audio rendering for immediate feedback

### Specification Format
```markdown
# Composition Title

## Instrumentation
- Violin I
- Violin II
- Viola
- Cello

## Structure
- Exposition (A-B-A)
- Development
- Recapitulation

## Style Parameters
- Key: E minor
- Tempo: Andante (i = 72)
- Mood: Melancholic, introspective
```

## Development Phases

### Phase 1: Core Infrastructure
- Basic MCP server with file operations
- Simple MusicXML generation for monophonic melodies
- Project initialization and state management
- Manual testing workflow

### Phase 2: Generation Capabilities
- Specification parser for markdown files
- Melodic generation with motif development
- Basic harmonization algorithms
- Multi-instrument arrangement

### Phase 3: Analysis Loop
- Technical correctness validation
- Spec compliance verification
- Audio analysis integration
- Automated revision suggestions

## Technical Constraints

### Music Theory Rules
- Implement common practice voice leading rules
- Support multiple musical styles (classical, jazz, contemporary)
- Configurable constraint systems for different genres
- Graceful handling of rule violations vs. creative liberties

### Performance Considerations
- Cache analysis results between iterations
- Parallel processing for multi-movement works
- Efficient MusicXML parsing and generation
- Incremental updates rather than full regeneration

### Error Handling
- Graceful MuseScore process failures
- Malformed MusicXML recovery
- Partial score completion when possible
- Clear error messages for specification issues

## Integration Points

### MuseScore CLI Operations
```bash
# Import MusicXML
mscore -o output.mscz input.musicxml

# Export audio
mscore -o output.wav input.mscz

# Export MIDI
mscore -o output.mid input.mscz
```

### File Watching
- Monitor specification changes for auto-regeneration
- Track MuseScore file modifications
- Automatic project backup on significant changes

## Success Metrics

- Technical accuracy: <5% notation errors in generated scores
- Spec compliance: >90% of specifications correctly implemented
- Iteration efficiency: Average 3-5 iterations to acceptable result
- Generation speed: <30 seconds for 5-minute pieces