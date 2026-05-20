```bash
docker run --name isaac-lab --entrypoint bash -it --gpus all --rm -e "ACCEPT_EULA=Y" --network=host \
   -e "PRIVACY_CONSENT=Y" \
   -e DISPLAY \
   -v $HOME/.Xauthority:/root/.Xauthority \
   -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
   -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
   -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
   -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
   -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
   -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
   -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
   -v ~/docker/isaac-sim/documents:/root/Documents:rw \
   nvcr.io/nvidia/isaac-lab:2.3.0
```

```bash
cd /workspace
git clone https://github.com/enactic/openarm_isaac_lab.git
```

```bash
cd openarm_isaac_lab
python -m pip install -e source/openarm
```

```bash
python ./scripts/tools/list_envs.py
```

### nano install
```bash
apt-get update && apt-get install -y nano
nano ./scripts/reinforcement_learning/rsl_rl/play.py
# Change line 81 to:
from isaaclab_tasks.utils.pretrained_checkpoint import get_published_pretrained_checkpoint
```

```bash
python ./scripts/reinforcement_learning/rsl_rl/train.py --task Isaac-Open-Drawer-OpenArm-v0 --num_envs 2048 --headless
```
### To find the training files
```bash
ls ./scripts/reinforcement_learning/rsl_rl/logs/
```
### To see the result
```bash
cd /workspace/openarm_isaac_lab
python ./scripts/reinforcement_learning/rsl_rl/play.py --task Isaac-Open-Drawer-OpenArm-v0 --num_envs 32 --load_run 2026-05-20_13-42-57
```
