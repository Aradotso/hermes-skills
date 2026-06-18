---
name: openclaw-videotranslate-skill
description: Translate video subtitles and generate multi-language dubbed audio tracks using OpenClaw's video translation skill with adaptive API scheduling
triggers:
  - translate video subtitles to another language
  - add dubbed audio track to video
  - convert video subtitles from English to Chinese
  - create multi-language video with TTS dubbing
  - extract and translate video captions
  - generate foreign language voiceover for video
  - translate and dub video content automatically
  - sync translated audio with video subtitles
---

# OpenClaw Video Translation & Dubbing Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

A professional-grade OpenClaw skill for translating video subtitles and generating dubbed audio across multiple languages. Features a 3D adaptive scheduler that handles API rate limits, automatic audio duration alignment, and lossless multi-track video output.

## What It Does

This skill processes videos in two modes:

1. **Subtitle-only mode**: Translates subtitles while keeping original audio, outputs dual-subtitle video
2. **Subtitle + Dubbing mode**: Translates subtitles AND generates TTS audio in the target language, with automatic timing alignment

Key capabilities:
- Extracts embedded or external subtitles (SRT/VTT)
- Translates using LLM or web translation APIs
- Generates natural-sounding TTS audio with voice mapping
- Automatically stretches/compresses audio to match original timing
- Outputs lossless multi-track MKV videos
- Handles API rate limits with adaptive 3D scheduling (batch size, payload size, concurrency)

## Installation

### Prerequisites

```bash
# Install FFmpeg (required for video processing)
# macOS:
brew install ffmpeg

# Ubuntu/Debian:
sudo apt install ffmpeg

# Windows (use Chocolatey):
choco install ffmpeg
```

### Install the Skill

```bash
# Clone the repository
git clone https://github.com/zbjincheng/openclaw-skill-videotranslate.git
cd openclaw-skill-videotranslate

# Install with pip
pip install -e .

# Or with development dependencies
pip install -e ".[dev]"
```

### Environment Setup

```bash
# Copy environment template
cp .env.example .env
```

Edit `.env` to add your API credentials:

```bash
# Translation API (LLM or Web service)
TRANSLATION_ENDPOINT=https://api.openai.com/v1/chat/completions
TRANSLATION_CREDENTIAL=your_api_key_here

# TTS API (for dubbing mode)
TTS_ENDPOINT=https://api.openai.com/v1/audio/speech
TTS_CREDENTIAL=your_api_key_here
```

## Configuration

The skill is configured via `manifest.yaml`. Here's a complete example:

```yaml
# manifest.yaml
skill:
  name: translation_dubbing_skill
  version: "1.0.0"

parameters:
  # Required: Input video file
  video_path:
    type: path
    required: true
    description: "Path to source video file"
  
  # Optional: External subtitle file (extracts embedded if omitted)
  subtitle_path:
    type: path
    required: false
    description: "Path to .srt or .vtt subtitle file"
  
  # Required: Source and target languages
  source_language:
    type: string
    required: true
    default: "en"
    description: "Source language code (en, zh-CN, ja, es, fr, de, ko)"
  
  target_language:
    type: string
    required: true
    default: "zh-CN"
    description: "Target language code for translation"
  
  # Required: Processing mode
  processing_mode:
    type: enum
    required: true
    default: "subtitle_and_dubbing"
    values:
      - subtitle_only
      - subtitle_and_dubbing
  
  # Required: Translation provider
  translation_provider:
    type: enum
    required: true
    values: [llm, web]
  
  translation_endpoint:
    type: string
    required: true
    description: "Translation API endpoint"
  
  translation_credential:
    type: secret
    required: true
    description: "API key for translation service"
  
  # Required if processing_mode is subtitle_and_dubbing
  tts_provider:
    type: enum
    required: false
    values: [llm, web]
  
  tts_endpoint:
    type: string
    required: false
    description: "TTS API endpoint"
  
  tts_credential:
    type: secret
    required: false
    description: "API key for TTS service"
  
  # Optional: Advanced scheduler tuning
  scheduler:
    max_concurrency: 10
    initial_batch_size: 20
    max_payload_tokens: 4000
    backoff_multiplier: 2.0
```

## Usage Patterns

### Basic Subtitle Translation (No Dubbing)

```python
from translation_dubbing_skill.entry import TranslationDubbingSkill
from translation_dubbing_skill.models import ProcessingMode

# Initialize skill
skill = TranslationDubbingSkill(
    video_path="input.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_ONLY,
    translation_provider="llm",
    translation_endpoint="https://api.openai.com/v1/chat/completions",
    translation_credential="${TRANSLATION_CREDENTIAL}",
    output_dir="./output"
)

# Run translation
result = skill.execute()
print(f"Output video: {result['output_video']}")
print(f"Subtitle file: {result['output_subtitle']}")
```

### Full Translation + Dubbing

```python
from translation_dubbing_skill.entry import TranslationDubbingSkill
from translation_dubbing_skill.models import ProcessingMode

skill = TranslationDubbingSkill(
    video_path="lecture.mp4",
    subtitle_path="lecture.srt",  # Optional: use external subtitles
    source_language="en",
    target_language="ja",
    processing_mode=ProcessingMode.SUBTITLE_AND_DUBBING,
    
    # Translation config
    translation_provider="llm",
    translation_endpoint="https://api.openai.com/v1/chat/completions",
    translation_credential="${TRANSLATION_CREDENTIAL}",
    
    # TTS config
    tts_provider="llm",
    tts_endpoint="https://api.openai.com/v1/audio/speech",
    tts_credential="${TTS_CREDENTIAL}",
    
    output_dir="./output"
)

# Execute with progress tracking
result = skill.execute()
```

### Using External Subtitle Files

```python
# When you already have subtitles extracted
skill = TranslationDubbingSkill(
    video_path="video.mp4",
    subtitle_path="video.srt",  # Provide external .srt or .vtt
    source_language="en",
    target_language="es",
    processing_mode=ProcessingMode.SUBTITLE_ONLY,
    translation_provider="web",
    translation_endpoint="https://translation-api.example.com/translate",
    translation_credential="${WEB_TRANSLATION_KEY}"
)
```

### Custom Scheduler Configuration

```python
# Fine-tune for rate-limited APIs
skill = TranslationDubbingSkill(
    video_path="long_video.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_AND_DUBBING,
    translation_provider="llm",
    translation_endpoint="${TRANSLATION_ENDPOINT}",
    translation_credential="${TRANSLATION_CREDENTIAL}",
    tts_provider="llm",
    tts_endpoint="${TTS_ENDPOINT}",
    tts_credential="${TTS_CREDENTIAL}",
    
    # Scheduler tuning
    scheduler_config={
        "max_concurrency": 5,          # Lower for strict rate limits
        "initial_batch_size": 10,      # Start with smaller batches
        "max_payload_tokens": 2000,    # Reduce token count per request
        "backoff_multiplier": 3.0,     # More aggressive backoff on 429
        "max_retries": 5
    }
)
```

## Working with Subtitles

### Parsing Subtitle Files

```python
from translation_dubbing_skill.subtitle import SubtitleParser

# Parse SRT or VTT
parser = SubtitleParser()
entries = parser.parse("video.srt")

for entry in entries:
    print(f"[{entry.start_time} -> {entry.end_time}]")
    print(f"  {entry.text}")
    print(f"  Speaker: {entry.speaker_id}")
```

### Creating Subtitle Objects

```python
from translation_dubbing_skill.models import SubtitleEntry

entry = SubtitleEntry(
    index=1,
    start_time="00:00:01,500",
    end_time="00:00:04,200",
    text="Hello, welcome to the tutorial.",
    speaker_id="narrator",
    duration_ms=2700
)
```

### Writing Translated Subtitles

```python
from translation_dubbing_skill.subtitle import SubtitleSerializer

# After translation
translated_entries = [
    SubtitleEntry(
        index=1,
        start_time="00:00:01,500",
        end_time="00:00:04,200",
        text="你好,欢迎来到教程。",
        speaker_id="narrator",
        duration_ms=2700
    )
]

serializer = SubtitleSerializer()
serializer.write("output_zh.srt", translated_entries, format="srt")
```

## Translation Providers

### LLM Provider (OpenAI-compatible)

```python
from translation_dubbing_skill.providers.translation import LLMTranslationProvider

provider = LLMTranslationProvider(
    endpoint="https://api.openai.com/v1/chat/completions",
    credential="${OPENAI_API_KEY}",
    model="gpt-4o",
    source_language="en",
    target_language="zh-CN"
)

# Translate batch of entries
translated = await provider.translate_batch(subtitle_entries)
```

The LLM provider:
- Automatically strips `<think>` reasoning blocks
- Parses JSON responses robustly
- Handles context window overflow with payload slicing
- Supports custom system prompts for translation style

### Web Provider (Generic Translation API)

```python
from translation_dubbing_skill.providers.translation import WebTranslationProvider

provider = WebTranslationProvider(
    endpoint="https://api.translator.com/v1/translate",
    credential="${WEB_API_KEY}",
    source_language="en",
    target_language="fr"
)

translated = await provider.translate_batch(subtitle_entries)
```

## TTS Providers

### LLM TTS Provider (OpenAI-compatible)

```python
from translation_dubbing_skill.providers.tts import LLMTTSProvider

provider = LLMTTSProvider(
    endpoint="https://api.openai.com/v1/audio/speech",
    credential="${OPENAI_API_KEY}",
    model="tts-1-hd",
    voice_mapping={
        "narrator": "alloy",
        "speaker1": "echo",
        "speaker2": "nova"
    }
)

# Generate audio for translated subtitle
audio_bytes = await provider.synthesize(
    text="你好,欢迎来到教程。",
    speaker_id="narrator",
    target_duration_ms=2700  # Auto-stretches to match original
)
```

### Web TTS Provider

```python
from translation_dubbing_skill.providers.tts import WebTTSProvider

provider = WebTTSProvider(
    endpoint="https://tts-api.example.com/synthesize",
    credential="${TTS_API_KEY}",
    voice_mapping={"default": "zh-CN-XiaoxiaoNeural"}
)

audio = await provider.synthesize(
    text="翻译后的文本",
    speaker_id="default",
    target_duration_ms=3000
)
```

## Audio Duration Alignment

The skill automatically aligns TTS audio to match original subtitle timing:

```python
from translation_dubbing_skill.align import AudioAligner

aligner = AudioAligner()

# Stretch or compress audio to exact duration
aligned_audio = aligner.align_duration(
    audio_bytes=tts_output,
    target_duration_ms=2700,
    original_duration_ms=3200  # TTS was 3.2s, needs to be 2.7s
)
```

Uses time-stretching (not pitch-shifted) to preserve voice quality.

## Video Muxing

### Creating Multi-Track Output

```python
from translation_dubbing_skill.mux import VideoMuxer

muxer = VideoMuxer()

output_path = muxer.mux_video(
    video_path="original.mp4",
    original_audio_path="original_audio.aac",
    dubbed_audio_path="dubbed_zh.mp3",
    original_subtitle_path="original_en.srt",
    translated_subtitle_path="translated_zh.srt",
    output_path="output_dual_track.mkv",
    source_language="en",
    target_language="zh-CN"
)
```

Output structure:
- **Video**: Original video stream (lossless copy)
- **Audio Track 1** (default): Dubbed target language
- **Audio Track 2**: Original source language
- **Subtitle Track 1** (default): Translated target language
- **Subtitle Track 2**: Original source language

## Progress Tracking

### Monitor Processing Status

```python
from translation_dubbing_skill.progress import ProgressListener

class MyProgressListener(ProgressListener):
    def on_state_change(self, stage: str, current: int, total: int, message: str):
        print(f"[{stage}] {current}/{total} - {message}")
    
    def on_error(self, error: Exception):
        print(f"Error: {error}")
    
    def on_complete(self, result: dict):
        print(f"Complete: {result}")

skill = TranslationDubbingSkill(
    video_path="video.mp4",
    # ... other params
    progress_listener=MyProgressListener()
)

skill.execute()
```

## Error Handling

### Common Error Types

```python
from translation_dubbing_skill.errors import (
    SubtitleExtractionError,
    TranslationAPIError,
    TTSAPIError,
    AudioAlignmentError,
    VideoMuxingError,
    RateLimitError
)

try:
    result = skill.execute()
except SubtitleExtractionError as e:
    print(f"Could not extract subtitles: {e}")
except TranslationAPIError as e:
    print(f"Translation failed: {e}")
    print(f"Failed entries: {e.failed_entries}")
except TTSAPIError as e:
    print(f"TTS synthesis failed: {e}")
except RateLimitError as e:
    print(f"Hit API rate limit: {e}")
    print(f"Retry after: {e.retry_after} seconds")
except VideoMuxingError as e:
    print(f"FFmpeg muxing failed: {e}")
```

### Automatic Retry Logic

The 3D scheduler automatically handles:
- **HTTP 429 (Rate Limit)**: Exponential backoff with jitter
- **Context Window Overflow**: Payload slicing and re-batching
- **Network Errors**: Configurable retry with backoff

```python
# Scheduler handles retries automatically
skill = TranslationDubbingSkill(
    # ... params
    scheduler_config={
        "max_retries": 5,
        "backoff_multiplier": 2.0,
        "initial_backoff_seconds": 1.0
    }
)
```

## Testing

Run the test suite:

```bash
# All tests
pytest

# Unit tests only (fast)
pytest -m "not integration"

# Integration tests (requires API keys)
pytest -m integration

# Specific test file
pytest tests/test_subtitle_parser.py

# With coverage
pytest --cov=src/translation_dubbing_skill
```

## Troubleshooting

### FFmpeg not found

```bash
# Check if FFmpeg is installed
ffmpeg -version

# If missing, install:
# macOS:
brew install ffmpeg

# Ubuntu:
sudo apt install ffmpeg
```

### API Rate Limits

If you're hitting rate limits frequently:

```python
# Reduce concurrency and batch sizes
skill = TranslationDubbingSkill(
    # ... params
    scheduler_config={
        "max_concurrency": 3,        # Lower concurrent requests
        "initial_batch_size": 5,     # Smaller batches
        "max_payload_tokens": 1500   # Smaller payloads
    }
)
```

### Audio Sync Issues

If dubbed audio doesn't align well:

```python
# The aligner uses time-stretching by default
# For better quality, ensure TTS generates close to target duration
from translation_dubbing_skill.align import AudioAligner

aligner = AudioAligner(
    stretch_algorithm="rubberband",  # Higher quality
    quality="high"
)
```

### Subtitle Extraction Fails

```bash
# Check if video has embedded subtitles
ffmpeg -i video.mp4

# If no embedded subtitles, provide external file:
skill = TranslationDubbingSkill(
    video_path="video.mp4",
    subtitle_path="video.srt",  # External subtitle
    # ... other params
)
```

### Memory Issues with Large Videos

Process in smaller chunks or reduce concurrency:

```python
skill = TranslationDubbingSkill(
    # ... params
    scheduler_config={
        "max_concurrency": 2,
        "chunk_size": 50  # Process 50 subtitle entries at a time
    }
)
```

## Advanced: Custom Providers

### Implementing a Custom Translation Provider

```python
from translation_dubbing_skill.providers.translation import TranslationProvider
from translation_dubbing_skill.models import SubtitleEntry
from typing import List

class MyCustomTranslationProvider(TranslationProvider):
    async def translate_batch(
        self, 
        entries: List[SubtitleEntry]
    ) -> List[SubtitleEntry]:
        translated = []
        for entry in entries:
            # Your custom translation logic
            translated_text = await self._translate(entry.text)
            translated.append(
                SubtitleEntry(
                    index=entry.index,
                    start_time=entry.start_time,
                    end_time=entry.end_time,
                    text=translated_text,
                    speaker_id=entry.speaker_id,
                    duration_ms=entry.duration_ms
                )
            )
        return translated
    
    async def _translate(self, text: str) -> str:
        # Call your translation API
        pass
```

### Implementing a Custom TTS Provider

```python
from translation_dubbing_skill.providers.tts import TTSProvider

class MyCustomTTSProvider(TTSProvider):
    async def synthesize(
        self,
        text: str,
        speaker_id: str,
        target_duration_ms: int
    ) -> bytes:
        # Your TTS logic
        audio_bytes = await self._generate_audio(text, speaker_id)
        
        # Use built-in aligner
        from translation_dubbing_skill.align import AudioAligner
        aligner = AudioAligner()
        aligned = aligner.align_duration(
            audio_bytes,
            target_duration_ms
        )
        return aligned
```

## Command-Line Interface

If the skill provides a CLI wrapper:

```bash
# Subtitle-only mode
openclaw-videotranslate \
  --video input.mp4 \
  --source en \
  --target zh-CN \
  --mode subtitle_only \
  --translation-provider llm \
  --translation-endpoint "${TRANSLATION_ENDPOINT}" \
  --translation-credential "${TRANSLATION_CREDENTIAL}" \
  --output ./output

# Full dubbing mode
openclaw-videotranslate \
  --video lecture.mp4 \
  --subtitle lecture.srt \
  --source en \
  --target ja \
  --mode subtitle_and_dubbing \
  --translation-provider llm \
  --translation-endpoint "${TRANSLATION_ENDPOINT}" \
  --translation-credential "${TRANSLATION_CREDENTIAL}" \
  --tts-provider llm \
  --tts-endpoint "${TTS_ENDPOINT}" \
  --tts-credential "${TTS_CREDENTIAL}" \
  --output ./output
```

## Best Practices

1. **Use subtitle_only mode first** to verify translation quality before dubbing
2. **Start with small concurrency** (3-5) and increase based on API limits
3. **Monitor token usage** - adjust `max_payload_tokens` to avoid context overflow
4. **Use external subtitles** when possible for better control
5. **Test with short videos** before processing long content
6. **Keep original files** - the skill doesn't modify source videos
7. **Use environment variables** for credentials, never hardcode

## Resources

- GitHub: https://github.com/zbjincheng/openclaw-skill-videotranslate
- License: Apache-2.0
- Python Version: 3.11+
- FFmpeg Documentation: https://ffmpeg.org/documentation.html
