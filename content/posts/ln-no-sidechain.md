---
title: "The Lightning Network is not a sidechain"
date: 2016-03-08
submitted: true
category: [Lightning-Network]
tags: [terminology,sidechain]
---

The Lightning network is not a sidechain.

A sidechain relies on its own blockchain which is coupled to the Bitcoin blockchain via a two-way peg.

On the other hand, the Lightning Network consists of native Bitcoin 2-of-2 multisig transactions.

When two Lightning nodes open a payment channel, they both send funds to one multisig address and each provides the counterparty with a pre-signed exit transaction. If a party wants to resolve the payment channel, they can unilaterally add their own (the second) signature and send the transaction to the Bitcoin network for confirmation.

However, it is more efficient to update the balance of the payment channel repeatedly by exchanging new exit transactions. The updated exit transactions simultaneously invalidate the previous exit transaction. Whenever anyone wants to get out or tries to cheat (by using an invalid exit transaction), the exit transaction is moved to the Bitcoin blockchain for arbitration. Newer exit transactions then may override older ones[^1].

So, while Lightning transactions can happen off-chain, they can be put on-chain anytime.

[^1]: this is a slight simplification of the mechanism
