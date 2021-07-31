# Mimblewimble CoinSwap Proposal

[Mimblewimble CoinSwap Proposal](https://forum.grin.mw/t/mimblewimble-coinswap-proposal/8322) describes a way to for parties to noninteractively engage in a coinswap/shuffle transaction where the trust model is that only one of the mixnodes needs to be honest in order to blind everyone from knowing any input/output links in this transaction. Even when all the mixnodes collude and know the links between the coinswaps, it still blinds your neighbours from knowing the input/output links of the transaction unless the data is made public. There [are ways](https://forum.grin.mw/t/mimblewimble-coinswap-proposal/8322/42) to blind the mixnodes as well if people really wanted to.

This document won't go into the details of how MW CoinSwap proposal works, but rather, we'll try to get an intuition of what it could mean if it was used by every single wallet, meaning that every output would be sent to the coinswap service.

From this point on, we assume that every created output is sent to the coinswap service which runs a daily shuffle - one shuffle every 24 hours. What this effectively means is that outputs that have been created in that daily interval must either be:
1. spent
2. a part of a coinswap transaction

One of the two above must be true. If the output was not spent, it will be in the coinswap transaction the service produces. If it was spent, then the newly created output will be sent to that service which, like the previous one, is either spent in that interval or it ends up being a part of the MW coinswap transaction.

This can be visually presented in the following way. For simplicity, let's supposed we will run the coinswap service every 3 minutes so that we only have to draw 3 blocks N, N+1 and N+2.

![Image of blocks](https://i.imgur.com/0HwhDXk.png)

The outputs that have been created in these blocks and have not been spent yet should end up in the coinswap transaction. To obtain these outputs we can simulate transaction aggregation of these blocks and due to the transaction cut-through removing the spent outputs, we should get exactly the outputs that are going to end up in a coinswap transaction. The outputs that have not been spent have a more solid background color.

The aggregate transaction of these blocks would be

![Image of block aggregation](https://i.imgur.com/BGAlOPB.png)

Unlike when doing the aggregate block transaction for the whole blockchain, we don't expect all the inputs to disappear here because they were created in previous blocks which we did not aggregate. But we're really only interested in the `Outputs` part of the transaction because we will find exactly those outputs that are going to land in a coinswap transaction.

This would mean that our coinswap transaction would look something like this

![Image of block aggregation](https://i.imgur.com/tmu91yB.png)

Every single output that has been created and not spent in the last 3 minutes/blocks gets a big shuffle. As you might have guessed by now, this shuffle finds all the new outputs in the UTXO set in a range of blocks and makes a coinswap between them.

The above example demonstrated a coinswap working by default in the range of 3 blocks that had a few inputs and outputs. The daily coinswap service when used by default would have a much larger coinswap transaction because it would contain the range of `60*24 = 1440` blocks. This could easily make the coinswap transaction have over a thousand inputs. We may even need to split the coinswaps into multiple transactions if the transaction was too large for a single block.
