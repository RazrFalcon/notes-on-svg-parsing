SVG Text is probably the single most complex part of the SVG.

SVG Text supports all languages; horizontal, vertical and text-on-path layouts;
right-to-left layout; filling, stroking, masking and filtering;
characters rotation; manual character positioning; automatic font matching; font fallback;
text alignment; text decorations and many many more.<br>
An SVG library simply _must_ implement its own text layout.

Some people might be surprised by this. After all, any decent 2D rendering library already includes
text rendering API. What else would you need?<br>
And the problem is that most libraries can only draw a horizontal layout,
hide some internal metadata or do not allow any kind of post-processing.
Which is not nearly enough for proper SVG Text support.

I genuinely do believe that it's simply impossible to implement SVG Text.
After all - no one did.
