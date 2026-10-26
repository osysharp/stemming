# Osysharp.Stemming

**Search that understands word endings.** A Swedish reader who types "betalningen" finds the article about
"betalningar"; an Arabic reader's "بالبطاقة" finds "البطاقات". Words are put in their base form, per language, on both
sides of a search.

## Use it

```osy
app Help {
  use Osysharp.Stemming@0;
  model "model/**/*.osy";
}
```

```osy
using Osysharp.Stemming;

var s = new Stemmer();
s.StemText("Så betalar du dina fakturor", "sv");   // "så betal du din faktur"
s.Stem("البطاقات", "ar") == s.Stem("بالبطاقة", "ar");  // true
```

Keep a stemmed copy beside the text when it is written, stem the query the same way, and match either:

```osy
row.Stems = new Stemmer().StemText(row.Text, row.Language);
var qs = new Stemmer().StemText(query, readerLanguage);
Article.Where(a => a.Text.Matches(query) || a.Stems.Matches(qs));
```

## Languages

| language | how |
|---|---|
| Swedish, Danish, Norwegian | the Snowball rules for each language |
| English | a light stemmer — plurals, `-ing`, `-ed`, `-ly` |
| Arabic | a light stemmer — the article, a leading conjunction or preposition, the common suffixes, short vowel marks |
| any other | word for word, lower-cased |

Deterministic, offline and fast: a stem depends only on the word and its language, so it is written once and searched
like any other text.

## Its tests

Inflections meeting in their stem in every language it knows, a whole text stemmed in order, and words of other
languages passing through unchanged.

## Examples

Taken from this package's own tests, which compile and run on every release.

```osy
using Osysharp.Stemming;

[Test]
void swedish_inflections_meet_in_their_stem() {
  Assert.Equal("betalning", S("betalningar", "sv"));
  Assert.Equal("betalning", S("betalningen", "sv"));
  Assert.Equal("betalning", S("betalning", "sv"));
  Assert.Equal("faktur", S("fakturor", "sv"));
  Assert.Equal("faktur", S("faktura", "sv"));
  Assert.Equal(S("leveranser", "sv-SE"), S("leveransen", "sv"));
}
```

From [`tests/stems.test.osy`](tests/stems.test.osy).

```osy
using Osysharp.Stemming;

[Test]
void arabic_article_conjunction_and_suffixes_are_taken_off() {
  // "with the card" and "the cards" share a stem.
  Assert.Equal(S("البطاقات", "ar"), S("بالبطاقة", "ar"));
  // "the invoice" and "my invoice".
  Assert.Equal(S("الفاتورة", "ar"), S("فاتورتي", "ar"));
  // "and the payment" and "payment".
  Assert.Equal(S("دفع", "ar"), S("والدفع", "ar"));
  // A short vowel mark changes nothing.
  Assert.Equal(S("كتاب", "ar"), S("كِتَاب", "ar"));
}
```

From [`tests/stems.test.osy`](tests/stems.test.osy).

```osy
using Osysharp.Stemming;

[Test]
void danish_and_norwegian_inflections_meet_in_their_stem() {
  Assert.Equal(S("betalinger", "da"), S("betalingerne", "da"));
  Assert.Equal(S("betalinger", "nb"), S("betalingene", "nb"));
}
```

From [`tests/stems.test.osy`](tests/stems.test.osy).

## Learn more

- [The Osysharp.Stemming reference](https://osysharp.com/reference/ui/stemming-kit/)
- [Osysharp.Stemming in the package catalogue](https://osysharp.com/packages/#osysharp-stemming)
- [The Osy# reference](https://osysharp.com/reference/) — the language, the runtime and every kit
- [`llms-full.txt`](https://osysharp.com/llms-full.txt) — the whole reference and specification as one Markdown file, for a coding agent

## Licence

MIT — see [`LICENSE`](LICENSE).
