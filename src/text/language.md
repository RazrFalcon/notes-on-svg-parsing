# Language

When a text has characters not present in a specified font family(ies),
an SVG library would try to fallback to a _random_ font that contains those characters.
This process is called _font fallback_. It's completely undefined
and it's a rabbit hole on its own.

The problem is that a single Unicode character can have different meanings depending on the language.
It's not an issue for most languages but can become one
for Chinese derivatives (aka [CJK](https://en.wikipedia.org/wiki/CJK_characters)).

To force the text language, SVG uses the `xml:lang` attribute:

```xml
<g font-family="sans-serif" font-size="32">
    <!-- Default (will usually fallback to Chinese) -->
    <text x="100" y="58" text-anchor="middle">刃直海角骨入</text>
    <!-- Japanese -->
    <text x="100" y="108" text-anchor="middle" xml:lang="ja">刃直海角骨入</text>
    <!-- Traditional Chinese -->
    <text x="100" y="158" text-anchor="middle" xml:lang="zh-HANT">刃直海角骨入</text>
</g>
```

Here we define `font-family` to be essentially anything and we have three
text elements with exactly the same content, but different languages.

Based on my tests, only Chrome, Firefox and librsvg do handle `xml:lang`
and the expected output should look something like this:

<p align="center">
<img src="images/text-language.png" width="200"/>
</p>

As you can see, while our Unicode strings are identical, they are rendered differently.<br>
On macOS, Chrome would fallback to the following fonts:

- PingFang SC for the "default" language
- Hiragino Kaku Gothic ProN for Japanese
- PingFang TC for Traditional Chinese

And while the actual "style" of a glyph can be different depending on a font,
the hieroglyph itself should always be the same.

<br>

Note: This chapter was inspired by
[this article](https://heistak.github.io/your-code-displays-japanese-wrong/).
