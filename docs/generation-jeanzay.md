# Generating a new OSCAR Version on Jean Zay

## Compile the latest version of Unogliant

This is currently prefered to just getting it from `cargo install ungoliant`.

- `git clone https://github.com/oscar-project/ungoliant`
- Get inside a `compil` node: `srun --partition=compil -A <GROUP ID>@cpu --pty bash`
- Run `module load llvm boost cargo` (`boost` and `llvm` for compiling KenLM/fasttext)
- Run `cd ungoliant`
- Run `cargo b --release --features kenlm`

## Get data from CommonCrawl

Use the `prepost` node for that, and bear in mind that jobs are killed at the 20h mark. 
This means you'll likely run out of time before finishing the download.

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
path = "./shards"
num_shards = 88000 # change this
for i in range(num_shards):
    if not os.path.isfile(f"{path}/{i}.txt.gz"):
        print(f"{i} missing")
```

If shards are missing, you can take the n-th (0-based) line(s) from the `wet.paths` and download them again. 
A semi-automatic way of doing that should be implemented some time.

### Generate OSCAR

When you have your shards ready, create a new SLURM file with:

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
