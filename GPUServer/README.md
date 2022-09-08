# User guide

## Manual Path
Manual (server): `/Data/config/group-resource/GPUServer/README.md`

Manual (online): [https://github.com/gdmnl/group-doc/tree/main/GPUServer](https://github.com/gdmnl/group-doc/tree/main/GPUServer)

*Chinese version at [README_CN.md](README_CN.md) / 中文版本见 [README_CN.md](README_CN.md)*

## How to start
The login information should be like this:
```
ssh ubuntu@<ip> -p <port>
    password: <password>
    IP: <ip>
    port: <port>
    mapped username: <username>
```

Use command `ssh ubuntu@<ip> -p <port>` to login (you may need NTU VPN).

Enter initial password, you will be asked to change it the first time logging in. Please set a strong password.

Now you can use as user `ubuntu` (usually the "mapped username: `<username>`" is not required).

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
### Network
* Use `ssh-copy-id "-p <port> ubuntu@<ip>"` to add public key
* Connect using **VSCode**:
  1. Install extension such as [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)
  2. Follow the guide or manually add below information to `~/.ssh/config`:
    ```
    Host [your name]
        HostName <ip>
        User ubuntu
        Port <port>
    ```
* Connect using **Jupyter Notebook / Jupyter Lab**:
  1. Add port forwarding in SSH connection: `ssh ubuntu@<ip> -p <port> -L 8888:<ip>:<port+66>`
  2. On the server, start Jupyter Notebook / Jupyter Lab: `jupyter notebook --no-browser`
    * If the default port is not `localhost:8888`, manually identify by: `jupyter notebook --no-browser --port 8888 --ip 0.0.0.0`
  3. On your local machine, open browser and go to [http://localhost:8888](http://localhost:8888)
* Port mapping as below. Other ports are disabled, contact admin if you need to access them (e.g. GUI, Remote Desktop)
    | Port in *container* | Port in *host* |
    | --- | --- |
    | 22 | `<port>` |
    | 8888 | `<port+66>` |
* Outflow connection in *container* uses default LXD NAT

### Machine
* Each machine is a container host by LXD 4 with ZFS backend
* Default user in *container* is `ubuntu` which has root
* User in *host* is mapped with `<username>`, contact admin if you need to access it
* Container nesting (using Docker inside container) is disabled by default, contact admin if you need
* You have full access inside the container. Please be responsible for performing actions. If the container is broken, contact admin to restore it
* Please routinely backup your important data (whether in *container* or *host*)

### Directory
* When you download files and datasets that can be shared, a good practice is to store them in `/Resource`
* When you need to install large softwares, a good practice is to first find them in `/Data`. If you do not find the software, feel free to ask admin to add a mirror of them (or add them by yourself if you are a *host* user)
* Files that are important or require efficient IO can be stored in `/home0/<username>` (see below for details)

Below directories in *host* are available inside *container*:
  * Share data folder (software packages, etc): `/Data` (read only)
  * Share resource folder (dataset, models, etc): `/Resource` (read and write)
  * User home in host system: `/home0/<username>` (deploy on SSD)

Below are special directories in *host*:
  * Share data folder (mapped as `/Data` in *container*): `/data/data` (read and write)
  * Share resource folder (mapped as `/Resource` in *container*): `/fs/resource` (read and write)
  * Host user cannot directly access container file system. To transfer files with container, use home directory `/home/<username>` (mapped as `/home0/<username>` in *container*)

### NVIDIA
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
* User inside container can only view local process by `nvidia-smi`. TODO: Global GPU usage can be viewed by `nvgpu`

### Python
* Miniconda 4.12.0 with Python 3.9 already installed
* Reinstall Conda (common Conda versions are stored in `/Data/conda/`):
    ```sh
    cp /Data/conda/Miniconda3-<ver>-Linux-x86_64.sh ~
    bash Miniconda3-<ver>-Linux-x86_64.sh
    ```
* Configuration in `~/.condarc`: `pkgs_dirs` is set to `/Resource/conda/pkgs` to use shared cache
  * In *host*, execute `conda config --set pkgs_dirs /fs/resource/conda/pkgs` to do the same
