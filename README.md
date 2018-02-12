Zanashinar is a Unicode font supporting Zanabazar Square, encoded in Unicode 10.0.
It is a fork of [BabelStone Zanabazar](http://www.babelstone.co.uk/Fonts/Zanabazar.html)
using OpenType GSUB and GPOS extensively instead of precomposing every cluster.

## Background

It came to pass, as the Mongols came up from the east,
that they found a plain in the land of Shinar; and they dwelt there.<br>
And they said one to another, Go to, let us build us a temple and a dhāraṇī pillar;
and let us devise us an orthography, lest we add unto the sūtras, or lest we diminish them.<br>
And the devas came down to see the temple and the dhāraṇī pillar,
which the children of the east builded.<br>
And the devas said, Behold, the people is one, and they have all one script;
and it must not be so done, to make known the sūtras before the day of the Śākyamuni’s coming.<br>
Go to, let us go down, and there confound their script,
that they may not understand one another’s writings.<br>
So the devas came unto the monks of the Mongols in dreams;
and each monk awoke, and created a new script.<br>
And Phagpa created the ’Phags-pa script;
and Zanabazar created the horizontal square script.<br>
And Zaya Pandita created the clear script;
and the Soyombo script created itself.

## The font

Zanashinar is nearly the same, from a user’s perspective, as the original font.
The differences are mainly internal.
Zanashinar contains 270 glyphs, which it combines using many GSUB and GPOS lookups;
BabelStone Zanabazar contains 3366 glyphs and a single 'ccmp' lookup.
Building clusters out of multiple glyphs is more flexible and, I think, easier for the developer.

### How it works

Every Zanabazar Square character has a nominal glyph
and may have some contextual alternative glyphs.
Every combining character has a mark-to-base anchor,
and many have mark-to-mark anchors for chaining marks together.

### Script and language

The script is 'DFLT', for compatibility with shapers that have not been updated to Unicode 10.0,
and to avoid the enforced order of combining marks,
as no order has yet been specified for Zanabazar Square.
The language systems are 'dflt' and 'SAN '.

### GSUB

All the GSUB lookups use the features 'ccmp', 'rclt', or 'rlig'.
There is no practical difference between them; it’s just for developer convenience.
The main thing is that they are all required so it is safe to assume they are enabled.

The first GSUB lookup adds a U+11A47 ZANABAZAR SQUARE SUBJOINER after every
U+11A3A ZANABAZAR SQUARE CLUSTER-INITIAL LETTER RA.
This makes it easier to create the subjoined consonants later,
since this ensures that every subjoined consonant follows a subjoiner.

The next lookup substitutes U+11A3E ZANABAZAR SQUARE CLUSTER-FINAL LETTER VA
with a form that looks better below glyphs with ink at the bottom right.
Most glyphs do; an exception is U+11A14 ZANABAZAR SQUARE LETTER TTA.

Then, to support the head letters *la* and *sa* as used in Tibetan,
those glyphs are substituted with half-height head forms.

The next lookup adds wide forms of certain below-base marks
when U+11A00 ZANABAZAR SQUARE LETTER A is at the bottom of a stack.
These marks are the vowels U+11A02 and U+11A03 and the killers U+11A33 and U+11A34.
So that marks above, which may separate U+11A00 from the mark below, do not interfere,
the lookup uses the mark class "below" consisting of the two vowels below.
It would be simple to add the two killers to the mark class, but it is not necessary,
because they never cooccur with marks above.
Incidentally, the killers don’t really have wide forms:
they are just replaced with glyphs that are placed further to the left,
which isn’t really the cleanest technique, but it was easier to implement that way.

The subjoined consonant glyphs are formed from subjoiners and consonants.

Above-base vowels get wide forms when *a* is at the top of the stack.
Similar to the other wide form lookup, this one uses the mark class "above".
The two lookups must be separated by the subjoined consonant lookup because
below-base marks should be widened after either the base form of *a* or its subjoined form,
whereas above-base marks should be widened only after its base form.
Putting the below-base widening first makes that lookup slightly simpler
since it only needs to consider the base form of *a*.

Some consonants have special half-height forms following head letters.

Any remaining subjoiners are replaced with U+200B ZERO WIDTH SPACE to avoid a visible glyph.

Four consonants have a kind of tsa ’phru which goes all droopy in the presence of *ai*.
This lookup uses a mark class so that intervening marks have no effect.

Two vowels, plus their wide forms, are truncated in the presence of *ai* or *au*.
The vowel mark may precede or follow the semivowel mark.
This lookup uses a mark set.
The difference between a mark class and a mark set is not really important.

### GPOS

The first six GPOS lookups are three pairs,
the first of which is mark-to-base and the second of which is mark-to-mark.
The 'mark' lookup must precede the associated 'mkmk' lookup
so that the latter can have the final say.
The pairs are for marks above, consonants below, and vowels below,
where “consonant below” includes the vowel length mark but not the cluster-final consonants.
This is because the vowel length mark attaches to the bottom consonant,
not including cluster-final consonants and ignoring any intervening vowel marks.

The 'mkmk' lookups use mark sets to ignore irrelevant marks.
In general, a 'mkmk' lookup should have a mark class or set
containing all the relevant marks and base marks.

The next lookup adds blank space on the left of narrow consonants
in the presence of a subjoined U+11A00, which is wider.
Naturally, this uses a mark class to ignore intervening subjoined consonants.

### Differences from BabelStone Zanabazar

There are two intentional user-visible differences from BabelStone Zanabazar.

The lookup that substitutes head forms of *la* and *sa* is disabled in Sanskrit,
since the head forms are apparently only used in Tibetan stacks.
The Tibetan behavior remains the default for the font.

All arbitrary stacks with any number of subjoined consonants are supported,
not just those that are attested.

## Unicode encoding issues

Unicode, unfortunately, does not specify the order of combining marks in Zanabazar Square.
The only real ambiguity is where to put the vowel length mark.
Perhaps it should go after vowel marks and before semivowel marks, as specified in
[the Unicode proposal](http://www.unicode.org/L2/L2015/15337-zanabazar-square.pdf).
Perhaps it should go immediately after the subjoined consonants, as in Tibetan.
Ideally, Unicode would specify and the renderer would insert dotted circles in all the wrong orders;
but, for lack of a spec, the font fully supports both orders.

There is also the matter of how to encode the variants of *ai* and *au*
that look like double *e* and *o*.
The Unicode proposal explicitly calls them glyph variants of
U+11A07 ZANABAZAR SQUARE VOWEL SIGN AI and U+11A08 ZANABAZAR SQUARE VOWEL SIGN AU,
but that seems so obviously wrong.
Both BabelStone Zanabazar and Zanashinar encode them as double vowel signs.

## Font format

Much like the Mongols of old, modern font technology engineers keep tweaking and innovating.
There are innumerable hundreds of thousands of myriads of font formats.
The only one that matters is OpenType.
An OpenType font may have TrueType outlines or PostScript outlines.
TrueType outlines are most appropriate for Zanabazar Square.
PostScript outlines are most appropriate for Zanabazar Cubic.
The release of Zanashinar uses TrueType outlines.
