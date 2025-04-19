<h2 align=center>Gensyn Testnet Node Guide chạy bằng WSL máy cá nhân</h2>

## 💻 Yêu cầu hệ thống

| Yêu cầu                             | Chi tiết                                                    |
|-------------------------------------|-------------------------------------------------------------|
| **CPU Architecture**                | `arm64` or `amd64`                                          |
| **Recommended RAM**                 | 24 GB                                                       |
| **CUDA Devices (Recommended)**      | `RTX 3090`, `RTX 4070`, `RTX 4090`, `A100`, `H100`          |
| **Python Version**                  | Python >= 3.10 (Với MAC thì cần nâng cấp)                   |


## 📥 Hướng dẫn cài đặt

**1. Cài đặt `sudo`**
```bash
sudo apt update && apt install -y
```
**2. Cài đặt các dependencies khác**
```bash
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip
```
**3. Cài đặt Node.js and npm**  
```bash
curl -sSL https://raw.githubusercontent.com/zunxbt/installation/main/node.sh | bash
```
**4. Cài đặt nvidia-msi DRIVER (OPTIONAL)**
**Chọn 1 trong các lệnh sau tùy theo phiên bản GPU **
```bash
sudo apt install nvidia-utils-525         # version 525.147.05-0ubuntu1, or
```
```bash
sudo apt install nvidia-utils-525-server  # version 525.147.05-0ubuntu1
```
```bash
sudo apt install nvidia-utils-470         # version 470.256.02-0ubuntu0.24.04.1
```
```bash
sudo apt install nvidia-utils-470-server  # version 470.256.02-0ubuntu0.24.04.1
```
```bash
sudo apt install nvidia-utils-535         # version 535.183.01-0ubuntu0.24.04.1
```
```bash
sudo apt install nvidia-utils-535-server  # version 535.230.02-0ubuntu0.24.04.3
```
```bash
sudo apt install nvidia-utils-550         # version 550.120-0ubuntu0.24.04.1
```
```bash
sudo apt install nvidia-utils-565-server  # version 565.57.01-0ubuntu0.24.04.3
```
```bash
sudo apt install nvidia-utils-570-server  # version 570.86.15-0ubuntu0.24.04.4
```
```bash
sudo apt install nvidia-utils-550-server  # version 550.144.03-0ubuntu0.24.04.1
```
**Sau khi cài xong driver nvidia thì check lại như sau:**
```bash
nvidia-smi
```
Kết quả hiển thị là thành công nếu gồm các thông tin: 
Driver Version: 5xx.xx
CUDA Version: 12.x

**5. Clone git**
```bash
cd $HOME && rm -rf gensyn-testnet && git clone https://github.com/zunxbt/gensyn-testnet.git && chmod +x gensyn-testnet/gensyn.sh && ./gensyn-testnet/gensyn.sh
cd rl-swarm
```
**6. Tạo Screen session**
```bash
screen -S gensyn
```
**7. Kích hoạt venv**
```bash
python3 -m venv .venv
source .venv/bin/activate
```
**8. Cài PyTorch trong venv với CUDA support (OPTIONAL)**
```bash
pip install --upgrade pip
pip uninstall torch -y
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

**9. Cấu hình gpu_memory_utilization (OPTIONAL)**
- Mục đích: Một số máy cấu hình thấp yêu cầu VRAM, RAM nên xảy ra tình trạng lỗi: ValueError: No available memory for the cache blocks => cần điều chỉnh cấu hình của gpu_memory_utilization 
- Từ thư mục hệ thống ~/rl-swarm$, nhấn lệnh sau:
```bash
nano hivemind_exp/configs/gpu/grpo-qwen-2.5-0.5b-deepseek-r1.yaml
```
- Tìm đến dòng gpu_memory_utilization, chỉnh từ 0.2 thành 0.4 hoặc 0.5
- Sau đó ấn Ctr+X, nhấn Y, và nhấn Enter

**10. Chạy node**
```bash
./run_rl_swarm.sh
```
- Hệ thống sẽ yêu cầu trả lời một vài câu hỏi => trả lời để tiếp tục
- ```Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]``` : Viết `N`
- Khi xuất hiện thông tin như hình bên dưới nghĩa là đã chạy node thành công

![Screenshot 2025-04-01 061641](https://github.com/user-attachments/assets/b5ed9645-16a2-4911-8a73-97e21fdde274)

 ## 🔄️ Back up `swarm.pem`
- Mục đích: Sau khi Node Gensyn chạy, thì việc lưu file swarm.pem để backup là rất quan trọng. Nếu không, khi khởi động lại node thì sẽ mất hết toàn bộ dữ liệu đã chạy.

- 1. Mở `Windows Powershell`
- Gõ lệnh dưới đây:
```
copy "\\wsl$\Ubuntu\home\WSL-USERNAME\rl-swarm\swarm.pem" "PC-PATH"
```
Trong đó: WSL-USERNAME: USERNAME trong WSL; PC-PATH: Nơi muốn lưu file backup
- Trường hợp của mình thì lệnh sẽ có dạng thế này
```
copy "\\wsl$\Ubuntu\home\one\rl-swarm\swarm.pem" "D:\Backup"
```
 ## 🔄️ Recover `swarm.pem`
Mục đích: Tiếp tục chạy node Peer ID cũ (Gồm toàn bộ contribution trước đây), hoặc sửa lỗi Daemon failed to start in 15.0 seconds

1. Xóa file swarm.pem hiện tại (nếu có)
- Gõ lệnh dưới đây
```
cd rl-swarm
rm ~/rl-swarm/swarm.pem
```
2. Copy file backup vào folder rl-swarm
- Gõ lệnh dưới đây
```
cp /mnt/PC-PATH/swarm.pem ~/rl-swarm/swarm.pem
```
Trong đó: PC-PATH là nơi đã lưu file backup phía trên
- Trường hợp của mình thì lệnh sẽ có dạng thế này
```
cp /mnt/d/backup/swarm.pem ~/rl-swarm/swarm.pem
```

## 🟢 Trạng thái node

### 1. Check Logs
Mục đích: Check trạng thái node chạy
Sử dụng lệnh sau
```
screen -r gensyn
```
Dùng lệnh Ctr+A+D để detach màn đang chạy
### 2. Check Wins
- Vào link : https://gensyn-node.vercel.app/
- Nhập mã Peer-ID 

### 3. Check và rewards
- Truy cập tele bot: @gensynImpek_bot
- Nhập mã Peer-ID, nodename, evm address
> [!Chú ý]
> Nếu nhìn thấy `0x0000000000000000000000000000000000000000` in `Connected EOA Address` section, Có nghĩa là node đang chưa đc track. Thì quay thực hiện 1. Recover node phía trên; 2. Nhập đúng email cũ.

## ⚠️ Troubleshooting

### 🔴 Lỗi Daemon failed to start in 15.0 seconds
Xử lý:
```
nano $(python3 -c "import hivemind.p2p.p2p_daemon as m; print(m.__file__)")
```
- Tìm đến dòng `startup_timeout: float = 15,` , Sửa 15 thành 120.
- Lưu thay đổi `Ctrl` + `X` nhấn `Y` and rồi nhấn `Enter`
- Sau đó chạy lại node bằng lệnh:
```bash
./run_rl_swarm.sh
```
### 🔴 Lỗi AttributeError: 'NoneType' object has no attribute 'split'
Xử lý
Truy cập file lỗi:
```
cd rl-swarm
nano hivemind_exp/gsm8k/stage2_rewards.py
```
Tại đây tìm đến dòng **def extract_xml_ids**
1. Xóa đoạn code hiện tại.
2. Copy đoạn code bên dưới
```
def extract_xml_ids(text: str) -> list[str]:
    if not text or "<student>" not in text:
        return []

    ids = []
    ids_raw = text.split("<student>")[1:]
    for id in ids_raw:
        ids.append(id.split("</student>")[0].strip())
    return ids
```
3. Nhấn Ctr+O -> Enter
4. Ctrl+x
5. Restart node

### 🔴 Lỗi KeyError: 'question'
Xử lý: 
Cách 1: Cập nhật rl-swarm: 
```
cd rl-swarm
git pull
```
Restart lại
Cách 2: Sửa file lỗi:
1. Truy cập file lỗi - stage_utils:
```
cd rl-swarm
nano hivemind_exp/gsm8k/stage_utils.py
```
2. Tìm đến dòng lỗi: 
Nhấn Ctr+W để hiện thị công cụ tìm kiếm, sau đó gõ: **{"role": "system", "content": output["question"]},**
3. Xóa đoạn lỗi đó và thay bằng: **{"role": "system", "content": output.get("question", output.get("prompt", ""))},**
4. Ctr+O -> Enter
5. Ctr+X
6. Restart
