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

## Build livepeer

### Clone forked repos

Download repositories for `go-livepeer`:
```
cd ~
git clone https://github.com/livepeer/go-livepeer.git
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
export PKG_CONFIG_PATH=~/compiled/lib/pkgconfig
export HIGHEST_CHAIN_TAG=rinkeby
make
```
This should create a `livepeer` binary file at `/home/ubuntu/go-livepeer/livepeer`.

## Run Livepeer Broadcaster

```
/home/ubuntu/go-livepeer/livepeer -broadcaster
```

When livepeer broadcaster is ready, it will show you `Video Ingest Endpoint rtmp://127.0.0.1:1935`.

## Publish a stream into Livepeer Broadcaster

```
sudo apt install -y ffmpeg

ffmpeg -re -f lavfi -i \
       testsrc=size=500x500:rate=24,format=yuv420p \
       -f lavfi -i sine -c:v libx264 -b:v 1000k \
       -x264-params keyint=72 -c:a aac -f flv \
       rtmp://127.0.0.1:1935/test-stream
```

## Install Textile

```
cd ~
wget https://github.com/textileio/textile/releases/download/v2.6.9/hub_v2.6.9_linux-amd64.tar.gz
tar -xzf hub_v2.6.9_linux-amd64.tar.gz
rm hub_v2.6.9_linux-amd64.tar.gz
cd hub_v2.6.9_linux-amd64
sudo ./install
hub version
hub init
```
Then you wil be required to create a username, and connect your email address.

Once complete, switch into the directory which contains all the video segment files, and initialise textile:
```
cd ~/.lpData/offchain
hub buck init
```
Follow the instructions to initialise a new bucket.

Once done, create a `watch` process, to watch for new files written to this folder:
```
hub buck watch
```
This will begin monitoring the `~/.lpData/offchain` folder for new files, which it will write to textile.
