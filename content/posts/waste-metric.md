---
title: "What is the Waste Metric?"
date: 2022-05-06
submitted: true
tags: [coin-selection]
category: [bitcoin]
---

## Coin Selection

Funds are tracked in discrete portions, so called _Unspent Transaction Outputs_ (see [UTXO  model](https://bitcoin.stackexchange.com/q/49853/5406)). Generally, a wallet's funds are split across multiple to numerous such UTXOs. When a user sends a transaction, their wallet software needs to pick some of their UTXOs to fund the transaction. The process of picking the input set of a transaction is referred to as _Coin Selection_.

The primary goal of Coin Selection is to raise sufficient funds to pay for the recipient outputs and fees of the transaction, but there are secondary goals as well:

- **low immediate fees**: As transactions must bid for the blockspace they occupy, we want      transactions to be reasonably sized to limit their cost.
- **low overall fees**: All UTXOs must be spent eventually to allow us to reassign their value  to new outputs. Sometimes it may be opportune to use additional inputs for a transaction    crafted while there is little blockspace demand to consolidate funds into fewer UTXOs.
- **financial privacy**: As the blockchain is public and will exist forevermore, the details of our transactions are visible to all. This means that we may reveal financial information to our business partners when we pay them, and companies surveilling Bitcoin usage in general may be able to glean additional data from our usage patterns. We want to limit the information we leak about the wallet's history, its UTXO pool, and total funding.

As a means to achieve our secondary goals, it is useful to consider the **composition of the wallet's UTXO pool**. We want funds to be split in useful ways. For example, UTXOs of homogeneous values are less useful than UTXOs of diverse values. We want to have a sufficient count of UTXOs to not reveal all of our funds to business partners and surveillants in each transaction, but few enough that our wallet isn't fragmented into little pieces, where we combine a lot of transaction history and incur high fees with every transaction.

Take for example two wallets that each have 1 ₿ trying to send 0.6 ₿. One has the UTXOs [0.5, 0.5], the other has [0.1, 0.2, 0.3, 0.4]. The first wallet would need to use both its UTXOs and send itself a change output of 0.4 ₿. All of its funds would be in flight, it would have no confirmed UTXOs left to send an independent transaction, and the recipient would learn that the sender has an additional 0.4 ₿. The second wallet might use the 0.2 ₿ and 0.4 ₿, which would leave two other UTXOs untouched in the wallet, and not require creation of a change output.

Generally, the combination space spanned by a diverse wallet is more densely populated than a homogeneous wallet. Split 4×0.25 ₿ a wallet with 1 ₿ can only combine the amounts {0.25, 0.5, 0.75, 1}, while a wallet with [0.1, 0.2, 0.3, 0.4] could combine all of {0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1}.

## Feerate sensitive coin selection

Wanting to pay low fees for the current transaction but also minimizing the overall fees paid over the lifetime of the wallet may conflict. Especially if a wallet is involved in a large volume of payments, simplistic approaches to Coin Selection like always using the largest UTXOs first, will cause the wallet's UTXO pool to increasingly fragment over time. The biggest contributors to transaction weight are the inputs. When then a large amount is paid while there is high blockspace demand, a heavily fragmented wallet might be forced to use numerous inputs which then causes a high transaction fee.
Obviously, we want to keep our transaction weight at high feerates low to minimize the transaction fee. To that end, a wallet operator might create consolidation transactions for the express purpose of defragmenting the wallet.
While these additional transactions allow minimizing costs at high feerates, a similar effect can be achieved by opportunistically using more inputs than necessary when creating transactions at low feerates without requiring users to manually intervene.
As more UTXOs are spent at lower feerates than at high feerates, the average feerate at which UTXOs are spent is lowered which decreases the overall lifetime fee expenditures of the wallet.

## The waste metric

The waste metric provides a heuristic per which a wallet's automated coin selection can achieve feerate sensitive coin selection.

![waste metric formula: waste = weight×(feerate-longtermfeerate)+change+excess][1]
- weight: total weight of the input set
- feerate: the transaction's target feerate
- L: the long-term feerate estimate which the wallet might need to pay to redeem remaining UTXOs
- change: the cost of creating _and spending_ a change output
- excess: the amount by which we exceed our selection target when creating a changeless transaction, mutually exclusive with cost of change

The resulting **waste score** is a measure of the fees for the input set at the current feerate compared to spending the same inputs at the hypothetical long-term feerate. The waste score also accounts for the cost of creating a change versus dropping funds that exceed the selection target to fees in the case of a changeless transaction. When using the waste score to evaluate the input set candidates produced by multiple coin selection attempts, the scoring results in a preference for minimal input sets at high feerates and a preference for larger input sets below the long-term feerate estimate. Lighter modern output types have a lower waste score at high feerates and a higher waste score at low feerates, causing legacy inputs to be preferentially spent at low feerates and modern inputs preferred at high feerates. Change avoidance is always preferred as long as the _excess_ doesn't exceed the _cost of change_.

Unfortunately, the waste metric cannot capture all of our concerns: we do not account for privacy, and just optimizing for the best score would degenerate at the extremes. At high feerates, a single input solution would always win, while the best score at low feerates would be achieved by spending all UTXOs in the wallet for every transaction. We therefore rely on our selection of coin selection algorithms to produce sensible input set candidates and only use the waste metric to select from those candidates, rather than optimizing for the best waste score outcome.

  [1]: https://latex.codecogs.com/svg.image?waste~score=weight(feerate-L)+change+excess
