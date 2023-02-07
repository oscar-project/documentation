# oscar-tools

`oscar-tools` is a toolkit that was created along with [OSCAR-2201](../versions/oscar-2201.md) to make operations on the corpus easy and fast.

At its core, `oscar-tools` provides a set of operations targeted at a **given OSCAR version**. As such, you shoudn't expect to have all operations available on all OSCAR versions. For example, at the time of writing, `deduplicate` is not available for OSCAR 22.01-like corpora. 

The CLI of `oscar-tools` is still a bit messy and can be confusing, because we are actively working on it and on implementing essential features.

## Installation

### From `releases`

!!! note
    Binaries are not available yet.

### From `cargo`

!!! note
    `cargo install oscar-tools` is not available yet.

### From repository

!!! note 
    This could evolve rapidly.

Right now the latest version sits on the `dev-oscario` branch, where we're slowly replacing inline IO blocks by our Corpus IO library, [`oscar-io`](https://github.com/oscar-corpus/oscar-io).

```sh
$> git clone https://github.com/oscar-corpus/oscar-tools #Clone the repository
$> cd oscar-tools
$> git checkout dev-oscario #Change branch
$> cargo b --release #Build the project. 
$> # Building might take some time because of 
$> # the parquet dependency that will soon be optional.
$> touch target/release/oscar-tools #Binary is here and self-sufficient.
```

## Usage

`oscar-tools --help` might help you find the parameters/operations you're looking for.

!!! note
    In the tool, `v1` corresponds to 2019-like corpora, whereas `v2` corresponds to 22.01-like corpora.

Each operation has different parameters.


## v1 / OSCAR 2019

At the time of writing, the only operation available is `dedup`. It uses `runiq` to deduplicate corpora. 

```
oscar-tools-v1-dedup 
line deduplication

USAGE:
    oscar-tools v1 dedup [ARGS]

ARGS:
    <SOURCE>         Corpus source file.
    <DESTINATION>    Corpus destination file. Should not exist.

OPTIONS:
    -h, --help    Print help information

```

## v2 / OSCAR 22.01

There is a lot more operations implemented on OSCAR 22.01-like corpora.

### `extract-tags`

`extract-tags` extracts documents that meet certain annotation constraints. 

```
oscar-tools-v2-extract-tags 
Extracts a OSCAR v2 corpus restricting tags. Included tags must be present and excluded ones must be
absent. Use --clean to extract documents with no annotation only

USAGE:
    oscar-tools v2 extract-tags [OPTIONS] [--] [ARGS]

ARGS:
    <SOURCE>         Corpus source file/folder. If folder, splits corpus files in provided
                     folder
    <DESTINATION>    Corpus source file/folder. If folder, splits corpus files in provided
                     folder

OPTIONS:
        --clean                only return documents with no tags. include and exclude will be
                               ignored
    -e, --exclude <tags>...    space separated tags to exclude.
    -h, --help                 Print help information
    -i, --include <tags>...    space separated tags to include.
```

### `extract-text`

`extract-text` "converts" a 2201-like corpus into a 2019-like corpus, by removing all metadata and only storing sentences. Keep in mind that while the *format* will be similar to 2109-like corpora, the filtering is a bit different and lines from other languages won't be stripped. 

```
Extract text from documents. The output will be a OSCAR v1 (2019)-compatible corpus.

USAGE:
    oscar-tools v2 extract-text [OPTIONS] <SOURCE> <DESTINATION>

ARGS:
    <SOURCE>         Corpus source file.
    <DESTINATION>    Corpus destination file (OSCAR v1 (2019)-like)

OPTIONS:
        --del_src    If set, deletes source files as they are being extracted.
    -h, --help       Print help information
```
