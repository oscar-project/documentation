# Getting access to OSCAR

To acces the dataset please go to [HuggingFace](https://huggingface.co/).

=== "HuggingFace"

    ### Using `datasets`

    _The following implies that you already have installed the Python datasets library_

    1. Create an account on [HuggingFace](https://huggingface.co/).
    2. Create a [user access token](https://huggingface.co/docs/hub/security-tokens). 
    3. Open the OSCAR Team [page](https://huggingface.co/oscar-corpus).
    4. Open your corpus of choice. Instructions should be in the corpus page.

    After all of this, you should be able to easily use OSCAR data with the `datasets` library :slight_smile: :

    ```py
    # example with OSCAR 2201
    from datasets import load_dataset


    dataset = load_dataset("oscar-corpus/OSCAR-2201",
                            use_auth_token=True, # required
                            language="ar", 
                            streaming=True, # optional
                            split="train") # optional

    for d in dataset:
        print(d) # prints documents
    ``` 

    ### Using Git LFS

    You can also get the raw data from HuggingFace using Git LFS.

    The following steps assume you have git and git-lfs installed, and are on a UNIX system. 
    The procedure should roughly be the same on Windows, but hasn’t been attempted.

    This will download the Basque corpus from OSCAR 2109.

    ```sh
    GIT_LFS_SKIP_SMUDGE=1 git clone https://huggingface.co/datasets/oscar-corpus/OSCAR-2109 
    cd OSCAR-2109 # go inside the directory
    git lfs pull --include packaged/eu/eu.txt.gz # pull the required file(s) (here the Basque corpus). Check with the manpage for pull options
    ```
