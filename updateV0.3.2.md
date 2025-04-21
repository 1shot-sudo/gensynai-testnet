<h2 align=center>UPDATE TO VER 0.3.2</h2>
 
 1. Download file upload về máy cá nhân
 - Link: https://github.com/gensyn-ai/rl-swarm/releases/tag/v0.3.2
 - Chạy bằng wsl thì down bản này 
 https://github.com/1shot-sudo/gensynai-testnet/blob/587f4ec0ed15193920c2700ac304b1db8c2dddb3/Screenshot_3.png
 
 2. Giải nén file
 Vào thư mục hệ thống, chạy lệnh giải nén.
 ```
 cd rl-swarm
 tar -xvzf /mnt/c/Users/namao/Downloads/rl-swarm-0.3.2.tar.gz
 ```
 Trong đó: c/Users/namao/Downloads/rl-swarm-0.3.2.tar.gz là đường dẫn bạn giải nén file download về. Đổi tên user <namao> và tên ổ đĩa <C> để phù hợp với đường dẫn của máy bạn.
 3. Chạy lại node
 
 ```
 cd rl-swarm/rl-swarm-0.3.2
 python3 -m venv .venv
 source .venv/bin/activate
 ./run_rl_swarm.sh
 ```
