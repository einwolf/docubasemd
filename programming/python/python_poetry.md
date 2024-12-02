# Python poetry

Installing extras

```bash
pip install gymnasium[box2d,atari,accept-rom-license"]

poetry add gymnasium --extras "box2d atari accept-rom-license"
```

Using extra repo urls

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

# source added in 1.2? Not in 1.1. Is in 1.3 and 1.4.
poetry source add --secondary pytorch-cu118 https://download.pytorch.org/whl/cu118
poetry add --source pytorch-cu118 torch torchvision torchaudio
```
