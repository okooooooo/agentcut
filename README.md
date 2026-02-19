# AgentCut - Multi-Agent AI Video Production

AgentCut turns a single text prompt into a fully produced video using 6 specialized AI agents that collaborate through a structured pipeline.

## Architecture

```
User Prompt
    |
    v
[Director Agent] -- Analyzes creative vision, plans shots
    |
    v
[Script Agent] -- Writes production-ready script with video prompts, narration, subtitles
    |
    v
[Visual Agent] ----+---- [Voice Agent] ----+---- [Music Agent]
 (Hailuo Video)    |     (Speech-2.6-HD)   |     (Music-2.0)
                   v                        v
              [Editor Agent] -- ffmpeg compositing: concat, mix, subtitle burn-in
                   |
                   v
              Final MP4
```

**Pipeline:** Director -> Script -> Visual + Voice + Music (parallel) -> Editor

## Tech Stack

- **LLM:** MiniMax M1 (chat completions for Director & Script agents)
- **Video:** MiniMax Hailuo 2.3 (1080P, 6s clips, async task polling)
- **Voice:** MiniMax Speech-2.6-HD (TTS with emotion control)
- **Music:** MiniMax Music-2.0 (instrumental background music)
- **Composition:** ffmpeg (concat, audio mixing, subtitle burn-in)
- **Backend:** Python FastAPI + SSE streaming
- **Frontend:** HTML + Tailwind CSS

## Quick Start

### Prerequisites

- Python 3.10+
- ffmpeg installed (`brew install ffmpeg` or `apt install ffmpeg`)
- MiniMax API key

### Setup

```bash
git clone https://github.com/calderbuild/agentcut.git
cd agentcut

pip install -r backend/requirements.txt

echo "MINIMAX_API_KEY=your-key-here" > .env
```

### Run

```bash
python -m backend.main
```

Open http://localhost:8000 in your browser.

### Usage

1. Enter a video description (or pick a template)
2. Choose duration and shot count
3. Click "Start Production"
4. Watch the 6 agents work in real-time via the pipeline visualization
5. Download the final MP4

## API

| Endpoint | Method | Description |
|---|---|---|
| `/api/create` | POST | Start a production job |
| `/api/stream/{job_id}` | GET | SSE event stream for real-time progress |
| `/api/status/{job_id}` | GET | Poll job status |
| `/api/download/{job_id}` | GET | Download final video |
| `/api/health` | GET | Health check |

## Project Structure

```
backend/
  agents/
    director.py   -- Creative director: prompt -> shot outline
    script.py     -- Scriptwriter: outline -> production script
    visual.py     -- Visual artist: script -> video clips (parallel)
    voice.py      -- Voice artist: script -> narration audio
    music.py      -- Composer: style+mood -> background music
    editor.py     -- Editor: ffmpeg composition
  pipeline.py     -- Agent orchestration
  main.py         -- FastAPI server
  config.py       -- Configuration
frontend/
  index.html      -- Single-page app
```

## Built for

[Return of the Agents Hackathon](https://aforehacks.org) by Afore Capital & AI Valley (Feb 2026)
