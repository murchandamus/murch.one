---
title: "Responding to Jonald Fyookball's article on \"Lightning Network's Infeasibility\""
date: 2017-06-27
submitted: true
---
*This article was originally [published](https://murchandamus.medium.com/i-have-just-read-jonald-fyookballs-article-https-medium-com-jonaldfyookball-mathematical-fd112d13737a) on my medium page on 2017-06-27.*

I have just read [Jonald Fyookball's article](https://medium.com/@jonaldfyookball/mathematical-proof-that-the-lightning-network-cannot-be-a-decentralized-bitcoin-scaling-solution-1b8147650800) which claims to provide mathematical proof that the Lightning Network will fail to provide a 'Decentralized Bitcoin Scaling Solution'.

It starts with a short description of single hop and multihop payments. After that it asserts that it will be infeasible to scale such a network topology to a any significant size.

Before exploring the details, the author already knows what the topology of the Lightning Network will look like (image from the article):

{{< figure src="/images/fyookball-decentralized.png" alt="Comparison of centralized, decentralized and distributed networks from Fyookball's article" class="center" >}}

Let's ignore for a moment the obvious grievance of evidence being circularly derived from preconceived conclusions instead of observations and evidence.

The author correctly recognizes that using LN for a single payment would be pointless as a direct on-chain payment would be more efficient. Next, they present a model of what they think potential routes would look like from the viewpoint of a user (image from the article):

{{< figure src="/images/fyookball-tree.png" alt="Showing a tree with a single path highlighted" class="center" >}}

The author muses that "probably only one of these channels will reach the intended recipient at any given time". This crucial assumption is not further corroborated, and doesn't make any sense. Simply, the presented tree model is not an accurate representation:  
When a LN participant searches for a route, they're obviously only interested in directed payment capacity. This aspect is correctly represented in the tree. However, the nodes along the route are interconnected. While this could even allow cycles to occur, cycles are not possible in the route construction, as it would allow participants involved multiple times to cut out the cycle when learning the secret for the first time. The resulting graph is what we call a DAG or directed acyclic graph:

{{< figure src="/images/wikipedia-dag.png" alt="Image showing a Directed acyclic graph" caption="Directed acyclic graph [[image from wikipedia](https://en.wikipedia.org/wiki/Directed_acyclic_graph)]" class="center" >}}

The interesting takeaway is that the **assumption of only one route is far-fetched**: once a connection is found, any part of the route can be exchanged for a parallel path, allowing for a multitude of possible combinations.

### Misrepresentation of payment forwarding as 'Lending'

The multihop payments in LN are constructed such that forwarding a contract to the next hop is equivalent to committing to the contract. Once the recipient receives the contract through the route, they can use the enclosed secret to immediately pull in the payment from the last forwarder. To get paid, the recipient inextricably trades the secret to the last forwarder, who in turn then can trigger the payment to themselves. Forwarders do not lend money. They trade balance in one payment channel for balance in another. As each hop has to advance the payment before receiving it, they are intrinsically motivated to execute the next hop as soon as possible. This allows to surmise that the recipient will be paid immediately, and in all likelihood all of the hops will be settled within milliseconds after a route has been established.  
Once both hops a forwarder is involved in are resolved, all balances are settled and free to be used in other payments. It is therefore inexplicable what Jonald is getting at, when they suggest that a high amount of routing activity would reduce the availability of a user's channel.

### Assumptions about Routing

The author next collects a bunch of assertions from which they conclude that the network would be deficient.

1. **Random searching of the graph has a low success rate**  
This point is likely related to the deficient route model, however, it is trivial to do better than randomly searching the graph. An early approach suggested by [Rusty Russel](https://medium.com/@rusty_lightning/lightning-routing-rough-background-dbac930abbad) for example prescribes each peer learning their local topology. If and when the network grows too large for this, it could be overarched with a landmark approach. The suggestion of randomly generating and trying routes is laughably inefficient and thus can't be taken serious as a limiting factor for the network.
2. **Channels are exclusively used in one direction**  
Firstly, by channels being part of various routing attempts in different directions, it is not obvious that channels will not rebalance themselves more frequently, and even if it were true, doubling the channels would not be a solution. Besides, it could for example be feasible to combine onchain payments with a "Lightning Network refund" for more frequent rebalancing of payment channels, in fact, this could relieve users of creating change outputs.
3. **Routing for others unbalances channels and decreases usable channels.**  
Again, since channels are bidirectional and we're not in a tree graph but an interconnected graph, it may actually be possible that e.g. A→B→C→D and A→C→B→D both exist, thus even two subsequent payments from A to D may rebalance the channel between B and C.
4. **Wealth disparity limits number of routes that can forward payments.**  
Obviously channels can only forward to the limit of their own capacity. In conclusion a route can only transfer the capacity of the weakest link. This is in particular why Lightning Network has been described as a **scalability solution for micropayments and low-value payments**. As low-value payments are least competitive on-chain but more frequent, this is in fact a great proposition. Larger payments are more economical on-chain anyway as the fees will represent a smaller relative portion of the transferred value. It seems obvious, that Lightning Network payment requests would soon be extended to allow payments to be split over multiple routes to mitigate the capacity limitations.
5. **"There always exists a risk of a routing channel becoming unresponsive (either intentionally or unintentionally). This risk also grows exponentially with an increasing number of hops."**  
As there are many routes from each node to each other node, the network is self-mending. The channel partner of the offline party can choose to terminate the channel and after conclusion thereof put the funds into another channel that is more reliable. If a peer goes offline while participating in a multihop payment, either the payment fails, or the offline participant will only collect their payment with a delay. Participants with low uptimes will likely be peered less with and have smaller channel capacities. They will also likely not be heavy participants in the payment network anyway.

As the model on which Fyookball is basing their calculations is already far-fetched, I will not bother addressing the math.

