# `text-decoration`

What if I tell you that `text-decoration` is one of the least supported SVG features?
Yes, the one that is used to underline a text.
How is it even possible?! Let's find out!

The first unintuitive thing you have to understand about `text-decoration`
is that it's inheritable and uses properties from the element it was defined on.<br>
So in the case of:

```xml
<text>
    <tspan fill="green" text-decoration="underline">
        <tspan fill="black">Text</tspan>
    </tspan>
</text>
```

our underline would be green and not black.

<img src="images/text-decoration-inheritence.png">

Hm... Ok, let's ignore different width, height, position and everything else for now.

<br>

The second unintuitive thing is that decoration properties include font properties as well.
So if we have:

```xml
<text>
    <tspan fill="green" font-family="Arial" font-size="200" text-decoration="underline">
        <tspan fill="black" font-family="Noto Sans" font-size="48">Text</tspan>
    </tspan>
</text>
```

then our underline would use Arial font metrics and not Noto Sans one.
And yes, underline thickness and position are stored in the font.

In reality though...

<img src="images/text-decoration-font.png">

Firefox and Safari are the only one "correct" here.

<br>

The third unintuitive thing is that decorations are cumulative.
Meaning we can define multiple `text-decoration`'s separately and they have to be combined.
And yes, preserved their individual styles.

```xml
<text x="50" y="60" font-family="Noto Sans" font-size="48">
    <tspan fill="green" text-decoration="underline">
        <tspan fill="blue" text-decoration="line-through">
            <tspan fill="black">Text</tspan>
        </tspan>
    </tspan>
</text>
```

<img src="images/text-decoration-inheritence-multiple.png">

Here, Firefox and Batik are correct.
But at this point it's even hard to tell who is right or wrong.

<br>

Another problem with such fundamental properties such as `text-decoration` is that they can affect
many other SVG constructs. Like a vertical text.

```xml
<text x="100" y="100" font-family="Amiri" font-size="20" text-anchor="middle"
      writing-mode="tb" text-decoration="underline">本 Hi! اليابان</text>
```

<img src="images/text-decoration-vertical.png">

Well, yeah... Safari, for a browser, is comically bad here.

<br>

Let's also remember that letters in SVG can be rotated. In which case underline
is not longer... well, a line.

```xml
<linearGradient id="lg1">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="green"/>
</linearGradient>
<text x="10" y="65" rotate="15" fill="url(#lg1)"
      font-family="Noto Sans" font-size="86" text-decoration="underline">Text</text>
```

<img src="images/text-decoration-rotate.png">

Note that `text-decoration` would be converted into paths and become a part of
the whole text content. Basically, the `text` element would become a group
with paths.

As you can see, Safari has an incorrect gradient position on underline.
Batik and librsvg are drawn just a line. And Inkscape simply gave up.

<br>

Finally, let's ignore all the rules and try to render the most absurd case possible.
A vertical text, partially on a circle path, with decoration defined outside the text element.

```xml
<g text-decoration="overline" fill="red">
    <text writing-mode="tb" fill="black">
        <textPath xlink:href="#path1">
            非常に長いテキ
        </textPath>
        スト。
    </text>
</g>
```

<img src="images/text-decoration-on-path.png">

Yeah... This is SVG for you. No one knows what to do.
