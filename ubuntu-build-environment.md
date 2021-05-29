# Ubuntu Build Environment

These instructions help you to set up a build environment for Livestream NFT Minting project.

It assumes you are starting from a clean ubuntu server.

If you would like a clean ubuntu server, ask in [videoDAC Discord](https://discord.gg/6GQt6b7MTV).

## Base setup

### Update and Install Packages
```
sudo apt update
sudo apt upgrade -y
sudo apt-get update -y && sudo apt-get -y install build-essential pkg-config autoconf gnutls-dev git curl
```
### Install `golang`
```
wget https://golang.org/dl/go1.16.4.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.16.4.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

nano ~/.profile
```
Paste this text onto the end of the .profile file (`Ctrl-Shift-V` on Linux Terminal):
```
export PATH=$PATH:/usr/local/go/bin
```
Save the file (Ctrl-S), close (Ctrl-X) and continue:
```
go version
```
Should print the version number.

## Install `geth` on Rinkeby

Download the latest `geth` binary:
```
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.10.3-991384a7.tar.gz
tar -xzf geth-linux-amd64-1.10.3-991384a7.tar.gz
rm geth-linux-amd64-1.10.3-991384a7.tar.gz
```
Then, set up `geth` to run as a system service:
```
cd /etc/systemd/system
sudo nano rinkeby-geth.service
```
Paste the following into the file
```
[Unit]
Description=service to start geth with default sync on Rinkeby
After=network.target

[Service]
User=ubuntu
Type=simple
Restart=always
RestartSec=1s
WorkingDirectory=/home/ubuntu/
ExecStart=/home/ubuntu/geth-linux-amd64-1.10.3-991384a7/geth --rinkeby --http --http.addr 0.0.0.0

[Install]
WantedBy=default.target
```
Save the file `Ctrl-S`, close `Ctrl-X` and continue:
```
sudo systemctl enable rinkeby-geth.service
sudo systemctl start rinkeby-geth.service
```
Then to observe:
```
sudo journalctl -fu rinkeby-geth.service
```
Exit observation with `Ctrl-C`.

## Install `ipfs`

Download the latest `ipfs` binary:
```
cd ~
wget https://dist.ipfs.io/go-ipfs/v0.8.0/go-ipfs_v0.8.0_linux-amd64.tar.gz
tar -xvzf go-ipfs_v0.8.0_linux-amd64.tar.gz
rm go-ipfs_v0.8.0_linux-amd64.tar.gz
cd go-ipfs
sudo bash install.sh
ipfs --version
```
Initialise ipfs:
```
ipfs init
```
Then, set up `ipfs daemon` to run as a system service:
```
cd /etc/systemd/system
sudo nano ipfs-daemon.service
```
Paste the following into the file
```
[Unit]
Description=service to start ipfs daemon
After=network.target

[Service]
User=ubuntu
Type=simple
Restart=always
RestartSec=1s
WorkingDirectory=/home/ubuntu/
ExecStart=/usr/local/bin/ipfs daemon

[Install]
WantedBy=default.target
```
Save the file (Ctrl-S), close (Ctrl-X) and continue:
```
sudo systemctl enable /etc/systemd/system/ipfs-daemon.service
sudo systemctl start ipfs-daemon.service
```
Then to observe:
```
sudo journalctl -fu ipfs-daemon.service
```
## Build livepeer

### Clone forked repos

Download `videoDAC` forked repositories for `go-livepeer` and `lpms`:
```
git clone https://github.com/videoDAC/go-livepeer.git
git clone https://github.com/videoDAC/lpms.git
```

### Install ffmpeg for Livepeer

Install ffmpeg (required for building) from `go-livepeer` repo. This may take some time:
```
cd go-livepeer
./install_ffmpeg.sh
rm /home/ubuntu/*.gz /home/ubuntu/*.xz
```

### Build livepeer
```
cd /home/ubuntu/go-livepeer
export PKG_CONFIG_PATH=~/compiled/lib/pkgconfig
export HIGHEST_CHAIN_TAG=rinkeby
make
```

## Run Livepeer

If your Rinkeby node is not finished syncing, run this:
```
/home/ubuntu/go-livepeer/livepeer -broadcaster -network rinkeby -ethUrl https://rinkeby.infura.io/v3/9b7ee8501114410ca67288cc277c65d8
```

If your Rinkeby node has finished syncing, run this:
```
/home/ubuntu/go-livepeer/livepeer -broadcaster -network rinkeby -ethUrl http://localhost:8545
```
