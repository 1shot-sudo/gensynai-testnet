<h2 align=center>Gensyn Testnet Node Guide</h2>

## 💻 System Requirements

| Requirement                         | Details                                                     |
|-------------------------------------|-------------------------------------------------------------|
| **CPU Architecture**                | `arm64` or `amd64`                                          |
| **Recommended RAM**                 | 24 GB                                                       |
| **CUDA Devices (Recommended)**      | `RTX 3090`, `RTX 4070`, `RTX 4090`, `A100`, `H100`          |
| **Python Version**                  | Python >= 3.10 (For Mac, you may need to upgrade)           |


## 📥 Hướng dẫn cài đặt

1. **Cài đặt `sudo`**
```bash
sudo apt update && apt install -y
```
2. **Cài đặt các dependencies khác**
```bash
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip
```
3. **Cài đặt Node.js and npm**  
```bash
curl -sSL https://raw.githubusercontent.com/zunxbt/installation/main/node.sh | bash
```
4. **Tạo `screen` session**
```bash
screen -S gensyn
```
5. **Chạy node**
```bash
cd $HOME && rm -rf gensyn-testnet && git clone https://github.com/zunxbt/gensyn-testnet.git && chmod +x gensyn-testnet/gensyn.sh && ./gensyn-testnet/gensyn.sh
```
- It will ask some questions, you should send response properly
- ```Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]``` : Write `N`
- When you will see interface like this, you can detach from this screen session

![Screenshot 2025-04-01 061641](https://github.com/user-attachments/assets/b5ed9645-16a2-4911-8a73-97e21fdde274)

7. **Detach from `screen session`**
- Use `Ctrl + A` and then press `D` to detach from this screen session.

 ## 🔄️ Back up `swarm.pem`
After running the Gensyn node, it is essential to back up the swarm.pem file from your remote server (GPU or VPS) to your local PC. If you lose this file, your contribution will also be lost. Some GPU servers do not support SCP or SFTP, so I will provide distinct methods — one specifically for GPU servers and another for VPS.

### 1. Back up `swarm.pem` from GPU server to local PC
- For this, you must need to connect to GPU server using [SSH](https://github.com/zunxbt/gensyn-testnet?tab=readme-ov-file#-connect-via-ssh) (Recommened to do these stuffs on Command Prompt or Power Shell)
- Now exit from this GPU server using this command
```
exit
```
- Now replace `SSH-COMMAND` in the below command with the command which your received from provider, then replace `YOUR-PC-PATH` where you want to download this swarm.pem file and then execute it on your Command prompt or Power shell
```
SSH-COMMAND "cat ~/rl-swarm/swarm.pem" > "YOUR-PC-PATH\swarm.pem"
```
- In my case, this command looks like this :
```
ssh -p 69 root@69.69.69.69 "cat ~/rl-swarm/swarm.pem" > "C:\Users\USER\Downloads\swarm.pem"
```
- Done, your `swarm.pem` file is now saved on your local system

### 2. Back up `swarm.pem` from VPS server to local PC
- For this, I recommend to use `Command Prompt` or `Power Shell`
- If you are using **Command Prompt** then use the below commmand, make sure to replace `VPS-USERNAME` , `VPS-IP`and `YOUR-PC-PATH` (where you want to save swarm.pem file) with actual value
```
scp VPS-USERNAME@VPS-IP:~/rl-swarm/swarm.pem "YOUR-PC-PATH"
```
- In my case this command looks like this :
```
scp root@69.69.69.69:~/rl-swarm/swarm.pem "C:\Users\USER\Downloads"
```
- If you are using **Powershell** then use the below commmand, make sure to replace `VPS-USERNAME` , `VPS-IP`and `YOUR-PC-PATH`(where you want to save swarm.pem file) with actual value
```
scp VPS-USERNAME@VPS-IP:~/rl-swarm/swarm.pem 'YOUR-PC-PATH'
```
- In my case this command looks like this :
```
scp root@69.69.69.69:~/rl-swarm/swarm.pem 'C:\Users\USER\Downloads'
```

### 3. Back up `swarm.pem` from WSL to local PC
- First, open `Command Prompt` or `Windows Powershell`
- Then use the below command, make sure to replace `YOUR-WSL-USERNAME` `YOUR-PC-PATH`(where you want to save swarm.pem file) with actual value
```
copy "\\wsl$\Ubuntu\home\YOUR-WSL-USERNAME\rl-swarm\swarm.pem" "YOUR-PC-PATH"
```
- In my case, it looks like this
```
copy "\\wsl$\Ubuntu\home\zun24\rl-swarm\swarm.pem" "C:\Users\USER\Downloads"
```

## 🟢 Node Status

### 1. Check Logs
- To check whether your node is running or not, you can check logs
- To check logs you need to re-attach with screen session, so use the below command
```
screen -r gensyn
```
- If you see everything running then it's fine
- Now detach from this screen session, Use `Ctrl + A` and then press `D` to detach from this screen session.
- Everytime you reattach, every time you should detach

### 2. Check Wins
- Visit : https://gensyn-node.vercel.app/
- Enter Peer-ID that you often see this in your logs
- The more win, the better

> [!Note]
> If you see `0x0000000000000000000000000000000000000000` in `Connected EOA Address` section, that means your contribution is not being recorded, so you should run the node from beginning with fresh new email (means u need to delete existing `swarm.pem` file

## ⚠️ Troubleshooting

### 🔴 Daemon failed to start in 15.0 seconds
- If you are facing this issue then follow this step by step guide
- First use tihs command
```
nano $(python3 -c "import hivemind.p2p.p2p_daemon as m; print(m.__file__)")
```
- Then scroll down and look for this line `startup_timeout: float = 15,` , here u need to modify this 15 with 120, and after modifying it will look like this : `startup_timeout: float = 120,`
- Save this changes, first use `Ctrl` + `X` and then press `Y` and then press `Enter`
- Now use this command again to run `rl-swarm`
```bash
./run_rl_swarm.sh
```
