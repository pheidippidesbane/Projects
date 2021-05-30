---
layout: default
---

[Home](./index.html)

# Optimised Portfolio Tool

Let's say you are an investor.

You have a universe of stocks that you are considering for investment.

You are a believer in the efficient market hypothesis, to an extent.

Cheap stocks are probably cheap because they deserve to be cheap, i.e. they are destined for the dustbin of history, like XOM.

Expensive stocks are probably expensive because they're going to be the next AAPL, like TSLA.

## Investing in the Index

Why don't you just invest in the index? It has a number of advantages:

1. Simple and easy.
2. Usually pretty cheap in terms of management fees (index ETFs typically have an expense ratio around 10bps annually).
3. Probably going to have similar asset returns to any hegde fund strategy if your an efficient market hypothesis believer (which is somewhat self defeating if you think about it; why would an efficient market keep paying PMs' salaries?).

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
