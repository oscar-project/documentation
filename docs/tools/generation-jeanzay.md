# Generating a new OSCAR Version on Jean Zay

## Compile the latest version of Unogliant

This is currently preferred to just getting it from `cargo install ungoliant`.

- `git clone https://github.com/oscar-project/ungoliant`
- Open an interactive session on a `compil` node: `srun --partition=compil -A <GROUP ID>@cpu --pty bash`
- Run `module load llvm boost cargo` (`boost` and `llvm` are necessary for compiling _KenLM_ and _FastText_)
- Run `cd ungoliant`
- Run `cargo b --release --features kenlm`

## Download the data from CommonCrawl

We advise the use of the `prepost` partition for downloading the data form _Common Crawl_. However, please bear in mind that jobs are limited to 20hours in the `prepost` partition, meaning that you'll likely run out of time before completing the download of a whole _Common Crawl_ dump.

- Download the `wet.paths.gz` file for the latest release (likely [here](https://commoncrawl.org/connect/blog/)
- `gzip -d wet.paths.gz`

Create a `dl_corpus.slurm` file with the following text inside:

```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=get_cc # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<YOUR MAIL>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="64" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <GROUP ID>@cpu

export CARGO_HOME=<CARGO HOME PATH (in SCRATCH if you can>
export PATHS_FILE=<PATH TO wet.PATHS>
export DST=<DESTINATION>


./target/release/ungoliant download $PATHS_FILE $DST
```

When the time has run out, you have to ensure that the last downloaded shards weren't corrupted (because of a potential kill while downloading).

Then, after potentially removing faulty shards, run the following slurm job.
The only difference with the previous one is the use of the `-o n` parameter on `ungoliant download`, which will ignore the first `n` lines of the `wet.paths`.
You can/should also use another `DESTINATION` folder, and then do the merge by hand.

```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=get_cc # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<YOUR MAIL>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="64" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <GROUP ID>@cpu

export CARGO_HOME=<CARGO HOME PATH (in SCRATCH if you can>
export PATHS_FILE=<PATH TO wet.PATHS>
export DST=<DESTINATION>


./target/release/ungoliant download -o <NB_DOWNLOADED> $PATHS_FILE $DST
```

You can then check that no shards are missing:

```py
import os

shards_dir = "./shards"
paths_file = "wet.paths"
cc_rooturl = "https://data.commoncrawl.org/"

missing_shards = list()
for i in range(88000):
    if not os.path.isfile(f"{shards_dir}/{i}.txt.gz"):
        missing_shards.append(i)
print(f"missing {len(missing_shards)} shards")

with open(paths_file) as f:
    shard_paths = f.readlines()
    for missing_shard_number in missing_shards:
        print(
            f"wget -nc {cc_rooturl}{shard_paths[missing_shard_number].strip()} -O {missing_shard_number}.txt.gz"
        )
```

This will give you the `wget` commands to get the missing shards, with a `-nc` param to avoid overwriting already existing files.

### Generate OSCAR

When you have your shards ready, create a new SLURM file with:

We use a QoS of t4 because since we can only use one node and corpus generation time is likely >20h, we need the 100 mark.

Other strategies could be tested (for example, splitting CC data into 4 buckets and launch 4 `ungoliant` jobs.
Then, merging back the datasets should be done.
*Note that in that case, rebuild files will be less efficient (since we'll have 4 of them)*

```bash
#! /bin/bash

#SBATCH --partition=cpu_p1
#SBATCH --job-name=gen_oscar # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<YOUR MAIL>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="40" # nombre de coeurs à réserver par tâche
#SBATCH --time="100:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH --qos=qos_cpu-t4
#SBATCH -A <GROUP ID>@cpu

export CARGO_HOME=<CARGO HOME PATH>
export CC_FOLDER=<SHARDS PATH>
export KENLM_FOLDER=<PATH TO KENLMS MODELS IF APPLICABLE>
export CORPUS=<DESTINATION FOLDER>
export BLOCKLIST=<BLOCKLIST FOLDER (must contain subfolders with category names..)>
export LID_PATH=<PATH TO FASTTEXT LangID>
export UNGOLIANT_PATH=<PATH TO UNGOLIANT BINARY>

$UNGOLIANT_PATH pipeline $CC_FOLDER $CORPUS --blocklist-path $BLOCKLIST --kenlms-path $KENLM_FOLDER --lid-path $LID_PATH
```

As of Jan. 2023, using `ungoliant 1.3.0 ([c14acc8](https://github.com/oscar-project/ungoliant/tree/c14acc8c6a87913d138a022cf4819024d66b3e06))`, with a 88,000-shard dump of CommonCrawl ([November/December 2022](https://commoncrawl.org/2022/12/nov-dec-2022-crawl-archive-now-available/), ~9.5TB compressed), this process took around 20 hours and yielded a corpus weighing arount 12TB (uncompressed).

### Move OSCAR

Files in `$SCRATCH` are deleted after 30 days if no R/W is operated on them. You should move out files to `$STORE` if you plan on keeping them. 
Unfortunately, due to the file size, you'll need to launch another job to do the copying of the files.

!!! warning
    `rsync -n` enables a dry-run, enabling you to see which files would be moved, and where. Remove the `-n` parameter when you want to perform the actual copy.
```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=copy_oscar # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=julien.abadji@inria.fr    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="4" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <GROUP ID>@cpu

export SRC=<CORPUS SOURCE>
export DST=<CORPUS DESTINATION>

rsync -anvP $SRC $DST
```

On the same example as before, copying took around 9 hours.

### Preparing for release

## Splitting

We use `oscar-tools` to split the corpus.

!!! note
    At the time of writing, `oscar-tools` is not available via [crates.io](https://crates.io)/`cargo install`, so you have to compile it from source. Luckily, it's easy.

??? note "Compiling `oscar-tools`"
    1. Get the source: `git clone https://github.com/oscar-project/oscar-tools`
    2. Go inside a `compil` node: ` srun --partition=compil -A <GROUP ID>@cpu --pty bash`
    3. `cd oscar-tools`
    4. `CARGO_HOME=<Somewhere not in your ~, like $SCRATCH/.cargo> cargo b --features zstd --release`.
    5. Wait ~some hours~ 
    6. That's it! Your binary sits at `target/release/oscar-tools`.

```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=split_oscar # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<Your email address>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="10" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <group id>@cpu

export OSCAR_TOOLS_BIN=<path to oscar-tools binary>
export CORPUS=<path to corpus>
export DST=<where the split corpus will be put>

$OSCAR_TOOLS_BIN v2 split $CORPUS $DST -s 2000
```

This step took around 3 hours (assuming both `CORPUS` and `DST` are on `$SCRATCH`).

## Compressing

```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=compress_oscar # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<email address>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="48" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <group id>@cpu

export OSCAR_TOOLS_BIN=<link to oscar-tools binary>
export CORPUS=<path to split focus>
export DST=<where the compressed ocrpus will be saved>

$OSCAR_TOOLS_BIN v2 compress $CORPUS $DST
```

This step took around 2 hours, going from 12TB to 3.3TB

## Checksuming

The last step is to create `checksum` files for each language, so that people can check that their downloads have been successful.
Also, it acts as a split list for [download-oscar](https://pypi.org/project/download-oscar/).

```bash
#! /bin/bash

#SBATCH --partition=prepost
#SBATCH --job-name=compress_oscar # create a short name for your job
#SBATCH --mail-type=BEGIN,END,FAIL          # Mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=<email address>    # Where to send mail
#SBATCH --nodes="1" #Combien de nœuds
#SBATCH --ntasks-per-node="1" # Une tâche par GPU
#SBATCH --cpus-per-task="48" # nombre de coeurs à réserver par tâche
#SBATCH --time="20:00:00" # temps d'exécution maximum demande (HH:MM:SS)
#SBATCH -A <group id>@cpu

export OSCAR_TOOLS_BIN=<link to oscar-tools binary>
export CORPUS=<path to split focus>

$OSCAR_TOOLS_BIN v2 checksum $CORPUS
```

The process took around 2 hours.
