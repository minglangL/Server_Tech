
# 15服务器
## 使用服务器conda环境

### conda和pip
conda环境中的使用的pip可能不是在当前环境中，而是主目录的pip，这会导致环境串联，在激活环境后**务必检查当前使用的pip**
```cmd=
conda activate my_env
which pip

# 正确的pip
# /home/furh/anaconda3/envs/my_env/bin/pip

# 错误的pip
# /home/furh/.local/bin/pip
```

如果是错误的pip，先检查当前环境中是否安装pip
```cmd=
conda list
```
查看输出是否有pip，若没有则安装pip
```cmd=
conda install pip
```
之后执行以下命令更换pip

```cmd=
export PATH="/home/furh/anaconda3/envs/my_env/bin:$PATH"
```
注意替换其中的环境名。
之后再次使用`which pip`检查，确保使用的是当前环境的pip。
**重要：该命令仅在当前终端的当前环境生效，切换终端或环境务必再次执行。**

### 防止与base环境串联导致包冲突等问题
首先输入
```
python -c "import sys; from pprint import pprint; pprint(sys.path)"
```
![d067ca95-fe29-4775-bc4d-9f2d11e707d7](https://hackmd.io/_uploads/rJ8jgbULgg.png)
一旦如图所示，furh/.local的site-packages在你的环境的site-packages之上，就证明你的环境和base串联了
此时只需要输入
```
export PYTHONNOUSERSITE=1
```
即可解决
再次执行
``` 
python -c "import sys; from pprint import pprint; pprint(sys.path)"
```
![f79249db-abbc-4d25-9cb2-4767a9e0ce85](https://hackmd.io/_uploads/HkLubb8Iel.png)
会发现只剩下你自己环境相关的路径


---
## 下载数据
### HuggingFace下载
使用bench环境中的huggingface-cli下载，例如下载`THUDM/GLM-4.1V-9B-Thinking`模型到本地文件夹`/path/your_path`
```cmd=
conda activate bench
huggingface-cli download THUDM/GLM-4.1V-9B-Thinking --local-dir /path/your_path
```
可以使用`--include`下载指定文件，如下载以.pth结尾的文件
```cmd=
huggingface-cli download THUDM/GLM-4.1V-9B-Thinking --include "*.pth" --local-dir /path/your_path
```
或下载某个文件夹下文件
```cmd=
huggingface-cli download THUDM/GLM-4.1V-9B-Thinking --include "des_folder/*" --local-dir /path/your_path
```

若下载数据集，则需要在命令后加入`--repo-type dataset`指令
```cmd=
conda activate bench
huggingface-cli download THUDM/GLM-4.1V-9B-Thinking --local-dir /path/your_path --repo-type dataset
```



### 百度网盘下载
先将文件保存到网盘中（联系minglang，服务器目前登录该用户，非必要不要变动），然后使用`bypy`命令下载。
先查询网盘存储文件
```cmd=
bypy list
# 示例输出
# /apps/bypy ($t $f $s $m $d):
# D DOTA-v2.0 0 2025-06-23, 12:35:28
# D DOTA1.0 0 2025-06-23, 12:52:44
# D DOTA1.5 0 2025-06-23, 12:38:21
# D Million-AID 0 2025-06-21, 03:55:56
```
然后下载其中的`Million-AID`文件夹到本地路径`/path/your_path`,该路径要写**相对路径**
```cmd=
bypy --downloader aria2 download Million-AID /path/your_path
```



---

## 硬盘
### 虚拟盘制作
to do
### 硬盘挂载
192.168.83.15服务器有一块新加硬盘，挂载在主目录的newdata目录，即
```
/home/furh/newdata
```
若该目录为空，说明硬盘未挂载，一般出现在服务器重启后，执行以下命令重新挂载
```cmd=
sudo mount /dev/vgdata/lvdata01 /home/furh/newdata
```

---

## NAS
### NAS硬件
nas的ip为`192.168.83.17`，若ping不到该地址，说明nas掉线，需要去机房检查nas运行状况。
若nas未开机（指示灯不亮），开机即可。
其他情况待完善。

### NAS挂载
能够ping通nas后，可以挂载，其挂载目录为
```
/home/furh/nas_data
```
挂载命令为
```cmd=
sudo mount 192.168.83.17:/volume1/nas_data /home/furh/nas_data
```
其中`192.168.83.17:/volume1/nas_data`为nas地址，无需更改；`/home/furh/nas_data`为本地地址，按需更改。

若出现`mount.nfs: Stale file handle`错误，重启服务器nfs服务，再次挂载即可，重启命令为
```cmd=
sudo systemctl restart nfs-client.target
```
之后检查nfs服务状态
```cmd=
sudo systemctl status nfs-client.target
```
类似以下输出为正常
```
● nfs-client.target - NFS client services
     Loaded: loaded (/lib/systemd/system/nfs-client.target; enabled; vendor preset: enabled)
     Active: active since Sun 2025-07-06 07:53:01 UTC; 52s ago
```
之后重新挂载即可。

---

## tmux
最简操作流程
```
新建会话tmux new -s my_session
在tmux窗口正常运行代码
Ctrl+b d离开tmux窗口
下次运行，使用tmux attach-session -t my_session重新连接到会话
```

### 常用指令集
新建会话
```
tmux new -s session-name
```

分离会话（离开tmux窗口）
```
tmux detach
```

查看全部会话
```
tmux ls
```

接入会话
```
tmux attach -t session-name
```

杀死会话
```
tmux kill-session -t 0
```

快捷键
```
Ctrl+b d:分离当前会话（离开窗口）
```
