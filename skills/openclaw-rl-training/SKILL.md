---
name: openclaw-rl-training
description: Train personalized AI agents with reinforcement learning from conversational feedback using OpenClaw-RL's async framework
triggers:
  - train an agent with OpenClaw-RL
  - set up reinforcement learning for my LLM
  - configure OpenClaw RL training
  - implement GRPO or OPD training
  - use OpenClaw-RL for agent optimization
  - deploy async RL training pipeline
  - train personalized AI with user feedback
  - set up agentic RL environment
---

# OpenClaw-RL Training Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

OpenClaw-RL is a fully asynchronous reinforcement learning framework that trains personalized AI agents from natural conversation feedback. It wraps self-hosted models in an OpenClaw-compatible API, intercepts live multi-turn conversations, and continuously optimizes the policy in the background without interrupting usage.

**Key capabilities:**
- Fully async 4-component architecture (serving, rollout, evaluation, training)
- Three learning paradigms: Binary RL (GRPO), On-Policy Distillation (OPD), Hybrid Combine
- Self-hosted and private — runs entirely on your infrastructure
- Supports personal agent optimization and general agentic RL (terminal, GUI, SWE, tool-call)
- Zero manual labeling — automatic trajectory creation from conversations

## Installation

### Prerequisites

```bash
# Python 3.8+ required
# CUDA-capable GPU(s) for training
# Docker (optional, for containerized deployment)
```

### Clone and Setup

```bash
git clone https://github.com/Gen-Verse/OpenClaw-RL.git
cd OpenClaw-RL

# Install dependencies for your chosen method
cd openclaw-combine  # or openclaw-rl, openclaw-opd, etc.
pip install -r requirements.txt

# Install slime framework
cd ../slime
pip install -e .

# Install Megatron-LM
cd ../Megatron-LM
pip install -e .
```

### Environment Variables

```bash
export OPENCLAW_API_KEY=your_api_key_here
export WANDB_API_KEY=$YOUR_WANDB_KEY  # For experiment tracking
export HF_TOKEN=$YOUR_HF_TOKEN  # For model downloads
```

## Architecture Components

OpenClaw-RL has 4 decoupled async components:

1. **Agent Server** - Serves the model via OpenClaw-compatible API
2. **Rollout Collector** - Intercepts conversations, creates training trajectories
3. **Judge/PRM Evaluator** - Scores interactions asynchronously with majority voting
4. **Policy Trainer** - Optimizes the model using collected feedback

## Training Methods

### 1. Binary RL (GRPO)

Uses Process Reward Model to score each turn, then applies GRPO advantage estimation with PPO-style clipped loss.

```bash
cd openclaw-rl

# Configure training script
export MASTER_ADDR=localhost
export MASTER_PORT=6000
export NNODES=1
export NODE_RANK=0
export GPUS_PER_NODE=8

# Launch training
bash run_binary_rl.sh
```

**Key configuration in script:**

```bash
#!/bin/bash

# Model paths
CKPT_PATH=/path/to/your/model/checkpoint
TOKENIZER_PATH=/path/to/tokenizer

# Rollout configuration
ROLLOUT_ARGS="
    --rollout-function-path rollout_binary.py \
    --num-rollout-workers 4 \
    --rollout-batch-size 32 \
    --max-turns 10
"

# Reward model configuration
REWARD_ARGS="
    --custom-rm-path process_reward_model.py \
    --rm-checkpoint /path/to/prm/checkpoint \
    --reward-aggregation majority
"

# Training hyperparameters
OPTIMIZER_ARGS="
    --lr 1e-6 \
    --lr-warmup-samples 100 \
    --clip-grad 1.0 \
    --ppo-clip-ratio 0.2 \
    --num-epochs 1
"

# Launch distributed training
torchrun --nproc_per_node=$GPUS_PER_NODE \
    --nnodes=$NNODES \
    --node_rank=$NODE_RANK \
    --master_addr=$MASTER_ADDR \
    --master_port=$MASTER_PORT \
    slime/train_grpo.py \
    $ROLLOUT_ARGS \
    $REWARD_ARGS \
    $OPTIMIZER_ARGS
```

### 2. On-Policy Distillation (OPD)

Extracts textual hints from next-state feedback, creates enhanced teacher trajectories, uses token-level log-prob gaps as directional advantages.

```bash
cd openclaw-opd

# Launch OPD training
bash run_opd_training.sh
```

**OPD configuration example:**

```python
# custom_opd_loss.py
import torch
import torch.nn.functional as F

def compute_opd_loss(
    student_logprobs,
    teacher_logprobs,
    advantage_mask,
    clip_ratio=0.2
):
    """
    OPD loss: token-level advantage from teacher-student log-prob gap
    """
    # Compute log-probability ratio
    logratio = student_logprobs - teacher_logprobs
    ratio = torch.exp(logratio)
    
    # Apply clipping
    clipped_ratio = torch.clamp(ratio, 1 - clip_ratio, 1 + clip_ratio)
    
    # Compute advantages (negative gap = student should improve)
    advantages = teacher_logprobs - student_logprobs
    
    # Masked loss (only on relevant tokens)
    loss_unclipped = -advantages * ratio
    loss_clipped = -advantages * clipped_ratio
    loss = torch.max(loss_unclipped, loss_clipped)
    
    # Apply mask and return mean
    masked_loss = loss * advantage_mask
    return masked_loss.sum() / advantage_mask.sum()
```

**OPD rollout script:**

```python
# rollout_opd.py
import asyncio
from typing import List, Dict

async def collect_opd_trajectory(
    prompt: str,
    student_model,
    teacher_augmentation_fn,
    max_turns: int = 10
) -> Dict:
    """
    Collect trajectory with teacher augmentation
    """
    trajectory = {
        "student_responses": [],
        "teacher_responses": [],
        "rewards": [],
        "advantages": []
    }
    
    current_prompt = prompt
    
    for turn in range(max_turns):
        # Student generation
        student_response = await student_model.generate(current_prompt)
        
        # Get next-state feedback (from user/env)
        feedback = await get_next_feedback(student_response)
        
        # Extract hint and create augmented teacher prompt
        hint = await extract_hint_from_feedback(feedback)
        teacher_prompt = augment_prompt_with_hint(current_prompt, hint)
        
        # Teacher generation
        teacher_response = await student_model.generate(teacher_prompt)
        
        # Store trajectory data
        trajectory["student_responses"].append(student_response)
        trajectory["teacher_responses"].append(teacher_response)
        
        # Update for next turn
        current_prompt = create_next_prompt(student_response, feedback)
    
    return trajectory
```

### 3. Hybrid Combine Method

Combines Binary RL scalar rewards with OPD token-level signals for stronger optimization.

```bash
cd openclaw-combine

# Launch hybrid training (one-line deployment)
bash run_combine_training.sh
```

**Hybrid loss implementation:**

```python
# hybrid_loss.py
import torch

def compute_hybrid_loss(
    student_logprobs,
    teacher_logprobs,
    scalar_rewards,
    opd_weight=0.5,
    binary_weight=0.5,
    clip_ratio=0.2
):
    """
    Hybrid loss combining Binary RL and OPD
    """
    # Binary RL component (GRPO)
    advantages_binary = compute_gae(scalar_rewards)
    logratio = student_logprobs - student_logprobs.detach()
    ratio = torch.exp(logratio)
    
    pg_loss1 = -advantages_binary * ratio
    pg_loss2 = -advantages_binary * torch.clamp(
        ratio, 1 - clip_ratio, 1 + clip_ratio
    )
    binary_loss = torch.max(pg_loss1, pg_loss2).mean()
    
    # OPD component (token-level)
    advantages_opd = teacher_logprobs - student_logprobs
    opd_loss = -advantages_opd.mean()
    
    # Combine with weights
    total_loss = (
        binary_weight * binary_loss +
        opd_weight * opd_loss
    )
    
    return total_loss, {
        "binary_loss": binary_loss.item(),
        "opd_loss": opd_loss.item(),
        "total_loss": total_loss.item()
    }
```

## Personal Agent Optimization

### Setup OpenClaw Extension

```bash
# Install the RL training headers extension
cd extensions/rl-training-headers
npm install
npm run build

# Configure in your OpenClaw instance
# Add to openclaw config.json:
```

```json
{
  "extensions": [
    {
      "name": "rl-training-headers",
      "enabled": true,
      "config": {
        "rollout_endpoint": "http://localhost:8000/rollout",
        "training_mode": "async",
        "session_tracking": true
      }
    }
  ]
}
```

### Launch Personal Agent Training

```bash
# Start the model server
cd openclaw-combine
python serve_model.py \
    --model-path /path/to/your/model \
    --port 8000 \
    --gpu-ids 0,1

# Start rollout collector
python collect_rollouts.py \
    --api-endpoint http://localhost:8000 \
    --output-dir ./rollouts \
    --session-aware

# Start async trainer
python train_async.py \
    --rollout-dir ./rollouts \
    --checkpoint-dir ./checkpoints \
    --method combine \
    --gpus 2,3,4,5
```

## General Agentic RL

### Terminal Agent

```bash
cd terminal-rl

# Configure environment
export TASK_TYPE=bash_commands
export MAX_STEPS=50

# Launch training
bash run_terminal_agent.sh
```

**Terminal rollout example:**

```python
# terminal_rollout.py
import asyncio
import subprocess

async def terminal_rollout(agent_model, task_description: str):
    """
    Collect terminal interaction trajectory
    """
    trajectory = []
    terminal_state = initialize_terminal()
    
    for step in range(MAX_STEPS):
        # Agent generates command
        command = await agent_model.generate(
            f"Task: {task_description}\nCurrent state: {terminal_state}\nCommand:"
        )
        
        # Execute in terminal
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True,
            timeout=10
        )
        
        # Compute reward based on output
        reward = compute_terminal_reward(result, task_description)
        
        trajectory.append({
            "command": command,
            "output": result.stdout,
            "error": result.stderr,
            "reward": reward
        })
        
        # Update state
        terminal_state = get_terminal_state()
        
        if task_completed(result, task_description):
            break
    
    return trajectory
```

### GUI Agent

```bash
cd gui-rl

# Launch GUI agent training with vision model
bash run_gui_agent.sh --model qwen3.5-vl
```

**GUI interaction example:**

```python
# gui_rollout.py
from PIL import Image
import pyautogui

async def gui_rollout(vision_model, task: str):
    """
    Collect GUI interaction trajectory with screenshots
    """
    trajectory = []
    
    for step in range(MAX_GUI_STEPS):
        # Capture screen
        screenshot = pyautogui.screenshot()
        
        # Agent decides action based on visual input
        action = await vision_model.generate(
            prompt=f"Task: {task}\nWhat action should I take?",
            image=screenshot
        )
        
        # Parse and execute action
        parsed_action = parse_gui_action(action)
        execute_gui_action(parsed_action)
        
        # Get reward from environment/user feedback
        reward = await get_gui_reward(task, screenshot, parsed_action)
        
        trajectory.append({
            "screenshot": screenshot,
            "action": action,
            "reward": reward
        })
    
    return trajectory
```

### SWE Agent

```bash
cd swe-rl

# Launch software engineering agent training
bash run_swe_agent.sh --benchmark swe-bench-lite
```

### Tool-Call Agent

```bash
cd toolcall-rl

# Configure available tools
export TOOLS_CONFIG=./tools_config.json

# Launch tool-call agent training
bash run_toolcall_agent.sh
```

**Tool-call training example:**

```python
# toolcall_trainer.py
import json

def train_toolcall_agent(model, tools_config_path: str):
    """
    Train agent to use tools effectively
    """
    with open(tools_config_path) as f:
        tools = json.load(f)
    
    # Create tool-augmented prompts
    tool_descriptions = format_tool_descriptions(tools)
    
    # Training loop
    for batch in dataloader:
        tasks = batch["tasks"]
        
        # Collect trajectories with tool usage
        trajectories = []
        for task in tasks:
            trajectory = collect_toolcall_trajectory(
                model=model,
                task=task,
                available_tools=tools
            )
            trajectories.append(trajectory)
        
        # Compute loss and update
        loss = compute_toolcall_loss(trajectories)
        loss.backward()
        optimizer.step()
```

## LoRA Training Support

```bash
# Configure LoRA parameters
export USE_LORA=true
export LORA_RANK=16
export LORA_ALPHA=32
export LORA_DROPOUT=0.1

# Launch with LoRA
bash run_combine_training.sh --lora
```

**LoRA configuration:**

```python
# lora_config.py
from peft import LoraConfig, get_peft_model

def setup_lora_model(base_model, lora_rank=16, lora_alpha=32):
    """
    Configure model with LoRA adapters
    """
    lora_config = LoraConfig(
        r=lora_rank,
        lora_alpha=lora_alpha,
        target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
        lora_dropout=0.1,
        bias="none",
        task_type="CAUSAL_LM"
    )
    
    peft_model = get_peft_model(base_model, lora_config)
    peft_model.print_trainable_parameters()
    
    return peft_model
```

## Cloud Deployment

### Tinker Deployment

```bash
# Configure Tinker credentials
export TINKER_API_KEY=$YOUR_TINKER_KEY
export TINKER_PROJECT_ID=your_project_id

# Deploy to Tinker
bash deploy_to_tinker.sh
```

### Fireworks AI Deployment

```bash
# Configure Fireworks AI
export FIREWORKS_API_KEY=$YOUR_FIREWORKS_KEY

# Deploy training job
bash deploy_to_fireworks.sh --gpus 8 --method combine
```

## Configuration Files

### Training Configuration

```yaml
# config/training_config.yaml
model:
  name: qwen3.5-4b
  checkpoint_path: /path/to/checkpoint
  tokenizer_path: /path/to/tokenizer

training:
  method: combine  # binary, opd, or combine
  batch_size: 32
  gradient_accumulation_steps: 4
  learning_rate: 1e-6
  warmup_steps: 100
  max_steps: 10000
  
  # Binary RL params
  ppo_clip_ratio: 0.2
  value_clip_ratio: 0.2
  gae_lambda: 0.95
  
  # OPD params
  teacher_temperature: 1.0
  hint_extraction_model: gpt-4
  
  # Hybrid params
  binary_weight: 0.5
  opd_weight: 0.5

rollout:
  num_workers: 4
  max_turns: 10
  session_aware: true
  parallel_envs: 16

evaluation:
  judge_model: gpt-4
  majority_voting: true
  num_judges: 3
  eval_frequency: 100
```

### Rollout Configuration

```json
{
  "rollout_config": {
    "collection_mode": "async",
    "max_concurrent_sessions": 100,
    "session_timeout": 3600,
    "trajectory_format": "multi_turn",
    "message_classification": {
      "main_line": ["user", "assistant"],
      "side": ["system", "tool"]
    },
    "reward_computation": {
      "type": "next_state_feedback",
      "aggregation": "majority",
      "num_samples": 3
    }
  }
}
```

## Common Patterns

### Custom Reward Function

```python
# custom_reward.py
import torch

class CustomRewardModel:
    def __init__(self, checkpoint_path: str):
        self.model = load_reward_model(checkpoint_path)
    
    def compute_reward(
        self,
        prompt: str,
        response: str,
        next_feedback: str
    ) -> float:
        """
        Compute reward based on response quality and next feedback
        """
        # Encode inputs
        inputs = self.tokenize(
            f"Prompt: {prompt}\nResponse: {response}\nFeedback: {next_feedback}"
        )
        
        # Get reward score
        with torch.no_grad():
            reward = self.model(inputs).item()
        
        return reward
    
    def batch_compute_rewards(self, batch_data):
        """
        Efficiently compute rewards for batch
        """
        rewards = []
        for item in batch_data:
            reward = self.compute_reward(
                item["prompt"],
                item["response"],
                item["feedback"]
            )
            rewards.append(reward)
        return torch.tensor(rewards)
```

### Session-Aware Trajectory Processing

```python
# session_processor.py
from collections import defaultdict

class SessionAwareProcessor:
    def __init__(self):
        self.sessions = defaultdict(list)
    
    def add_interaction(self, session_id: str, interaction: dict):
        """
        Add interaction to session trajectory
        """
        self.sessions[session_id].append(interaction)
    
    def get_training_trajectories(self, min_turns: int = 3):
        """
        Extract complete trajectories for training
        """
        trajectories = []
        
        for session_id, interactions in self.sessions.items():
            if len(interactions) >= min_turns:
                # Classify messages
                main_line = [
                    i for i in interactions
                    if i["role"] in ["user", "assistant"]
                ]
                
                # Create trajectory with advantages
                trajectory = self.compute_trajectory_advantages(main_line)
                trajectories.append(trajectory)
        
        return trajectories
    
    def compute_trajectory_advantages(self, interactions: list):
        """
        Compute GAE advantages for trajectory
        """
        rewards = [i["reward"] for i in interactions]
        values = [i.get("value", 0) for i in interactions]
        
        advantages = compute_gae(
            rewards=rewards,
            values=values,
            gamma=0.99,
            lambda_=0.95
        )
        
        return {
            "interactions": interactions,
            "advantages": advantages
        }
```

## Monitoring and Debugging

### Weights & Biases Integration

```python
# wandb_logging.py
import wandb

def setup_wandb_logging(project_name: str, config: dict):
    """
    Initialize W&B tracking
    """
    wandb.init(
        project=project_name,
        config=config,
        name=f"openclaw-rl-{config['method']}"
    )

def log_training_metrics(step: int, metrics: dict):
    """
    Log metrics to W&B
    """
    wandb.log({
        "step": step,
        "loss/total": metrics["total_loss"],
        "loss/binary": metrics.get("binary_loss", 0),
        "loss/opd": metrics.get("opd_loss", 0),
        "reward/mean": metrics["mean_reward"],
        "reward/std": metrics["std_reward"],
        "gradient/norm": metrics["grad_norm"],
        "learning_rate": metrics["lr"]
    })
```

### Debug Rollout Collection

```bash
# Enable debug logging
export OPENCLAW_DEBUG=true
export ROLLOUT_LOG_LEVEL=DEBUG

# Test rollout collection
python -m openclaw_combine.test_rollout \
    --num-samples 10 \
    --output-dir ./debug_rollouts
```

## Troubleshooting

### Out of Memory During Training

```bash
# Reduce batch size and use gradient accumulation
export BATCH_SIZE=8
export GRAD_ACCUM_STEPS=8

# Enable gradient checkpointing
export USE_GRADIENT_CHECKPOINTING=true

# Use LoRA instead of full fine-tuning
export USE_LORA=true
export LORA_RANK=8
```

### Slow Rollout Collection

```python
# Increase parallel workers
ROLLOUT_ARGS="
    --num-rollout-workers 16 \
    --parallel-envs 32 \
    --async-collection
"
```

### Reward Model Disagreement

```yaml
# Use majority voting with more judges
evaluation:
  judge_model: gpt-4
  majority_voting: true
  num_judges: 5  # Increase from 3
  consensus_threshold: 0.6
```

### Training Instability

```bash
# Reduce learning rate and clip gradients
export LEARNING_RATE=5e-7
export CLIP_GRAD_NORM=0.5

# Adjust PPO clipping
export PPO_CLIP_RATIO=0.1

# Enable value function clipping
export VALUE_CLIP=true
```

### Session Tracking Issues

```python
# Check session classification
from openclaw_combine.utils import inspect_sessions

sessions = inspect_sessions("./rollouts")
for session_id, data in sessions.items():
    print(f"Session {session_id}:")
    print(f"  Total turns: {len(data)}")
    print(f"  Main-line turns: {sum(1 for i in data if i['type'] == 'main')}")
    print(f"  Side turns: {sum(1 for i in data if i['type'] == 'side')}")
```

## Best Practices

1. **Start with small scale**: Test with 1-2 GPUs and small batch sizes before scaling
2. **Monitor gradients**: Watch for gradient explosion/vanishing in early steps
3. **Use wandb**: Track experiments systematically with Weights & Biases
4. **Checkpoint frequently**: Save checkpoints every 100-500 steps for recovery
5. **Validate rollouts**: Inspect collected trajectories before full training runs
6. **Combine methods gradually**: Start with Binary RL, then OPD, then Hybrid
7. **Keep framework unmodified**: Use extension points instead of modifying core code
