---
layout: default
---


[Home](./index.html)

# Big Chainrings

Watching the time-trial stage of the Tour de France or Giro D'Italia these days, you are going to see a lot of **BIG** chainrings.
The reason given for this by proponents is that the chain bends less over the teeth, and so the losses to friction are less.

This is certainly true. A larger chainring with a fixed chain pitch results in less of a kink. The theory often repeated, however, misses some key additional changes caused by large rings:

1. A larger ring requires the chain links to move at a faster absolute speed relative to the bike/rider given a fixed speed. So although each articulation might involve less friction, articulations are more frequent.
2. A larger ring requires a lower chain tension to maintain the fixed wheel torque required for a given speed. This implies that the articulations are once again induce less friction loss.

Now we see that the situation is not so simple after all.

## Model

Consider the simple bicycle model in _Figure 1_:

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/Torque.jpg" alt="Model" width="400" 
     height="250"> </p>
<p align="center"> <i> Figure 1: Simple bicycle model. </i> </p>
  
You are doing a full gas time trial. Therefore:
<p align = "center">
<a href="https://www.codecogs.com/eqnedit.php?latex=\hat{\beta}&space;=&space;\frac{Cov(R_{stock},&space;R_{market})}{Var(R_{market})}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\hat{\beta}&space;=&space;\frac{Cov(R_{stock},&space;R_{market})}{Var(R_{market})}" title="\hat{\beta} = \frac{Cov(R_{stock}, R_{market})}{Var(R_{market})}" /></a> <span style="float:right;">
        (1)
    </span>
</p>


	
<p>
<math>
  Speed<sub>bike</sub> = constant
</math>
</p>

<p>
<math>
  F<sub>r</sub> = constant
</math>
</p>

<p>
<math>
  Articulation rate = <sup>Speed<sub>bike</sub></sup>&frasl;<sub>P</sub> x <sup>r<sub>1</sub></sup>&frasl;<sub>r<sub>t</sub></sub> 
</math>
</p>

<p>
<math>
  F<sub>c</sub> = F<sub>r</sub> x <sup>r<sub>t</sub></sup>&frasl;<sub>r<sub>1</sub></sub>
</math>
</p>

<p>
<math>
	&alpha;<sub>2</sub> = <sup>P</sup> &frasl; <sub>r<sub>2</sub></sub>
</math>
</p>

<p>
<math>
	&alpha;<sub>1</sub> = <sup>P</sup> &frasl; <sub>r<sub>1</sub></sub> = constant x <sup>P</sup> &frasl; <sub>r<sub>2</sub></sub>
</math>
</p>

<p>
<math>
  Friction Losses = k<sub>1</sub> x Articulation rate x F<sub>c</sub> x (&alpha;<sub>1</sub> + &alpha;<sub>1</sub>)
	= k<sub>1</sub> x <sup>Speed<sub>bike</sub></sup>&frasl;<sub>P</sub> x <sup>r<sub>1</sub></sup>&frasl;<sub>r<sub>t</sub></sub> x F<sub>r</sub> x <sup>r<sub>t</sub></sup>&frasl;<sub>r<sub>1</sub></sub> x <sup>P</sup> &frasl; <sub>(r<sub>2</sub>) x (1 + constant) </sub>
	= <sup> constant </sup> &frasl; <sub>r<sub>2</sub></sub>
</math>
</p>

### So yes, the friction losses scale inversely with ring size.

<!---
<MATH>&int;_a_^b^{f(x)<over>1+x} dx</MATH>    
    
<math>
	H(s) = ∫<sub>0</sub><sup>∞</sup> e<sup>-st</sup> h(t) dt
</math>
	
	
<math>
	(<array align="c"> <item>
		&ldet;<array align="cc">
			<item>x<sub>11</sub>
			<item>x<sub>12</sub>
			<item>x<sub>21</sub>			
			<item>x<sub>22</sub>
		</array><rd>&rdet;
		<item> y <item> z
	</array>)
</math>
				
<math>
	C <box>dV<sub>out</sub><over>dt</box> = I<sub>b</sub>
	&tanh;(<box>κ(V<sub>in</sub>-V<sub>out</sub>)<over>2</box>)
</math>
    
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
