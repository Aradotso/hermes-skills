---
name: openclaw-videotranslate-skill
description: Translate video subtitles and generate auto-dubbing with LLM/TTS providers using OpenClaw's adaptive scheduler
triggers:
  - translate video subtitles to another language
  - add dubbed audio to video in different language
  - convert video subtitles with TTS dubbing
  - create multi-track video with translated audio
  - process video translation and dubbing
  - generate multilingual video with subtitles
  - translate and dub video content
  - add foreign language voiceover to video
---

# OpenClaw Video Translation & Dubbing Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This OpenClaw skill translates video subtitles and generates TTS-based auto-dubbing across multiple languages. It features a 3D adaptive scheduler to handle API rate limits, supports dual processing modes (subtitle-only or subtitle+dubbing), and outputs lossless multi-track videos with aligned audio.

## Installation

```bash
# Clone the repository
git clone https://github.com/zbjincheng/openclaw-skill-videotranslate.git
cd openclaw-skill-videotranslate

# Install the skill
pip install -e .

# For development with testing dependencies
pip install -e ".[dev]"
```

**Prerequisites:**
- Python 3.11 or 3.12
- FFmpeg installed and available in PATH

```bash
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt install ffmpeg
```

## Configuration

Create a `.env` file from the template:

```bash
cp .env.example .env
```

Key environment variables to configure:

```bash
# Translation API credentials
TRANSLATION_ENDPOINT=https://api.your-provider.com/v1/chat/completions
TRANSLATION_API_KEY=your_api_key_here

# TTS API credentials (required for dubbing mode)
TTS_ENDPOINT=https://api.your-tts-provider.com/v1/audio/speech
TTS_API_KEY=your_tts_api_key_here
```

The skill uses `manifest.yaml` for declarative configuration:

```yaml
parameters:
  video_path:
    type: path
    required: true
    description: "Path to source video file"
  
  source_language:
    type: string
    required: true
    default: "en"
    description: "Source language code (en, zh-CN, ja, etc.)"
  
  target_language:
    type: string
    required: true
    default: "zh-CN"
    description: "Target language code"
  
  processing_mode:
    type: enum
    required: true
    default: "subtitle_and_dubbing"
    values: ["subtitle_only", "subtitle_and_dubbing"]
  
  translation_provider:
    type: enum
    required: true
    values: ["llm", "web"]
  
  translation_endpoint:
    type: string
    required: true
  
  translation_credential:
    type: secret
    required: true
  
  tts_provider:
    type: enum
    required: false
    values: ["llm", "web"]
  
  tts_endpoint:
    type: string
    required: false
  
  tts_credential:
    type: secret
    required: false
```

## Usage Patterns

### Basic Subtitle Translation (No Dubbing)

```python
from translation_dubbing_skill.models import ProcessingMode, SkillConfig
from translation_dubbing_skill.entry import run_skill

config = SkillConfig(
    video_path="input_video.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_ONLY,
    translation_provider="llm",
    translation_endpoint="${TRANSLATION_ENDPOINT}",
    translation_credential="${TRANSLATION_API_KEY}",
)

# Run the skill
result = run_skill(config)
print(f"Output video: {result.output_video_path}")
print(f"Translated subtitles: {result.subtitle_path}")
```

### Full Translation + Dubbing

```python
from translation_dubbing_skill.models import ProcessingMode, SkillConfig
from translation_dubbing_skill.entry import run_skill

config = SkillConfig(
    video_path="input_video.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_AND_DUBBING,
    translation_provider="llm",
    translation_endpoint="${TRANSLATION_ENDPOINT}",
    translation_credential="${TRANSLATION_API_KEY}",
    tts_provider="llm",
    tts_endpoint="${TTS_ENDPOINT}",
    tts_credential="${TTS_API_KEY}",
)

result = run_skill(config)
# Output: Multi-track MKV with dubbed audio + subtitles
```

### Working with Subtitle Entries

```python
from translation_dubbing_skill.subtitle import SubtitleParser, SubtitleEntry
from datetime import timedelta

# Parse existing subtitles
parser = SubtitleParser()
entries = parser.parse_file("subtitles.srt")

# Create a new subtitle entry
entry = SubtitleEntry(
    index=1,
    start_time=timedelta(seconds=0),
    end_time=timedelta(seconds=2.5),
    text="Hello, world!",
    speaker="Narrator"
)

# Serialize back to SRT format
from translation_dubbing_skill.subtitle import SRTSerializer
serializer = SRTSerializer()
srt_content = serializer.serialize([entry])
```

### Custom Translation Provider

```python
from translation_dubbing_skill.providers.translation import TranslationProvider
from translation_dubbing_skill.models import SubtitleEntry
from typing import List

class CustomTranslationProvider(TranslationProvider):
    async def translate_batch(
        self,
        entries: List[SubtitleEntry],
        source_lang: str,
        target_lang: str
    ) -> List[SubtitleEntry]:
        # Implement custom translation logic
        translated = []
        for entry in entries:
            # Your translation API call here
            translated_text = await your_api_call(entry.text)
            entry.text = translated_text
            translated.append(entry)
        return translated
```

### Using the 3D Adaptive Scheduler

```python
from translation_dubbing_skill.scheduler import AdaptiveScheduler
from translation_dubbing_skill.providers.translation import LLMTranslationProvider

scheduler = AdaptiveScheduler(
    provider=LLMTranslationProvider(
        endpoint="${TRANSLATION_ENDPOINT}",
        api_key="${TRANSLATION_API_KEY}"
    ),
    initial_batch_size=10,
    initial_concurrency=5,
    max_tokens_per_batch=4000
)

# Process subtitle entries with automatic rate limit handling
translated_entries = await scheduler.process_all(
    entries=subtitle_entries,
    source_language="en",
    target_language="zh-CN"
)
```

### Audio Duration Alignment

```python
from translation_dubbing_skill.align import AudioAligner
from pathlib import Path

aligner = AudioAligner()

# Stretch audio to fit target duration
aligned_audio = aligner.align_audio(
    audio_path=Path("dubbed_audio.mp3"),
    target_duration=2.5,  # seconds
    output_path=Path("aligned_audio.mp3")
)
```

### Multi-Track Video Muxing

```python
from translation_dubbing_skill.mux import VideoMuxer

muxer = VideoMuxer()

result = muxer.mux_tracks(
    video_path="original.mp4",
    audio_tracks=[
        {"path": "original_audio.aac", "language": "eng", "title": "Original"},
        {"path": "dubbed_audio.mp3", "language": "zho", "title": "Chinese", "default": True}
    ],
    subtitle_tracks=[
        {"path": "original.srt", "language": "eng", "title": "English"},
        {"path": "translated.srt", "language": "zho", "title": "Chinese", "default": True}
    ],
    output_path="output.mkv"
)
```

## Progress Monitoring

```python
from translation_dubbing_skill.progress import ProgressListener, ProgressEvent

class CustomProgressListener(ProgressListener):
    def on_stage_start(self, stage: str):
        print(f"Starting stage: {stage}")
    
    def on_progress(self, event: ProgressEvent):
        print(f"Progress: {event.current}/{event.total} - {event.message}")
    
    def on_stage_complete(self, stage: str):
        print(f"Completed stage: {stage}")

# Attach listener to skill execution
listener = CustomProgressListener()
result = run_skill(config, progress_listener=listener)
```

## Testing

```bash
# Run all tests
pytest

# Run only unit tests (skip integration tests)
pytest -m "not integration"

# Run specific test file
pytest tests/test_subtitle_parser.py

# Run with coverage
pytest --cov=src/translation_dubbing_skill
```

## Common Error Handling

```python
from translation_dubbing_skill.errors import (
    RateLimitError,
    ContextWindowExceededError,
    SubtitleParseError,
    TTSError
)

try:
    result = run_skill(config)
except RateLimitError as e:
    # The scheduler will automatically retry with backoff
    print(f"Rate limit hit, will retry: {e}")
except ContextWindowExceededError as e:
    # Payload too large, will be split automatically
    print(f"Context window exceeded: {e}")
except SubtitleParseError as e:
    print(f"Failed to parse subtitles: {e}")
except TTSError as e:
    print(f"TTS generation failed: {e}")
```

## Advanced Scheduling Configuration

Tune the 3D scheduler for your API limits:

```python
from translation_dubbing_skill.scheduler import SchedulerConfig

scheduler_config = SchedulerConfig(
    initial_batch_size=15,
    min_batch_size=1,
    max_batch_size=50,
    initial_concurrency=10,
    min_concurrency=1,
    max_concurrency=20,
    max_tokens_per_batch=8000,
    rate_limit_backoff_base=2.0,
    rate_limit_backoff_max=60.0,
    context_window_split_ratio=0.7
)

scheduler = AdaptiveScheduler(provider, config=scheduler_config)
```

## Troubleshooting

**FFmpeg not found:**
```bash
# Verify FFmpeg installation
ffmpeg -version

# Add to PATH if needed (macOS/Linux)
export PATH="/usr/local/bin:$PATH"
```

**Rate limit errors persist:**
- Reduce `initial_concurrency` in scheduler config
- Decrease `max_tokens_per_batch` to send smaller payloads
- Check your API provider's rate limit documentation

**Subtitle parsing fails:**
```python
# Validate subtitle format
from translation_dubbing_skill.subtitle import SubtitleParser

parser = SubtitleParser()
try:
    entries = parser.parse_file("subtitles.srt")
except SubtitleParseError as e:
    print(f"Invalid subtitle format: {e}")
    # Try re-encoding the file as UTF-8
```

**Audio sync issues:**
- Ensure original video has consistent frame rate
- Check that TTS audio files are generated correctly
- Verify duration alignment thresholds in `AudioAligner`

**Multi-track video won't play:**
- Ensure output format is MKV (containers like MP4 may not support multiple audio tracks)
- Verify player supports multi-track playback (VLC, mpv recommended)
