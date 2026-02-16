---
name: text-to-short-video
description: Convert text into short-form videos by extracting key scenes, generating storyboard images, narration, subtitles, and final renders. Use when users ask to turn articles, scripts, chapters, notes, or marketing copy into 30-60 second videos for Shorts/Reels/TikTok, especially when prompt generation must follow the JSON format used by `openai-text-to-image-storyboard`.
---

# Text to Short Video

## Core Dependencies

Always coordinate these skills in order:

1. `openai-text-to-image-storyboard` to generate storyboard images.
2. `docs-to-voice` to generate narration audio, timeline, and SRT subtitles.
3. `remotion-best-practices` to compose and render the final short video.

## Required Inputs

Collect the minimum required inputs before execution:

- `project_dir` (absolute path)
- `content_name` (folder and output name)
- source text (article/chapter/script/copy)
- output video size (`width x height`, for example `1080x1920`; default: `1080x1920`)
- subtitle style and narration preferences when explicitly required

If critical inputs are missing, ask concise follow-up questions.

## Environment Setup

Use the template:

- `/Users/tszkinlai/.codex/skills/text-to-short-video/.env.example`
- Copy it to:
  - `/Users/tszkinlai/.codex/skills/text-to-short-video/.env`

Important settings:

- `OPENAI_API_URL` and `OPENAI_API_KEY` for image generation (required)
- `DASHSCOPE_API_KEY` when `DOCS_TO_VOICE_MODE=api`
- `TEXT_TO_SHORT_VIDEO_WIDTH` and `TEXT_TO_SHORT_VIDEO_HEIGHT` to control final render size

Execution rule:

- Always pass `--env-file /Users/tszkinlai/.codex/skills/text-to-short-video/.env`
  so dependent scripts read configuration from this skill-level `.env`.

## Workflow

### 1) Extract video scenes from text

- Parse input text and select 3-6 scenes that are visually clear and story-coherent.
- For each scene, prepare:
  - `title`
  - one-line visual focus
  - one-line narration key point
- Keep total pacing suitable for a 30-60 second short video.

### 2) Build a 30-60 second narration script

- Write a compact narration script aligned with the selected scenes.
- Keep sentence timing natural for subtitles and voice generation.
- Ensure scene order in narration matches scene order in prompts.

### 3) Generate `prompts.json` using the same JSON format as `openai-text-to-image-storyboard`

Save file at:

- `<project_dir>/pictures/<content_name>/prompts.json`

Use one of these formats only.

#### Format A: simple list

```json
[
  {
    "title": "Opening Hook",
    "prompt": "cinematic city skyline at dusk, fast push-in, dramatic contrast, energetic mood"
  },
  {
    "title": "Conflict Beat",
    "prompt": "close-up on determined protagonist, wind-blown hair, neon reflections, tense expression"
  }
]
```

#### Format B: recurring-character structured format (recommended)

```json
{
  "characters": [
    {
      "id": "hero_01",
      "name": "Ari",
      "appearance": "short black hair, brown eyes, athletic build",
      "outfit": "dark hoodie, utility pants, white sneakers",
      "description": "focused and alert"
    }
  ],
  "scenes": [
    {
      "title": "Street Discovery",
      "description": "night market street with rain reflections and neon signs",
      "character_ids": ["hero_01"],
      "character_descriptions": {
        "hero_01": "walking quickly while scanning the crowd"
      },
      "camera": "medium tracking shot",
      "lighting": "blue-magenta neon rim light"
    }
  ]
}
```

Rules:

- Reuse the same character skeleton (`id/name/appearance/outfit/description`) across all scenes.
- For each scene, only update per-scene motion/emotion in `character_descriptions`.
- For multi-character scenes, include all related IDs in `character_ids`.

### 4) Generate storyboard images

```bash
python /Users/tszkinlai/.codex/skills/openai-text-to-image-storyboard/scripts/generate_storyboard_images.py \
  --project-dir "<project_dir>" \
  --env-file /Users/tszkinlai/.codex/skills/text-to-short-video/.env \
  --content-name "<content_name>" \
  --prompts-file "<project_dir>/pictures/<content_name>/prompts.json"
```

Use aspect ratio mapping when needed:

- vertical -> `9:16`
- horizontal -> `16:9`

### 5) Generate narration and subtitles

```bash
python /Users/tszkinlai/.codex/skills/docs-to-voice/scripts/docs_to_voice.py \
  --project-dir "<project_dir>" \
  --project-name "<content_name>" \
  --env-file /Users/tszkinlai/.codex/skills/text-to-short-video/.env \
  --text "<narration_script>"
```

Expected output under `<project_dir>/audio/<content_name>/`:

- narration audio file
- timeline JSON
- `.srt` subtitle file

### 6) Compose and render final video

- Build or reuse Remotion workspace at:
  - `<project_dir>/video/<content_name>/remotion/`
- Use `remotion-best-practices` rules for compositions, audio sync, subtitles, and transitions.
- Resolve final render size in this priority order:
  1. explicit user input (`<width>x<height>`)
  2. `TEXT_TO_SHORT_VIDEO_WIDTH` + `TEXT_TO_SHORT_VIDEO_HEIGHT` from `.env`
  3. default `1080x1920`
- Apply the resolved size to Remotion composition `width` and `height`.
- Keep image aspect ratio consistent with final video size (`9:16` for vertical, `16:9` for horizontal).
- Default to one final short video unless user explicitly asks for multiple clips.
- Keep Remotion project sources for user revisions.

### 7) Enforce final aspect ratio and size (post-processing, required)

After render, always run:

```bash
python /Users/tszkinlai/.codex/skills/text-to-short-video/scripts/enforce_video_aspect_ratio.py \
  --input-video "<rendered_video_path>" \
  --output-video "<final_output_video_path>" \
  --env-file /Users/tszkinlai/.codex/skills/text-to-short-video/.env \
  --force
```

Behavior:

- If rendered video aspect ratio differs from target (`width x height`), the script center-crops first, then scales.
- If aspect ratio matches but pixel size differs, the script scales to the target size.
- If both aspect ratio and size already match, the script keeps the original content (copy/no-op).

## Output Contract

Return absolute paths for:

- `prompts.json`
- generated storyboard image directory
- narration audio file
- subtitle `.srt` file
- final rendered `.mp4`
- Remotion project directory

Also report:

- chosen scene list and why each scene was selected
- final duration check (must be 30-60 seconds)
- final render size check (`width x height`)
- post-process result (whether center crop was applied)
- subtitle sync spot-check (start/middle/end)

## Quality Gate Checklist

Before finishing, verify:

- prompt file strictly uses one supported JSON format above
- scene order is consistent across prompts, narration, and final timeline
- rendered duration is within 30-60 seconds
- rendered video size matches requested or configured `width x height`
- when aspect ratio mismatches, post-process center crop is executed
- subtitles are time-aligned with narration
- all output paths exist and are returned as absolute paths
