---
title: "A look at an early LN testnet topology"
date: 2017-12-29
submitted: true
category: [lightning-network]
tags: [topology]
---

*This article originally [appeared](https://murchandamus.medium.com/a-look-at-an-early-ln-testnet-topology-a874a6ff41d9) on my medium site on 2017-12-29.*

Let's take a look together.

{{< figure src="/images/lopp-ln-testnet.png" class="center" caption="A commenter on my Fyookball response: 'This [[tweeted image](https://twitter.com/lopp/status/932726696364650498)] shows a lot of centralization around hubs'" >}}

We see about ten "supernodes" that stick out by having a lot of channels. We also see that there are some smaller nodes that form channels exclusively with these supernodes. I assume this is what you're referring to, when you decry "centralization around hubs".

However, I'd like to point out a few more things in this graph.  
If we look at the cluster in the top left for example, we see that every node in that cluster is connected to the supernode, but there are also numerous channels between the other nodes around it. The supernode is connecting to the bigger cluster with a payment channel directly to another supernode as well as through a small bridge node.

If we look at the area around the two yellow highlighted supernodes, we see a large number of smaller nodes that form connections to multiple supernodes as well as other nodes.

{{< figure src="/images/fyookball-decentralized.png" caption="The expected topology of LN according to Fyookball" class="center" >}}

Comparing this to the expected topology from Fyookball’s article, you’ll note that the "Decentralized (with centralized hubs)" topology is distinctly different from what we see in the LN graph. In the suggested "Hub and Spoke" graph, only hubs connect hubs, while nodes exclusively connect to hubs. Obviously, the "Distributed" graph is a showcase, but it's clear to see that the actually exhibited topology is at least a mixture between the "Decentralized" and "Distributed" topologies.

Let's take another look, today:

{{< figure src="/images/testnet-graph.png" class="center" caption="The LN testnet graph on 2017–12–28 via stevenroose.github.io/lightninggraph/" >}}

This marvellous jumble we see, makes it clear that we're not seeing a hub and spoke topology.–
If anything, this clutter illustrates how easy it to bypass the “supernodes”.
