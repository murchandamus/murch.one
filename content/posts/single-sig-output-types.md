---
title: "A breakdown of different output types and their address formats"
date: 2021-12-23
draft: true
---

I was inspired by a recent [reddit post](https://www.reddit.com/r/Bitcoin/comments/rjdao2/people_keep_asking_me_about_the_different_types/) to write my own description of the various single-sig output formats in Bitcoin. I’ll be covering only output types that make use of a single signature.

## “Legacy Outputs” aka P2PKH

Pay to Public Key Hash was the first output type that got an address standard. Addresses for P2PKH outputs start with “1” and use the *Base58Check* encoding. The address encoding provides a checksum and represents a shorthand to communicate recipient information—which improved the UX over the prior situation which required the sender to handle the recipient’s full public key or non-standard output script to send a transaction.

Example: `1MDPuAy9WCbNQin71j9S3MKAAe9mGBRNVx`

Issues:

- High transaction weight
- Case-sensitive
- Bigger data footprint than Pay to Public Key

## “Wrapped Segwit Outputs” aka P2SH-P2WPKH

Segwit aimed to introduce [a new family of output types](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki) which we refer to as *native segwit*, but the authors realized that adoption of a new address format would take time. The segwit softfork additionally introduced *wrapped segwit* outputs as a transitional solution, to be forward-compatible to wallets that could send to P2SH addresses. Since P2SH was rolled out in March 2012 and already widely used for multisig, most wallet software supported sending to P2SH when segwit was activated in 2017.

P2SH-wrapped Pay to Witness Public Key outputs are indistinguishable from other P2SH-uses until spent. All P2SH addresses start with the prefix “3” and use the *Base58Check* encoding like P2PKH addresses.

Wrapped segwit outputs lock funds to a *P2SH Program*, but their input script contains a *Witness Program* that redirects the script validation to the *Witness Stack*. While this achieved forward-compatibility, the redirection requires additional data that needs to be relayed on the network and stored in the blockchain. Since witness data is discounted by 75% when assessing transaction weight, wrapped segwit inputs are cheaper to create, even while their data footprint is larger than P2PKH.

All P2SH addresses start with “3”.

Example: `3Mwz6cg8Fz81B7ukexK8u8EVAW2yymgWNd`

Issues:

- Case-sensitive
- Redirection to the witness stack for evaluation adds extra data
- Even bigger data footprint than P2PKH

## Native Segwit Outputs

Instead of locking funds to a *P2SH Program*, the output script for native segwit outputs directly contains the *Witness Program*. This way, native segwit inputs only need a witness stack, and make do without the redirection data needed in the wrapped segwit construction, leaving only a zero-length stub for the input script. Native segwit outputs are represented with *bech32* addresses for version 0, and *bech32m* for version 1–16. The bech32 encoding is single-case, making it easier to note down and dictate as well as more efficient to encode in QR codes . The bech32 encoding is engineered to provide error-detection guarantees. Bech32 and bech32m addresses start with “bc1”.

### 0) P2WPKH aka Native Segwit v0

Often simply referred to as “native segwit”, Pay to Witness Public Key Hash outputs lock funds to a public key hash similar to how P2PKH works. However, P2WPKH inputs provide the public key and signature in the Witness Stack instead of the input script, thus benefiting from the witness discount. Bech32 addresses encoding version 0 native segwit outputs start with “bc1q”, because “q” encodes 0 in bech32.

Example: `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4`

Issues:

- Bech32 addresses are still not supported by some wallets and services

### 1) P2TR aka Taproot aka Native Segwit v1

With the recent activation of Taproot, we add native segwit v1 outputs to our portfolio. Pay to Taproot outputs lock funds directly to a public key in the output’s witness program, which means (for single-sig uses) that the input only needs a single script argument, a signature, instead of needing to provide both a public key and signature like P2WPKH. P2TR uses Schnorr signatures, which are more compactly encoded than ECDSA signatures, reducing the signature size from 71-72 B to 64 B. This means that P2TR has the smallest data footprint even while the overall weight of input and output is slightly bigger than for P2WPKH. In addition, more complex spending conditions can be encoded in the leaves of the taptree that’s tweaked into the public key contained in the witness program. Bech32m addresses encoding P2TR outputs are longer than the bech32 addresses encoding P2WPKH outputs, since public keys are longer than the 20-byte hash used in P2WPKH. Addresses for P2TR outputs start with “bc1p”, because “p” encodes 1.

Example: `bc1pay2tapr00tajnawrkf897ccgsmk4e0x8ng5g3rv3qzd7jzfy2zxspy50gj`

Issues:

- Bech32m addresses are brand-new and not yet supported by many wallets and services

## Cost Considerations

![Table with overview of raw length, weight, and vsize of output types](/images/output-types.png)
Byte length vs weight vs vsize for single-sig output types

All four described output types satisfy single-sig usage, although P2TR can do a lot more under the hood. Generally, the transaction cost is cheaper for newer output types: Legacy > Wrapped Segwit > Native Segwit. While the overall cost of P2TR input and output is slightly higher than that of P2WPKH, P2TR shifts a portion of the cost from the input to the output. When you don’t know at what feerate you’ll need to pay to spend your funds later, you should keep them in P2TR outputs, since they’ll have the smallest input cost. Likewise, you should prefer P2TR when others are paying you: the sender pays the output cost while the recipient pays the input cost. Although, you may still bump into some counter-parties that cannot send to bech32, and many that cannot send to bech32m, yet, the economic incentives are clear. If your preferred wallet or service doesn’t support bech32(m) yet, please do ask them to do so.

If you’re considering your transactions’ data footprint on the blockchain, you should also strictly prefer P2TR as it get you the most bang for the byte (see column “raw [B]” in table above). The data footprint for the output types is P2SH-P2WPKH > P2PKH > P2WPKH > P2TR.

