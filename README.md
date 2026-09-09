# MarioRL

Modernized Super Mario Bros RL tutorial (2026). Fixes `gym`, `numpy 2.0` and Python 3.9 breaking changes — same 4-stage flow: Setup → Preprocess → Train → Test.

Notebook `Mario Tutorial.ipynb` has `# NOTE (2026):` comments on every change.

## Setup

Requires **Python 3.13+** (3.14 via Homebrew: `/opt/homebrew/bin/python3`). Fix `~/.zshrc` so Homebrew takes precedence:

```zsh
export PATH="/opt/homebrew/bin:$PATH"  # keep at end of ~/.zshrc, then source ~/.zshrc
```

```bash
rm -rf .venv
/opt/homebrew/bin/python3 -m venv .venv   # or python3.13
source .venv/bin/activate
pip install --upgrade pip wheel setuptools
pip install -r requirements.txt           # or: pip install "stable-baselines3[extra]" gymnasium gym-super-mario-bros nes-py torch matplotlib tqdm tensorboard rich
pip install jupyter
jupyter notebook  # open Mario Tutorial.ipynb → Run All
```

The notebook also self-installs via its first `%pip install` cell.

## What changed

- `gym` → `gymnasium` (`import gymnasium as gym`, `gymnasium.wrappers.GrayscaleObservation`)
- `gym-super-mario-bros 7.3.0` → `9.1.0`, `nes-py 8.2.1` → `9.0.1` (fixes `uint8` overflow with `numpy 2`)
- `env.reset() / env.step()` → `obs, info = env.reset(seed=…)` and `obs, reward, terminated, truncated, info = env.step(a)`
- `gym.make` now needs `render_mode='rgb_array'`; plots use `matplotlib` instead of `env.render()`
- `torch 1.10+cu113` → `torch 2.x` (auto CUDA/MPS), `DummyVecEnv` factory fixed

## Troubleshooting

- `OverflowError: Python integer 1024 out of bounds for uint8` → upgrade to `nes-py>=9` + `gym-super-mario-bros>=9` on Python 3.13+ or `pip install "numpy<2"`
- `which python3` shows `/usr/bin/python3` → move `export PATH="/opt/homebrew/bin:$PATH"` to end of `~/.zshrc` and `source ~/.zshrc`
- `.venv` still 3.9 after PATH fix → `rm -rf .venv` and recreate with Homebrew Python

Smoke test: `python -c "import gymnasium as gym; import gym_super_mario_bros; from nes_py.wrappers import JoypadSpace; from gym_super_mario_bros.actions import SIMPLE_MOVEMENT; env=gym.make('SuperMarioBros-v0', render_mode='rgb_array'); env=JoypadSpace(env, SIMPLE_MOVEMENT); print(env.reset()[0].shape); env.close()"`

## Files

- `Mario Tutorial.ipynb` — verified on Python 3.14.7 + gymnasium 1.3.0 + SB3 2.9.0 + torch 2.14.0
- `requirements.txt` — modern pins (`python>=3.13`)
