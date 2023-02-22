# Getting access to OSCAR

There are two ways of accessing OSCAR: through [Huma-Num](https://www.huma-num.fr/), or through [HuggingFace](https://huggingface.co/).
Depending on your status, you might not have the choice.

|              | Research/Academic  | Individual               |
|--------------|:--------------------:|:--------------------------:|
| Huma-Num     | :heavy_check_mark: | :heavy_multiplication_x: |
| Hugging-Face | :heavy_check_mark: | :heavy_check_mark:       |

=== "Huma-Num"

    You can apply for an access request by sending us an email!

    !!! warning
        Carefully respect the following instructions, as incorrect submissions might significantly delay your access.

    !!! danger
        Do not create an account by yourselves, as it could delay you access by weeks! We will create an account for you.

    Send us an email at **contact at oscar-project.org**, with **OSCAR Access Request** as the title, and the following (completed) as the body:

    ```
    - First name:
    - Last name:
    - Affiliation:
    - Contact details:
    - Corpus version: 
    - Languages:

    + a short description of your usecase.
    ```

    !!! note
        Access requests can take **some days** to be answered, sometimes more.

        We post updates on our [Discord](https://discord.com/invite/4JNg9FTar4) server on exceptional delays, and you can always contact us there to inquire about yours.
    
    After some time, you should get an email back from us with access instructions!

=== "HuggingFace"

    ### Using `datasets`

    _The following implies that you already have installed the Python datasets library_

    1. Create an account on [HuggingFace](https://huggingface.co/).
    2. Create a [user access token](https://huggingface.co/docs/hub/security-tokens). 
    2. Open the OSCAR Team [page](https://huggingface.co/oscar-corpus).
    3. Open your corpus of choice. Instructions should be in the corpus page.

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
