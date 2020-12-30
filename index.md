# Grin

## What is Grin?

It's a different way to design a blockchain that was posted anonymously in June 2016. But how is it different from all the other blockchain designs? Grin's blockchain is really just a single big transaction. This doesn't make any sense when you first hear it and it seems hard to believe at first, but it turns out it's true. This post will explore the world of Grin transactions and how they fit together to form a blockchain.

## Intro to Confidential Transactions

Grin builds on a lot of previous research which was done by the Bitcoin researchers. It uses [Confidential Transactions](https://github.com/AdamISZ/ConfidentialTransactionsDoc/blob/master/essayonCT.pdf) at the core of its design which means that all the amounts are blinded. But it doesn't use the idea as was originally described and instead improves on it - we won't go into the details in this post because the goal is to get a high level idea of why it's considered by many cryptographers the most interesting blockchain design to date. What Grin achieves is that the UTXO is described as a single point on an elliptic curve. This means that it looks a bunch of random characters and numbers to anyone observing it e.g. `09fe4d4d2aa97ba9c52ea494c967bddb12d1e394bbe91692e5340ba4d9cca2fddd`. This random sequence holds the information about both the owner and the amount, but does not reveal. That's correct, there are no addresses, just a single curve point. There's some annoyance with outputs that are presented this way which is that anyone could create coins out of thin air if you're not careful. To prevent this, we need to check that the amount the output hides is within a certain range, but we must do this in a way that does not reveal the amount itself. This is achieved with a zero knowledge rangeproof. Now that we know what the outputs look like, let's check what Grin transactions look like.

## Grin transaction

A transaction in Grin is composed of 3 sets. A set of `inputs`, a set of `outputs` and a set of `kernels`. We are familiar with the inputs and outputs from Bitcoin already (except for the part that they look like a single random sequence of character now), but the `kernels` is something that was introduced in the Mimblewimble whitepaper. A kernel composed of two things, a `random curve point` and a `signature`. Each transaction has a single kernel which:
1. proves the transaction does not lose any coins or create new ones out of thin air - `Σinput.amount - Σoutput.amount = 0`
2. proves the authenticity of the transaction, meaning that all the input and output owners authenticated this transaction

This is quite neat, we have a single curve point and a signature that takes care of all the important things in a transaction!


Let's take a look at one transaction on Grin.

![Image of a Grin transaction](https://i.imgur.com/JVrlwVE.jpeg)

As we can see from the picture, this is a transaction that has a single input, 2 outputs and a kernel that pays 0.011 fee. A person that observed this transaction can't tell who the sender or the receiver or what amounts any of the inputs and outputs hold. They can also know this if they have some metadata about the inputs and outputs.

_Note: I simplified some things intentionally to not get you lose on the details. The important thing is that you understand the big picture._

### Transaction aggregation

Grin allows us to concatenate two transaction to obtain a single transaction. We call this transaction aggregation, but is known in the Bitcoin space as a CoinJoin. This Coinjoin is more powerful than the one on the Bitcoin network because you don't need to match the amounts since they're not visible and it does not require the transaction owners to do the CoinJoin interactively. This means that anyone that sees two Grin transactions can just concatenate them together into a single transaction.

Let's say we have two transactions that look like this

![Tx aggregation 1](https://i.imgur.com/DEK329f.jpg)

We can concatenate them together to obtain a new aggregated transaction that cannot be decomposed into two transactions (except we can do it since we have seen the transactions prior to their aggregation).

![Tx aggregation 2](https://i.imgur.com/dxxswG1.jpg)

We have a single transaction now. Notice however that the inputs, outputs and kernels have shuffled their order a bit. This is because they are sorted ascendingly in order to prevent guessing which could be a part of the same transaction e.g. the last two outputs are likely from the same transaction. Since the result is just another Grin transaction, we can aggregate it with a new transaction to get an even bigger transaction - we can repeat this as many times as we like.

This allows us to aggregate all the transactions in a block into a single transaction. We don't need to represent the block as a sequence of transactions, we can just say that it contains a set of inputs, a set of outputs and a set of kernels. The block itself is just a couple of headers and a single transaction!

### Transaction Cut-through

In Bitcoin, the new nodes joining the network need to download the history of all transactions and replay them locally to reproduce the state of the ledger. Mimblewimble improves on this. Let's take a look at what happens when a transaction is made on this new blockchain design.

Let's say we have this transaction

![Image of a Grin transaction](https://i.imgur.com/JVrlwVE.jpeg)

Let's now consider another transaction that spends one of the outputs from the first transaction

![Image of a Grin tx spending a the output of a previous transaction](https://i.imgur.com/av1z9oW.jpeg)

Let's now aggregate the two transactions together

![Image of Grin cut-through](https://i.imgur.com/OgsQtUS.jpg)

Huh? Not what we expect is it? Where did the green input and output disappear?! Turns out that when an output is spent, we can delete the matching input and output and forget about them all together and the transaction is still valid! This means that we can spend and forget even before the transaction hits on the chain. This process is called transaction cut-through and was first introduced on [Bitcointalk forum by Greg Maxwell](https://bitcointalk.org/index.php?topic=281848.0). We have already learned that the block is a single transaction so we can forget before we publish the block, and in fact, Grin consensus does not allow a transaction where a cut-through has not been done.

And we have finally arrived at the moment where we'll see the real magic.

### The whole blockchain is a transaction

As I mentioned at the beginning, the whole Grin blockchain is a single big transaction. We already know that each block is a single transaction, so we can align all the sequential blocks transaction together. Since we know that given any two transactions we can aggregate them together to get a new valid transaction, we can just aggregate all the block transactions into a single big transaction. But since this transaction contains the whole blockchain history, it means that every spent input in a transaction will have a matching output where it was created, the exception being the coinbase inputs (they're the only inputs that don't have a matching output!) and the outputs that have not been spent yet. This means that every input and spent output in the history of the chain can be completely forgotten, we just need to keep the kernels for all the transactions! If we were to validate this cut-through transaction, it would be balanced. In fact, this is exactly the data that a new node needs in order to sync to the network. The initial sync is nothing more than the download of this one big transactions and the validation of it.

![Grin block txs](https://i.imgur.com/rWADXmo.jpeg)

And after we cut-through the whole blockchain we get the following transaction

![Grin blockchain tx](https://i.imgur.com/ev6JOk0.jpeg)

> and voilá! is again a valid transaction.

To read about the original proposed design in detail, please read the [original Mimblewimble whitepaper](https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin).