# OSCAR 23.01

OSCAR 23.01 is the January 2023 version of the OSCAR Corpus based on the [November/December 2022 dump of Common Crawl](https://commoncrawl.org/2022/12/nov-dec-2022-crawl-archive-now-available/). While being quite similar to OSCAR 22.01, it contains several new features, including [KenLM](https://kheafield.com/code/kenlm/)-based adult content detection, precomputed [Locality-Sensitive Hashes](https://fr.wikipedia.org/wiki/Locality_sensitive_hashing) for near deduplication, and [blocklist](https://dsi.ut-capitole.fr/blacklists/index_en.php)-based categories. OSCAR 23.01 has also moved from gzip to [Zstandard compression](https://facebook.github.io/zstd/). You might already have `zstd` installed on your system, but if not, please check the [Zstandard website](https://facebook.github.io/zstd/) for installation instructions.

## Access

!!! note
    If you already have access to the corpus, there's nothing to do!
    Go up in the file hierarchy on the link you've been given, and you should find the new corpus.

Access to the OSCAR Corpus changes depending on your status. More info on our dedicated page.

[Getting access](../../accessing){ .md-button }

## New Features

### Categories

OSCAR 22.01 leveraged the [UT1 Blocklists](https://dsi.ut-capitole.fr/blacklists/index_en.php) project to attempt to classify some adult content present in OSCAR.
The OSCAR 23.01 pipeline iterated on this to include all of the blocklists provided by UT1.

!!! warning
    The UT1 Blocklists page lists all the categories along with a short description.
    We **strongly** encourage you to read the descriptions if you plan on using them. Please also note that these descriptions are in _French_. We're working on an English translation of them.

!!! note
    A document can belong to multiple categories.

These categories are in a field that is at this path: `metadata.categories`.

!!! example
    ```js
    {
        "content":"foo",
        "metadata": {
            // ...
            "categories": ["blog", "news"],
            // ...
        }
        // ...
    }
    ```

### KenLM-based Adult Content Filtering

For a select number of subcorpora, a measure of perplexity has been added. This perplexity comes from a [KenLM](https://kheafield.com/code/kenlm/) model trained on harmful content, previously gathered by using the `adult` annotation in OSCAR 22.01.
In other terms, the lower it is, the more likely a given document contains harmful/adult content.

!!! danger
    This feature can be considered as unstable/unsafe, since we also want to evaluate its impact on particular issues.

    As such, we do not provide a boolean value indicating if a given document can be harmful/adult content, but rather the raw perplexity.
    We have found a threshold that works well in **English**, but encourage you to experiment with it and to report back your findings.

### Locality Sentitive Hashing

We use [TLSH](https://tlsh.org/papers.html) to compute a hash for each document.

[Locality sensitive hashing](https://en.wikipedia.org/wiki/Locality-sensitive_hashing) is a hashing method that computes similar hashes for similar documents.

This can be used to do both exact- and near- deduplication.

Hashes are at `metadata.tlsh`.

### Minor changes

- `metadata.annotations` has been renamed `metadata.quality_warnings`, and only contains length based quality warnings (see the [OSCAR 2201](versions/oscar-2201/#annotations) documentation for details).
- Some language tags have changed to better respect the [BCP47](https://en.wikipedia.org/wiki/IETF_language_tag):
    - `als` has become `gsw`. Previously, `als` was erroneously used as the tag for Alemannic/Swiss German, whereas it is the tag for Tosk Albanian.
    - `eml` has become `x-eml`. The `eml` tag is deprecated and as such has been replaced by a private tag (`x-eml`).

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

Some important notes:

1. Newline-separated content.
2. Headers from the crawled dumps are left untouched. See the [WARC specification](https://iipc.github.io/warc-specifications/specifications/warc-format/warc-1.1/#named-fields) for more info.
3. Since `warc_headers` are copied and content can be altered by [Ungoliant](TODO), `content-length` and `warc-block-digest` can be different from actual values.
4. Document-level identification. Computation details can be found on the [OSCAR 22.01 paper](https://aclanthology.org/2022.lrec-1.463/).
5. Perplexity of the document, computed using a KenLM model trained on harmful content. See [this pre-print](https://arxiv.org/abs/2212.10440) for more info. The lower this number is, the higher the probability that it will contain harmful or adult content. This annotation will be changed from `harmful_pp` to `harmful_ppl`in future releases.
6. Locality Sensitive Hash of the documents' content, using [TLSH](https://tlsh.org/). Useful for both exact and near deduplication.
7. _(Corresponds to `annotations` pre-23.01)_ Potential quality warnings. Based on content/sentence length. See [[OSCAR 22.01 paper](https://aclanthology.org/2022.lrec-1.463/) for more info.
8. Blocklist-based categories. Uses the [UT1 Blocklist](https://dsi.ut-capitole.fr/blacklists/index_en.php), plus custom additions (TODO). Please refer to the UT1 website for categories description. Note that the descriptions are in French.
9. Sentence-level identifications. A `null` value means no identification with a good enough threshold (>0.8 on 23.01).

## Language table

|     | Language                 | Code        | Size         | # docs   | # words   |
|----:|:-------------------------|:------------|:-------------|:---------|:----------|
|   0 | Afrikaans                | af          | 48M          | soon     | soon      |
|   1 | Amharic                  | am          | 598M         | soon     | soon      |
|   2 | Aragonese                | an          | 32K          | soon     | soon      |
|   3 | Arabic                   | ar          | 129G         | soon     | soon      |
|   4 | Egyptian Arabic          | arz         | 5.5M         | soon     | soon      |
|   5 | Assamese                 | as          | 475M         | soon     | soon      |
|   6 | Asturian                 | ast         | 256K         | soon     | soon      |
|   7 | Avaric                   | av          | 48K          | soon     | soon      |
|   8 | South Azerbaijani        | azb         | 41M          | soon     | soon      |
|   9 | Azerbaijani              | az          | 3.9G         | soon     | soon      |
|  10 | Bashkir                  | ba          | 433M         | soon     | soon      |
|  11 | Belarusian               | be          | 2.7G         | soon     | soon      |
|  12 | Bulgarian                | bg          | 50G          | soon     | soon      |
|  13 | Bihari languages         | bh          | 32K          | soon     | soon      |
|  14 | Bangla                   | bn          | 22G          | soon     | soon      |
|  15 | Tibetan                  | bo          | 564M         | soon     | soon      |
|  16 | Bishnupriya              | bpy         | 6.8M         | soon     | soon      |
|  17 | Breton                   | br          | 51M          | soon     | soon      |
|  18 | Bosnian                  | bs          | 16K          | soon     | soon      |
|  19 | Russia Buriat            | bxr         | 48K          | soon     | soon      |
|  20 | Catalan                  | ca          | 20G          | soon     | soon      |
|  21 | Cebuano                  | ceb         | 51M          | soon     | soon      |
|  22 | Chechen                  | ce          | 19M          | soon     | soon      |
|  23 | Central Kurdish          | ckb         | 895M         | soon     | soon      |
|  24 | Czech                    | cs          | 101G         | soon     | soon      |
|  25 | Chuvash                  | cv          | 52M          | soon     | soon      |
|  26 | Welsh                    | cy          | 422M         | soon     | soon      |
|  27 | Danish                   | da          | 19G          | soon     | soon      |
|  28 | German                   | de          | 749G         | soon     | soon      |
|  29 | Lower Sorbian            | dsb         | 512          | soon     | soon      |
|  30 | Divehi                   | dv          | 247M         | soon     | soon      |
|  31 | Greek                    | el          | 112G         | soon     | soon      |
|  32 | English                  | en          | 4.2T         | soon     | soon      |
|  33 | Esperanto                | eo          | 629M         | soon     | soon      |
|  34 | Spanish                  | es          | 543G         | soon     | soon      |
|  35 | Estonian                 | et          | 11G          | soon     | soon      |
|  36 | Basque                   | eu          | 1.6G         | soon     | soon      |
|  37 | Persian                  | fa          | 110G         | soon     | soon      |
|  38 | Finnish                  | fi          | 53G          | soon     | soon      |
|  39 | French                   | fr          | 547G         | soon     | soon      |
|  40 | Western Frisian          | fy          | 94M          | soon     | soon      |
|  41 | Irish                    | ga          | 81M          | soon     | soon      |
|  42 | Scottish Gaelic          | gd          | 464K         | soon     | soon      |
|  43 | Galician                 | gl          | 366M         | soon     | soon      |
|  44 | Guarani                  | gn          | 16K          | soon     | soon      |
|  45 | Goan Konkani             | gom         | 2.6M         | soon     | soon      |
|  46 | Swiss German             | gsw         | 384K         | soon     | soon      |
|  47 | Gujarati                 | gu          | 6.3G         | soon     | soon      |
|  48 | Hebrew                   | he          | 21G          | soon     | soon      |
|  49 | Hindi                    | hi          | 36G          | soon     | soon      |
|  50 | Croatian                 | hr          | 39M          | soon     | soon      |
|  51 | Upper Sorbian            | hsb         | 288K         | soon     | soon      |
|  52 | Haitian Creole           | ht          | 128K         | soon     | soon      |
|  53 | Hungarian                | hu          | 182G         | soon     | soon      |
|  54 | Armenian                 | hy          | 5.6G         | soon     | soon      |
|  55 | Interlingua              | ia          | 64K          | soon     | soon      |
|  56 | Indonesian               | id          | 27G          | soon     | soon      |
|  57 | Interlingue              | ie          | 512          | soon     | soon      |
|  58 | Iloko                    | ilo         | 96K          | soon     | soon      |
|  59 | Ido                      | io          | 112K         | soon     | soon      |
|  60 | Icelandic                | is          | 2.9G         | soon     | soon      |
|  61 | Italian                  | it          | 327G         | soon     | soon      |
|  62 | Japanese                 | ja          | 228G         | soon     | soon      |
|  63 | Lojban                   | jbo         | 2.0M         | soon     | soon      |
|  64 | Javanese                 | jv          | 96K          | soon     | soon      |
|  65 | Georgian                 | ka          | 9.2G         | soon     | soon      |
|  66 | Kazakh                   | kk          | 3.8G         | soon     | soon      |
|  67 | Khmer                    | km          | 3.4G         | soon     | soon      |
|  68 | Kannada                  | kn          | 2.9G         | soon     | soon      |
|  69 | Korean                   | ko          | 46G          | soon     | soon      |
|  70 | Karachay-Balkar          | krc         | 336K         | soon     | soon      |
|  71 | Kurdish                  | ku          | 221M         | soon     | soon      |
|  72 | Komi                     | kv          | 272K         | soon     | soon      |
|  73 | Cornish                  | kw          | 512          | soon     | soon      |
|  74 | Kyrgyz                   | ky          | 581M         | soon     | soon      |
|  75 | Latin                    | la          | 7.7M         | soon     | soon      |
|  76 | Luxembourgish            | lb          | 27M          | soon     | soon      |
|  77 | Lezghian                 | lez         | 1.2M         | soon     | soon      |
|  78 | Limburgish               | li          | 512          | soon     | soon      |
|  79 | Lombard                  | lmo         | 80K          | soon     | soon      |
|  80 | Lao                      | lo          | 538M         | soon     | soon      |
|  81 | Lithuanian               | lt          | 19G          | soon     | soon      |
|  82 | Latvian                  | lv          | 9.2G         | soon     | soon      |
|  83 | Maithili                 | mai         | 32K          | soon     | soon      |
|  84 | Malagasy                 | mg          | 16M          | soon     | soon      |
|  85 | Eastern Mari             | mhr         | 28M          | soon     | soon      |
|  86 | Minangkabau              | min         | 5.0M         | soon     | soon      |
|  87 | Macedonian               | mk          | 5.5G         | soon     | soon      |
|  88 | Malayalam                | ml          | 6.3G         | soon     | soon      |
|  89 | Mongolian                | mn          | 6.5G         | soon     | soon      |
|  90 | Western Mari             | mrj         | 1008K        | soon     | soon      |
|  91 | Marathi                  | mr          | 4.9G         | soon     | soon      |
|  92 | Malay                    | ms          | 7.1M         | soon     | soon      |
|  93 | Maltese                  | mt          | 3.3M         | soon     | soon      |
|  94 | Maltese                  | multi       | 14G          | soon     | soon      |
|  95 | Mirandese                | mwl         | 512          | soon     | soon      |
|  96 | Burmese                  | my          | 3.3G         | soon     | soon      |
|  97 | Mazanderani              | mzn         | 352K         | soon     | soon      |
|  98 | Nahuatl languages        | nah         | 32K          | soon     | soon      |
|  99 | Low German               | nds         | 14M          | soon     | soon      |
| 100 | Nepali                   | ne          | 5.4G         | soon     | soon      |
| 101 | Newari                   | new         | 4.9M         | soon     | soon      |
| 102 | Dutch                    | nl          | 180G         | soon     | soon      |
| 103 | Norwegian Nynorsk        | nn          | 12M          | soon     | soon      |
| 104 | Norwegian                | no          | 3.8G         | soon     | soon      |
| 105 | Occitan                  | oc          | 784K         | soon     | soon      |
| 106 | Odia                     | or          | 642M         | soon     | soon      |
| 107 | Ossetic                  | os          | 57M          | soon     | soon      |
| 108 | Punjabi                  | pa          | 1.6G         | soon     | soon      |
| 109 | Polish                   | pl          | 193G         | soon     | soon      |
| 110 | Piedmontese              | pms         | 4.3M         | soon     | soon      |
| 111 | Western Panjabi          | pnb         | 117M         | soon     | soon      |
| 112 | Pashto                   | ps          | 318M         | soon     | soon      |
| 113 | Portuguese               | pt          | 131G         | soon     | soon      |
| 114 | Quechua                  | qu          | 512          | soon     | soon      |
| 115 | Romanian                 | ro          | 56G          | soon     | soon      |
| 116 | Russian                  | ru          | 1.3T         | soon     | soon      |
| 117 | Sakha                    | sah         | 81M          | soon     | soon      |
| 118 | Sanskrit                 | sa          | 65M          | soon     | soon      |
| 119 | Sindhi                   | sd          | 161M         | soon     | soon      |
| 120 | Serbian (Latin)          | sh          | 2.1M         | soon     | soon      |
| 121 | Sinhala                  | si          | 2.8G         | soon     | soon      |
| 122 | Slovak                   | sk          | 27G          | soon     | soon      |
| 123 | Slovenian                | sl          | 1.9G         | soon     | soon      |
| 124 | Somali                   | so          | 512          | soon     | soon      |
| 125 | Albanian                 | sq          | 4.1G         | soon     | soon      |
| 126 | Serbian                  | sr          | 8.7G         | soon     | soon      |
| 127 | Sundanese                | su          | 16K          | soon     | soon      |
| 128 | Swedish                  | sv          | 65G          | soon     | soon      |
| 129 | Swahili                  | sw          | 1.9M         | soon     | soon      |
| 130 | Tamil                    | ta          | 17G          | soon     | soon      |
| 131 | Telugu                   | te          | 4.3G         | soon     | soon      |
| 132 | Tajik                    | tg          | 1.2G         | soon     | soon      |
| 133 | Thai                     | th          | 100G         | soon     | soon      |
| 134 | Turkmen                  | tk          | 5.2M         | soon     | soon      |
| 135 | Filipino                 | tl          | 880M         | soon     | soon      |
| 136 | Turkish                  | tr          | 93G          | soon     | soon      |
| 137 | Tatar                    | tt          | 981M         | soon     | soon      |
| 138 | Uyghur                   | ug          | 257M         | soon     | soon      |
| 139 | Ukrainian                | uk          | 52G          | soon     | soon      |
| 140 | Urdu                     | ur          | 4.3G         | soon     | soon      |
| 141 | Uzbek                    | uz          | 23M          | soon     | soon      |
| 142 | Vietnamese               | vi          | 170G         | soon     | soon      |
| 143 | Volapük                  | vo          | 800K         | soon     | soon      |
| 144 | Walloon                  | wa          | 192K         | soon     | soon      |
| 145 | Waray                    | war         | 720K         | soon     | soon      |
| 146 | Wu Chinese               | wuu         | 80K          | soon     | soon      |
| 147 | Kalmyk                   | xal         | 512          | soon     | soon      |
| 148 | Unknown language [x-eml] | x-eml       | 512          | soon     | soon      |
| 149 | Mingrelian               | xmf         | 9.8M         | soon     | soon      |
| 150 | Yiddish                  | yi          | 208M         | soon     | soon      |
| 151 | Yoruba                   | yo          | 80K          | soon     | soon      |
| 152 | Chinese                  | zh          | 1.4T         | soon     | soon      |
