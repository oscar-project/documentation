# OSCAR 22.01

OSCAR 22.01 is the OSCAR version from January, 2022. 
It features a **different** file layout that makes it **not** backward compatible with code designed to run with previous OSCAR versions.

## Summary

OSCAR 22.01 is **document-oriented**, which means that rather than extracting lines and sorting them in language subcorpora, we identify documents as a whole. The main differences are that sentences in a document are contiguous and should make sense one after another, but sentences are not guaranteed to be of the subcorpus' language.

!!! note 
    As an example, the English Wikipedia page about [La Marseillaise](https://en.wikipedia.com/wiki/La_Marseillaise) contains sentences in French (The anthem's lyrics). In line-oriented corpora, these sentences would have been put in the French subcorpus. In OSCAR 22.01, they should be along with the article, in a document classified as English.

## Layout

As previous corpora, there is one subcorpus per language, plus **one new subcorpus for multilingual documents**.
Subcorpora are distributed in [JSONLines](https://jsonlines.org), split into 1GB chunks, then gzipped.

!!! note
    Splits are completely independent and self-contained: It is possible to only download `en_meta_134.jsonl.gz` and to do processing on it.

### Example document

```js
{
  "content":"newline\nseparaaaaaaaaaaated\ncontent", // (1)
  "warc_headers":{ // (2) 
    "warc-refers-to":"<urn:uuid:83f2e1d4-5ed3-41db-86ff-f7826c4c20f9>", 
    "warc-date":"2021-09-16T11:07:14Z",
    "warc-block-digest":"sha1:X3OWP47FG2O5LBNMFSNB44FJF2SSRC26",
    "warc-type":"conversion",
    "warc-identified-content-language":"eng",
    "content-length":"1694",
    "warc-target-uri":"https://foo.bar",
    "warc-record-id":"<urn:uuid:3304bc27-17d0-4ffd-a692-340381478a5f>",
    "content-type":"text/plain"
  },
  "metadata":{
    // (3)
    "identification":{
      "label":"en",
      "prob":0.6268374
    },

    // (4)
    "annotation":[
      "short_sentences",
      "footer"
    ],

    // (5)
    "sentence_identifications":[
      {
        "label":"en",
        "prob":0.93925816
      },
      null,
      {
        "label":"en",
        "prob":0.9606543
      }
    ]
  }
}

```

1. Content. Lines are separated by `\n`.
2. Headers from the crawler. Note that nothing is changed, so the content length may be incorrect.
3. Document-wide identification. `prob` is the weighted average of the confidence of identified lines.
4. Annotations of the document. `null` if no annotation.
5. Line-by-line identifications. `null` for each line that has no identification.

## Filtering

!!! tip
    Filtering can be done using [`oscar-tools`](https://github.com/oscar-corpus/oscar-tools), a high performance toolkit that provides rapid and efficient ways of transforming corpora into what you need. More info [here](oscar-tools).

Filtering can be done using classic Python tools, such as [`ujson`](https://github.com/ultrajson/ultrajson). 
While we don't supply a Python library enabling easy filtering/transformation for OSCAR 22.01, we provide some filtering examples that you can change to better suit your needs.

### Getting documents that come from wikipedia only

Using filters on `warc_headers.warc-target-uri` makes filtering on URLs easy.

```py
TODO
```

### Extracting lines from non-annotated documents

Non-annotated documents are suspected to be cleaner than annotated ones, so extracting their content should be interesting to do. We extract lines from documents where `metadata.annotations == null`.

```py
TODO
```

### Getting Alemannic lines from the German corpus

As detailed in our paper, we found that the German corpus has a (relative to the Alemannic corpus size) important amount of Alemannic. We use a filter on `metadata.sentence_identifications` to extract those sentences.

```py
TODO
```