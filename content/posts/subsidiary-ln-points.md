---
title: "Some subsidiary points on Lightning Network"
date: 2017-06-29
submitted: true
category: [lightning-network]
tags: [ln]
---

*This article first [appeared](https://murchandamus.medium.com/some-subsidiary-points-on-lightning-network-cc68bc81d6f1) on my medium page on 2017-06-29.*

I was pondering whether it is a good use of my time to respond a second time to Jonald Fyookball. Since the new article mostly repackages the same misunderstandings, I have decided to only provide some subsidiary information.

## Who performs the Routing in Lightning Network?

When a node in Lightning Network wants to initiate a payment **the sender picks every hop of the route**. To that end, they use "Onion routing" — they construct a packet much like a prank christmas present from your older brother: a multitude of ever smaller boxes, until it is finally revealed that it's just a pair of alpaca socks.

For illustration, let's look at a simple example with two hops. Alice wants to send 1mBTC to Carol, and Alice and Carol each have a payment channel with Bob.

Alice sends an encrypted message to Bob with the following content:

1. A cryptographic contract which commits Alice to signing over 1 mBTC to Bob if he can provide the secret.
2. Designation of Carol as the next hop.
3. A cryptographic contract which commits Bob to signing over 1 mBTC to Carol if she can provide the secret.
4. An encrypted message to be delivered to Carol.

If Bob agrees to the terms of the payment and has sufficient capacity, he may forward the payment request. If he doesn't, he responds with a failure message, which allows Alice to construct an alternative route. If he doesn't react, the payment request times out after a fraction of a second. Let's assume Bob agrees and forwards the payment request. Bob sends a message to Carol with the following content:

1. A cryptographic contract which commits Bob to signing over 1 mBTC to Carol if she can provide the secret.
2. The encryted message to Carol.

Carol decrypts the message and finds the invoice identifier, and the secret. She can now use the secret to execute her cryptographic contract with Bob. She gets paid *immediately* while *inextricably* providing Bob with the secret. Bob can now use the secret to get back his prepayment from Alice by executing the cryptographic contract with Alice.

## Observations

* There is no centralized routing queue. Routing is performed by the sender with knowledge of their local neighborhood.
* All participants in a multihop payment have to be online to agree on the contract for it to come to pass. Thus, the expected occurrence of unresponsive channels is low. If a node along the route dropped from the network, a failure response is returned.
* The recipient is paid immediately. Others are incentivized to settle as quickly as possible. Only the amount locked to a contract is unavailable for transactions, the remaining capacity is free to be used in other payments.
* Unresponsive behavior is easy to punish: Nodes that don't respond to payment requests will not be considered for later payment attempts. Eventually they lose their channel partners for not being useful. The network mends by finding routes around them.
* Every participant in a multihop payment learns only about the previous and next hop in the chain. They also cannot tell if a neighboring hop is sender or receiver of the payment.

## "Hubs" are just well-connected nodes

In discussion around Lightning Network the term "hub" frequently pops up. This term makes little sense, as there is no clear distinction of "hubs" and other nodes. Each node can create as many payment channels as they wish to commit funds to and find useful. Nodes that are well-connected might be perceived as a more useful payment channel partner, but at some point such nodes would inadvertently arrive at the limit of funds they are willing to commit to payment channels. Besides, if there are fees to be earned for routing payments, it may actually be more profitable to extend routes into remotely connected regions of the network. In any case, there is no restriction on other routes emerging around well-connected nodes.
