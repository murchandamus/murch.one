---
title: "PSA: Wrong fee rates on block explorers"
date: 2017-12-12
submitted: true
category: [bitcoin]
tags: [feerates,terminology]
---

*This article [originally appeared](https://murchandamus.medium.com/psa-wrong-fee-rates-on-block-explorers-48390cbfcc74) on my medium page on 2017-12-12.*  
*Update: Blockchair.com, BTC.com and Smartbit.com.au have corrected their fee rates since my article. Thank you!*

We’ve been getting support requests from customers inquiring why their transaction’s fee rate is not matching the parameters they set.

The problem is that popular blockchain explorers don’t get the fee rate right. Let’s look at the following transaction with two P2SH-P2WSH multisig inputs and six outputs: `cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8`

The customer set a fee rate of 160.20 sat/B. Naturally, they were surprised when they saw what blockchain explorers are telling us:

{{< figure src="/images/tradeblock.png" class="center" caption="[Tradeblock.com says the fee rate is 216.81 sat/B.](https://tradeblock.com/bitcoin/tx/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8)" >}}


{{< figure src="/images/smartbit.png" class="center" caption="[smartbit.com.au says the fee rate is 90.50 sat/B](https://www.smartbit.com.au/tx/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8). — Update: smartbit.com.au has fixed their fee rates since this article. Thank you." >}}

It’s pretty clear that the fee is 78,920 satoshis…

## …but what does this transaction “weigh“?

*Stripped size:* **364 bytes**  
This is the size of a transaction when ignoring the witness.  
Without witness each input is 76 bytes long, each of the five P2PKH outputs has 34 bytes, the P2SH output has 32 bytes, and the transaction header is 10 bytes.  
`2×76+5×34+32+10 = 364 bytes`

Fee rate: 78920 satoshis / 364 bytes = 216.8 sat/B

*Raw size:* **872 bytes**  
The raw byte length of the transaction.  
Each of the two inputs has a raw byte length of 329 bytes, each of the five P2PKH outputs has 34 bytes, the P2SH output has 32 bytes, and the transaction header is 12 bytes long.  
`2×329+5×34+32+12 = 872 bytes`

Fee rate: 78920 satoshis / 872 bytes = 90.5 sat/B

## Where are the 160.2 sat/B we are looking for?

The explorers are showing us “fee / stripped transaction size” and “fee / raw transaction size”. While the calculation with the stripped transaction size overestimates the fee rate by considering the transaction smaller than it is, using the raw transaction size underestimates the fee rate by ignoring the witness discount.

However, since Segregated Witness activated, the relevant fee rate is “fee / virtual transaction size” in [satoshis per virtual byte].

*Virtual size:* **491 vbytes**  
The virtual size (vsize) of the transaction respective to the portion of a block it occupies, corresponding to the size of non-segwit transactions.  
The vsize is calculated as follows:

`(3×non-witness size + raw transaction size) / 4`  
or  
`(4×non-witness size + witness size) / 4`

For non-segwit transactions the vsize is equal to the size.
Each of the two inputs weighs 139 vbytes, each of the five P2PKH outputs weighs 34 vbytes, the P2SH output weighs 32 vbytes, and the transaction has a header of 10.5 vbytes.  
`2×139+5×34+32+11 = 491 vbytes`

Fee rate: 78920 satoshis / 491 vbytes = **160.7 sat/vB**

## At last, BitFlyer gets it right by using scattershot:

{{< figure src="/images/bitflyer.png" class="center" caption="[BitFlyer lists all: the fee per raw tx size, the fee per WU and the actual fee rate.](http://chainflyer.bitflyer.jp/Transaction/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8)" >}}

Could we pretty, pretty please have more blockexplorers that show us the relevant fee rate?

## Appendix: Additional honorary mentions


{{< figure src="/images/blockchair.png" class="center" caption="[Blockchair tells us the “fee per kB” is 0.00216813 BTC.](https://blockchair.com/bitcoin/transaction/261494937) — Update: Blockchair has fixed their fee rates since this article. Thank you." >}}

{{< figure src="/images/blockchain.png" class="center" caption="[Blockchain.info shows the fee rate over raw byte length and a correct “Fee per weight unit” of 40.2 sat/WU, which however doesn’t compare to the established fee rates.](https://blockchain.info/tx/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8)" >}}

{{< figure src="/images/btc-com.png" class="center" caption="[btc.com shows virtual size, but calculates the fee with the raw byte length: 90.5 sats/B.](https://btc.com/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8)— Update: BTC.com has fixed their fee rates since this article. Thank you." >}}

{{< figure src="/images/blocktrail.png" class="center" caption="[blocktrail.com shows stripped size of 364 bytes and a fee rate of 216.8 sat/B.](blocktrail.com shows stripped size of 364 bytes and a fee rate of 216.8 sat/B.)" >}}

{{< figure src="/images/blockcypher.png" class="center" caption="[blocktrail.com shows stripped size of 364 bytes and a fee rate of 216.8 sat/B.](https://www.blocktrail.com/BTC/tx/cdeea0d6c37a046d5b7e13a75bc0c9842493f41dd5a97d248df43552ad9e15c8)" >}}


