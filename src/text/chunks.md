# Text chunks

When developing [resvg](https://github.com/RazrFalcon/resvg),
text chunks were probably one of the hardest things about text to wrap my head around.

When someone thinks about SVG text they probably imagine something like this:

```xml
<text x="5" y="10" font-family="Arial">
    <span fill="red">red</span> text
</text>
```

Basically, a text with some styles applied to it. And this is true in most cases, but it's not how
SVG text structure actually looks like. SVG has an additional, hidden structure level called
_text chunk_.
Every time there is an `x` or `y` attribute in `<text>` or `<tspan>` - a new text chunk is defined.
This means that an SVG text element actually contains a list of text chunks,
where each chunk contains text with styles applied to it.

In some sort of pseudo-code it should look like:

```python
class Text:
    chunks: list[TextChunk] = []

class TextChunk:
    text: str = ""
    spans: list[TextSpan] = []
    alignment: int = 0 # start/mid/end

class TextSpan:
    # fill, stroke, font, etc.
```

For example, this text element has two chunks: _some_ at 5,10 and _text_ at 5,20.

```xml
<text x="5" y="10" font-family="Arial">
    some
    <tspan y="20">text</tspan>
</text>
```

<br>

Why is this important? Because this is what a _line of text_ in SVG means - a chunk.<br/>
This is a _line of text_ that would be passed to a text shaper/layout,
and not the whole element's content.<br/>
This is a _line of text_ that would be aligned via `text-anchor`,
and not the whole element's content or individual `<tspan>`.<br/>
It's a very subtle nuance, but many libraries fail to handle it correctly.

For example, in this case, `text-anchor` has no effect because that `<tspan>`
doesn't define a new chunk by setting an absolute coordinate.

```xml
<text x="5" y="10" font-family="Arial">
    some
    <tspan text-anchor="end">text</tspan>
</text>
```

In theory, each text chunk can be extracted into its own `text` element, while `tspan` can't.

<br>

Also, while it's not clear from the spec, each `textPath` defines a new chunk as well.

Another interesting edge case is that there could be only one `writing-mode` per text element.
An individual chunk or span cannot have their own writing modes.
