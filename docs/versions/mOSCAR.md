# mOSCAR

mOSCAR is a large-scale multilingual and multimodal document corpus crawled from the web. It covers 163 languages, 315M documents, 214B tokens and 1.2B images. We carefully conduct a set of filtering and evaluation steps to make sure mOSCAR is sufficiently safe, diverse and of good quality.

## Access

Access to the mOSCAR is granted via the [Hugging Face Hub](https://huggingface.co/datasets/oscar-corpus/mOSCAR).

All data is avaialble at [https://huggingface.co/datasets/oscar-corpus/mOSCAR](https://huggingface.co/datasets/oscar-corpus/mOSCAR).

## Layout

```
{
    'images': [{'img_idx': '#000002',
                'sha512': '65c1e5605d48f8753256f758bd442cbdd43e6987691227b1ea6b81430ff36609f46d448c8171546232fe0c258d9e44ce4378f32e8ada5c43c314df5a5e230de2',
                'url': 'https://actuconsommation.fr/wp-content/uploads/2020/05/Disneylands-Japon-1068x712.jpg'}],
    'metadata': [{'node_order': 'img_#000002|txt_#000000|txt_#000001|txt_#000002|txt_#000003|txt_#000004|txt_#000005|txt_#000006|txt_#000009',
                  'url': 'https://actuconsommation.fr/2020/05/11/disneyland-une-reouverture-sous-haute-securite-a-shanghai-ce-lundi/'}],
    'text': [{'text': 'Disneyland : une réouverture sous haute sécurité à Shanghai ce lundi', 'text_idx': '#000000'},
             {'text': 'Des milliers de visiteurs ont pu pénétrer lundi dans le Disneyland de Shanghai, le premier des six parcs de [...]', text_idx': '#000001'},
             [...] ]
}
```

## Language table

| Lang. name             | Code     | Family        | Script     | #documents  | #images     | # tokens       |
| ---------------------- | -------- | ------------- | ---------- | ----------- | ----------- | -------------- |
| Acehnese               | ace_Latn | Austronesian   | Latin      | 7,803      | 32,461      | 2,889,134      |
| Mesopotamian Arabic    | acm_Arab | Afro-Asiatic   | Arabic     | 2,274      | 10,620      | 1,047,748      |
| Tunisian Arabic        | aeb_Arab | Afro-Asiatic   | Arabic     | 7,640      | 41,570      | 2,715,187      |
| Afrikaans              | afr_Latn | Indo-European  | Latin      | 54,895     | 247,774     | 39,956,585     |
| South Levantine Arabic | ajp_Arab | Afro-Asiatic   | Arabic     | 12,098     | 87,837      | 5,167,813      |
| Tosk Albanian          | als_Latn | Indo-European  | Latin      | 861,678    | 2,569,164   | 452,737,251    |
| Amharic                | amh_Ethi | Afro-Asiatic   | Ge‘ez      | 39,588     | 152,646     | 35,089,019     |
| North Levantine Arabic | apc_Arab | Afro-Asiatic   | Arabic     | 19,904     | 128,966     | 9,560,701      |
| Modern Standard Arabic | arb_Arab | Afro-Asiatic   | Arabic     | 3,936,851  | 15,126,931  | 3,401,919,964  |
| Najdi Arabic           | ars_Arab | Afro-Asiatic   | Arabic     | 60,229     | 296,741     | 43,610,873     |
| Moroccan Arabic        | ary_Arab | Afro-Asiatic   | Arabic     | 142,386    | 698,051     | 204,723,454    |
| Egyptian Arabic        | arz_Arab | Afro-Asiatic   | Arabic     | 835,529    | 4,054,632   | 653,626,387    |
| Assamese               | asm_Beng | Indo-European  | Bengali    | 3,948      | 9,210       | 640,390        |
| Asturian               | ast_Latn | Indo-European  | Latin      | 165,745    | 962,723     | 37,547,944     |
| Awadhi                 | awa_Deva | Indo-European  | Devanagari | 29,324     | 107,483     | 4,961,635      |
| Central Aymara         | ayr_Latn | Aymaran        | Latin      | 27,384     | 151,889     | 5,148,970      |
| South Azerbaijani      | azb_Arab | Turkic         | Arabic     | 8,274      | 38,233      | 5,256,693      |
| North Azerbaijani      | azj_Latn | Turkic         | Latin      | 516,021    | 1,808,060   | 257,825,849    |
| Bashkir                | bak_Cyrl | Turkic         | Cyrillic   | 4,532      | 17,174      | 3,038,766      |
| Bambara                | bam_Latn | Manding        | Latin      | 7,674      | 39,190      | 1,243,332      |
| Balinese               | ban_Latn | Austronesian   | Latin      | 1,886      | 11,266      | 542,015        |
| Belarusian             | bel_Cyrl | Indo-European  | Cyrillic   | 63,309     | 287,539     | 72,976,520     |
| Bemba                  | bem_Latn | Atlantic–Congo | Latin      | 1,096      | 7,479       | 1,340,471      |
| Bengali                | ben_Beng | Indo-European  | Bengali    | 270,406    | 947,035     | 35,858,814     |
| Bhojpuri               | bho_Deva | Indo-European  | Devanagari | 6,366      | 28,131      | 875,463        |
| Banjar                 | bjn_Latn | Austronesian   | Latin      | 5,427      | 27,803      | 1,898,526      |
| Bosnian                | bos_Latn | Indo-European  | Latin      | 1,960,599  | 7,633,049   | 1,255,000,505  |
| Buginese               | bug_Latn | Austronesian   | Latin      | 3,312      | 18,648      | 588,678        |
| Bulgarian              | bul_Cyrl | Indo-European  | Cyrillic   | 2,591,998  | 11,670,028  | 1,760,971,620  |
| Catalan                | cat_Latn | Indo-European  | Latin      | 1,153,864  | 4,736,634   | 606,447,390    |
| Cebuano                | ceb_Latn | Austronesian   | Latin      | 16,990     | 91,234      | 10,748,818     |
| Czech                  | ces_Latn | Indo-European  | Latin      | 3,918,837  | 13,291,309  | 2,823,172,996  |
| Central Kurdish        | ckb_Arab | Indo-European  | Arabic     | 36,725     | 136,566     | 22,322,689     |
| Crimean Tatar          | crh_Latn | Turkic         | Latin      | 6,376      | 24,124      | 1,742,727      |
| Welsh                  | cym_Latn | Indo-European  | Latin      | 40,408     | 165,897     | 27,748,345     |
| Danish                 | dan_Latn | Indo-European  | Latin      | 2,076,298  | 9,559,600   | 1,238,277,499  |
| German                 | deu_Latn | Indo-European  | Latin      | 20,662,696 | 87,976,200  | 8,544,986,218  |
| Southwestern Dinka     | dik_Latn | Nilo-Saharan   | Latin      | 1,712      | 6,635       | 1,319,943      |
| Greek                  | ell_Grek | Indo-European  | Greek      | 4,916,081  | 15,209,058  | 2,923,201,041  |
| English                | eng_Latn | Indo-European  | Latin      | 52,215,013 | 207,904,315 | 33,570,108,782 |
| Esperanto              | epo_Latn | Artificial     | Latin      | 25,157     | 124,996     | 28,586,195     |
| Estonian               | est_Latn | Uralic         | Latin      | 1,040,368  | 5,217,366   | 619,215,048    |
| Basque                 | eus_Latn | Isolate        | Latin      | 849,043    | 3,445,539   | 277,145,498    |
| Faroese                | fao_Latn | Indo-European  | Latin      | 15,411     | 60,340      | 6,691,327      |
| Fijian                 | fij_Latn | Austronesian   | Latin      | 1,528      | 8,776       | 487,388        |
| Finnish                | fin_Latn | Uralic         | Latin      | 2,396,033  | 10,365,333  | 1,781,044,864  |
| French                 | fra_Latn | Indo-European  | Latin      | 20,305,739 | 78,179,601  | 14,362,579,829 |
| Friulian               | fur_Latn | Indo-European  | Latin      | 37,290     | 256,456     | 5,949,600      |
| Nigerian Fulfulde      | fuv_Latn | Atlantic-Congo | Latin      | 1,568      | 7,124       | 401,852        |
| West Central Oromo     | gaz_Latn | Afro-Asiatic   | Latin      | 4,058      | 11,763      | 1,786,093      |
| Scottish Gaelic        | gla_Latn | Indo-European  | Latin      | 29,710     | 153,249     | 14,605,090     |
| Irish                  | gle_Latn | Indo-European  | Latin      | 68,858     | 315,132     | 47,438,400     |
| Galician               | glg_Latn | Indo-European  | Latin      | 518,973    | 2,381,475   | 217,063,180    |
| Guarani                | grn_Latn | Tupian         | Latin      | 490,945    | 2,416,633   | 89,921,114     |
| Gujarati               | guj_Gujr | Indo-European  | Gujarati   | 23,062     | 91,320      | 3,324,866      |
| Haitian Creole         | hat_Latn | Indo-European  | Latin      | 257,745    | 1,570,699   | 62,847,106     |
| Hausa                  | hau_Latn | Afro-Asiatic   | Latin      | 25,364     | 104,934     | 13,089,932     |
| Hebrew                 | heb_Hebr | Afro-Asiatic   | Hebrew     | 1,109,591  | 4,766,483   | 893,327,320    |
| Hindi                  | hin_Deva | Indo-European  | Devanagari | 579,430    | 1,830,667   | 122,558,353    |
| Chhattisgarhi          | hne_Deva | Indo-European  | Devanagari | 1,581      | 7,263       | 273,174        |
| Croatian               | hrv_Latn | Indo-European  | Latin      | 1,719,617  | 8,425,510   | 1,010,674,096  |
| Hungarian              | hun_Latn | Uralic         | Latin      | 3,534,506  | 15,390,083  | 2,831,715,050  |
| Armenian               | hye_Armn | Indo-European  | Armenian   | 339,962    | 1,141,885   | 205,635,952    |
| Igbo                   | ibo_Latn | Atlantic-Congo | Latin      | 11,529     | 68,049      | 8,701,070      |
| Ilocano                | ilo_Latn | Austronesian   | Latin      | 78,872     | 523,195     | 8,116,113      |
| Indonesian             | ind_Latn | Austronesian   | Latin      | 7,016,291  | 17,324,777  | 3,981,843,468  |
| Icelandic              | isl_Latn | Indo-European  | Latin      | 244,676    | 1,027,465   | 137,015,973    |
| Italian                | ita_Latn | Indo-European  | Latin      | 12,937,153 | 47,476,971  | 8,311,790,842  |
| Javanese               | jav_Latn | Austronesian   | Latin      | 24,785     | 135,583     | 16,908,805     |
| Japanese               | jpn_Jpan | Japonic        | Kanji      | 14,415,292 | 23,893,768  | 8,923,348,944  |
| Kabyle                 | kab_Latn | Afro-Asiatic   | Latin      | 18,508     | 106,730     | 4,079,553      |
| Kannada                | kan_Knda | Dravidian      | Kannada    | 12,978     | 42,621      | 1,442,776      |
| Kashmiri               | kas_Arab | Indo-European  | Arabic     | 3,109      | 11,408      | 5,731,910      |
| Georgian               | kat_Geor | Kartvelian     | Georgian   | 354,436    | 1,304,281   | 275,223,026    |
| Kazakh                 | kaz_Cyrl | Turkic         | Cyrillic   | 252,242    | 732,648     | 140,049,214    |
| Halh Mongolian         | khk_Cyrl | Mongolic       | Cyrillic   | 124,412    | 508,217     | 84,535,241     |
| Khmer                  | khm_Khmr | Austroasiatic  | Kher       | 24,495     | 122,243     | 3,043,925      |
| Kinyarwanda            | kin_Latn | Atlantic-Congo | Latin      | 30,401     | 172,201     | 12,049,616     |
| Kyrgyz                 | kir_Cyrl | Uralic         | Cyrillic   | 53,010     | 199,713     | 34,404,281     |
| Northern Kurdish       | kmr_Latn | Indo-European  | Latin      | 39,262     | 164,666     | 23,834,960     |
| Korean                 | kor_Hang | Koreanic       | Hanja      | 2,614,089  | 13,563,283  | 2,006,080,705  |
| Lao                    | lao_Laoo | Kra-Dai        | Lao        | 50,611     | 208,768     | 31,029,380     |
| Ligurian               | lij_Latn | Indo-European  | Latin      | 8,751      | 56,266      | 2,958,179      |
| Limburgish             | lim_Latn | Indo-European  | Latin      | 189,547    | 1,076,047   | 42,534,327     |
| Lingala                | lin_Latn | Atlantic-Congo | Latin      | 24,614     | 152,132     | 4,053,459      |
| Lithuanian             | lit_Latn | Indo-European  | Latin      | 1,688,811  | 8,869,443   | 1,161,476,040  |
| Lombard                | lmo_Latn | Indo-European  | Latin      | 30,506     | 151,855     | 9,058,614      |
| Latgalian              | ltg_Latn | Indo-European  | Latin      | 11,948     | 61,624      | 4,148,492      |
| Luxembourgish          | ltz_Latn | Indo-European  | Latin      | 44,987     | 246,346     | 16,676,872     |
| Ganda                  | lug_Latn | Afro-Asiatic   | Latin      | 1,878      | 7,215       | 789,917        |
| Mizo                   | lus_Latn | Sino-Tibetan   | Latin      | 7,880      | 26,817      | 4,978,472      |
| Standard Latvian       | lvs_Latn | Indo-European  | Latin      | 896,243    | 4,141,648   | 587,653,855    |
| Magahi                 | mag_Deva | Indo-European  | Devanagari | 1,097      | 3,847       | 205,763        |
| Malayalam              | mal_Mlym | Dravidian      | Malayalam  | 14,140     | 52,679      | 1,689,010      |
| Marathi                | mar_Deva | Indo-European  | Devanagari | 50,391     | 163,868     | 6,689,250      |
| Minangkabau            | min_Latn | Austronesian   | Latin      | 9,341      | 35,309      | 1,256,931      |
| Macedonian             | mkd_Cyrl | Indo-European  | Cyrillic   | 542,250    | 1,853,070   | 307,232,151    |
| Maltese                | mlt_Latn | Afro-Asiatic   | Latin      | 120,888    | 709,242     | 36,097,957     |
| Maori                  | mri_Latn | Austronesian   | Latin      | 24,322     | 130,137     | 24,957,914     |
| Burmese                | mya_Mymr | Sino-Tibetan   | Mon        | 8,144      | 44,188      | 539,527        |
| Dutch                  | nld_Latn | Indo-European  | Latin      | 17,096,727 | 65,606,013  | 9,670,041,731  |
| Norwegian Nynorsk      | nno_Latn | Indo-European  | Latin      | 199,355    | 1,012,313   | 67,799,774     |
| Norwegian Bokmål       | nob_Latn | Indo-European  | Latin      | 2,229,702  | 9,698,128   | 1,294,178,095  |
| Nepali                 | npi_Deva | Indo-European  | Devanagari | 31,239     | 127,193     | 3,138,539      |
| Nyanja                 | nya_Latn | Atlantic-Congo | Latin      | 12,047     | 67,192      | 8,596,769      |
| Occitan                | oci_Latn | Indo-European  | Latin      | 164,852    | 671,881     | 59,309,549     |
| Odia                   | ory_Orya | Indo-European  | Odia       | 4,319      | 15,574      | 378,635        |
| Pangasinan             | pag_Latn | Austronesian   | Latin      | 4,214      | 32,287      | 546,071        |
| Eastern Panjabi        | pan_Guru | Indo-European  | Gurmukhi   | 11,497     | 46,168      | 1,887,991      |
| Papiamento             | pap_Latn | Indo-European  | Latin      | 55,224     | 363,015     | 10,002,655     |
| Southern Pasto         | pbt_Arab | Indo-European  | Arabic     | 32,604     | 110,807     | 29,170,322     |
| Western Persian        | pes_Arab | Indo-European  | Arabic     | 7,048,946  | 25,200,571  | 6,210,479,015  |
| Plateau Malgasy        | plt_Latn | Austronesian   | Latin      | 32,521     | 120,673     | 29,263,848     |
| Polish                 | pol_Latn | Indo-European  | Latin      | 14,549,605 | 60,639,244  | 11,104,144,109 |
| Portuguese             | por_Latn | Indo-European  | Latin      | 8,145,664  | 26,530,423  | 4,760,063,083  |
| Dari                   | prs_Arab | Indo-European  | Arabic     | 515,041    | 2,589,859   | 517,053,967    |
| Ayacucho Quechua       | quy_Latn | Quechuan       | Latin      | 1,578      | 11,817      | 362,690        |
| Romanian               | ron_Latn | Indo-European  | Latin      | 5,180,171  | 17,964,048  | 3,548,291,261  |
| Rundi                  | run_Latn | Atlantic-Congo | Latin      | 20,001     | 67,096      | 8,686,054      |
| Russian                | rus_Cyrl | Indo-European  | Cyrillic   | 15,913,845 | 69,542,828  | 18,909,213,208 |
| Sango                  | sag_Latn | Atlantic-Congo | Latin      | 2,124      | 13,556      | 454,455        |
| Sicilian               | scn_Latn | Indo-European  | Latin      | 73,199     | 424,362     | 27,110,743     |
| Sinhala                | sin_Sinh | Indo-European  | Sinhalese  | 58,767     | 221,183     | 14,270,972     |
| Slovak                 | slk_Latn | Indo-European  | Latin      | 3,008,599  | 15,067,234  | 1,963,804,563  |
| Slovenian              | slv_Latn | Indo-European  | Latin      | 1,472,025  | 7,210,285   | 935,834,754    |
| Samoan                 | smo_Latn | Austronesian   | Latin      | 12,346     | 71,359      | 14,954,824     |
| Shona                  | sna_Latn | Atlantic-Congo | Latin      | 12,698     | 68,782      | 6,112,600      |
| Sindhi                 | snd_Arab | Indo-European  | Arabic     | 21,095     | 74,289      | 17,647,825     |
| Somali                 | som_Latn | Afro-Asiatic   | Latin      | 77,343     | 301,429     | 34,554,975     |
| Southern Sotho         | sot_Latn | Atlantic-Congo | Latin      | 7,718      | 43,146      | 6,156,450      |
| Spanish                | spa_Latn | Indo-European  | Latin      | 22,713,366 | 78,361,087  | 14,616,773,475 |
| Sardinian              | srd_Latn | Indo-European  | Latin      | 675,539    | 4,059,493   | 106,159,957    |
| Serbian                | srp_Cyrl | Indo-European  | Cyrillic   | 604,557    | 2,286,171   | 401,223,741    |
| Sundanese              | sun_Latn | Austronesian   | Latin      | 44,310     | 236,025     | 13,627,832     |
| Swedish                | swe_Latn | Indo-European  | Latin      | 3,302,730  | 10,860,518  | 1,779,284,152  |
| Swahili                | swh_Latn | Atlantic-Congo | Latin      | 137,134    | 593,418     | 59,454,896     |
| Silesian               | szl_Latn | Indo-European  | Latin      | 23,535     | 132,459     | 5,996,972      |
| Tamil                  | tam_Taml | Dravidian      | Tamil      | 36,196     | 167,669     | 4,834,946      |
| Tatar                  | tat_Cyrl | Turkic         | Cyrillic   | 37,188     | 143,842     | 22,831,350     |
| Telugu                 | tel_Telu | Dravidian      | Telugu     | 22,974     | 81,033      | 2,273,772      |
| Tajik                  | tgk_Cyrl | Turkic         | Cyrillic   | 125,236    | 417,591     | 90,503,778     |
| Tagalog                | tgl_Latn | Austronesian   | Latin      | 151,437    | 673,814     | 97,708,639     |
| Thai                   | tha_Thai | Kra-Dai        | Thai       | 2,983,837  | 11,621,786  | 2,839,211,104  |
| Tigrinya               | tir_Ethi | Afro-Asiatic   | Ge‘ez      | 2,657      | 8,707       | 1,725,422      |
| Tok Pisin              | tpi_Latn | Indo-European  | Latin      | 5,063      | 35,169      | 460,853        |
| Turkmen                | tuk_Latn | Turkic         | Latin      | 13,024     | 57,354      | 9,766,999      |
| Turkish                | tur_Latn | Turkic         | Latin      | 4,478,700  | 12,401,091  | 2,394,669,068  |
| Twi                    | twi_Latn | Atlantic-Congo | Latin      | 3,305      | 13,634      | 495,220        |
| Uyghur                 | uig_Arab | Turkic         | Arabic     | 10,713     | 41,709      | 6,785,318      |
| Ukrainian              | ukr_Cyrl | Indo-European  | Cyrillic   | 2,721,424  | 10,929,796  | 1,928,351,595  |
| Urdu                   | urd_Arab | Indo-European  | Arabic     | 407,098    | 1,239,125   | 242,007,283    |
| Northern Uzbek         | uzn_Latn | Turkic         | Latin      | 156,632    | 798,155     | 89,022,562     |
| Venetian               | vec_Latn | Indo-European  | Latin      | 330,611    | 1,830,777   | 71,077,531     |
| Vietnamese             | vie_Latn | Viet-Muong     | Latin      | 12,621,521 | 47,411,488  | 11,616,191,199 |
| Wolof                  | wol_Latn | Atlantic-Congo | Latin      | 4,658      | 20,380      | 1,596,432      |
| Xhosa                  | xho_Latn | Atlantic-Congo | Latin      | 25,950     | 142,387     | 15,809,823     |
| Eastern Yiddish        | ydd_Hebr | Indo-European  | Hebrew     | 12,486     | 57,510      | 17,369,727     |
| Yoruba                 | yor_Latn | Atlantic-Congo | Latin      | 56,700     | 286,933     | 32,614,558     |
| Yue Chinese            | yue_Hant | Sino-Tibetan   | Hant       | 33,671     | 203,513     | 24,172,441     |
| Chinese (Simplified)   | zho_Hans | Sino-Tibetan   | Hanzi      | 9,861,262  | 36,152,754  | 8,078,842,701  |
| Chinese (Traditional)  | zho_Hant | Sino-Tibetan   | Hant       | 3,967,966  | 16,307,258  | 2,962,854,441  |
| Standard Malay         | zsm_Latn | Austronesian   | Latin      | 1,179,744  | 5,488,632   | 432,667,199    |
| Zulu                   | zul_Latn | Atlantic-Congo | Latin      | 30,717     | 156,639     | 11,345,288     |


## License
These data are released under this licensing scheme:

- We do not own any of the text from which these data has been extracted.
- We license the actual packaging of these data under the Creative Commons CC BY 4.0 license.
- To the extent possible under law, Inria has waived all copyright and related or neighboring rights to OSCAR.
- This work is published from: France.
