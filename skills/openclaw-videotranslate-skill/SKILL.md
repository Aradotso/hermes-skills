---
name: openclaw-videotranslate-skill
description: Translate video subtitles and auto-dub audio across languages with OpenClaw framework, featuring 3D adaptive scheduler and lossless multi-track output
triggers:
  - translate video subtitles to another language
  - dub video with text-to-speech translation
  - convert video subtitles and audio to Chinese
  - generate multi-language video tracks with TTS
  - translate English video to Chinese with dubbing
  - create dual audio dual subtitle video
  - handle video translation with rate limit protection
  - process video subtitle translation and voice synthesis
---

# OpenClaw Video Translation & Dubbing Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

A professional OpenClaw skill for translating video subtitles and generating dubbed audio across multiple languages. Features a sophisticated 3D adaptive scheduler that prevents API rate limits, lossless multi-track video output, and pluggable translation/TTS providers.

## What It Does

This skill provides two processing modes:

1. **`subtitle_only`**: Translates subtitles while keeping original audio, outputs dual-subtitle video
2. **`subtitle_and_dubbing`**: Translates subtitles AND generates TTS voiceover with automatic duration alignment

The 3D adaptive scheduler intelligently balances batch size, payload size, and concurrency to handle API rate limits (HTTP 429) gracefully with exponential backoff.

## Installation

```bash
# Clone the repository
git clone https://github.com/zbjincheng/openclaw-skill-videotranslate.git
cd openclaw-skill-videotranslate

# Install with pip
pip install -e .

# Or with development dependencies
pip install -e ".[dev]"
```

**Prerequisites:**
- Python 3.11+
- FFmpeg in system PATH
  - macOS: `brew install ffmpeg`
  - Ubuntu: `sudo apt install ffmpeg`

## Configuration

### Environment Setup

Create a `.env` file with your API credentials:

```bash
cp .env.example .env
```

Example `.env` content:

```bash
# Translation API credentials
TRANSLATION_ENDPOINT=https://api.example.com/v1/chat/completions
TRANSLATION_API_KEY=your_translation_api_key_here

# TTS API credentials (for dubbing mode)
TTS_ENDPOINT=https://api.example.com/v1/audio/speech
TTS_API_KEY=your_tts_api_key_here
```

### Manifest Configuration

The skill uses `manifest.yaml` for configuration. Key parameters:

```yaml
parameters:
  video_path:
    type: path
    required: true
    description: "Path to source video file"
  
  source_language:
    type: string
    default: "en"
    description: "Source language code (en, zh-CN, ja, etc.)"
  
  target_language:
    type: string
    default: "zh-CN"
    description: "Target language code"
  
  processing_mode:
    type: enum
    values: [subtitle_only, subtitle_and_dubbing]
    default: subtitle_and_dubbing
  
  translation_provider:
    type: enum
    values: [llm, web]
    required: true
  
  translation_endpoint:
    type: string
    required: true
  
  translation_credential:
    type: secret
    required: true
  
  tts_provider:
    type: enum
    values: [llm, web]
    description: "Required for dubbing mode"
  
  tts_endpoint:
    type: string
    description: "Required for dubbing mode"
  
  tts_credential:
    type: secret
    description: "Required for dubbing mode"
```

## Usage Patterns

### Subtitle-Only Translation

Translate subtitles without dubbing, keeping original audio:

```python
from translation_dubbing_skill.entry import TranslationDubbingSkill
from translation_dubbing_skill.models import ProcessingMode

skill = TranslationDubbingSkill(
    video_path="input_video.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_ONLY,
    translation_provider="llm",
    translation_endpoint=os.getenv("TRANSLATION_ENDPOINT"),
    translation_credential=os.getenv("TRANSLATION_API_KEY"),
)

# Execute translation
result = await skill.execute()
print(f"Output video: {result['output_video_path']}")
print(f"Translated subtitles: {result['subtitle_path']}")
```

### Full Translation with Dubbing

Translate subtitles AND generate TTS voiceover:

```python
from translation_dubbing_skill.entry import TranslationDubbingSkill
from translation_dubbing_skill.models import ProcessingMode

skill = TranslationDubbingSkill(
    video_path="input_video.mp4",
    source_language="en",
    target_language="zh-CN",
    processing_mode=ProcessingMode.SUBTITLE_AND_DUBBING,
    translation_provider="llm",
    translation_endpoint=os.getenv("TRANSLATION_ENDPOINT"),
    translation_credential=os.getenv("TRANSLATION_API_KEY"),
    tts_provider="llm",
    tts_endpoint=os.getenv("TTS_ENDPOINT"),
    tts_credential=os.getenv("TTS_API_KEY"),
)

# Execute translation and dubbing
result = await skill.execute()
print(f"Output video: {result['output_video_path']}")
print(f"Dubbing audio: {result['dubbing_audio_path']}")
```

### With External Subtitle File

If you have an external subtitle file:

```python
skill = TranslationDubbingSkill(
    video_path="input_video.mp4",
    subtitle_path="subtitles.srt",  # External subtitle file
    source_language="en",
    target_language="ja",
    processing_mode=ProcessingMode.SUBTITLE_AND_DUBBING,
    translation_provider="llm",
    translation_endpoint=os.getenv("TRANSLATION_ENDPOINT"),
    translation_credential=os.getenv("TRANSLATION_API_KEY"),
    tts_provider="web",
    tts_endpoint=os.getenv("TTS_ENDPOINT"),
    tts_credential=os.getenv("TTS_API_KEY"),
)

result = await skill.execute()
```

## Working with Subtitle Parsers

Parse and manipulate subtitle files directly:

```python
from translation_dubbing_skill.subtitle import SubtitleParser, SubtitleSerializer
from translation_dubbing_skill.models import SubtitleEntry

# Parse existing subtitle file
parser = SubtitleParser()
entries = parser.parse("subtitles.srt")

# Modify subtitle entries
for entry in entries:
    entry.text = f"[Modified] {entry.text}"

# Serialize back to file
serializer = SubtitleSerializer()
serializer.serialize(entries, "output.srt", format="srt")
```

Create subtitle entries programmatically:

```python
from translation_dubbing_skill.models import SubtitleEntry
from datetime import timedelta

entry = SubtitleEntry(
    index=1,
    start=timedelta(seconds=0),
    end=timedelta(seconds=3.5),
    text="Hello, world!"
)

# Access properties
print(entry.duration)  # 3.5 seconds
print(entry.start_ms)  # 0 milliseconds
print(entry.end_ms)    # 3500 milliseconds
```

## Translation Provider Implementation

### Using LLM Translation Provider

```python
from translation_dubbing_skill.providers.translation import LLMTranslationProvider

provider = LLMTranslationProvider(
    endpoint=os.getenv("TRANSLATION_ENDPOINT"),
    credential=os.getenv("TRANSLATION_API_KEY"),
    source_language="en",
    target_language="zh-CN",
    model_name="gpt-4",  # Optional
)

# Translate a batch of subtitle entries
entries = [
    SubtitleEntry(1, timedelta(0), timedelta(2), "Hello"),
    SubtitleEntry(2, timedelta(2), timedelta(4), "How are you?"),
]

translated = await provider.translate_batch(entries)
for entry in translated:
    print(f"{entry.index}: {entry.text}")
```

### Custom Translation Provider

Implement the `TranslationProvider` protocol:

```python
from translation_dubbing_skill.providers.translation import TranslationProvider
from typing import List

class CustomTranslationProvider(TranslationProvider):
    async def translate_batch(
        self,
        entries: List[SubtitleEntry]
    ) -> List[SubtitleEntry]:
        # Your custom translation logic
        translated_entries = []
        for entry in entries:
            translated_text = await self._custom_translate(entry.text)
            translated_entries.append(
                SubtitleEntry(
                    index=entry.index,
                    start=entry.start,
                    end=entry.end,
                    text=translated_text
                )
            )
        return translated_entries
```

## TTS Provider Implementation

### Using LLM TTS Provider

```python
from translation_dubbing_skill.providers.tts import LLMTTSProvider

provider = LLMTTSProvider(
    endpoint=os.getenv("TTS_ENDPOINT"),
    credential=os.getenv("TTS_API_KEY"),
    language="zh-CN",
    voice="alloy",  # Voice ID
)

# Generate audio for subtitle entry
entry = SubtitleEntry(1, timedelta(0), timedelta(3), "你好，世界")
audio_path = await provider.synthesize(entry)
print(f"Audio saved to: {audio_path}")
```

## Audio Duration Alignment

The skill automatically aligns TTS audio duration with original subtitle timing:

```python
from translation_dubbing_skill.align import AudioAligner

aligner = AudioAligner()

# Stretch audio to fit target duration
aligned_audio = await aligner.align(
    audio_path="generated_audio.mp3",
    target_duration=3.5,  # seconds
    output_path="aligned_audio.mp3"
)
```

## 3D Adaptive Scheduler

The scheduler prevents API rate limits by adaptively tuning:
- **Batch size**: Number of subtitle entries per request
- **Payload size**: Total tokens/characters per batch
- **Concurrency**: Parallel request workers

```python
from translation_dubbing_skill.scheduler import AdaptiveScheduler

scheduler = AdaptiveScheduler(
    max_batch_size=20,
    max_payload_tokens=4000,
    max_concurrency=5,
    initial_batch_size=10,
    initial_concurrency=3,
)

# Process entries with adaptive rate limiting
async def process_fn(batch):
    return await translation_provider.translate_batch(batch)

results = await scheduler.execute(
    entries=all_subtitle_entries,
    process_fn=process_fn,
)
```

The scheduler automatically:
- Reduces batch size on token overflow
- Backs off concurrency on HTTP 429
- Retries with exponential backoff + jitter
- Reports progress events

## Progress Tracking

Listen to progress events:

```python
from translation_dubbing_skill.progress import ProgressListener, ProgressEvent

class MyProgressListener(ProgressListener):
    def on_progress(self, event: ProgressEvent):
        print(f"{event.stage}: {event.current}/{event.total} - {event.message}")

skill = TranslationDubbingSkill(
    video_path="input.mp4",
    # ... other params
    progress_listener=MyProgressListener(),
)

await skill.execute()
```

## Multi-Track Video Output

The skill produces lossless multi-track videos:

**Subtitle-only mode:**
- Original audio track (default)
- Original subtitle track
- Translated subtitle track (default)

**Dubbing mode:**
- Original audio track
- Dubbed audio track (default)
- Original subtitle track
- Translated subtitle track (default)

Output format: MKV container with lossless video re-muxing.

## Error Handling

```python
from translation_dubbing_skill.errors import (
    TranslationError,
    TTSError,
    RateLimitError,
    ContextWindowError,
)

try:
    result = await skill.execute()
except RateLimitError as e:
    print(f"Rate limit hit: {e.message}")
    # Scheduler handles this automatically with backoff
except ContextWindowError as e:
    print(f"Payload too large: {e.message}")
    # Scheduler reduces batch size automatically
except TranslationError as e:
    print(f"Translation failed: {e.message}")
except TTSError as e:
    print(f"TTS synthesis failed: {e.message}")
```

## Testing

```bash
# Run all tests
pytest

# Run unit tests only (skip integration)
pytest -m "not integration"

# Run with coverage
pytest --cov=translation_dubbing_skill --cov-report=html

# Run specific test module
pytest tests/test_subtitle.py
```

## Troubleshooting

### FFmpeg Not Found

```bash
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt update && sudo apt install ffmpeg

# Verify installation
ffmpeg -version
```

### API Rate Limits

The 3D adaptive scheduler handles rate limits automatically. If you still encounter issues:

1. Reduce `initial_concurrency` in scheduler config
2. Lower `max_batch_size` to decrease payload size
3. Check API provider rate limit documentation

### Subtitle Extraction Fails

If embedded subtitle extraction fails:

```python
# Provide external subtitle file
skill = TranslationDubbingSkill(
    video_path="input.mp4",
    subtitle_path="external_subtitles.srt",  # Use external file
    # ...
)
```

### Audio Alignment Quality

If stretched audio sounds distorted:

1. Use higher quality TTS voices
2. Adjust stretch ratio by modifying subtitle timing
3. Consider breaking long segments into smaller chunks

### Memory Issues with Large Videos

For large videos:

1. Process in chunks using external subtitle files
2. Increase system swap space
3. Use `subtitle_only` mode first, then dub separately

## Common Language Codes

- English: `en`
- Simplified Chinese: `zh-CN`
- Traditional Chinese: `zh-TW`
- Japanese: `ja`
- Korean: `ko`
- Spanish: `es`
- French: `fr`
- German: `de`
- Portuguese: `pt`
- Russian: `ru`
