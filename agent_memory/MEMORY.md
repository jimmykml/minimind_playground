# Agent memory — minimind_playground

Read this first. Keep it current: update it whenever progress, important changes, or user preferences change. Newest entries go at the top of the Progress log.

## User preferences / rules
- Language: user writes in Chinese; reply in Chinese.
- Do NOT add new files to the repo unless the user asked for a source-code change. All temp stuff (scratch .py scripts, logs, outputs, wandb run dirs) goes in `agent_space/tmp/` (gitignored). When running wandb scripts set `WANDB_DIR=agent_space/tmp`.
- `agent_memory/` is tracked on purpose (not gitignored); this file is the shared handoff for other sessions/agents.
- Don't commit unless asked. Work on a branch, not master, for changes.
- Secrets live in `/workspace/.env` (outside the repo, chmod 600): `WANDB_API_KEY`. Never write keys into the repo or this file.

## Environment
- Vast.ai GPU container, unprivileged. Repo at `/workspace/minimind_playground` (a clone of MiniMind, remote `origin`).
- Python env: `source /venv/main/bin/activate` (torch + wandb 0.30.0 installed). `swanlab` is NOT installed.
- `/workspace/.env` is auto-sourced by login shells/supervisor services; in other shells: `set -a; source /workspace/.env; set +a`.
- Full instance guide: `/workspace/CLAUDE.md`.

## Repo notes
- Training scripts in `trainer/`: pretrain, full_sft, lora, dpo, ppo, grpo, distillation, agent. Each has `--use_wandb` and `--wandb_project`; run from inside `trainer/` (paths like `../checkpoints`, `../out`).
- Checkpoint/resume logic: `lm_checkpoint` in `trainer/trainer_utils.py` (stores `wandb_id` for resume).
- `dataset/*.jsonl` and `out` are gitignored. Datasets already downloaded in `dataset/` (pretrain_t2t[_mini], sft_t2t[_mini], dpo, rlaif, agent_rl*, lora_*). No `out/` or `checkpoints/` yet (nothing trained).

## Important changes (branch `use-wandb`, uncommitted)
- Upstream default was SwanLab (`import swanlab as wandb`, China-friendly). Switched all 8 `trainer/train_*.py` to `import wandb` because this machine can reach wandb and user has a wandb key.
- `trainer/trainer_utils.py` `lm_checkpoint`: non-`get_run` branch now reads `wandb.run.id` (real wandb has no `wandb.id`), so resume keeps the same run. `get_run` (swanlab) branch kept.
- Not changed: `requirements.txt` still lists `swanlab==0.9.8`; READMEs still describe SwanLab as default.
- `.gitignore`: added `agent_space/`.

## Progress log
- 2026-10-07: Created `/workspace/.env` with WANDB_API_KEY. Audited code for wandb support (found it used swanlab). Created branch `use-wandb` and swapped to wandb. Smoke-tested wandb with a scratch script (`agent_space/tmp/test_wandb.py`): login + logging OK (project `wandb-smoke-test`, entity `jlkm905-xiaobai`). Real training with `--use_wandb` not yet run end-to-end.
- Earlier (2026-10-06): repo cloned, venv backup and `.hf_home` set up. Training/learning progress so far: none recorded yet.

## Open items / next steps
- Run an actual short training (e.g. `trainer/train_pretrain.py --use_wandb`) to confirm wandb logging + resume end-to-end (datasets already present; use the _mini ones for a quick test).
- Decide whether to commit the `use-wandb` branch and clean up swanlab mentions in requirements/READMEs.
