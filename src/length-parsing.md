# Length parsing

The [length type](https://www.w3.org/TR/css-values/#lengths) is essentially
a number + unit pair. Like `10mm` or `5%`. Nothing too crazy.

From the parsing perspective it doesn't look that hard either.
Most languages have something like a `string_to_number` function,
which in most cases would return the position of the last parsed digit,
making the unit suffix parsing trivial.

The problem is that we cannot use those functions in SVG. And CSS in general.
Why? Because of font units, aka `em` and `ex`.

Remember that SVG/CSS allows exponent notion in numbers.
Meaning that `1e5` is a perfectly valid number.
Now, if we try to parse `5em`, there is a great chance that our language-provided
function would fail, because this is not the correct exponent notation,
but still a valid SVG length. Same with `1e5em`.

Because of this, most SVG parsers had to implement a custom numbers parsing.
And usually it's enough to simply have a function that would properly split
length into number (string representation) and suffix. And we can then pass
the separated number to the `string_to_number` function of choice.
