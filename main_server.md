# Main_Server
## SSH连接
服务器使用私钥登录，所以在ssh连接时需配置私钥，以下教程将服务器ip地址设为`IP_S`，端口号设为`Port_S`，用户名为`User_Name`，实际使用时进行替换即可。
### 使用MobaXterm（推荐）
在Session中设置Advanced SSH settings中的Use private key即可。
MobaXterm支持ssh和SFTP文件传输。
若出现
```
Server refused our key
No supported authentication methods available (server sent: publickey)
```
说明ssh版本过旧，需升级MobaXterm

### 使用命令行
配置客户端电脑用户的主目录下的`.ssh`文件夹，修改（没有就新建）`config`文件，追加以下内容
```
Host host_name_1
    HostName IP_S
    User User_Name
    Port Port_S
    IdentityFile /path/to/file_1
Host host_name_2
    HostName IP_S
    User User_Name
    Port Port_S
    IdentityFile /path/to/file_2
```
配置的数量按需选择，只配置一个也可以。
配置好之后就可以使用ssh连接
```
ssh -t host_name_1
```
#### 传输数据
在**客户端**使用`scp`命令传输数据，同样配置ssh的config，二者是共享的。
配置后就可以传输文件
```
scp /path/local_file host_name_1:/path/server_dir
```

## 密码
登录到服务器后，建议修改自己的密码（它登录时不需要，仅在特定场合需要）
使用这个指令即可
```
passwd
```


---
## Conda 环境
若账号未预装Anaconda, 可以执行以下命令安装
```
cp /srv/data/Anaconda3-2025.06-1-Linux-x86_64.sh ~/
./Anaconda3-2025.06-1-Linux-x86_64.sh
source .bashrc
conda -V
# conda 25.5.1
```

安装完成后，可以将自己目录下的安装包删除
```
rm ~/Anaconda3-2025.06-1-Linux-x86_64.sh
```

---

## 数据使用
服务器共享数据的目录为`/srv/data`与`/srv/share_data`，存放所有人的共享数据，**注意不要修改或删除**。若想要修改，建议复制文件到自己目录下，再进行修改。
使用说明:

`/srv/data/datasets/` 为数据集文件夹；

`/srv/data/envs_share/` 为共享环境文件夹，仅供激活使用，但不要在其中安装或删除包，如需修改，将其cp到自己目录下再修改；

`/srv/data/model_checkpoints/` 为共享模型的权重文件夹。

可以自行创建文件夹，但请注意文件结构良好。

**新加共享文件夹**为`/srv/share_data`，与`/srv/data`配置相同。
自己用户目录以及`/srv/data`共享文件夹，与`/srv/share_data`共享文件夹为不同硬盘，相互传输速度可能会降低，但也不必特别注意。

---

## 数据下载
### 网页数据
下载到当前目录
```
curl -O https://example.com/file.tar.gz
```

### 百度网盘
**推荐方式：下载百度网盘数据，需要先将文件保存到网盘中（联系minglang，认证数据目前为该用户），然后使用`bypy`命令下载。**
百度网盘数据下载依托于bypy，一个python环境，目前存放于`/srv/data/envs_share/download`, 以及bypy认证数据，存放于`/srv/data/envs_share/.bypy`
#### 设置
将认证数据复制到自己用户目录
```
cp -r /srv/data/envs_share/.bypy/ ~/
```
#### 下载
1.激活下载环境
```
conda activate /srv/data/envs_share/download/
```

2.查询网盘存储文件
```
bypy list
# 示例输出
# /apps/bypy ($t $f $s $m $d):
# D DOTA-v2.0 0 2025-06-23, 12:35:28
# D DOTA1.0 0 2025-06-23, 12:52:44
# D DOTA1.5 0 2025-06-23, 12:38:21
# D Million-AID 0 2025-06-21, 03:55:56
```
3.然后下载指定目录，例如若要下载其中的`Million-AID`文件夹到本地路径`path/your_path`,该路径要写**相对路径**
```
bypy --downloader aria2 download Million-AID path/your_path
```
查询和下载命令都可以使用子目录，例如
```
bypy list Million-AID
# 示例输出
# /apps/bypy/Million-AID ($t $f $s $m $d):
# D test 0 2025-06-21, 11:55:56
# D train 0 2025-06-21, 11:55:56
bypy --downloader aria2 download Million-AID/test path/your_path
```

### HuggingFace下载
huggingface环境同样存储于python环境`/srv/data/envs_share/download`


---
#### 1.设置huggingface镜像
打开bash文件
```
nano ~/.bashrc
```
在文件末尾追加：
```
export HF_ENDPOINT=https://hf-mirror.com
```
按Crtl+X，再按Y确认，再按回车确认保存并退出
最后让修改生效
```
source ~/.bashrc
```

#### 2.激活下载环境
```
conda activate /srv/data/envs_share/download/
```
（可选）按需登录，某些数据集可能需要登录认证下载
```
hf auth login
```

#### 3.下载模型权重
例如下载`THUDM/GLM-4.1V-9B-Thinking`模型到本地文件夹`/path/your_path`
```
hf download THUDM/GLM-4.1V-9B-Thinking --local-dir /path/your_path
```
可以使用`--include`下载指定文件，如下载以.pth结尾的文件
```
hf download THUDM/GLM-4.1V-9B-Thinking --include "*.pth" --local-dir /path/your_path
```
或下载某个文件夹下文件
```
hf download THUDM/GLM-4.1V-9B-Thinking --include "des_folder/*" --local-dir /path/your_path
```

若**下载数据集**，则需要在命令后加入`--repo-type dataset`指令
```
hf download THUDM/GLM-4.1V-9B-Thinking --local-dir /path/your_path --repo-type dataset
```
