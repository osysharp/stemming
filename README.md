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
