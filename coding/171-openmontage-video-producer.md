---
name: openmontage-video-producer
description: "Produce finished videos (explainers, documentary montages, shorts, podcast clips, screen demos) by driving the open-source OpenMontage agentic video pipeline from Claude Code or another coding assistant"
category: coding
difficulty: intermediate
model_boost: "Weak models skip the setup check, burn paid API credits without approval, ignore licenses on stock footage, pick the wrong pipeline, and hand back renders without reviewing them"
source: "https://github.com/calesthio/OpenMontage (AGPL-3.0)"
---

# OpenMontage Video Producer

## Purpose
OpenMontage is an open-source (AGPL-3.0) agentic video production system. It is not a standalone app: it turns an AI coding assistant (Claude Code, Cursor, Copilot, Windsurf, Codex) into a video studio. The assistant reads its pipeline manifests and skill files, calls its Python tools, and renders a finished MP4. This skill tells you how to set it up, choose a pipeline, control cost, and deliver a reviewed video.

## When to Use
- Animated explainers, product or concept videos from a text brief
- Documentary-style montages built from real stock and public-archive footage
- Vertical shorts and reels (TikTok, Reels, YouTube Shorts) or LinkedIn videos
- Repurposing a podcast or long recording into clips
- Screen demos, talking-head videos, avatar spokesperson videos
- Localizing or dubbing an existing video

## Do NOT Use When
- You only need a GIF or a single animated asset (use skill 162 GIF Animator)
- You need frame-accurate manual editing (use a real NLE such as DaVinci Resolve)
- The output will ship inside closed-source software and the AGPL-3.0 terms are unacceptable
- There is no budget approval for paid providers and the zero-key path won't meet the brief

## Instructions

### Step 1: Check Prerequisites
- Python 3.10+
- FFmpeg on PATH
- Node.js 18+ (Node 22+ if you want the HyperFrames HTML/GSAP renderer)
- Optional: an NVIDIA GPU for local video generation (WAN, Hunyuan, CogVideo, LTX-Video)

```bash
python3 --version && ffmpeg -version | head -1 && node --version
```

### Step 2: Install
```bash
git clone https://github.com/calesthio/OpenMontage.git
cd OpenMontage
make setup
```

Without `make` (macOS/Linux):
```bash
python3 -m venv .venv && source .venv/bin/activate
python -m pip install -r requirements.txt
cd remotion-composer && npm install && cd ..
python -m pip install piper-tts
cp .env.example .env
```

Windows PowerShell: use `py -3 -m venv .venv`, `.venv\Scripts\Activate.ps1` and `Copy-Item .env.example .env`, with the same pip and npm steps. If `npm install` fails with `ERR_INVALID_ARG_TYPE`, run `npx --yes npm install`.

Smoke test with no API keys:
```bash
make demo
```

### Step 3: Configure Providers (all optional)
Edit `.env`. Add only what the brief needs.

| Need | Keys |
|------|------|
| Free stock footage/images | `PEXELS_API_KEY`, `PIXABAY_API_KEY`, `UNSPLASH_ACCESS_KEY` |
| Multi-model gateway (image/video) | `FAL_KEY`, `ATLASCLOUD_API_KEY` |
| Video generation | `KLING_API_KEY`, `RUNWAY_API_KEY`, `ARK_API_KEY` (Seedance), `XAI_API_KEY`, `GOOGLE_API_KEY` (Veo/Imagen) |
| Voice, music, SFX | `ELEVENLABS_API_KEY`, `OPENAI_API_KEY`, `SUNO_API_KEY` |
| Avatars | `HEYGEN_API_KEY` |
| Local GPU video | `make install-gpu`, then `VIDEO_GEN_LOCAL_ENABLED=true`, `VIDEO_GEN_LOCAL_MODEL=wan2.2-ti2v-5b` |

Zero-cost path: Piper TTS (offline narration), Archive.org / NASA / Wikimedia Commons footage, free-key stock sites, Remotion or HyperFrames + FFmpeg for rendering.

Never commit `.env`. Never paste keys into prompts or logs.

### Step 4: Open the Repo in the Assistant
Start Claude Code from the `OpenMontage/` folder. Its `CLAUDE.md` points the agent at `AGENT_GUIDE.md` and `PROJECT_CONTEXT.md`. To see what the current setup can do, have the agent inspect the tool registry (`registry.support_envelope()` and `registry.provider_menu()`).

Key folders:
- `pipeline_defs/`: YAML pipeline manifests
- `skills/`: pipeline, creative, core and meta skill files
- `tools/`: video, audio, graphics, enhancement, analysis, avatar, subtitle tools
- `styles/`: visual style playbooks
- `remotion-composer/`: React/Remotion composition engine
- `backlot/`: local live production dashboard
- `projects/<name>/renders/final.mp4`: final output

### Step 5: Pick the Pipeline
| Brief | Pipeline |
|-------|----------|
| Teach a concept with motion graphics | Animated Explainer |
| Stylized animated story | Animation / Character Animation |
| Presenter delivering a script | Avatar Spokesperson or Talking Head |
| Moody, filmic piece | Cinematic |
| Many short clips from one source | Clip Factory |
| Real-footage montage, little or no narration | Documentary Montage |
| Mix of generated and real footage | Hybrid |
| Translate or re-voice a video | Localization & Dub |
| Clips from a podcast episode | Podcast Repurpose |
| Software walkthrough | Screen Demo |

Every pipeline runs the same stages: research → proposal → script → scene plan → assets → edit → compose.

### Step 6: Write the Brief
Give the agent: length, format/aspect ratio, audience, tone, narration (yes/no, voice), footage source (generated, real only, mixed), and budget cap.

```
Make a 45-second animated explainer about why the sky is blue.
Audience: high school students. Format: YouTube Shorts (9:16).
Narration: Piper voice. Budget cap: $0 (free providers only).
```

```
Make a 90-second documentary montage about what a city feels like at 4am.
Use real footage only, no narration, elegiac tone. Format: 16:9 1080p.
```

### Step 7: Approve at the Gates
- Review the proposal and script before assets are generated; that is the cheapest point to change direction.
- Default budget cap is $10 total, and any single action over $0.50 asks for approval. Keep these on. Lower them for experiments.
- Typical spend: about $0.15–$1.50 per image/video provider run, a few dollars for a full generated video. Real-footage montages with Piper can be $0.

### Step 8: Review and Deliver
- Let the built-in quality gates and self-review run. Don't bypass a failing gate; fix the cause.
- Watch the render end to end: audio sync, subtitle timing, text legibility on mobile, safe areas for vertical formats.
- Record the attribution required by any stock or archive footage used.
- Deliver `projects/<name>/renders/final.mp4` plus a short report (template below).

## Output Template
```
OpenMontage Production Report
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BRIEF:        [one line]
PIPELINE:     [name]
FORMAT:       [profile, aspect, resolution, duration]
PROVIDERS:    [image / video / voice / music used]
COST:         [actual $ vs cap $]
ASSET SOURCES & LICENSES:
  - [source]: [license / attribution text]
QUALITY GATES: [passed / issues fixed]
OUTPUT:       projects/[name]/renders/final.mp4
NEXT STEPS:   [revisions requested, alt cuts, captions export]
```

## Quality Gates
1. `make demo` succeeds before any paid run.
2. Proposal and script approved by the user before asset generation.
3. Spend stays under the agreed cap; every paid action above the threshold was approved.
4. Every external asset has a known license and recorded attribution.
5. Final render watched end to end; audio, subtitles and pacing checked.
6. Output profile matches the target platform (aspect ratio, length limit).

## Examples

### Good Run
```
Brief: 60s LinkedIn explainer on our new feature, 1:1, calm tone
Pipeline: Animated Explainer, Piper narration, Pexels b-roll
Cost: $0.00 of $5 cap
Gates: script approved, subtitles retimed after review
Output: projects/feature-explainer/renders/final.mp4 + attribution list
```

### Bad Run
```
✗ No brief details: agent guesses format and tone, three re-renders
✗ Budget cap raised to $100 "to be safe": $38 spent on unused video clips
✗ Stock clips with unknown license used in a paid ad
✗ Render delivered without watching it: subtitles drift by 2s
```

## Common Mistakes
1. **Skipping setup checks**: missing FFmpeg or an old Node version fails late, after paid assets were generated.
2. **Wrong pipeline**: using Animated Explainer for a real-footage piece wastes generation credits.
3. **Vague briefs**: no length, format or audience means more revision rounds.
4. **Turning off approvals**: the spend gates exist to stop runaway API costs.
5. **Ignoring the license**: OpenMontage itself is AGPL-3.0; check obligations before building it into a hosted product.

## Anti-Patterns
1. **Generating everything**: if real or stock footage fits, use it; it's cheaper and often more credible.
2. **One giant render**: approve the script and scene plan first, then render.
3. **Hardcoding keys in pipeline files**: keys belong in `.env` only.
4. **Trusting self-review blindly**: the agent's review is a gate, not a replacement for a human watch-through.
