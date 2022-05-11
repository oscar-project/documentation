# oscar-tools

`oscar-tools` is a toolkit that was created along with [OSCAR-2201](versions/oscar-2201.md) to make operations on the corpus easy and fast.

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
    In the tool, `v1` corresponds to 21.09-like corpora, whereas `v2` corresponds to 22.01-like corpora.

Each operation has different parameters.
