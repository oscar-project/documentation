# OSCAR Quickstart

## What is OSCAR?
OSCAR is a collection of web-based multilingual corpus of several terabytes, containing subcorpora in more than 150 languages.

Each OSCAR Corpus has a version name that tells you its approximate generation time, which usually coincides with the source crawl time. 
The latest OSCAR Corpus is **OSCAR 2301**. 
We advise you to always use the latest version, as we incrementally include new features that enables new ways of filtering the corpus for your applications.

## Basic data layout

OSCAR is, since **OSCAR 2109**, **document-oriented**, which means that subcorpora are comprised of documents rather than individual lines. 

**This has important implications as to how to preprocess the data:**

You can (and will) find sentences in other languages than the one you're interested in. For example, it is expected to encounter **English** sentences in documents from the **French** subcorpus.
  
!!! example
    The Wikipedia article about the French anthem, [La Marseillaise](https://en.wikipedia.org/wiki/La_Marseillaise), contains its lyrics in French. 
    As such, this article is expected to be present in the **English** subcorpus with those **French** lyrics.

    The good news is that you can easily remove those sentences if you are not interested in them, thanks to the metadata provided alongside the main content.

OSCAR is distributed in [JSONLines](https://jsonlines.org/) files, usually compressed ([`gzip`](https://www.gnu.org/software/gzip/), [`zstd`](https://facebook.github.io/zstd/) depending on the version).

Each line of a file is a JSON Object representing a single document.
Here is an example from OSCAR 2301:

```js
{
   "content":"English sentence\nphrase en français\n????????????", // (1)
   "warc_headers":{ // (2)
      "warc-identified-content-language":"fra,eng",
      "warc-target-uri":"https://fr.wikipedia.org/wiki/...",
      "warc-record-id":"<urn:uuid:29eaa920-d299-4b1d-b687-c72bd8d68116>",
      "warc-type":"conversion",
      "content-length":"35298", // (3)
      "warc-refers-to":"<urn:uuid:39e42055-0d94-4e45-9c6c-9e7056635d64>",
      "warc-block-digest":"sha1:WFH2A5WHCS2H365GIAFYQPI7UOAMFGHB", // (3)
      "warc-date":"2022-11-26T09:45:47Z",
      "content-type":"text/plain"
   },
   "metadata":{
      "identification":{ // (4)
         "label":"fr",
         "prob":0.8938327
      },
      "harmful_pp":4063.1814, // (5)
      "tlsh":"tlsh:T125315FF2B6088901EEA097015DB39B4600B...", // (6)
      "quality_warnings":[ // (7)
         "short_sentences",
         "header",
         "footer"
      ],
      "categories":[ // (8)
         "examen_pix",
         "liste_bu"
      ],
      "sentence_identifications":[ // (9)
         {
            "label":"fr",
            "prob":0.99837273
         },
         {
            "label":"en",
            "prob":0.9992377
         },
         null
      ]
   }
}
```

1. Newline-separated content.
2. Headers from the crawled dumps, untouched. See the [WARC specification](https://iipc.github.io/warc-specifications/specifications/warc-format/warc-1.1/#named-fields) for more info.
3. Since `warc_headers` are copied and content can be altered by [Ungoliant](TODO), `content-length` and `warc-block-digest` can be different from actual values.
4. Document-level identification. Computation details can be found [here](todo).
5. TODO
6. Locality Sensitive Hash of the documents' content, using [TLSH](https://tlsh.org/). Useful for both exact and near deduplication.
7. _(Corresponds to `annotations` pre-2301)_ Potential quality warnings. Based on content/sentence length. See [here]() for more info.
8. Blocklist-bsaed categories. Uses the [UT1 Blocklist](https://dsi.ut-capitole.fr/blacklists/index_en.php), plus custom additions (TODO). Please refer to the UT1 website for categories description.
9. Sentence-level identifications. A `null` value means no identification with a good enough threshold (>0.8 on 2301).


## Getting access

There are different ways of getting access to OSCAR depending on your status! Head on to our [dedicated page](./accessing.md).

## Using the corpus

TODO say to head on to examples