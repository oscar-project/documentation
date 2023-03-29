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
3. Since `warc_headers` are copied and content can be altered by [Ungoliant](https://github.com/oscar-project/ungoliant) at generation stage, `content-length` and `warc-block-digest` can be different from actual values.
4. Document-level identification. Computation details can be found on the [OSCAR 22.01 paper](https://aclanthology.org/2022.lrec-1.463/).
5. Perplexity of the document, computed using a KenLM model trained on harmful content. See [this pre-print](https://arxiv.org/abs/2212.10440) for more info. The lower this number is, the higher the probability that it will contain harmful or adult content. This annotation will be changed from `harmful_pp` to `harmful_ppl`in future releases.
6. Locality Sensitive Hash of the documents' content, using [TLSH](https://tlsh.org/). Useful for both exact and near deduplication.
7. _(Corresponds to `annotations` pre-23.01)_ Potential quality warnings. Based on content/sentence length. See [[OSCAR 22.01 paper](https://aclanthology.org/2022.lrec-1.463/) for more info.
8. Blocklist-based categories. Uses the [UT1 Blocklist](https://dsi.ut-capitole.fr/blacklists/index_en.php), plus [custom additions](https://github.com/oscar-project/oscar-blocklists). Please refer to the UT1 website for categories description. Note that the categories are in French.
9. Sentence-level identifications. A `null` value means no identification with a good enough threshold (>0.8 on 23.01).

## Language table

**Note: English is missing from the table**

|     | Code   | Language                 | # docs      | # words        | Size             |
|----:|:-------|:-------------------------|:------------|:---------------|:-----------------|
|   0 | af     | Afrikaans                | 23,994      | 6,217,024      | 37.2 MB          |
|   1 | sq     | Albanian                 | 1,342,790   | 462,694,599    | 3.2 GB           |
|   2 | am     | Amharic                  | 119,434     | 40,262,809     | 512.9 MB         |
|   3 | ar     | Arabic                   | 25,012,116  | 10,081,452,882 | 110.7 GB         |
|   4 | an     | Aragonese                | 34          | 264            | 11.0 kB          |
|   5 | hy     | Armenian                 | 1,056,974   | 336,045,041    | 4.9 GB           |
|   6 | as     | Assamese                 | 89,542      | 24,395,215     | 412.1 MB         |
|   7 | ast    | Asturian                 | 440         | 10,917         | 74.1 kB          |
|   8 | av     | Avaric                   | 44          | 1,073          | 18.6 kB          |
|   9 | az     | Azerbaijani              | 1,159,994   | 316,850,330    | 3.0 GB           |
|  10 | bn     | Bangla                   | 3,474,086   | 1,092,983,765  | 19.1 GB          |
|  11 | ba     | Bashkir                  | 128,248     | 26,036,637     | 363.7 MB         |
|  12 | eu     | Basque                   | 678,474     | 136,672,615    | 1.2 GB           |
|  13 | be     | Belarusian               | 445,612     | 164,729,607    | 2.3 GB           |
|  14 | bh     | Bihari languages         | 48          | 507            | 6.8 kB           |
|  15 | bpy    | Bishnupriya              | 2,346       | 346,947        | 5.4 MB           |
|  16 | bs     | Bosnian                  | 20          | 395            | 3.0 kB           |
|  17 | br     | Breton                   | 36,338      | 4,759,407      | 31.4 MB          |
|  18 | bg     | Bulgarian                | 8,933,998   | 3,635,273,738  | 44.1 GB          |
|  19 | my     | Burmese                  | 430,276     | 82,433,836     | 3.0 GB           |
|  20 | ca     | Catalan                  | 6,953,898   | 2,240,460,836  | 15.3 GB          |
|  21 | ceb    | Cebuano                  | 16,174      | 6,263,404      | 41.1 MB          |
|  22 | ckb    | Central Kurdish          | 182,508     | 61,334,746     | 772.9 MB         |
|  23 | ce     | Chechen                  | 11,686      | 1,051,752      | 13.9 MB          |
|  24 | zh     | Chinese                  | 138,478,270 | 44,378,380,161 | 1.4 TB           |
|  25 | cv     | Chuvash                  | 16,652      | 3,039,925      | 42.3 MB          |
|  26 | kw     | Cornish                  | 8           | 80             | 432 Bytes        |
|  27 | hr     | Croatian                 | 31,808      | 3,542,961      | 26.5 MB          |
|  28 | cs     | Czech                    | 34,859,632  | 9,717,378,559  | 77.0 GB          |
|  29 | da     | Danish                   | 7,214,338   | 2,217,634,340  | 14.8 GB          |
|  30 | dv     | Divehi                   | 77,060      | 10,655,359     | 200.1 MB         |
|  31 | nl     | Dutch                    | 72,552,688  | 19,564,553,306 | 135.0 GB         |
|  32 | mhr    | Eastern Mari             | 9,502       | 1,615,215      | 22.9 MB          |
|  33 | arz    | Egyptian Arabic          | 3,958       | 385,511        | 3.7 MB           |
|  34 | eo     | Esperanto                | 226,924     | 67,774,923     | 474.8 MB         |
|  35 | et     | Estonian                 | 3,601,904   | 938,296,892    | 8.0 GB           |
|  36 | tl     | Filipino                 | 250,558     | 110,560,444    | 719.2 MB         |
|  37 | fi     | Finnish                  | 14,471,710  | 4,198,143,883  | 41.1 GB          |
|  38 | fr     | French                   | 158,334,998 | 62,127,088,294 | 430.5 GB         |
|  39 | gl     | Galician                 | 248,762     | 38,345,625     | 255.7 MB         |
|  40 | ka     | Georgian                 | 1,343,036   | 373,935,158    | 8.4 GB           |
|  41 | de     | German                   | 206,598,430 | 73,848,586,648 | 594.7 GB         |
|  42 | gom    | Goan Konkani             | 398         | 121,035        | 2.3 MB           |
|  43 | el     | Greek                    | 20,282,864  | 7,691,622,692  | 95.7 GB          |
|  44 | gn     | Guarani                  | 14          | 260            | 2.2 kB           |
|  45 | gu     | Gujarati                 | 425,552     | 417,001,705    | 5.6 GB           |
|  46 | ht     | Haitian Creole           | 2           | 20,671         | 93.1 kB          |
|  47 | he     | Hebrew                   | 3,997,888   | 1,697,158,891  | 18.0 GB          |
|  48 | hi     | Hindi                    | 5,514,454   | 2,475,605,444  | 32.6 GB          |
|  49 | hu     | Hungarian                | 21,349,372  | 16,013,364,289 | 150.1 GB         |
|  50 | is     | Icelandic                | 1,210,232   | 294,471,539    | 2.2 GB           |
|  51 | io     | Ido                      | 224         | 2,598          | 16.1 kB          |
|  52 | ilo    | Iloko                    | 144         | 4,411          | 28.0 kB          |
|  53 | id     | Indonesian               | 7,109,778   | 3,228,020,221  | 23.4 GB          |
|  54 | ia     | Interlingua              | 34          | 9,384          | 33.5 kB          |
|  55 | ie     | Interlingue              | 2           | 0              | 881 Bytes        |
|  56 | ga     | Irish                    | 29,894      | 9,054,923      | 63.2 MB          |
|  57 | it     | Italian                  | 89,021,606  | 36,327,274,203 | 259.4 GB         |
|  58 | ja     | Japanese                 | 94,236,404  | 4,401,059,165  | 181.2 GB         |
|  59 | jv     | Javanese                 | 172         | 3,286          | 25.7 kB          |
|  60 | xal    | Kalmyk                   | 2           | 27             | 315 Bytes        |
|  61 | kn     | Kannada                  | 448,500     | 124,924,350    | 2.6 GB           |
|  62 | krc    | Karachay-Balkar          | 496         | 8,385          | 122.4 kB         |
|  63 | kk     | Kazakh                   | 677,622     | 214,679,857    | 3.3 GB           |
|  64 | km     | Khmer                    | 450,660     | 59,880,231     | 3.2 GB           |
|  65 | kv     | Komi                     | 460         | 5,909          | 70.3 kB          |
|  66 | ko     | Korean                   | 15,147,698  | 3,435,866,935  | 38.1 GB          |
|  67 | ku     | Kurdish                  | 80,338      | 25,921,607     | 174.1 MB         |
|  68 | ky     | Kyrgyz                   | 144,288     | 32,062,783     | 489.3 MB         |
|  69 | lo     | Lao                      | 118,374     | 10,659,203     | 472.1 MB         |
|  70 | la     | Latin                    | 14,384      | 307,865        | 2.0 MB           |
|  71 | lv     | Latvian                  | 2,435,882   | 845,459,899    | 7.4 GB           |
|  72 | lez    | Lezghian                 | 676         | 60,634         | 856.6 kB         |
|  73 | li     | Limburgish               | 6           | 169            | 1.4 kB           |
|  74 | lt     | Lithuanian               | 5,182,028   | 1,674,362,574  | 14.5 GB          |
|  75 | jbo    | Lojban                   | 572         | 312,315        | 1.5 MB           |
|  76 | lmo    | Lombard                  | 112         | 3,269          | 21.0 kB          |
|  77 | nds    | Low German               | 5,248       | 1,612,175      | 10.7 MB          |
|  78 | dsb    | Lower Sorbian            | 8           | 84             | 664 Bytes        |
|  79 | lb     | Luxembourgish            | 18,090      | 2,514,838      | 18.4 MB          |
|  80 | mk     | Macedonian               | 1,063,298   | 389,344,425    | 4.7 GB           |
|  81 | mai    | Maithili                 | 46          | 467            | 6.8 kB           |
|  82 | mg     | Malagasy                 | 10,830      | 1,416,430      | 11.2 MB          |
|  83 | ms     | Malay                    | 11,500      | 238,477        | 2.6 MB           |
|  84 | ml     | Malayalam                | 800,936     | 236,597,838    | 5.8 GB           |
|  85 | mt     | Maltese                  | 5,180       | 149,886        | 1.3 MB           |
|  86 | mr     | Marathi                  | 729,578     | 252,706,331    | 4.5 GB           |
|  87 | mzn    | Mazanderani              | 384         | 16,115         | 169.2 kB         |
|  88 | min    | Minangkabau              | 2,436       | 305,589        | 3.8 MB           |
|  89 | xmf    | Mingrelian               | 7,318       | 283,316        | 6.1 MB           |
|  90 | mwl    | Mirandese                | 4           | 54             | 423 Bytes        |
|  91 | mn     | Mongolian                | 1,061,710   | 454,350,415    | 5.8 GB           |
|  92 | multi  | Multilingual             | 2,948,202   | 1,251,676,406  | 11.9 GB          |
|  93 | nah    | Nahuatl languages        | 38          | 279            | 2.4 kB           |
|  94 | ne     | Nepali                   | 1,152,156   | 278,901,036    | 4.9 GB           |
|  95 | new    | Newari                   | 1,996       | 229,703        | 4.0 MB           |
|  96 | no     | Norwegian                | 2,797,378   | 373,160,033    | 2.6 GB           |
|  97 | nn     | Norwegian Nynorsk        | 19,470      | 575,518        | 3.7 MB           |
|  98 | oc     | Occitan                  | 920         | 34,701         | 405.0 kB         |
|  99 | or     | Odia                     | 158,426     | 31,963,340     | 543.1 MB         |
| 100 | os     | Ossetic                  | 8,628       | 3,935,964      | 50.7 MB          |
| 101 | ps     | Pashto                   | 87,408      | 30,196,179     | 261.6 MB         |
| 102 | fa     | Persian                  | 23,813,882  | 9,609,206,698  | 93.2 GB          |
| 103 | pms    | Piedmontese              | 2,524       | 510,087        | 3.1 MB           |
| 104 | pl     | Polish                   | 57,184,826  | 18,073,705,588 | 147.1 GB         |
| 105 | pt     | Portuguese               | 36,062,800  | 15,172,557,311 | 105.0 GB         |
| 106 | pa     | Punjabi                  | 222,058     | 104,235,418    | 1.4 GB           |
| 107 | qu     | Quechua                  | 2           | 13             | 143 Bytes        |
| 108 | ro     | Romanian                 | 11,985,668  | 6,302,600,833  | 45.6 GB          |
| 109 | bxr    | Russia Buriat            | 72          | 698            | 8.2 kB           |
| 110 | ru     | Russian                  | 194,143,422 | 78,032,029,344 | 1.1 TB           |
| 111 | sah    | Sakha                    | 17,566      | 4,288,051      | 68.8 MB          |
| 112 | sa     | Sanskrit                 | 16,802      | 2,479,345      | 56.3 MB          |
| 113 | gd     | Scottish Gaelic          | 776         | 18,458         | 146.1 kB         |
| 114 | sr     | Serbian                  | 1,677,896   | 632,781,822    | 7.7 GB           |
| 115 | sh     | Serbian (Latin)          | 3,214       | 166,517        | 816.4 kB         |
| 116 | sd     | Sindhi                   | 48,566      | 14,667,207     | 131.6 MB         |
| 117 | si     | Sinhala                  | 301,066     | 172,755,385    | 2.6 GB           |
| 118 | sk     | Slovak                   | 8,931,784   | 2,704,716,280  | 21.5 GB          |
| 119 | sl     | Slovenian                | 1,112,560   | 192,816,743    | 1.4 GB           |
| 120 | so     | Somali                   | 6           | 51             | 503 Bytes        |
| 121 | azb    | South Azerbaijani        | 26,364      | 2,029,729      | 28.4 MB          |
| 122 | es     | Spanish                  | 153,574,556 | 63,388,237,965 | 429.9 GB         |
| 123 | su     | Sundanese                | 18          | 258            | 2.0 kB           |
| 124 | sw     | Swahili                  | 1,664       | 164,459        | 1.0 MB           |
| 125 | sv     | Swedish                  | 21,891,348  | 6,993,719,601  | 50.0 GB          |
| 126 | gsw    | Swiss German             | 342         | 34,328         | 232.7 kB         |
| 127 | tg     | Tajik                    | 144,932     | 76,987,285     | 1.0 GB           |
| 128 | ta     | Tamil                    | 1,638,238   | 738,824,392    | 15.8 GB          |
| 129 | tt     | Tatar                    | 262,654     | 59,253,765     | 833.8 MB         |
| 130 | te     | Telugu                   | 644,712     | 201,575,815    | 3.9 GB           |
| 131 | th     | Thai                     | 14,845,900  | 2,224,483,018  | 92.0 GB          |
| 132 | bo     | Tibetan                  | 62,352      | 6,062,558      | 531.6 MB         |
| 133 | tr     | Turkish                  | 26,654,330  | 8,290,890,087  | 73.7 GB          |
| 134 | tk     | Turkmen                  | 4,576       | 325,786        | 3.3 MB           |
| 135 | uk     | Ukrainian                | 10,059,992  | 3,183,842,018  | 44.7 GB          |
| 136 | x-eml  | Unknown language [x-eml] | 4           | 329            | 1.8 kB           |
| 137 | hsb    | Upper Sorbian            | 402         | 15,827         | 123.2 kB         |
| 138 | ur     | Urdu                     | 887,004     | 434,023,273    | 3.8 GB           |
| 139 | ug     | Uyghur                   | 51,304      | 14,659,554     | 219.8 MB         |
| 140 | uz     | Uzbek                    | 15,806      | 1,665,960      | 15.3 MB          |
| 141 | vi     | Vietnamese               | 33,933,994  | 22,424,984,210 | 140.8 GB         |
| 142 | vo     | Volapük                  | 896         | 49,968         | 371.9 kB         |
| 143 | wa     | Walloon                  | 390         | 6,347          | 34.3 kB          |
| 144 | war    | Waray                    | 1,494       | 19,665         | 126.8 kB         |
| 145 | cy     | Welsh                    | 151,512     | 52,250,043     | 333.0 MB         |
| 146 | fy     | Western Frisian          | 45,458      | 9,885,788      | 70.4 MB          |
| 147 | mrj    | Western Mari             | 496         | 60,180         | 765.8 kB         |
| 148 | pnb    | Western Panjabi          | 12,904      | 11,844,695     | 105.8 MB         |
| 149 | wuu    | Wu Chinese               | 136         | 1,199          | 26.8 kB          |
| 150 | yi     | Yiddish                  | 47,438      | 14,287,370     | 171.7 MB         |
| 151 | yo     | Yoruba                   | 128         | 2,396          | 16.6 kB          |
