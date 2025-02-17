# DiffusionDB  <a href="https://huggingface.co/datasets/poloclub/diffusiondb"><picture><source media="(prefers-color-scheme: dark)" srcset="https://i.imgur.com/yGxUUlX.png"><img src="favicon.ico" align="right" src="favicon.ico" height="40px"></picture>

[![hugging](https://img.shields.io/badge/🤗%20Hugging%20Face-Datasets-yellow)](https://huggingface.co/datasets/poloclub/diffusiondb)
[![license](https://img.shields.io/badge/License-CC0/MIT-blue)](#licensing)
[![arxiv badge](https://img.shields.io/badge/arXiv-2210.14896-red)](https://arxiv.org/abs/2210.14896)
[![datasheet](https://img.shields.io/badge/Data%20Sheet-Available-success)](https://poloclub.github.io/diffusiondb/datasheet.html)
<!-- [![DOI:10.1145/3491101.3519653](https://img.shields.io/badge/DOI-10.1145/3491101.3519653-blue)](https://doi.org/10.1145/3491101.3519653) -->

<img width="100%" src="https://user-images.githubusercontent.com/15007159/201762588-f24db2b8-dbb2-4a94-947b-7de393fc3d33.gif">

DiffusionDB is the first large-scale text-to-image prompt dataset. It contains **14 million images** generated by Stable Diffusion using prompts and hyperparameters specified by real users. The unprecedented scale and diversity of this human-actuated dataset provide exciting research opportunities in understanding the interplay between prompts and generative models, detecting deepfakes, and designing human-AI interaction tools to help users more easily use these models.

## Get Started

DiffusionDB is available at [🤗 Hugging Face Datasets](https://huggingface.co/datasets/poloclub/diffusiondb).

## Two Subsets

DiffusionDB provides two subsets (DiffusionDB 2M and DiffusionDB Large) to support different needs.

|Subset|Num of Images|Num of Unique Prompts|Size|Image Directory|Metadata Table|
|:--|--:|--:|--:|--:|--:|
|DiffusionDB 2M|2M|1.5M|1.6TB|`images/`|`metadata.parquet`|
|DiffusionDB Large|14M|1.8M|6.5TB|`diffusiondb-large-part-1/` `diffusiondb-large-part-2/`|`metadata-large.parquet`|

##### Key Differences

1. Two subsets have a similar number of unique prompts, but DiffusionDB Large has much more images. DiffusionDB Large is a superset of DiffusionDB 2M.
2. Images in DiffusionDB 2M are stored in `png` format; images in DiffusionDB Large use a lossless `webp` format.

## Dataset Structure

We use a modularized file structure to distribute DiffusionDB. The 2 million images in DiffusionDB 2M are split into 2,000 folders, where each folder contains 1,000 images and a JSON file that links these 1,000 images to their prompts and hyperparameters. Similarly, the 14 million images in DiffusionDB Large are split into 14,000 folders.

```bash
# DiffusionDB 2M
./
├── images
│   ├── part-000001
│   │   ├── 3bfcd9cf-26ea-4303-bbe1-b095853f5360.png
│   │   ├── 5f47c66c-51d4-4f2c-a872-a68518f44adb.png
│   │   ├── 66b428b9-55dc-4907-b116-55aaa887de30.png
│   │   ├── [...]
│   │   └── part-000001.json
│   ├── part-000002
│   ├── part-000003
│   ├── [...]
│   └── part-002000
└── metadata.parquet
```

```bash
# DiffusionDB Large
./
├── diffusiondb-large-part-1
│   ├── part-000001
│   │   ├── 0a8dc864-1616-4961-ac18-3fcdf76d3b08.webp
│   │   ├── 0a25cacb-5d91-4f27-b18a-bd423762f811.webp
│   │   ├── 0a52d584-4211-43a0-99ef-f5640ee2fc8c.webp
│   │   ├── [...]
│   │   └── part-000001.json
│   ├── part-000002
│   ├── part-000003
│   ├── [...]
│   └── part-010000
├── diffusiondb-large-part-2
│   ├── part-010001
│   │   ├── 0a68f671-3776-424c-91b6-c09a0dd6fc2d.webp
│   │   ├── 0a0756e9-1249-4fe2-a21a-12c43656c7a3.webp
│   │   ├── 0aa48f3d-f2d9-40a8-a800-c2c651ebba06.webp
│   │   ├── [...]
│   │   └── part-010001.json
│   ├── part-010002
│   ├── part-010003
│   ├── [...]
│   └── part-014000
└── metadata-large.parquet
```

These sub-folders have names `part-0xxxxx`, and each image has a unique name generated by [UUID Version 4](https://en.wikipedia.org/wiki/Universally_unique_identifier). The JSON file in a sub-folder has the same name as the sub-folder. Each image is a `PNG` file (DiffusionDB 2M) or a lossless `WebP` file (DiffusionDB Large). The JSON file contains key-value pairs mapping image filenames to their prompts and hyperparameters. For example, below is the image of `f3501e05-aef7-4225-a9e9-f516527408ac.png` and its key-value pair in `part-000001.json`.

<img width="300" src="https://i.imgur.com/gqWcRs2.png">

```json
{
  "f3501e05-aef7-4225-a9e9-f516527408ac.png": {
    "p": "geodesic landscape, john chamberlain, christopher balaskas, tadao ando, 4 k, ",
    "se": 38753269,
    "c": 12.0,
    "st": 50,
    "sa": "k_lms"
  },
}
```

The data fields are:

- key: Unique image name
- `p`: Prompt
- `se`: Random seed
- `c`: CFG Scale (guidance scale)
- `st`: Steps
- `sa`: Sampler

## Dataset Metadata

To help you easily access prompts and other attributes of images without downloading all the Zip files, we include two metadata tables  `metadata.parquet` and `metadata-large.parquet` for DiffusionDB 2M and DiffusionDB Large, respectively.

The shape of `metadata.parquet` is (2000000, 13) and the shape of `metatable-large.parquet` is (14000000, 13). Two tables share the same schema, and each row represents an image. We store these tables in the Parquet format because Parquet is column-based: you can efficiently query individual columns (e.g., prompts) without reading the entire table.

Below are three random rows from `metadata.parquet`.

| image_name                               | prompt                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |   part_id |       seed |   step |   cfg |   sampler |   width |   height | user_name                                                        | timestamp                 |   image_nsfw |   prompt_nsfw |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|-----------:|-------:|------:|----------:|--------:|---------:|:-----------------------------------------------------------------|:--------------------------|-------------:|--------------:|
| 0c46f719-1679-4c64-9ba9-f181e0eae811.png | a small liquid sculpture, corvette, viscous, reflective, digital art                                                                                                                                                                                                                                                                                                                                                                                                           |      1050 | 2026845913 |     50 |   7   |         8 |     512 |      512 | c2f288a2ba9df65c38386ffaaf7749106fed29311835b63d578405db9dbcafdb | 2022-08-11 09:05:00+00:00 |    0.0845108 |   0.00383462  |
| a00bdeaa-14eb-4f6c-a303-97732177eae9.png | human sculpture of lanky tall alien on a romantic date at italian restaurant with smiling woman, nice restaurant, photography, bokeh                                                                                                                                                                                                                                                                                                                                           |       905 | 1183522603 |     50 |  10   |         8 |     512 |      768 | df778e253e6d32168eb22279a9776b3cde107cc82da05517dd6d114724918651 | 2022-08-19 17:55:00+00:00 |    0.692934  |   0.109437    |
| 6e5024ce-65ed-47f3-b296-edb2813e3c5b.png | portrait of barbaric spanish conquistador, symmetrical, by yoichi hatakenaka, studio ghibli and dan mumford                                                                                                                                                                                                                                                                                                                                                                    |       286 | 1713292358 |     50 |   7   |         8 |     512 |      640 | 1c2e93cfb1430adbd956be9c690705fe295cbee7d9ac12de1953ce5e76d89906 | 2022-08-12 03:26:00+00:00 |    0.0773138 |   0.0249675   |



### Metadata Schema

`metadata.parquet` and `metatable-large.parquet` share the same schema.

|Column|Type|Description|
|:---|:---|:---|
|`image_name`|`string`|Image UUID filename.|
|`prompt`|`string`|The text prompt used to generate this image.|
|`part_id`|`uint16`|Folder ID of this image.|
|`seed`|`uint32`| Random seed used to generate this image.|
|`step`|`uint16`| Step count (hyperparameter).|
|`cfg`|`float32`| Guidance scale (hyperparameter).|
|`sampler`|`uint8`| Sampler method (hyperparameter). Mapping: `{1: "ddim", 2: "plms", 3: "k_euler", 4: "k_euler_ancestral", 5: "k_heun", 6: "k_dpm_2", 7: "k_dpm_2_ancestral", 8: "k_lms", 9: "others"}`.
|`width`|`uint16`|Image width.|
|`height`|`uint16`|Image height.|
|`user_name`|`string`|The unique discord ID's SHA256 hash of the user who generated this image. For example, the hash for `xiaohk#3146` is `e285b7ef63be99e9107cecd79b280bde602f17e0ca8363cb7a0889b67f0b5ed0`. "deleted_account" refer to users who have deleted their accounts. None means the image has been deleted before we scrape it for the second time.|
|`timestamp`|`timestamp`|UTC Timestamp when this image was generated. None means the image has been deleted before we scrape it for the second time. Note that timestamp is not accurate for duplicate images that have the same prompt, hypareparameters, width, height.|
|`image_nsfw`|`float32`|Likelihood of an image being NSFW. Scores are predicted by [LAION's state-of-art NSFW detector](https://github.com/LAION-AI/LAION-SAFETY) (range from 0 to 1). A score of 2.0 means the image has already been flagged as NSFW and blurred by Stable Diffusion.|
|`prompt_nsfw`|`float32`|Likelihood of a prompt being NSFW. Scores are predicted by the library [Detoxicy](https://github.com/unitaryai/detoxify). Each score represents the maximum of `toxicity` and `sexual_explicit` (range from 0 to 1).|

> **Warning**
> Although the Stable Diffusion model has an NSFW filter that automatically blurs user-generated NSFW images, this NSFW filter is not perfect—DiffusionDB still contains some NSFW images. Therefore, we compute and provide the NSFW scores for images and prompts using the state-of-the-art models. The distribution of these scores is shown below. Please decide an appropriate NSFW score threshold to filter out NSFW images before using DiffusionDB in your projects.


<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://i.imgur.com/KLbJkUr.png">
  <img alt="NSFW Score distributions." src="https://i.imgur.com/1RiGAXL.png" width="100%">
</picture>


## Loading DiffusionDB

DiffusionDB is large (1.6TB or 6.5 TB)! However, with our modularized file structure, you can easily load a desirable number of images and their prompts and hyperparameters. In the [`example-loading.ipynb`](https://github.com/poloclub/diffusiondb/blob/main/notebooks/example-loading.ipynb) notebook, we demonstrate three methods to load a subset of DiffusionDB. Below is a short summary.

### Method 1: Use Hugging Face Datasets Loader

You can use the Hugging Face [`Datasets`](https://huggingface.co/docs/datasets/quickstart) library to easily load prompts and images from DiffusionDB. We pre-defined 16 DiffusionDB subsets (configurations) based on the number of instances. You can see all subsets in the [Dataset Preview](https://huggingface.co/datasets/poloclub/diffusiondb/viewer/all/train).

> **Note**
> To use Datasets Loader, you need to install `Pillow` as well (`pip install Pillow`)

```python
import numpy as np
from datasets import load_dataset

# Load the dataset with the `large_random_1k` subset
dataset = load_dataset('poloclub/diffusiondb', 'large_random_1k')
```

### Method 2. Use a downloader script

This repo includes a Python downloader [`download.py`](https://github.com/poloclub/diffusiondb/blob/main/scripts/download.py) that allows you to download and load DiffusionDB. You can use it from your command line. Below is an example of loading a subset of DiffusionDB.

#### Usage/Examples

The script is run using command-line arguments as follows:

- `-i` `--index` - File to download or lower bound of a range of files if `-r` is also set.
- `-r` `--range` - Upper bound of range of files to download if `-i` is set.
- `-o` `--output` - Name of custom output directory. Defaults to the current directory if not set.
- `-z` `--unzip` - Unzip the file/files after downloading
- `-l` `--large` - Download from Diffusion DB Large. Defaults to Diffusion DB 2M.

##### Downloading a single file

The specific file to download is supplied as the number at the end of the file on HuggingFace. The script will automatically pad the number out and generate the URL.

```bash
python download.py -i 23
```

##### Downloading a range of files

The upper and lower bounds of the set of files to download are set by the `-i` and `-r` flags respectively.

```bash
python download.py -i 1 -r 2000
```

Note that this range will download the entire dataset. The script will ask you to confirm that you have 1.7Tb free at the download destination.

##### Downloading to a specific directory

The script will default to the location of the dataset's `part` .zip files at `images/`. If you wish to move the download location, you should move these files as well or use a symbolic link.

```bash
python download.py -i 1 -r 2000 -o /home/$USER/datahoarding/etc
```

Again, the script will automatically add the `/` between the directory and the file when it downloads.

##### Setting the files to unzip once they've been downloaded

The script is set to unzip the files _after_ all files have downloaded as both can be lengthy processes in certain circumstances.

```bash
python download.py -i 1 -r 2000 -z
```

### Method 3. Use `metadata.parquet` (Text Only)

If your task does not require images, then you can easily access all 2 million prompts and hyperparameters in the `metadata.parquet` table.

```python
from urllib.request import urlretrieve
import pandas as pd

# Download the parquet table
table_url = f'https://huggingface.co/datasets/poloclub/diffusiondb/resolve/main/metadata.parquet'
urlretrieve(table_url, 'metadata.parquet')

# Read the table using Pandas
metadata_df = pd.read_parquet('metadata.parquet')
```

## Dataset Creation

We collected all images from the official Stable Diffusion Discord server. Please read our [research paper](https://arxiv.org/abs/2210.14896) for details. The code is included in [`./scripts/`](./scripts/).

## Data Removal

If you find any harmful images or prompts in DiffusionDB, you can use [this Google Form](https://forms.gle/GbYaSpRNYqxCafMZ9) to report them. Similarly, if you are a creator of an image included in this dataset, you can use the [same form](https://forms.gle/GbYaSpRNYqxCafMZ9) to let us know if you would like to remove your image from DiffusionDB. We will closely monitor this form and update DiffusionDB periodically.

## Credits

DiffusionDB is created by [Jay Wang](https://zijie.wang), [Evan Montoya](https://www.linkedin.com/in/evan-montoya-b252391b4/), [David Munechika](https://www.linkedin.com/in/dmunechika/), [Alex Yang](https://alexanderyang.me), [Ben Hoover](https://www.bhoov.com), [Polo Chau](https://faculty.cc.gatech.edu/~dchau/).

## Citation

```bibtex
@article{wangDiffusionDBLargescalePrompt2022,
  title = {{{DiffusionDB}}: {{A}} Large-Scale Prompt Gallery Dataset for Text-to-Image Generative Models},
  author = {Wang, Zijie J. and Montoya, Evan and Munechika, David and Yang, Haoyang and Hoover, Benjamin and Chau, Duen Horng},
  year = {2022},
  journal = {arXiv:2210.14896 [cs]},
  url = {https://arxiv.org/abs/2210.14896}
}
```

## Licensing

The DiffusionDB dataset is available under the [CC0 1.0 License](https://creativecommons.org/publicdomain/zero/1.0/).
The Python code in this repository is available under the [MIT License](./LICENSE).

## Contact

If you have any questions, feel free to [open an issue](https://github.com/poloclub/diffusiondb/issues/new) or contact [Jay Wang](https://zijie.wang).
