# Node Types

#### Founder Nodes - (On-Demand workloads and playtest servers)

- Supply: 50,000
- Unlimited workloads per node (workload NFT not required)
	- Only one basic game workload per game (could run private and basic workloads)
	- Owner needs to configure node to run the workloads (defaults to none)
- Increasing price tier
- Ownership via NFT

#### Workload Nodes - (aka game specific) 
- Unlimited supply
- One basic workload per node
- Flat fee
- Ownership via NFT

# Workloads

## Games - basic

#### Examples
- Last Expedition (private server)
- Town Star
- Spidertanks

#### Description
The purpose and function of the basic game workload is to function as the public servers for the game. The basic game workload nodes are the only ones eligible for rewards as decribed below. The game developers can have more than one workload type for their games. An example would be Last Expeditions private server workload.

#### Basic Function

Each game workload will submit a heartbeat report. This report notifies Gyri which servers are available and ready to be played on. This report is submitted every 120 minutes in blocks. Workload servers follow the specific game channel via the blockchain add block events.

The heartbeat report is used to populate the applicable node queue. There will be two node queues the normal and on-demand. Game slots from game specific workloads are added to the normal queue and founder node workloads are added to the on-demand. This ensures that founder nodes are only utilized during high demand (user spike situations). The queues are First In First Out (FIFO) meaning that when a game slot is added to the queue it's added to the end and when a game slot is selected for fulfillment its selected from the top (first in). Both queues are stored in private data so clients don't have access to them.

Game slots will expire after
- 120 minutes in blocks, if their status is "awaiting"
- 5 minutes in blocks, if their status is "filling"

If a player wants to play a game it sends Gyri a request. When Gyri needs to find a game slot for a player it first looks in the normal queue at the top and checks the entire queue for an applicable game slot. If the normal queue is empty or no game slot is found it goes to the on-demand queue. This happens until a game slot is found. Once found the player is added to the player list and the client receives a game slot client response. The client then connects to the server with the information provided by this response. The server than peforms everything needed to prepare for the game/match. Once the match start requirements are met (usually player count) the server reports match start to Gyri. This updates the status to "playing". Upon match completion the client and server report back with their game slot completed transactions. These are used by Gyri to attest the match and calculate applicable rewards. Once completed that game slot is purged from the private data which removes it from queue.

> **NOTE**  - This is the most cryptographically secure way of handling these requests. An alternative is to completely rely on the game specific workloads but due to the "low cost" the risk of a bad actor reporting "made up matches" is increased.

#### Rewards
Nodes will only be rewarded for the work **they** do. Meaning, that for a node to earn a reward for hosting a match it needs to host the match. This reward will be in the game specific token. The rewards will also be escrowed with the node NFT. To transfer these rewards to a controlling wallet the owner will need to initiate the transfer.

> **NOTE** - There won't be any pool distributions.

## Gyri

#### Types
- Main Channel (asset channel)
- Game Channel

#### Description
Each channel has a max number of spots (ie 100) this limit is controlled by NFTs. These NFTs aren't sold via the Gala Store like game workloads. To acquire one you need to stake $GALA which will be escrowed with the NFT. To "unstake" you simply need to burn the NFT and wait the designated cooldown period. These NFT's are transferrable so they can also be sold/traded on the secondary market instead of unstaking. This allows for someone to exit when necessary but keeps the network secure. (Functions similar to liquid staking but utilizes NFTs instead of a liquid token) [Liquid Staking Report](https://mirror.chorus.one/liquid-staking-report.pdf)

#### Rewards
These nodes will earn transaction fees. For every transaction they endorse (validate) they earn a portion of the transaction fee. 

If the node is a peer node for
- main channel it'll get rewarded in  $GALA
- game channel it'll get rewarded in $GAME TOKEN

## Music / Film

Even though these workloads reside currently under the Gala Games ecosystem they should be detached and handle via their own node model. It can mirror that used by Gala Games but they should outline as necessary the requirements and expecations for running such workloads on their own.

# NFTs - storage of metadata assets

## Old Way - IPFS

Currently IPFS is being used to pin the NFT's metadata assets for storage. The current implementation is KUBO IPFS which is a vanilla IPFS node ran in Docker. If IPFS is still wanted it should be upgraded to IPFS cluster to ensure all game workloads are pinning the same asset pinsets. With that being said, I feel a better approach would be to rip IPFS out of the nodes and pivot to Arweave.

**Pros**
- Currently costs Gala nothing to store their NFT metadata assets
- Can be easily ochestrated with Kubernetes (v3 nodes)
- No limit on storage except based on hardware installed on

**Cons**
- Requires every node to have minimum requires specifically for IPFS operation
	- 2 GB of RAM and 2 CPU cores
- Every node needs to have adequate storage to hold required assets
- Nodes aren't held one to one accountable for assets being stored

## New Way - Arweave

Is to move the current NFT assets over to Arweave. This will be a pretty massive uplift development wise but the end result would be significantly better for the ecosystem. This shifts the NFT asset storage responsibility away from node operators and to Arweave. The cost for permanent storage is also minimal compared to requiring all nodes to operate an IPFS node.

Currently each node requires 2 GB of RAM and 2 CPU cores and 60GB of storage. This costs the average node operator $20/mo to ensure adequate system requirements to run an IPFS node. Extrapolate this out to all running "founder nodes" and that's on average $677,980/mo on IPFS alone.

In comparison if Arweave was used for this permanent storage - it'd cost $91.24 (https://ar-fees.arweave.dev/) and that's assuming 60GB of storage being used. This is super conservative since the current nodes minimum specs is 60GB and not all of it is being used to store NFT assets.

**Pros**
- Shifts responsibility away from node operators so they can focus on game workloads
- Cheap cheap cheap

**Cons**
- The development work to shift all current NFT's over to Arweave