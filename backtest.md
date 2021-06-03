---
layout: default
---


[Home](./index.html)


# Backtesting Technical Analysis Ideas

A fun thing to do when you've got a bloomberg terminal is to load up the 'First Word' component of launchpad and see what technical analysis advice they've got for you today. I sometimes find it tickling to see that they suggest selling gold because of a death cross, or that you should buy EURSEK because of a technical breakout.

This is perhaps a little unfair, as there certainly are cases in which financial instruments can see their price deviate from a Markov chain. However, these are probably going to be quite difficult to predict unless you understand the underlying reason for gamma stickiness or other such phenomenon, in which case you might as well start from understanding the market rather than drawing funny lines.

[This](https://www.reddit.com/r/wallstreetbets/comments/m2huy9/technical_analysis_of_todays_gme_movement/) piece is a brilliant characature of the typical day trading technical analyst, but aside from being pretty hilarious, it doesn't actually make any quantitative case against the existance of a 'textbook Salmon chart'!

I thought I would give a couple of technical analysis ideas a quick analysis of their own to see what I can find.

I have tried my best not to strawman these ideas.

## Momentum

_If a stock goes up today, it will probably go up tomorrow, and vice versa._

This the essence of a momentum strategy, and it directly contradicts the random walk model of financial instruments. The opposing thesis is a mean-reversion strategy where you think prices will tend to anti-correlate on a time shifted basis.

Unfortunately, I do not have access to large amounts of intra-day price data, but I do have daily price summaries. I can compare the previous day's price move with today's price move, over many days and many instruments in order to get an idea if there is some sort of correlation.:

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/AAPLmomentumshift1days.png" alt="Apple Momentum"> </p>
<p align="center"> <i> Figure 1: The relationship between one day's price move and the previous day's price move for AAPL. </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/9432Tmomentumshift3days.png" alt="Nippon Momentum"> </p>
<p align="center"> <i> Figure 2: The relationship between one day's price move and the third previous day's price move for NTT (Japan). </i> </p>

Indeed, I can summarise the correlation between today's move and the previous days' moves across a number of stocks and the S&P500 (SPY) in the following chart:

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/shiftvsr.png" alt="Shift Summary"> </p>
<p align="center"> <i> Figure 3: Momentum Summary. </i> </p>

What you can see across stocks, and particularly for a large index like the S&P500, is that there is little to no relationship between previous moves and the move that you can expect today. Sorry, momentum and mean-reversion investors.

## Death Cross and Golden Cross

[This](https://www.investopedia.com/terms/d/deathcross.asp) article from Investopedia explains the concept of these crosses better than I can.

I have tested this by taking the timeseries of closing prices for an asset, calculating the 50 and 200 DMA.

You start with enough money to buy one share of the company. You can chose to either:

1. Buy at the beginning and sell today or
2. Buy when the 50 DMA moves above the 200 DMA and sell when it moves below the 200 DMA. You then close out your position, long or short, today.

For each strategy, I calculate the return in terms of APR gained on your original sum. This has a number of drawbacks:

1. It gets inflated by large timescales due to compounding of relatively high growth rates.
2. It is excessively influenced by recent events.

However, a continuously compounded rate is not possible to calculate when you have a negative profit and APR at the end, which occured frequently in this study.

### Results

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/SPYbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/AAPLbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/VOW3DEbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/0941HKbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/9531Tbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/GOLDbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/4188Tbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/CPIJObacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/BBDbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/RACEMIbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/1177HKbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/0700HKbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/BABAbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/RIOLbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/BATSLbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/RYALbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/LDOMIbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/ENBTObacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/MCPAbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/FREFbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/ANZAXbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/1299HKbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/BHPLbacktest50v200DMA.png" alt="Backtest"> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/BHPLbacktest50v200DMA.png" alt="Backtest"> </p>

### Discussion

What does this show?

Firstly, it shows that trying to sell a death cross and buy a golden cross is actually a great way to end up with negative cash even in a bull market.

Secondly, it shows that buying the dip (i.e. reverse the actions and multiply the strategy returns by -1) is profitable, but in most cases less profitable that just buying and holding.


<p> <br /> <br /> <br /> <br /><br /> <br /><br /> </p>

* * *

<p align="center"> <i> Run, Pheidippides, one race more! The meed is thy due! </i> </p>
<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/android-chrome-256x256.png" alt="Antlers"> </p>
