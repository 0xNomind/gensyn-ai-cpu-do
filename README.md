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
cd rl-swarm
nano modal-login/package.json
```
* Update: `"viem":` to `"2.25.0"`

2- Upgrade
```bash
cd rl-swarm
cd modal-login
yarn install

yarn upgrade && yarn add next@latest && yarn add viem@latest

cd ..
```

### ⚠️ CPU-only Users: Ran out of input
Navigate:
```
cd rl-swarm
```
Edit:
```
nano hivemind_exp/configs/mac/grpo-qwen-2.5-0.5b-deepseek-r1.yaml
```
* Lower `max_steps` to `5` 

## 3) Run the swarm
Open a screen to run it in background
```bash
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
