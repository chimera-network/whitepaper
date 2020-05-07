# Chimera Network Whitepaper

## Goals
Chimera is a new public blockchain that features:

1. Support second-level finality.
2. It is easy for other public blockchains to do two-way anchoring, using light client

This can unlock many imaginative scenes. For example, stablecoin assets such as USDT on ETH can be trustedlessly moved to Chimera. So people can pay stablecoins through Chimera, and coins will arrive in a few seconds.

## Technology

### Consensus
Chimera will adopt a hybrid consensus: Nakamoto Consensus + Avalanche-like Consensus.

The consensus of blockchain can be divided into two major categories:

1. Nakamoto Consensus. Simple POW hash calculation + longest chain as a fork choice. Extremely robust, relatively safe, easy to understand, too simple to be wrong. One of the less mentioned advantages of this type of protocol is that it is easy to implement light clients.
2. (p)BFT. The validators of multiple nodes collectively endorse the new block. This type of consensus includes dPOS with dozens of nodes and Casper POS with a slightly larger number of nodes. Such protocols may be more centralized and have more security risks, such as long range attack and nothing at stake. This type of protocol has a less-mentioned shortcoming, which is liveness: when one-third of the nodes fail, the entire network may stop running.

We agree with [Nervos Foundation's respect for Nakamoto Consensus] (https://medium.com/nervosnetwork/why-we-love-nakamoto-consensus-5467c035fc55). We think that POW is very suitable as Chimera's "bottom-layer" consensus. For full nodes that do not require fast finality (such as some exchange nodes), they can use only POW consensus blocks as "Single Source of Truth". From the perspective of these nodes, Chimera's consensus is degenerated into the ordinary Nakamoto Consensus.


Chimera's second consensus will adopt a fast finality consensus similar to Avalanche. Strictly speaking, because Chimera does not use DAG topology, there are at most some uncle blocks, so the consensus here is not [Avalanche in the paper] (https://ipfs.io/ipfs/QmUy4jh5mGNZvLkjies1RWM4YuvJh5o2FYopNPVYwrRVGV), but closer to [Snowman Consensus which is more suitable for blockchains] (https://medium.com/avalabs/the-ava-platform-a-tech-primer-7a9b5de57a35). However, for the convenience of writing, the word "Avalanche" is used directly to refer to this type of **probabilistic fast finality consensus**.


How the two sets of consensus work together is a key point. A simple idea is that the network requires that miner must support Avalanche consensus. Each block confirmed in Nakamoto Consensus must first be confirmed by Avalanche. The mint reward of each block is shared among nodes participating in these two consensuses according to a certain percentage. This simple idea will be the starting point for our continuous improvement of the design in the future.


Chimera nodes can choose to join or leave Avalanche dynamically.
To prevent Sybil attack, POW or [Coin Age] (https://github.com/tyler-smith/snowglobe/blob/master/spec/snowglobe.md#sybil-resistance-via-coin-age) can be used to strengthen Avalanche security.

Hybrid consensus requires more design and inspection to ensure correct incentives and security.

Similar to our idea here, the BCH project has some [very early exploration and thinking for POW + Avalanche] (https://github.com/tyler-smith/snowglobe/blob/master/spec/snowglobe.md).

### Programming Model
Chimera will be similar to most recent chains, using WASM as the VM.

In Chimera's VM, you can use merkle proof / POW proof to verify the validity of the "Event" in the transaction on the EVM chain. Similarly, the events generated by transactions on the Chimera chain will also be canculated into the block header hash, so the Chimera light client on ETH can verify the validity of the Chimera Event without executing WASM.

A similar example in this regard is [Near's Rainbow Bridge] (https://github.com/near/rainbow-bridge).

## Unresolved technical issues that require further thinking

### Replay
Who will pay for the long-term two-way relay operating costs and GAS fees?
Should we allow verification of blocks with only dozens of consequent block headers but without evary block headers back to the light clent "start block"? If allowed, how to verify the difficulty of POW at this time?

### Upgrade and Fork
How to upgrade the light client on Chimera when the anchor chain is forked, considering both technique and governance?

### Fault tolerance
If the Light client makes mistakes due to bugs and other reasons, how to revert or fix?

## Financing and project planning
Seeking 100,000-150,000 USD financing. Investors can get 15-25% shares.

It is expected that using these investments and about 6 months, a team of 3-4 people will be built to complete the architecture design and prototype development. If technically successful, part of these investments may be used for marketing. After the prototype is basically built, we will consider the next round of financing and expand the team, or consider selling all this to a company willing to further develop and operate. If it is found technically not feasible, the remaining funds will be returned to the investors in proportion.



### Risk
It is not yet certain that the technical idea is completely feasible. Historically, there are also many precedents for blockchain projects that failed for technical reasons or did not meet expectations, such as Perlin and Plasma. It is expected that it will take 2-3 months to figure out the main technical risks of the project and determine whether it can be done. However, in the first two or three months, only few developers are needed. Even if the project is not considered technically feasible finally, the salaries cost will be most likely less than 30,000 USD.
