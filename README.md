# video-to-code-skill v1.2

A Claude Code Plugin following the open format: https://agentskills.io/what-are-skills, https://code.claude.com/docs/en/plugins

Inputs the **content** of any video into **Claude Code prompt / context window** as a **multimodal bundle of voice transcription and data summary, time synced with keyframes** - screenshots of key moments from the video so it can be used for all further requests related to that content.

There are **limitless usage scenarios** for this skill. Now you can show and tell your LLM Agent what needs to be done in a complete flow, illustrated by the pre-recorded video, instead of typing text and pasting screenshots in individual steps. You can even have "conversations" with an Agent about the content found in the video etc., etc...

Refer to **examples**: [references/usage-examples.md](references/usage-examples.md)

**Tested on**: MacBook with Silicon chip & Claude Code Opus 4.5 / 4.6

Note: Python script used to process video and audio content was **100% vibe coded**.

**Disclaimer**: Before using this or any agentic tool, make sure you understand its autonomy in your system. This skill is provided for experimental purposes and you, as a user, take full responsibility for its actions. See [MIT License](LICENSE)

**IMPORTANT**: I would like to advise you against extracting data from videos that haven't been done by you as unlikely, but potentially, they might be used for malicious prompt injection. There are some directives in the Skill to prevent this happening, but with LLMs nothing can be guaranteed. 

## What it does

1. Extracts **keyframes** at scene changes using OpenCV
2. Generates **audio transcription** using Whisper (MLX-accelerated on Mac)
3. Syncs transcript segments with keyframes
4. Produces a structured analysis with timestamps
5. Saves a detailed `summary.md` with overview, key moments table, and a long-form walkthrough
6. If the video contains narration/speech, saves `narration.md` with the full transcript in screenplay-ready format with timestamp ranges
7. Finally, it asks the user what needs to be done with the new context

## Installation

### Option 1: From Anthropic marketplace (preferred)

```
/plugin install video-to-code-skill@claude-plugins-official
```

### Option 2: From GitHub

```
/plugin marketplace add PiotrLason/video-to-code-skill
/plugin install video-to-code-skill@piotrlason-video-to-code-skill
```

### Option 3: From local clone

Clone the repo and add it as a local marketplace:

```bash
git clone https://github.com/PiotrLason/video-to-code-skill.git
```

```
/plugin marketplace add ./video-to-code-skill
/plugin install video-to-code-skill@video-to-code-skill
```

### After installation

Run `/video-to-code-skill:setup-video-to-code-skill` to set up dependencies, then `/video-to-code-skill:run-video-to-code-skill` to analyze a video.

### Updating

```
/plugin marketplace update video-to-code-skill
```

## Prerequisites

*Dependencies are installed by running `/video-to-code-skill:setup-video-to-code-skill`.*

| Requirement | Installation |
|---|---|
| Python 3 | Pre-installed on macOS |
| opencv-python | `pip install opencv-python numpy` |
| mlx-whisper | `pip install mlx-whisper` (Mac) |
| openai-whisper | `pip install openai-whisper` (fallback) |
| ffmpeg | `brew install ffmpeg` (for audio extraction) |

## Input

- Video file (`.mov`, `.mp4`, `.webm`) placed in `~/video-to-code-skill-storage/` folder
- If multiple videos exist, picks the **latest by modification time**
- If **no video file** is found in the storage folder, the skill automatically falls back to the **most recent archived video** from `~/video-to-code-skill-storage/archive/`
- If launched with a **timestamp parameter** (`YYYY-MM-DD_HH-MM-SS`), the skill loads the matching archived analysis directly, skipping video processing entirely

## Usage in Claude Code

### Skills

| Skill | Invocation | Description |
|-------|------------|-------------|
| **setup-video-to-code-skill** | `/video-to-code-skill:setup-video-to-code-skill` | Sets up storage folder and installs dependencies. Run once before first use. |
| **run-video-to-code-skill** | `/video-to-code-skill:run-video-to-code-skill` | Analyzes a video — extracts keyframes, transcribes audio, summarizes, and archives. |

### Parameters (for `/video-to-code-skill:run-video-to-code-skill`)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `-dt`, `-detection_threshold` | Keyframe detection threshold (0-100). Lower values capture more keyframes. | `1` |
| `YYYY-MM-DD_HH-MM-SS` | Timestamp of an archived video to load directly from the archive, skipping video processing. | — |

Setting custom detection threshold is useful to tune the Skill to different video sources. Time will tell which settings work best for different applications of this Skill.
More information will be added here.

Examples:

```
/video-to-code-skill:run-video-to-code-skill
/video-to-code-skill:run-video-to-code-skill -dt 5
/video-to-code-skill:run-video-to-code-skill 2026-03-19_10-12-51
```

## Output

### Generated files

Video and analysis are archived to:

Located in `~/video-to-code-skill-storage/archive/YYYY-MM-DD_HH-MM-SS [video_filename]/analysis/`:

| File | Description |
|---|---|
| `analysis.json` | Full structured data |
| `transcript.json` | Audio transcription with timestamps |
| `analysis.md` | Human-readable summary |
| `keyframes/` | PNG images of key moments |

Located in `~/video-to-code-skill-storage/archive/YYYY-MM-DD_HH-MM-SS [video_filename]/`:

| File | Description |
|---|---|
| `summary.md` | Detailed video analysis: high-level overview, key moments table with timestamps, and a long-form detailed walkthrough |
| `narration.md` | *(only if video contains speech)* Full narration transcript in screenplay-ready format with timestamp ranges |

### Console output

- Video filename and duration
- Summary of what the user is demonstrating
- Key moments with timestamps
- User's apparent intent

## Contribution

You are welcome to contribute:

1. Extending the tool's capabilities by opening a PR
2. Submitting bug fixes
3. Adding usage examples in [references/usage-examples.md](references/usage-examples.md)
4. Adding information on detection threshold settings recommended for specific applications in this file.

## License

[MIT License](LICENSE)

## Contact and Questions

Piotr Lason: Find me on [LinkedIn](https://linkedin.com/in/piotrlason)
