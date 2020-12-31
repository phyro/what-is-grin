# Grin's emission

Grin's emission model is the simplest one possible. One coin gets created every second. Forever. This means that it does not have a hard cap on the supply. But [as was mentioned by Nick Szabo](https://twitter.com/NickSzabo4/status/1077317105148547072), the important thing cryptocurrencies achieve has less to do with the hard cap on the supply, the important thing is the emission predictability. The problem with today's fiat model is that it is not predictable. If we were to ask people in 2019 how much money will be printed in 2020, nobody would have guessed it. Grin pushed its emission model to the extreme where it choose the most intuitive interpretation for the emission which also helps preventing a possible future change. Once you have a coin per second, it's very hard to convince people to change it because any change would need to go to a more complicated model - this includes halvings! What is more interesting is its relation to time.

The Bitcoin emission model is exponential because the halvings are an exponential function that raises the exponent every 4 years. It is just decreasing exponentially to a fixed point of 21 million coins. Exponential functions are tricky because they are not intuitive. They start of slow but soon speed up in ways most people don't really imagine. The Bitcoin strategy is to rely solely on fees over time, but for this to succeed, it needs to increase in value dramatically to retain the same security. But there are [some interesting problems](https://www.cs.princeton.edu/~arvindn/publications/mining_CCS.pdf) that appear when you rely solely on the fees or rather when the miner income from fees far surpasses the income from the block reward. 

Grin's emission model is the first that emulates time perfectly. The reason why Bitcoin can't do this is because time is a linear function and we've shown that Bitcoin emission is an exponential function so you can't really have a mapping to time from it. Grin not only has a linear emission, but the emission is modelled to be 1 Grin per second which allows us to think of time as value. People already intuitively know that time is valuable, but we've never had a global shared time. We do now with Grin. Exchanging Grin can be thought of as buying/selling time or exchanging time intervals. Time is infinite but predictable which is why it looks like a great choice for an emission model. It also discourages hoarding coins in the first years which might help get us to a more fair coin distribution. It also rewards the miners with the same amount of time reward (60 Grin per block) regardless whether they mined a block in the first year or in the next decade.

![grin time](https://i.imgur.com/uwLAr8g.png)

You can read more about the emission on the official [Monetary policy docs](https://github.com/mimblewimble/docs/wiki/Monetary-Policy).