# fill-rule

A typical SVG renderer doesn't actually render text directly to the canvas,
like a GUI library would, but rather converts it into paths and then renders them.
After all, a text in SVG is basically a fancy way to define paths.

The edge case that arises when treating text as paths are that it must not be affected
by the `fill-rule` property. It might not affect a typical Latin text, but in complex scripts,
it might lead to bugs like this:

<p align="center">
<img src="images/text-fill-rule.png" width="250"/>
</p>

And before you think that no one would make such an obvious mistake,
the image above was produced by Inkscape 1.2 (2022).
