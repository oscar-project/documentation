# OSCAR 2301

OSCAR 2301 is the January 2023 
## Summary
## Access

TODO Access page

## New Features

### Categories

OSCAR 22.01 leveraged the [UT1 Blocklists]() project to attempt to classify some adult content present in OSCAR.
The OSCAR 23.01 pipeline iterated on this to include all of the blocklists provided by UT1.

The UT1 Blocklists page lists all the categories along with a short description. 
We **strongly** encourage you to read the descriptions if you plan on using them.

These categories are in a field that is at this path: `metadata.categories`.

A single document can have multiple categories.

### KenLM-based adult content filtering

_Work by TODO_

For a select number of subcorpora, a measure of perplexity has been added. This perplexity comes from a KenLM model trained on harmful content, previously gathered by using the `adult` annotation in OSCAR 22.01.
In other terms, the lower it is, the more likely a given document contains harmful/adult content. 

!!! warning
    This feature can be considered as unstable/unsafe, since we also want to evaluate its impact on particular issues.

    As such, we do not provide a boolean value indicating if a given document can be harmful/adult content, but rather the raw perplexity.
    We have found a threshold that works well in **English**, but encourage you to experiment with it and to report back your findings.

### Locality-Sentitive Hashing

We use [TLSH](https://tlsh.org/papers.html) to compute a hash for each document. These hashes are particular in that two similar documents should have similar hashes. 

This can enable both exact- and near- deduplication. 


### Minor changes

- `metadata.annotations` has been renamed `metadata.quality_warnings`, and only contains length based quality warnings (see TODO).

## Layout

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

## Language table

|     | Language                 | File size    | # docs   | # words   |
|----:|:-------------------------|:-------------|:---------|:----------|
|   0 | Afrikaans                | 48M          | soon     | soon      |
|   1 | Amharic                  | 598M         | soon     | soon      |
|   2 | Aragonese                | 32K          | soon     | soon      |
|   3 | Arabic                   | 129G         | soon     | soon      |
|   4 | Egyptian Arabic          | 5.5M         | soon     | soon      |
|   5 | Assamese                 | 475M         | soon     | soon      |
|   6 | Asturian                 | 256K         | soon     | soon      |
|   7 | Avaric                   | 48K          | soon     | soon      |
|   8 | South Azerbaijani        | 41M          | soon     | soon      |
|   9 | Azerbaijani              | 3.9G         | soon     | soon      |
|  10 | Bashkir                  | 433M         | soon     | soon      |
|  11 | Belarusian               | 2.7G         | soon     | soon      |
|  12 | Bulgarian                | 50G          | soon     | soon      |
|  13 | Bihari languages         | 32K          | soon     | soon      |
|  14 | Bangla                   | 22G          | soon     | soon      |
|  15 | Tibetan                  | 564M         | soon     | soon      |
|  16 | Bishnupriya              | 6.8M         | soon     | soon      |
|  17 | Breton                   | 51M          | soon     | soon      |
|  18 | Bosnian                  | 16K          | soon     | soon      |
|  19 | Russia Buriat            | 48K          | soon     | soon      |
|  20 | Catalan                  | 20G          | soon     | soon      |
|  21 | Cebuano                  | 51M          | soon     | soon      |
|  22 | Chechen                  | 19M          | soon     | soon      |
|  23 | Central Kurdish          | 895M         | soon     | soon      |
|  24 | Czech                    | 101G         | soon     | soon      |
|  25 | Chuvash                  | 52M          | soon     | soon      |
|  26 | Welsh                    | 422M         | soon     | soon      |
|  27 | Danish                   | 19G          | soon     | soon      |
|  28 | German                   | 749G         | soon     | soon      |
|  29 | Lower Sorbian            | 512          | soon     | soon      |
|  30 | Divehi                   | 247M         | soon     | soon      |
|  31 | Greek                    | 112G         | soon     | soon      |
|  32 | English                  | 4.2T         | soon     | soon      |
|  33 | Esperanto                | 629M         | soon     | soon      |
|  34 | Spanish                  | 543G         | soon     | soon      |
|  35 | Estonian                 | 11G          | soon     | soon      |
|  36 | Basque                   | 1.6G         | soon     | soon      |
|  37 | Persian                  | 110G         | soon     | soon      |
|  38 | Finnish                  | 53G          | soon     | soon      |
|  39 | French                   | 547G         | soon     | soon      |
|  40 | Western Frisian          | 94M          | soon     | soon      |
|  41 | Irish                    | 81M          | soon     | soon      |
|  42 | Scottish Gaelic          | 464K         | soon     | soon      |
|  43 | Galician                 | 366M         | soon     | soon      |
|  44 | Guarani                  | 16K          | soon     | soon      |
|  45 | Goan Konkani             | 2.6M         | soon     | soon      |
|  46 | Swiss German             | 384K         | soon     | soon      |
|  47 | Gujarati                 | 6.3G         | soon     | soon      |
|  48 | Hebrew                   | 21G          | soon     | soon      |
|  49 | Hindi                    | 36G          | soon     | soon      |
|  50 | Croatian                 | 39M          | soon     | soon      |
|  51 | Upper Sorbian            | 288K         | soon     | soon      |
|  52 | Haitian Creole           | 128K         | soon     | soon      |
|  53 | Hungarian                | 182G         | soon     | soon      |
|  54 | Armenian                 | 5.6G         | soon     | soon      |
|  55 | Interlingua              | 64K          | soon     | soon      |
|  56 | Indonesian               | 27G          | soon     | soon      |
|  57 | Interlingue              | 512          | soon     | soon      |
|  58 | Iloko                    | 96K          | soon     | soon      |
|  59 | Ido                      | 112K         | soon     | soon      |
|  60 | Icelandic                | 2.9G         | soon     | soon      |
|  61 | Italian                  | 327G         | soon     | soon      |
|  62 | Japanese                 | 228G         | soon     | soon      |
|  63 | Lojban                   | 2.0M         | soon     | soon      |
|  64 | Javanese                 | 96K          | soon     | soon      |
|  65 | Georgian                 | 9.2G         | soon     | soon      |
|  66 | Kazakh                   | 3.8G         | soon     | soon      |
|  67 | Khmer                    | 3.4G         | soon     | soon      |
|  68 | Kannada                  | 2.9G         | soon     | soon      |
|  69 | Korean                   | 46G          | soon     | soon      |
|  70 | Karachay-Balkar          | 336K         | soon     | soon      |
|  71 | Kurdish                  | 221M         | soon     | soon      |
|  72 | Komi                     | 272K         | soon     | soon      |
|  73 | Cornish                  | 512          | soon     | soon      |
|  74 | Kyrgyz                   | 581M         | soon     | soon      |
|  75 | Latin                    | 7.7M         | soon     | soon      |
|  76 | Luxembourgish            | 27M          | soon     | soon      |
|  77 | Lezghian                 | 1.2M         | soon     | soon      |
|  78 | Limburgish               | 512          | soon     | soon      |
|  79 | Lombard                  | 80K          | soon     | soon      |
|  80 | Lao                      | 538M         | soon     | soon      |
|  81 | Lithuanian               | 19G          | soon     | soon      |
|  82 | Latvian                  | 9.2G         | soon     | soon      |
|  83 | Maithili                 | 32K          | soon     | soon      |
|  84 | Malagasy                 | 16M          | soon     | soon      |
|  85 | Eastern Mari             | 28M          | soon     | soon      |
|  86 | Minangkabau              | 5.0M         | soon     | soon      |
|  87 | Macedonian               | 5.5G         | soon     | soon      |
|  88 | Malayalam                | 6.3G         | soon     | soon      |
|  89 | Mongolian                | 6.5G         | soon     | soon      |
|  90 | Western Mari             | 1008K        | soon     | soon      |
|  91 | Marathi                  | 4.9G         | soon     | soon      |
|  92 | Malay                    | 7.1M         | soon     | soon      |
|  93 | Maltese                  | 3.3M         | soon     | soon      |
|  94 | multi                    | 14G          | soon     | soon      |
|  95 | Mirandese                | 512          | soon     | soon      |
|  96 | Burmese                  | 3.3G         | soon     | soon      |
|  97 | Mazanderani              | 352K         | soon     | soon      |
|  98 | Nahuatl languages        | 32K          | soon     | soon      |
|  99 | Low German               | 14M          | soon     | soon      |
| 100 | Nepali                   | 5.4G         | soon     | soon      |
| 101 | Newari                   | 4.9M         | soon     | soon      |
| 102 | Dutch                    | 180G         | soon     | soon      |
| 103 | Norwegian Nynorsk        | 12M          | soon     | soon      |
| 104 | Norwegian                | 3.8G         | soon     | soon      |
| 105 | Occitan                  | 784K         | soon     | soon      |
| 106 | Odia                     | 642M         | soon     | soon      |
| 107 | Ossetic                  | 57M          | soon     | soon      |
| 108 | Punjabi                  | 1.6G         | soon     | soon      |
| 109 | Polish                   | 193G         | soon     | soon      |
| 110 | Piedmontese              | 4.3M         | soon     | soon      |
| 111 | Western Panjabi          | 117M         | soon     | soon      |
| 112 | Pashto                   | 318M         | soon     | soon      |
| 113 | Portuguese               | 131G         | soon     | soon      |
| 114 | Quechua                  | 512          | soon     | soon      |
| 115 | Romanian                 | 56G          | soon     | soon      |
| 116 | Russian                  | 1.3T         | soon     | soon      |
| 117 | Sakha                    | 81M          | soon     | soon      |
| 118 | Sanskrit                 | 65M          | soon     | soon      |
| 119 | Sindhi                   | 161M         | soon     | soon      |
| 120 | Serbian (Latin)          | 2.1M         | soon     | soon      |
| 121 | Sinhala                  | 2.8G         | soon     | soon      |
| 122 | Slovak                   | 27G          | soon     | soon      |
| 123 | Slovenian                | 1.9G         | soon     | soon      |
| 124 | Somali                   | 512          | soon     | soon      |
| 125 | Albanian                 | 4.1G         | soon     | soon      |
| 126 | Serbian                  | 8.7G         | soon     | soon      |
| 127 | Sundanese                | 16K          | soon     | soon      |
| 128 | Swedish                  | 65G          | soon     | soon      |
| 129 | Swahili                  | 1.9M         | soon     | soon      |
| 130 | Tamil                    | 17G          | soon     | soon      |
| 131 | Telugu                   | 4.3G         | soon     | soon      |
| 132 | Tajik                    | 1.2G         | soon     | soon      |
| 133 | Thai                     | 100G         | soon     | soon      |
| 134 | Turkmen                  | 5.2M         | soon     | soon      |
| 135 | Filipino                 | 880M         | soon     | soon      |
| 136 | Turkish                  | 93G          | soon     | soon      |
| 137 | Tatar                    | 981M         | soon     | soon      |
| 138 | Uyghur                   | 257M         | soon     | soon      |
| 139 | Ukrainian                | 52G          | soon     | soon      |
| 140 | Urdu                     | 4.3G         | soon     | soon      |
| 141 | Uzbek                    | 23M          | soon     | soon      |
| 142 | Vietnamese               | 170G         | soon     | soon      |
| 143 | Volapük                  | 800K         | soon     | soon      |
| 144 | Walloon                  | 192K         | soon     | soon      |
| 145 | Waray                    | 720K         | soon     | soon      |
| 146 | Wu Chinese               | 80K          | soon     | soon      |
| 147 | Kalmyk                   | 512          | soon     | soon      |
| 148 | Unknown language [x-eml] | 512          | soon     | soon      |
| 149 | Mingrelian               | 9.8M         | soon     | soon      |
| 150 | Yiddish                  | 208M         | soon     | soon      |
| 151 | Yoruba                   | 80K          | soon     | soon      |
| 152 | Chinese                  | 1.4T         | soon     | soon      |

``` plotly
{
    "data": [
        {
            "x": ["giraffes", "orangutans", "monkeys"],
            "y": [20, 14, 23],
            "type": "bar"
        }
    ]
}
```