![image](https://github.com/user-attachments/assets/8ad5a694-e287-4d45-ba57-203f58a19714)

# Run RL Swarm (Testnet) Node
RL Swarm is a fully open-source framework developed by GensynAI for building reinforcement learning (RL) training swarms over the internet. This guide walks you through setting up an RL Swarm node and a web UI dashboard to monitor swarm activity.

## Hardware Requirements
- CPU: Minimum 16GB RAM (more RAM recommended for larger models or datasets).

## Swap File if USE DO $48 -> 4/8 
Buat file Swapfile :
```
sudo fallocate -l 16G /swapfile
```

Set Perm : 
```
sudo chmod 600 /swapfile
```

Format :
```
sudo mkswap /swapfile
```

Enable : 
```
sudo swapon /swapfile
```

Enable on boot : 
```
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Cek :
```
free
```

Reboot :
```
sudo reboot
```

---

## 1) Install Dependencies
**1. Update System Packages**
```bash
sudo apt-get update && sudo apt-get upgrade -y
```
**2. Install General Utilities and Tools**
```bash
sudo apt install screen curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

**3. Install Python**
```bash
sudo apt-get install python3 python3-pip python3-venv python3-dev -y
```

**4. Install Node**
```
sudo apt-get update
```
```
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```
```
sudo apt-get install -y nodejs
```
```
node -v
```
```bash
sudo npm install -g yarn
```
```bash
yarn -v
```

**5. Install Yarn**
```bash
curl -o- -L https://yarnpkg.com/install.sh | bash
```
```bash
export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"
```
```bash
source ~/.bashrc
```

---

## 2) Clone the Repository
```bash
git clone https://github.com/gensyn-ai/rl-swarm/
cd rl-swarm
```

---

# Troubleshooting:

### ⚠️ Upgrade viem & Node version in Login Page
1- Modify: `package.json`
```bash
cd $HOME/rl-swarm
nano modal-login/package.json
```
* Update: `"viem":` to `"2.25.0"`

2- Upgrade
```bash
cd $HOME/rl-swarm
cd modal-login
yarn install

yarn upgrade && yarn add next@latest && yarn add viem@latest

cd ..
```

### ⚠️ CPU-only Users: Ran out of input
Navigate:
```
cd $HOME/rl-swarm
```
Edit:
```
rm -rf hivemind_exp/configs/mac/grpo-qwen-2.5-0.5b-deepseek-r1.yaml && nano hivemind_exp/configs/mac/grpo-qwen-2.5-0.5b-deepseek-r1.yaml
```
Fill with this :
```
# Model arguments
model_name_or_path: Gensyn/Qwen2.5-0.5B-Instruct
model_revision: main
torch_dtype: float32  # Native CPU precision
attn_implementation: eager  # CPU-friendly attention
bf16: false
tf32: false
output_dir: runs/gsm8k/multinode/Qwen2.5-0.5B-Instruct-Gensyn-Swarm

# Dataset arguments
dataset_id_or_path: 'openai/gsm8k'

# LoRA Arguments
use_peft: true
lora_alpha: 8
lora_dropout: 0.05
lora_r: 4
lora_target_modules: ["q_proj", "k_proj", "v_proj", "o_proj"]
lora_task_type: "CAUSAL_LM"

# Training arguments
max_steps: 10  # For testing; scale up as needed
per_device_train_batch_size: 32  #Increase for higher batch in one process
gradient_accumulation_steps: 4
gradient_checkpointing: false
learning_rate: 5.0e-5
lr_scheduler_type: cosine
warmup_ratio: 0.01
# GRPO specific parameters
beta: 0.001
max_prompt_length: 128
max_completion_length: 256
num_generations: 1
use_vllm: false

# CPU-specific optimizations
num_workers: 4  # Adjust based on your total threads 
ddp_enabled: false
torch_distributed_backend: "gloo"  # CPU-friendly DDP backend
torch_num_threads: 4  # Adjust based on your total threads
torch_intraop_threads: 4  # Explicit intra-op parallelism and Adjust based your total threads
torch_interop_threads: 2   # Minimal inter-op threads to avoid contention
pin_memory: false
non_blocking: false
prefetch_factor: 4  # Boost data pre-loading efficiency
torch_use_deterministic_algorithms: false  # Speed over determinism
torch_use_cpu_affinity: true  # Bind threads to cores (AMD EPYC optimization)

# Logging arguments
logging_strategy: steps
logging_steps: 2
report_to:
- tensorboard
save_strategy: "steps"
save_steps: 25
seed: 42

# Script arguments
max_rounds: 10000
```

## 3) Run the swarm
Open a screen to run it in background
```bash
cd $HOME/rl-swarm
screen -S swarm
```
Install swarm
```bash
python3 -m venv .venv
source .venv/bin/activate
./run_rl_swarm.sh
```
Press `Y`

---

## 4) Login
**1- You have to receive `Waiting for userData.json to be created...` in logs**
**2- Open login page in browser**
* **Local PC:** `http://localhost:3000/`
* **VPS users:** Do not receive OTP code in emails by logging in 3000 port on browser. You have to forward port by entering a command in their local pc powershell command prompt. (Step 3 of this section)

**3- ⚠️ If you can't login or no email code received, Forward port:**
* In windows start menu, Search **Powershell** and open its terminal in your local PC
* Enter the command below and replace your vps ip with `Server_IP` and your vps port(.eg 22) with `SSH_PORT`
```
ssh -L 3000:localhost:3000 root@Server_IP -p SSH_PORT
```
* ⚠️ Make sure you enter the command in your own local Windows Powershell cmd and NOT your VPS terminal.
* This prompts you to enter your VPS password, when you enter it, you connect and tunnel to your vps
* Now go to browser and open `http://localhost:3000/` and login

**4- Login with your preferred method**

* After login, your terminal starts installation.

* Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N] N

### Screen commands
* Minimize: `CTRL` + `A` + `D`
* Return: `screen -r swarm`
* Stop and Kill: `screen -XS swarm quit`

---

## Backup
**You need to backup `swarm.pem`**.
### `VPS`:
Connect your VPS using `Mobaxterm` client to be able to move files to your local system. Back up these files:**
* `/root/rl-swarm/swarm.pem`

### `WSL`:
Search `\\wsl.localhost` in your ***Windows Explorer*** to see your Ubuntu directory. Your main directories are as follows:
* If installed via a username: `\\wsl.localhost\Ubuntu\home\<your_username>`
* If installed via root: `\\wsl.localhost\Ubuntu\root`
* Look for `rl-swarm/swarm.pem`

# Node Health
### Official Dashboard
* Top 100 round-participants: https://dashboard.gensyn.ai/

![image](https://github.com/user-attachments/assets/cd8e8cd3-f057-450a-b1a2-a90ca10aa3a6)

### Telegram Bot
Search you `Node ID` here with `/check` here: https://t.me/gensyntrackbot 
* `Node-ID` is near your Node name

![image](https://github.com/user-attachments/assets/2946ddf4-f6ef-4201-b6a0-cb5f16fb4cec)

* ⚠️ If receiving `EVM Wallet: 0x0000000000000000000000000000000000000000`, your `onchain-participation` is not being tracked and you have to Install with `New Email` and ***Delete old `swarm.pem`***

![image](https://github.com/user-attachments/assets/8852d3b3-cb13-473e-863f-f4cbe3d0abdd)

---
