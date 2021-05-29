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
cd ~
git clone https://github.com/videoDAC/go-livepeer.git
git clone https://github.com/videoDAC/lpms.git
```

### Install ffmpeg for Livepeer

Install ffmpeg (required for building) from `go-livepeer` repo. This may take some time:
```
cd go-livepeer
./install_ffmpeg.sh
```
NOTE: if using 20.04, the install_ffmpeg.sh will fail first time, but will succeed second time.

### Build livepeer
```
cd ~/go-livepeer
export PKG_CONFIG_PATH=~/compiled/lib/pkgconfig
export HIGHEST_CHAIN_TAG=rinkeby
make
```

## Run Livepeer

```
/home/ubuntu/go-livepeer/livepeer -broadcaster -network rinkeby -ethUrl https://rinkeby.infura.io/v3/9b7ee8501114410ca67288cc277c65d8
```

# Next steps

To add a `-mint` flag to the command:
```
/home/ubuntu/go-livepeer/livepeer -broadcaster -mint -ipfsUrl http://127.0.0.1:5001/webui -network rinkeby -ethUrl https://rinkeby.infura.io/v3/9b7ee8501114410ca67288cc277c65d8
```

So that if the software is run with the `-mint` flag, then the `livepeer` process:

1. Writes the video segment `.ts` files to IPFS, and retrieves the CID
2. Mints an NFT referencing the IPFS CID.
