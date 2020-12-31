# Transaction building process

This post will attempt to describe the difference in the transaction building in Bitcoin and Mimblewimble.
Both protocols use the UTXO model, meaning that the transaction is a state transition that transfers value from some inputs to some outputs, so they can easily be compared. A transaction is really just a signed value transfer.

_Disclaimer: What I'll describe below isn't exactly accurate, but it seems like a model that allows for a comparison of the two approaches._

## Bitcoin

A Bitcoin transaction can be thought of these steps:
1. Sender prepares the transaction metadata (how much money they want to send)
2. Sender populates the inputs and outputs describing a full transaction
3. Sender signs the transaction

## Mimblewimble

A Mimblewimble transaction has a bit different flow which is a more generalized way to construct a transaction. The steps can be thought as:
1. Party `A` prepares the transaction metadata (how much money they want to send/receive)
2. Party `B` populates _their_ inputs and outputs describing only a part of the transaction and signs this along with the metadata
3. Party `A` populates _their_ inputs and outputs describing only a part of the transaction and signs this along with the metadata

This is a bit different because we have parties signing for the inputs and outputs that they own in a transaction. A transaction where Alice sends money to Bob looks like

![grin_generalized_tx](https://i.imgur.com/QOwiG0x.jpg)

I've colored green the data that is signed by the sender and red the data that is signed by the receiver. The kernel here is the transaction metadata and is signed by both parties. What is interesting here is that the receiver needs to agree to receive the outputs and confirms that they know how much they will receive. This is a very important detail because it allows the receiver to say no to people sending them money which can avoid a whole class of known attacks called dust attacks where people send you outputs with small amounts in order to try to trace your transactions. This interactive flow also allows the receiver to contribute their own inputs like can be seen below

![grin_generalized_tx2](https://i.imgur.com/rWCliBz.jpg)

A transaction where the receiver also contributes an input is known as a [PayJoin](https://en.bitcoin.it/wiki/PayJoin) which helps obfuscate the sender's inputs and makes it less clear in which direction the money is flowing. PayJoin transactions are one way to break the [common input ownership heuristic](https://en.bitcoin.it/wiki/Privacy#Common-input-ownership_heuristic).

To summarize, the parties involved in the transaction need to construct the transaction jointly, but this does not mean that they need to be online at the same time, instead, partial transaction are sent to the other party to add their part to the transaction.

## Comparison

I won't be enumerating the pros and cons of each. I'll just describe what this means _for the transactions_. There are two important ideas that come from this generalization:

1. **User ownership of state - it is impossible to create outputs _for you_ without your approval.** In Bitcoin, while the transaction creates outputs for both the sender and the receiver, it is only the sender that has explicitly agreed with this change in state.
There is no way for the receiver to disagree with this. Mimblewimble *requires* a signature from both parties involved for the transaction to be valid. This requirement means that all the parties involved need to do their own part (inputs and outputs + signature) to construct a valid state transition.
2. **Transaction flexibility - it's entirely possible that the receiver contributes the inputs which makes payjoins natural.** The set of all possible transactions that you can do with the Bitcoin flow is a subset of all possible transactions that can be done with the generalized flow that is used by the Mimblewimble.