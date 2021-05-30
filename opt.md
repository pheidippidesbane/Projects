---
layout: default
---

[Home](./index.html)

# Optimised Portfolio Tool



Let's say you are an investor.

You have a universe of stocks that you are considering for investment.

You are a believer in the efficient market hypothesis, to an extent.

Cheap stocks are probably cheap because they deserve to be cheap, i.e. they are destined for the dustbin of history, like XOM.

Expensive stocks are probably expensive because they're going to be the next AAPL or TSLA.



## Investing in the Index



Why don't you just invest in the index? It has a number of advantages:

1. Simple and easy.
2. Usually pretty cheap in terms of management fees (index ETFs typically have an expense ratio around 10bps annually).
3. Probably going to have similar asset returns to any hegde fund strategy if you're an efficient market hypothesis believer (which is somewhat self defeating if you think about it; why would an efficient market keep paying PMs' salaries?).

However, given an index of stocks, it seems very unlikely that the optimal weighting of securities just happens to be exactly the same as the weighting by order of market capitalisation (or whatever methodology the index provider uses). Surely you want to diversify _better_.



## Diversifying



How do you diversify _better_?

For a start, maybe you don't want to go all in on the S&P500 which is heavily dominated by big tech stocks. You probably want to weight stocks so that anti-correlated stocks offset eachother.

### Yes! Let's chose weights that anti-correlate optimally!

I will show you a tool that I have made that uses some assumptions to chose the optimal portfolio weights given a universe of stocks.



## The Tool



This tool requires a number of assumptions for validity:

1. The expected return of any asset can be modelled by it's historical volatility (in this case, linearly).
2. The historical covariances of assets are indicative of future covariance.
3. Securties are homogeneous in their propensity for cyclicality (a potential avenue for future development of the model).

Here is the python code (with paths REDACTED):

```python

# -*- coding: utf-8 -*-
"""
Created on Sat Jan 16 10:56:00 2021

@author: pheidippidesbane
"""

#######################
### IMPORT PACKAGES ###
#######################

import sys
sys.path
sys.path.append(r'PATH\pythonsoftwarefoundation.python.3.9_qbz5n2kfra8p0\localcache\local-packages\python39\site-packages')
import pandas_datareader.data as web
import datetime as dt
import pandas as pd
import numpy as np
import random as rd
import matplotlib.pyplot as plt

figure_number = 0 # start counting figures

################
### SETTINGS ###
################

start_of_universe = 1970
stocks_path = 'PATH\\YAHOO_STONKS.csv'
exclude = ['RACE.MI', 'BABA', '1299.HK']
number_of_trials = 100000
pc_change = 1
method = 'both' # 'random' 'optimise'
pc_risk_free_rate = -0.18

##############
### PRICES ###
##############

# Graph a stock from start_year until now
def graph(stock, start_year, figure_number, graph_on = True):
    start = dt.datetime(start_year,1,1)
    end = dt.datetime.now()
    dfe = web.DataReader(stock, 'yahoo', start, end)
    df_out = pd.DataFrame()
    df_out[stock] = dfe['Adj Close']
    if graph_on == True:
        plt.figure(figure_number)
        plt.plot(df_out[stock])
        plt.title(stock)
        figure_number += 1
    return df_out, figure_number

# Get the list of stocks to consider for the portfolio
yahoo_stocks = list(pd.read_csv(stocks_path)['SECURITY'])
for i in exclude: 
    try:
        yahoo_stocks.remove(i)
    except:
        None

# Get a time series of prices for the stocks
df_prices = pd.DataFrame()
df_prices, figure_number = graph(yahoo_stocks[0], start_of_universe, figure_number, graph_on = False)
for i in range(1,len(yahoo_stocks)):
    df_prices, figure_number = df_prices.merge(graph(yahoo_stocks[i], start_of_universe, figure_number, graph_on = False)[0], how = 'inner', left_index = True, right_index = True), graph(yahoo_stocks[i], start_of_universe, figure_number, graph_on = False)[1]
df_prices.dropna(inplace = True)

# Find the number of years of data
number_of_years = abs((list(df_prices.index)[0] - list(df_prices.index)[-1]).total_seconds() / (365*24*60*60))

############################
### COVARIANCES AND VOLS ###
############################

df_normalised = pd.DataFrame(columns = df_prices.columns, index = df_prices.index)
df_normalised2 = pd.DataFrame(columns = df_prices.columns, index = df_prices.index)
for column in list(df_normalised.columns): df_normalised[column] = list(map(lambda x: x*len(list(df_prices[column]))/sum(list(df_prices[column])),df_prices[column]))
for column in list(df_normalised2.columns):
    for i in range(1,len(df_normalised2.index)):
        df_normalised2.loc[df_normalised.index[i],column] = np.log(df_normalised.loc[df_normalised.index[i],column]/df_normalised.loc[df_normalised.index[i-1],column])
df_normalised2.drop(df_normalised2.index[0], axis = 0, inplace = True)
df_cov_raw = pd.DataFrame(np.cov(np.transpose(np.array(df_normalised2).astype(float))),columns = df_normalised2.columns, index = df_normalised2.columns)
df_cov = pd.DataFrame(np.multiply(np.array(df_cov_raw),(252)),columns = df_cov_raw.columns, index = df_cov_raw.columns)
vols = pd.DataFrame(columns = ['VOLATILITY'], index = df_cov.index)
vols[vols.columns[0]] = [np.sqrt(df_cov.loc[i,i])*100 for i in df_cov.columns]
df_input = df_cov

#######################################
### SECURITIES, WEIGHTS AND RETURNS ###
#######################################

securities = list(df_input.columns)
weights = list([1/len(securities)]*len(securities))
sec_wei_cols = ['SEC', 'WEI']
sec_wei = pd.DataFrame(columns=sec_wei_cols)
sec_wei[sec_wei_cols[0]] = securities

##########################################
### RETURN AND VOL FOR GIVEN PORTFOLIO ###
##########################################

def ret_var(weights, risk_free_rate = pc_risk_free_rate/100, sec_wei = sec_wei):
    sec_wei[sec_wei_cols[1]] = weights
    phi = 0.4 #arbitrary CAPM gradient
    sec_wei['W-RETURN'] = list(map(lambda x,y: risk_free_rate + phi * x * np.sqrt(df_input.loc[y, y]), sec_wei.WEI, sec_wei.SEC))
    portfolio_return = sum(sec_wei['W-RETURN'])
    variance = 0
    for security1 in securities:
        for security2 in securities:
            variance = (variance +
                        float(sec_wei[sec_wei['SEC']==security1]['WEI'])*
                        float(sec_wei[sec_wei['SEC']==security2]['WEI'])*
                        float(df_input.loc[security1,security2]))
    stdev = np.sqrt(variance)
    return portfolio_return, stdev

############################
### SET UP RESULTS TABLE ###
############################

results_cols = []
for i in range(1, len(securities)+1):
    results_cols.append(str('W'+str(i)))
results_cols.append('R')
results_cols.append('S')
results_cols.append('Sh')
df_results = pd.DataFrame(columns = results_cols, index=range(1,number_of_trials+1))
for j in range(1,len(weights)+1):
    df_results.iloc[1-1,j-1] = weights[j-1]
R, S = ret_var(weights)
df_results.iloc[1-1,len(weights)+1-1] = R
df_results.iloc[1-1,len(weights)+2-1] = S
df_results.iloc[1-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S

#################
### ITERATION ###
#################

if method == 'optimise':
    for i in range(2,number_of_trials+1):
        weight_to_change = rd.randint(1, len(securities))
        weights[weight_to_change-1] = weights[weight_to_change-1]*(1+pc_change/100)
        weights = list(np.multiply(weights,1/sum(weights)))
        for j in range(1,len(weights)+1):
            df_results.iloc[i-1,j-1] = weights[j-1]
        R, S = ret_var(weights)
        df_results.iloc[i-1,len(weights)+1-1] = R
        df_results.iloc[i-1,len(weights)+2-1] = S
        df_results.iloc[i-1,len(weights)+3-1] = R/S
        if df_results.iloc[i-1,len(weights)+3-1] < df_results.iloc[i-2,len(weights)+3-1]:
            weights[weight_to_change-1] = weights[weight_to_change-1]*(1-pc_change/50)
            weights = list(np.multiply(weights,1/sum(weights)))
            for j in range(1,len(weights)+1):
                df_results.iloc[i-1,j-1] = weights[j-1]
            R, S = ret_var(weights)
            df_results.iloc[i-1,len(weights)+1-1] = R
            df_results.iloc[i-1,len(weights)+2-1] = S
            df_results.iloc[i-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S
        print(i)
elif method == 'random':
    for i in range(2,number_of_trials+1):
        weights = [rd.randint(1,100) **4 for i in weights]
        weights = list(np.multiply(weights,1/sum(weights)))
        for j in range(1,len(weights)+1):
            df_results.iloc[i-1,j-1] = weights[j-1]
        R, S = ret_var(weights)
        df_results.iloc[i-1,len(weights)+1-1] = R
        df_results.iloc[i-1,len(weights)+2-1] = S
        df_results.iloc[i-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S
        print(i)
else:
    for i in range(2,round(number_of_trials/2)):
        weights = [rd.randint(1,100) **4 for i in weights]
        weights = list(np.multiply(weights,1/sum(weights)))
        for j in range(1,len(weights)+1):
            df_results.iloc[i-1,j-1] = weights[j-1]
        R, S = ret_var(weights)
        df_results.iloc[i-1,len(weights)+1-1] = R
        df_results.iloc[i-1,len(weights)+2-1] = S
        df_results.iloc[i-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S
        print(i)
    j = i
    for i in range(j,number_of_trials+1):
        weight_to_change = rd.randint(1, len(securities))
        weights[weight_to_change-1] = weights[weight_to_change-1]*(1+pc_change/100)
        weights = list(np.multiply(weights,1/sum(weights)))
        for j in range(1,len(weights)+1):
            df_results.iloc[i-1,j-1] = weights[j-1]
        R, S = ret_var(weights)
        df_results.iloc[i-1,len(weights)+1-1] = R
        df_results.iloc[i-1,len(weights)+2-1] = S
        df_results.iloc[i-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S
        if df_results.iloc[i-1,len(weights)+3-1] < df_results.iloc[i-2,len(weights)+3-1]:
            weights[weight_to_change-1] = weights[weight_to_change-1]*(1-pc_change/50)
            weights = list(np.multiply(weights,1/sum(weights)))
            for j in range(1,len(weights)+1):
                df_results.iloc[i-1,j-1] = weights[j-1]
            R, S = ret_var(weights)
            df_results.iloc[i-1,len(weights)+1-1] = R
            df_results.iloc[i-1,len(weights)+2-1] = S
            df_results.iloc[i-1,len(weights)+3-1] = (R-pc_risk_free_rate/100)/S
        print(i)

#############
### PLOTS ###
#############

# RETURN, SHARPE and VOL

plt.figure(figure_number)
figure_number += 1
#plt.yticks([])
plt.plot(range(1,number_of_trials+1), df_results.iloc[:,len(weights)+1-1] *100, label = 'Return')
plt.plot(range(1,number_of_trials+1), df_results.iloc[:,len(weights)+2-1] *100, label = 'Vol')
plt.plot(range(1,number_of_trials+1), df_results.iloc[:,len(weights)+3-1], label = 'Sharpe')
plt.xlabel('Iterations')
plt.ylabel('')
plt.grid(True)
plt.legend()
plt.savefig('PATH\\DESCENT.png')

# SHARPE
plt.figure(figure_number)
figure_number += 1
#plt.yticks([])
plt.plot(range(1,number_of_trials+1), df_results.iloc[:,len(weights)+3-1], label = 'Sharpe')
plt.xlabel('Iterations')
plt.ylabel('')
plt.grid(True)
plt.legend()
plt.savefig('PATH\\SHARPE_DESCENT.png')


# PORTFOLIO WEIGHTS

plt.figure(figure_number)
figure_number += 1
#plt.plot(list(map(lambda x: x[:-2],securities)), np.multiply(weights,100))
#plt.plot(list(sec_wei.sort_values(sec_wei.columns[1])[sec_wei.columns[0]]),list(sec_wei.sort_values(sec_wei.columns[1])[sec_wei.columns[1]]))
for i in range(0,i):
    #plt.plot(list(map(lambda x: x[:-2],securities)), np.multiply(list(df_results.iloc[i,0:len(securities)]),100), alpha = 0.3)
    plt.plot(list(map(lambda x: x,securities)), np.multiply(list(df_results.iloc[i,0:len(securities)]),100), alpha = 0.3)
#plt.plot(list(map(lambda x: x[:-2],securities)), np.multiply(list(df_results.iloc[i,0:len(securities)]),100), alpha = 1, linewidth = 3)
plt.plot(list(map(lambda x: x,securities)), np.multiply(list(df_results.iloc[i,0:len(securities)]),100), alpha = 1, linewidth = 3)
plt.xlabel('Equity')
plt.ylabel('% Weight')
plt.xticks(rotation=90)
plt.grid(True)
plt.savefig('PATH\\WEIGHTS.png')


# ALL STONK PRICES

plt.figure(figure_number)
figure_number += 1
plt.plot(df_normalised)
plt.xlabel('Year')
plt.title('All Securities Relative Performance')
plt.grid(True)
plt.savefig('PATH\\WEIGHTS.png')

# MPT CHART

plt.figure(figure_number)
figure_number += 1
if method == 'both':
    two_colours = ['red', 'blue']
else:
    two_colours = ['blue', 'blue']
colours = [two_colours[0] for i in range(0,round(number_of_trials/2))] + [two_colours[1] for i in range(round(number_of_trials/2), number_of_trials + 1)]
colours = colours[0:number_of_trials]
plt.scatter(df_results['S'] * 100, df_results['R'] * 100, s = 0.1, alpha = 0.3, c = colours)
plt.axis([0,1.2*max(df_results['S'] * 100),0,1.2*max(df_results['R'] * 100)])
x = [0, 1.2*max(df_results['S'] * 100)]
y = [max(df_results['Sh']) * i + pc_risk_free_rate for i in x]
plt.plot(x, y, c = 'black', alpha = 0.3)
plt.xlabel('Vol')
plt.ylabel('Return')
plt.grid(True)
plt.savefig('PATH\\MPT.png')


# WHOLE PORTFOLIO

df_port = df_normalised.copy()
df_port['Total'] = np.multiply(np.array(df_port[df_port.columns[0]]),float(list(sec_wei[sec_wei['SEC']==df_port.columns[0]]['WEI'])[0]))
for i in range(1,len(df_normalised.columns)):
    df_port['Total'] = df_port['Total'] + np.multiply(np.array(df_port[df_port.columns[i]]),float(list(sec_wei[sec_wei['SEC']==df_port.columns[i]]['WEI'])[0]))
plt.figure(figure_number)
figure_number += 1
plt.plot(df_port['Total'])
plt.xlabel('Year')
plt.title('Portfolio Realised Performance')
plt.grid(True)
plt.savefig('PATH\\PORTFOLIO.png')

```

## So what does this tool actually do?


Basically the tool iterates over a variety of possible portfolio weightings and calculates the expected return and volatility of the portfolio given a modelled relationship between the two.

Initially, if chooses a number of random weightings in order to expose the cases where the optimisation has found a bad local maximum.

The second half of the iteration uses stochastic gradient descent to optimise portfolio weights for maximum Sharpe ratio (shown in blue in _Figure 1_).

<p align = "center"><a href="https://www.codecogs.com/eqnedit.php?latex=\overbrace{\overrightarrow{weights}^{*}}^{\text{Vector&space;of&space;Optimal&space;Weights}}&space;=&space;\operatorname*{argmin}_{\overrightarrow{weights}}&space;Sharpe&space;(\overrightarrow{weights})&space;=&space;\operatorname*{argmin}_{\overrightarrow{weights}}\frac{\displaystyle\sum_{i&space;\in&space;securities}&space;(weight_i&space;\times&space;\overbrace{R_i}^{\text{Expected&space;Return}})}{\sigma_{portfolio}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\overbrace{\overrightarrow{weights}^{*}}^{\text{Vector&space;of&space;Optimal&space;Weights}}&space;=&space;\operatorname*{argmin}_{\overrightarrow{weights}}&space;Sharpe&space;(\overrightarrow{weights})&space;=&space;\operatorname*{argmin}_{\overrightarrow{weights}}\frac{\displaystyle\sum_{i&space;\in&space;securities}&space;(weight_i&space;\times&space;\overbrace{R_i}^{\text{Expected&space;Return}})}{\sigma_{portfolio}}" title="\overbrace{\overrightarrow{weights}^{*}}^{\text{Vector of Optimal Weights}} = \operatorname*{argmin}_{\overrightarrow{weights}} Sharpe (\overrightarrow{weights}) = \operatorname*{argmin}_{\overrightarrow{weights}}\frac{\displaystyle\sum_{i \in securities} (weight_i \times \overbrace{R_i}^{\text{Expected Return}})}{\sigma_{portfolio}}" /></a><p align = "right">(1)</p></p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/MPT.png" alt="Modern Portfolio Theory"> </p>
<p align="center"> <i> Figure 1: Modelled risk-return profiles of randomly weighted (red) and progressively optimised (blue) portfolios. </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/PORTFOLIO.png" alt="Porfolio"> </p>
<p align="center"> <i> Figure 2: Historical performance of portfolio using optimised weights. </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/DESCENT.png" alt="Descent"> </p>
<p align="center"> <i> Figure 3: Development of portfolio expectation and volatility. </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/SHARPE_DESCENT.png" alt="Sharpe Descent"> </p>
<p align="center"> <i> Figure 4: Development of portfolio Sharpe Ratio. </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/WEIGHTS.png" alt="Weights"> </p>
<p align="center"> <i> Figure 5: Tested portfolio weights and optimal weights (colour). </i> </p>

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/ALLSTOCKS.png" alt="All Stocks"> </p>
<p align="center"> <i> Figure 6: Historical performance of stocks in universe relative to their mean. </i> </p>

<!---
Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://pheidippidesbane.github.io/Projects/images/Picture1.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
-->
