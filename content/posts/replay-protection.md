---
title: "On the Lack of Replay Protection"
date: 2026-07-06
submitted: true
category: [bitcoin]
tags: [softfork,rdts]
---


## What is happening?

Around August 8th, 2026 at block height 961,632, BIP110: Reduced Data Temporary Softfork (RDTS) nodes are going to start enforcing mandatory signaling for their unpopular softfork.
As RDTS is supported by less than one percent of the hashrate, Bitcoin miners will likely find dozens of blocks before an RDTS miner finds the first signaling block.
RDTS nodes will reject the first non-signaling block which will cause a blockchain fork as they relegate themselves to a stunted chaintip extending about one block per day.

{{< figure src="/images/orangesurf-sim.png" class="center" caption="Simulation of a fork scenario with [OrangeSurf’s Fork Simulator](https://bip110.orange.surf/simulator.html)" >}}

Meanwhile, the Bitcoin network will hum on with blocks expected just seconds slower than every ten minutes.
While Bitcoin nodes would accept the chaintip with RDTS rules, the chain started by the first non-signaling block will have pulled ahead dozens of blocks by the time RDTS finds its first block.

## Hope for an airdrop

While there is broad rejection of an intolerant minority trying to impose new rules on the Bitcoin network, some are hoping for the split to settle into a forkcoin being spun out of Bitcoin.
So far, RDTS support has been so low that there might not be a market for selling such an airdrop, but beyond that, any hope to sell would require being able to independently move coins on the two chaintips.

## Replay Attacks

While RDTS would introduce several new consensus rules upon activation, during mandatory signaling only blocks are restricted compared to Bitcoin.
Transactions sent to either Bitcoin or RDTS will be acceptable to the other chaintip—and we have seen in previous forks that transactions get relayed between node populations in such splits—unless at least one of the inputs of the transaction has been spent on the other chaintip previously.
In previous splits (e.g., Bitcoin Cash forking off), Bitcoin services would temporarily suspend service to allow the dust to settle, but this is not expected this time due to RDTS having vanishing support.
Either way, users that are interested in preserving their coins on both chaintips should be aware that any transaction made on either chaintip would likely result in funds on both chaintips being moved unless they take special precautions.

## Splitting coins

RDTS nodes are likely configured not to relay transactions with inscriptions or large OP_RETURNs, so there has been some speculation that using one or the other could allow a transaction to only be included in the Bitcoin chaintip as RDTS nodes would likely not include it in a block.
However, the RDTS nodes do not enforce those rules at consensus level during the mandatory signaling.
If some hashrate were signaling readiness for RDTS without enforcing the usual mempool policies of BIP110 nodes, such blocks may include any high-feerate consensus-valid transactions.
A different approach to create the first split coins would be to leverage the expected diverging blockspace production.
As Bitcoin blocks will be expected to have almost a normal cadence, but RDTS blocks are expected about once per day, it would be expected that the feerates in RDTS blocks should be substantially higher.
A user could therefore attempt to pay themselves with a low feerate transaction on the Bitcoin chaintip, and once that transaction has several confirmations, spend the same UTXO on the RDTS chaintip with a high feerate transaction to a different output.

{{< figure src="/images/conflicting-txs.png" class="center" caption="Bob received a transaction output *tx_B:0*. Bob creates a low-feerate transaction *tx_C* that sends his funds to himself. Once *tx_C* is confirmed, he sends a high-feerate transaction *tx_D* that spends the same *tx_B:0* and sends them to a different address. Only one of *tx_C* and *tx_D* can be confirmed in either chaintip as they both spend the same UTXO. Once both transactions are confirmed in their respective chaintip, Bob has successfully split his coin." >}}

RDTS miners could still undermine such a splitting attempt (against their economic interest) by including the low-feerate transaction already confirmed in the Bitcoin chaintip instead of selecting the juicy high-feerate transaction offered on the RDTS chaintip.
A split would only be reliable once the conflicting transactions have multiple confirmations on the respective chaintips.
Once a user has access to a UTXO that is unique to one chaintip, the replay-protection can be easily propagated. Spending a replay-protected UTXO will extend the replay-protection to all outputs of that transaction.

Beyond manually split coins, any outputs from Coinbase transactions will be inherently unique to that chaintip.
Once they reach maturity at 100 confirmations, they could be used to propagate replay-protection in the same manner.

## “Firing the malicious miners”

Rumours have been floating around that RDTS proponents are already sketching a hard fork to reduce difficulty or change the proof-of-work algorithm as a fallback.
While the messy split and meager support make it doubtful that there will be much of a market for the airdrop, doubling down with a hard fork would firmly establish the minority-hashrate chaintip as a new forkcoin and make it much more likely to be listed on exchanges.
In either case, transactions would likely remain replayable between Bitcoin and the RDTS chain(-tip), so users vying to retain independent control of their coins on both chains should be sure to separate their UTXOs in some manner.
