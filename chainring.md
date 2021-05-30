
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

<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/Torque.jpg" alt="Model"> </p>
<p align="center"> <i> Figure 1: Simple bicycle model. </i> </p>
  
You are doing a full gas time trial. Therefore:

<math>
  Speed<sub>bike</sub> = constant
</math>


<math>
  F<sub>r</sub> = constant
</math>


<math>
  Articulation rate = <sup>Speed<sub>bike</sub></sup>&frasl;<sub>P</sub> x <sup>r<sub>1</sub></sup>&frasl;<sub>r<sub>t</sub></sub> 
</math>

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