# User guide for dmal-Dell7920
- [User guide for dmal-Dell7920](#user-guide-for-dmal-dell7920)
  - [Manual Path](#manual-path)
  - [How to start](#how-to-start)
  - [Commonly Used Functions](#commonly-used-functions)
    - [Connection](#connection)
      - [SSH Commands](#ssh-commands)
      - [VSCode Connection](#vscode-connection)
      - [Jupyter Notebook / Jupyter Lab Connection](#jupyter-notebook--jupyter-lab-connection)
    - [Software Configs](#software-configs)
      - [Python](#python)
      - [NVIDIA softwares](#nvidia-softwares)
      - [GCC/G++](#gccg)
  - [Default settings](#default-settings)
  - [Advance instructions](#advance-instructions)
    - [Machine](#machine)
    - [Network](#network)
    - [File System](#file-system)

## Manual Path
Manual (server): `/Data/config/group-resource/GPUServer/README.md`

Manual (online): [https://github.com/gdmnl/group-doc/tree/main/GPUServer](https://github.com/gdmnl/group-doc/tree/main/GPUServer)

## How to start
The login information should be like this:
```
ssh ubuntu@<ip> -p <port>
    password: <password>
    IP: <ip>
    port: <port>
```

Use command `ssh ubuntu@<ip> -p <port>` to login (you may need NTU VPN).

Enter initial password, you will be asked to change it the first time logging in. Please set a strong password. Use `passwd` to change password at any time.

Now you can use as user `ubuntu`. **Container machine is not suitable for IO-heavy tasks!**

**Monthly reboot schedule: every 12th day of the month at 4:30 am (UTC+8).** Please arrange your tasks accordingly.

## Commonly Used Functions
### Connection
#### SSH Commands
* Add public key (login without password) by [ssh-copy-id](https://linuxhint.com/use-ssh-copy-id-command/): `ssh-copy-id "-p <port> ubuntu@<ip>"`
* Transfer files by [scp](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/): `scp -P <port> <local file> ubuntu@<ip>:<server path>`

#### VSCode Connection
1. Install extension such as [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)
2. Follow the guide or manually add below information to local file `~/.ssh/config`:
  ```
  Host [connection name]
      HostName <ip>
      User ubuntu
      Port <port>
  ```

#### Jupyter Notebook / Jupyter Lab Connection
1. Add port forwarding in SSH connection: `ssh ubuntu@<ip> -p <port> -L 8888:<ip>:<port+66>`
2. On the server, start Jupyter Notebook / Jupyter Lab: `jupyter notebook --no-browser`
    * If the default port is not `localhost:8888`, manually identify by: `jupyter notebook --no-browser --port 8888 --ip 0.0.0.0`
3. On your local machine, open browser and go to [http://localhost:8888](http://localhost:8888)

### Software Configs
#### Python
* Miniconda 4.12.0 with Python 3.9 already installed
* Reinstall Conda (common Conda versions are stored in `/Data/conda/`):
    ```sh
    cp /Data/conda/Miniconda3-<ver>-Linux-x86_64.sh ~
    bash Miniconda3-<ver>-Linux-x86_64.sh
    ```
* Configuration in `~/.condarc`: `pkgs_dirs` is set to `/Resource/conda/pkgs` to use shared cache
  * In *host*, execute `conda config --set pkgs_dirs /fs/resource/conda/pkgs` to do the same

#### NVIDIA softwares
* Reinstall CUDA (common CUDA versions are stored in `/Data/cuda/`):
    ```sh
    sudo rm -rf /usr/local/cuda
    sudo cp -r /Data/cuda/cuda-<ver> /usr/local/cuda
    ```
* Reinstall cuDNN (common cuDNN versions are stored in `/Data/cuda/`):
    ```sh
    cd /Data/cuda/cudnn-linux-x86_64-8.3.2.44_cuda11.5-archive/
    sudo cp include/cudnn*.h /usr/local/cuda/include
    sudo cp -P lib/libcudnn* /usr/local/cuda/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
    ```
* User inside container can only view local process by `nvidia-smi`.

#### GCC/G++
* *Host* users only
* Display installed versions: `update-alternatives --display gcc`
* Switch version: `sudo update-alternatives --set gcc <gcc-path>`

## Default settings
The installed machine comes with:
* Full access inside container. Can use freely without affecting others (even able to install and run Docker!)
* Ubuntu 20.04
* CUDA (11.6.2) and cuDNN (8.4.1.50)
* Miniconda (4.12.0) and Python 3.9
* Share folders `/Data` (read only) and `/Resource` (read and write) can be used by all server users
* A mapped user `<username>` in host (by default disabled)

---
## Advance instructions
### Machine
* Each machine is a container host by LXD 4 with ZFS backend
* Default user in *container* is `ubuntu` which has root privilege
* User in *host* is mapped with `<username>`, contact admin if you need to access it
* Container nesting (using Docker inside container) is disabled by default, contact admin if you need
* You have full access inside the container. Please be responsible for your operations. If the container is broken, contact admin to restore it
* Please routinely backup your important data for users both in *container* or *host*

### Network
* Port mapping as below. Other ports are disabled, contact admin if you need to access them (e.g. GUI, Remote Desktop)
  | Port in *container* | Port in *host* |
  | --- | --- |
  | 22 | `<port>` |
  | 8888 | `<port+66>` |
* Outflow connection in *container* uses default LXD NAT

### File System
* When you need to install large softwares, a good practice is to first find them in `/Data`. If you do not find the software, feel free to ask admin to add a mirror of them (or add them by yourself if you are a *host* user)
* When you download files and datasets that can be shared, a good practice is to store them in `/Resource`
* Home directory of user in *host* `/home/<username>` is mapped in *container* at `/home0/<username>`. Typical use: files requiring efficient IO, transfer files between *host* and *container*
* For *host* users, please free SSD space by moving unused files to `/data/documents/<username>` from home directory
* For more information on disk partition and usage, refer to [machine_info.md](machine_info.md#disk-partition)

  | Path in *container* | Path in *host* | Description | Use Case |
  | --- | --- | ---:| ---:|
  | `/Data` (read only) | `/data/data` (read and write) | Shared data folder (software packages, etc) | Copies of large softwares |
  | `/Resource` (read and write) | `/fs/resource` (read and write) | Shared resource folder (dataset, models, etc) | Downloaded files and datasets that can be shared |
  | `/home0/<username>` (SSD, read and write) | `/home/<username>` (read and write) | Home directory of user in *host* | Transfer file between *container* and *host* |
