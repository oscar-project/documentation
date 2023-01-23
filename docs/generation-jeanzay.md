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

When the time has run out, you have to ensure that the last downloaded shards weren't corrupted (because of a potential kill while downloading). TODO

Then, after potentially removing faulty shards, run the following slurm job.
The only difference with the previous one is the use of the `-o n` parameter on `ungoliant download`, which will ignore the first `n` lines of the `wet.paths`.

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
