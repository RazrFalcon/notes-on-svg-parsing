# A character

This should be an easy one, right? A string of text consists of characters. Everyone knows that!

I think that from a user perspective, a _character_ is something you can _iterate_ on.
Press left on a keyboard and a cursor moves by _one character_. Sounds easy. Let's begin!

<!-- an ugly way to have dashes instead of dots -->
&ndash; Does A a character?<br>
&ndash; Sure!<br>
&ndash; Does Á a charter?<br>
&ndash; Sure is!<br>
&ndash; Does &#xFB01; a character?<br>
&ndash; Nope! It's two characters.<br>
&ndash; Wrong! It's a [ligature](https://en.wikipedia.org/wiki/Ligature_(writing))
and therefore a single character (can depend on a font).<br>
&ndash; Does => a character?<br>
&ndash; No?<br>
&ndash; It can be! Depending on [a font](https://github.com/tonsky/FiraCode).<br>
&ndash; Does 😃 a character?<br>
&ndash; Sure.<br>
&ndash; Does क्षि a character?<br>
&ndash; Hm...<br>
&ndash; It is!<br>
&ndash; Does द्ध्र्य a character?<br>
&ndash; Again?!<br>
&ndash; Yes! And it is.<br>
&ndash; Does ȧ̶̻̫̍̽̔ a character?<br>
&ndash; Wut?!<br>
&ndash; It is! And it's called [Zalgo](https://en.wikipedia.org/wiki/Zalgo_text).<br>

A modern text layout is a ridiculously complicated topic. It includes Unicode, TrueType fonts,
shaping and many more. But all we care for now is just the definition of a _character_.

In modern typography, all _characters_ above are called
[grapheme clusters](https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries).
This is what _a character_ is from a computer perspective. And what SVG uses as well.

A grapheme cluster is not a Unicode code point (like UTF-32), but rather a collection of code points.
And to get grapheme clusters from a string we would have to pass it, along with a font, to
[a shaper](https://harfbuzz.github.io/what-is-harfbuzz.html).

<br>

Ok, so an SVG character is a grapheme cluster. Needs some time to wrap your head around
and study modern typography, but looks simple enough. Wrong!

First, SVG 1.1 doesn't actually define what a character is. Seriously.
If you open [the spec](https://www.w3.org/TR/SVG11/text.html) there are no mentions of it.<br>
Luckily, SVG 2 fixed this and provided us with a
[definition](https://www.w3.org/TR/SVG2/text.html#Definitions)
of... two kinds of characters?! What?!<br>
Welcome to the world of SVG!

In SVG 2 we have an _addressable character_ aka UTF-16 code point and a _typographic character_
aka an _extended grapheme cluster_ (but not really).<br>
To quote [the spec](https://drafts.csswg.org/css-text-3/#typographic-character-unit):

> Text Segmentation defines a unit called the grapheme cluster which
approximates the typographic character. A UA must use the extended grapheme cluster (not legacy
grapheme cluster), as defined in UAX29, as the basis for its typographic character unit.
However, the UA should tailor the definitions as required by typographic tradition since
the default rules are not always appropriate or ideal — and is expected to tailor
them differently depending on the operation as needed.

If someone knows what this mean in a human language - please let me know.

In short, all the above simply means that `x`, `y`, `dx`, `dy` and `rotate` attributes operate
on _addressable characters_ and everything else works with _typographic characters_.

Weirdly enough, characters placement along the path (in the case of `textPath`)
is done using _typographic_ and not _addressable characters_.
While technically this is still a positioning phase.
