# TrackStudio Architecture

## System Overview

TrackStudio is built on a hybrid development/production architecture with three main components:

1. **Orchestrator (Go)**: Backend API and processing engine
2. **WebApp (Next.js)**: User interface
3. **CQAI Integration**: AI services (audio analysis, LLM, image generation)

## Component Diagram

```
[User] → [Next.js UI] → [Go API] → [SQLite DB]
                          ↓
                    [Queue Worker] → [CQAI Services]
                          ↓
                    [Video Generator] → [Storage]
                          ↓
                    [YouTube Upload]
```

## Processing Pipeline

1. **Upload**: User uploads vocal/music stems + lyrics
2. **Analysis**: Audio analyzed for BPM, key, duration
3. **Parsing**: Lyrics parsed into sections
4. **Prompt Generation**: LLM creates image prompts
5. **Image Generation**: Zimage creates backgrounds
6. **Video Composition**: FFmpeg combines all elements
7. **Upload**: YouTube automation

See [Implementation Guide](guides/IMPLEMENTATION.md) for details.
