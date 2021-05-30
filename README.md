## Livestream-NFT-Minting

To set up a development and test environment, follow this [project deliverable](https://github.com/videoDAC/Livestream-NFT-Minting/blob/main/ubuntu-build-environment.md).

To onwardly fund this project, contribute to this [Gitcoin Grant](https://gitcoin.co/grants/2604/livestream-nft-minting-videodac).

The objective of the project is to enable NFTs to be minted in real-time, from livestream video.

## Concept

Enable creators of livestreaming content to automatically mint NFTs of video content they are streaming.

## Summary

The objective of the project is to enable NFTs to be minted in real-time, from livestream video being processed by Livepeer's "Broadcaster" functionality.

The project aims to build on top of Livepeer, use a Layer 2 on Ethereum for minting, and use Swarm or IPFS/Filecoin for storage.

## Proposed Implementation

### Context

[Livepeer's "Broadcaster"](https://github.com/videoDAC/livepeer-broadcaster/blob/master/README.md) is a simple livestreaming server.

It receives livestream video as `rtmp`, and serves livestream video as `hls`:

![image](https://user-images.githubusercontent.com/2212651/112746920-d33e2680-8fcf-11eb-924b-e4bb648f8c77.png)

When converting `rtmp` into `hls`, the Broadcaster creates a sequence of "segments" of video content.

Each `.ts` "segment" is around 2 seconds long, and exists for around 10-12 seconds before being gc'd:

![image](https://user-images.githubusercontent.com/2212651/119944765-4d7c2e80-bfb2-11eb-8010-23da6e72ff47.png)

### Approach

This project aims to add functionality to Livepeer's "Broadcaster" to allow these segments of video to be:

a) stored to a decentralised storage network (IPFS/Filecoin or Swarm)

b) minted as NFTs on a Layer 2.

This would enable automated minting of livestream videos as NFTs when livestreaming using a Livepeer "Broadcaster" with this feature enabled.

The good news is that Livepeer "Broadcaster" already has a signing key built in, to be able to connect to Ethereum.

# What is Livepeer?

Livepeer ([livepeer.org](https://livepeer.org)) is a protocol, a live mainnet, a staking token, an open-source software suite, and the video layer of web3 stack, on Ethereum.

The delegated-Proof-of-Stake (dPoS) protocol, launched in May 2018, provides inflation-funding mechanisms to reward for continuous participation in the network.

The mainnet is a production transcoding network, where nodes receive source video, and transcode live video ("resize it" / "squash it") into "more accessible" formats, in sub-real-time, in exchange for streaming fees in ETH.

The staking token is [Livepeer Token (LPT)](https://etherscan.io/token/0x58b6a8a3302369daec383334672404ee733ab239), which can be used to curate nodes on the live mainnet, and receive a share of their fees and rewards. This can be done by connecting your wallet to [Livepeer's Protocol Explorer](https://explorer.livepeer.org).

The open-source software suite is available on [Livepeer's Github](https://github.com/livepeer) page, containing stuff for people who like Solidity, golang, javascript, typescript and C.
