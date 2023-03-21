# Intro

After working on my SVG rendering library ([resvg](https://github.com/RazrFalcon/resvg))
for the past 5 years, and working closely with SVG for the past 10, I've thought
that this is enough time to have some interesting insight worth sharing.
And there are definitely a lot to talk about.

When people think SVG, they usually imagine something like this:

```xml
<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
    <rect x="20" y="20" width="160" height="160" fill="green"/>
</svg>
```

A very simple, user-friendly, XML-based vector file format.

And while SVG is indeed designed to be easy to read and write by a human,
parsing and rendering of it are notoriously hard for a machine.
The SVG spec is 20 years old and there are still no libraries
that support even 80% of it. Yes, after 20 years one still cannot _just render_ an SVG.

Whenever I receive bug reports for my SVG library,
people often use the phrase "my SVG isn't rendering _correctly_".
Which cracks me up every time. There is no such thing as a _correctly_ rendered SVG.
As soon as you start using "advanced" features such as text, filters, or, God forbid,
animations - it would simply not work. Never.
And even if you will manage to make it work, as soon as you try a different render - it
will fall apart again.

The idea behind this "book" is to answer the popular question of "how hard can it be?".
A reader expected to have some prior knowledge about SVG, but it should be a fun read either way.

I'm trying my best not to fall into a rant, but when it comes to SVG - it's really hard.

**NOTE:** English is not my native language, so this "book" will probably be hard to digest.
And this is on top of the complexity of the subject itself.

**NOTE:** This "book" isn't finished and still work in progress.
