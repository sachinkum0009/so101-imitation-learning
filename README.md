# Imitation Learning with SO101

## Demo

https://github.com/user-attachments/assets/35ca7b1e-3ef0-47ac-b40a-9381fb2fbc7b



## Commands

> Calibrate Follower and Leader

```bash
uv run lerobot-calibrate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=my_awesome_follower_arm2
```

```bash
uv run lerobot-calibrate \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=my_awesome_leader_arm4
```

> Teleoperate Arms
```bash
uv run python -m lerobot.teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=my_awesome_follower_arm3 \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=my_awesome_leader_arm4
```


> Record Training Dataset
```bash
uv run lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM0 \
    --robot.id=my_awesome_follower_arm3 \
    --robot.cameras="{ wrist: {type: opencv, index_or_path: 4, width: 640, height: 480, fps: 30}, front: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30}}" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM1 \
    --teleop.id=my_awesome_leader_arm4 \
    --display_data=true \
    --dataset.repo_id=${HF_USER}/eval_record_test2 \
    --dataset.num_episodes=10 \
    --dataset.single_task="Pick up toothbrush" --resume=true --dataset.reset_time_s=10
```

> Train ACT policy
```bash
uv run lerobot-train \
  --dataset.repo_id=${HF_USER}/record-test2 \
  --policy.type=act \
  --output_dir=outputs/train/act_so101_test \
  --job_name=act_so101_test \
  --policy.device=cuda \
  --wandb.enable=true \
  --policy.repo_id=${HF_USER}/so101_policy_toothbrush
```

> Push to Huggingface
```bash
uv run hf upload ${HF_USER}/act_so101_test   outputs/train/act_so101_test/checkpoints/last/pretrained_model
```


> Run inference and evaluation
```bash
uv run lerobot-record  \
  --robot.type=so101_follower \
  --robot.port=/dev/ttyACM0 \
  --robot.cameras="{ wrist: {type: opencv, index_or_path: 4, width: 640, height: 480, fps: 30}, front: {type: opencv, index_or_path: 2, width: 640, height: 480, fps: 30}}" \
  --robot.id=my_awesome_follower_arm3 \
  --teleop.type=so101_leader \
  --teleop.port=/dev/ttyACM1 \
  --teleop.id=my_awesome_leader_arm4 \
  --display_data=false \
  --dataset.repo_id=${HF_USER}/eval_record_test3 \
  --dataset.single_task="Pick up toothbrush" \
  --policy.path=${HF_USER}/act_so101_test
```

## References
- https://huggingface.co/docs/lerobot/il_robots
