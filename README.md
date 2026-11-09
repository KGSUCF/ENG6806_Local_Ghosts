# ENG6806 Local Ghosts

Experiments with local AI models run through Ollama.

## Setup

- PC: AMD Ryzen AI 9 HX PRO with Radeon 890M graphics built into the processor, 96 GB RAM, Windows
- Ollama with `gpt-oss:20b` (13 GB). `ollama ps` reports **100% GPU**, and replies start almost immediately.

## In Search of Pink: Claude vs. a local model

- `pink/claude-original/index.html`: the app built with Claude, saved from the claude.ai artifact.
- `pink/prompt-for-local-model.md`: a description of that app, given to the local model as one prompt.
- `pink/local/`: what each local model produced.
- `pink/notes.md`: observations and comparison.
