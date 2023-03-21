# The root of the problem

So why it's so hard to parse and render SVG?

In my opinion, the main issue is the lack of a reference implementation.
No one actually knows how SVG suppose to work.
By writing a reference implementation, including a test suite,
a lot of issues described in this "book" would never happen.

Instead, we have Chrome. It probably supports around 80% of the spec.
Which is not that great, but the best we got so far.
And good luck figuring out how it works. It's a huge codebase
and even finding the right source file could be challenging.

As for a test suite, SVG 1.1 actually had one. A very rudimentary, but still.
And SVG 2 has some SVG-inside-HTML tests, which are impossible to use outside a browser.
I have ended up writing [my own test suite](https://github.com/RazrFalcon/resvg-test-suite).
It's far from being complete but already includes 1500 tests.
Which should give you a hint about the monstrosity we're dealing with.

Asking the SVG Working Group is not an option either.
I have [contacted them](https://github.com/w3c/svgwg/issues?q=is%3Aissue+author%3ARazrFalcon)
multiple times during `resvg` development
and the most common answer I've got was "we don't know".
Which wouldn't be a thing if we had a reference implementation.

In the end, I genuinely believe that it's simply _impossible_ to implement the whole SVG spec.
There is just too much of it.

<br>

Maybe we should _just_ use something else? You might ask. Like what?<br>
The only open-ish SVG alternative is PDF. Which is a nightmare on its own,
while being conceptually simpler than SVG, since it doesn't have
a text layout, filters/effects, animations and events.
