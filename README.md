# Breaking Bad: A Dataset for Geometric Fracture and Reassembly

_Accepted by NeurIPS 2022 Datasets and Benchmarks Track_

Please visit [our website](https://breaking-bad-dataset.github.io) for more dataset information.

## Accessing the dataset

### Dataset preview

Please visit [our website](https://breaking-bad-dataset.github.io) for interactive viewing of our dataset.

We also provide decompressed sample data (mesh files in `obj` format) under `example_data/`.
You can use [MeshLab](https://www.meshlab.net/) to view them.

### Access the entire dataset

We provide a compressed version of our dataset, together with a python decompressor script that you can run to locally decompress it. Proceed as follows (this assumes you have [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) installed):

0. **Download the Breaking Bad dataset from [Dataverse](https://doi.org/10.5683/SP3/LZNPKB) and unzip files**.
   To reproduce the main results in the paper, you only need to download `everyday` and `artifact` subset as well as the `data_split.tar.gz`.
   For the `other` subset, we split the zip file into 4 parts because of the single file size limit on Dataverse.
   Refer to [here](https://unix.stackexchange.com/questions/40480/how-to-unzip-a-multipart-spanned-zip-on-linux) for how to unzip splitted zip files.
   Make sure the unzipped dataset looks like

```
$DATA_ROOT/
├──── data_split/
│     ├──── everyday.train.txt
│     ├──── everyday.val.txt
│     ├──── artifact.train.txt
│     ├──── artifact.val.txt
│     ├──── other.train.txt
│     ├──── other.val.txt
├──── everyday_compressed/
│     ├──── BeerBottle/
│     |     |──── 3f91158956ad7db0322747720d7d37e8/
|     |     |     |──── compressed_data.npz
|     |     |     |──── compressed_mesh.obj
|     │     |     |──── mode_0/
|     |     |     |     |──── compressed_fracture.npy
•     •     •     •
•     •     •     •
|     |     |     |──── mode_19/
|     |     |     |──── fractured_0/
•     •     •     •
•     •     •     •
|     |     |     |──── fractured_79/
│     |     |──── 6da7fa9722b2a12d195232a03d04563a/
│     |     |──── 2927d6c8438f6e24fe6460d8d9bd16c6/
•     •     •
•     •     •
│     ├──── Bottle/
│     |     |──── 1/
│     |     |──── 1b64b36bf7ddae3d7ad11050da24bb12/
│     |     |──── 1c79735033726294724d5ee7f09ab66b/
•     •     •
•     •     •
│     ├──── Bowl/
•     •
•     •
├──── artifact_compressed/
│     ├──── 39084_sf/
│     ├──── 39085_sf/
│     ├──── 39086_sf/
•     •
•     •
├──── other_compressed/
│     ├──── 32770_sf/
│     ├──── 34783_sf/
│     ├──── 34784_sf/
•     •
•     •
```

1. Clone this repository

```bash
git clone git@github.com:Breaking-Bad-Dataset/Breaking-Bad-Dataset.github.io.git breaking-bad-dataset
```

2. Navigate to the repository

```bash
cd breaking-bad-dataset/
```

3. Install dependencies

```bash
conda create -n breaking-bad python=3.8
conda activate breaking-bad
conda install numpy scipy tqdm
conda install -c conda-forge igl
```

4. Run decompressor script

```bash
python decompress.py --data_root $DATA_ROOT --subset $SUBSET --category $CATEGORY
```

where `$DATA_ROOT` is the path to the Breaking Bad dataset folder.
`$SUBSET` is the name of the subset you want to process, i.e. one of `['everyday', 'artifact', 'other']`.
You can also input `all` to decompress the entire dataset, which is very time-consuming and takes ~1T disk storage.
`$CATEGORY` is only used for the `everyday` subset and specifies the object category you want to decompress, e.g. `Bottle`, `Bowl`.
You can also input `all` to decompress all the categories.
For example, to decompress the `Bottle` category in the `everyday` subset run

```bash
python decompress.py --data_root $DATA_ROOT --subset everyday --category Bottle
```

to decompress the `artifact` subset run

```bash
python decompress.py --data_root $DATA_ROOT --subset artifact
```

After decompressing everything, the structure of the dataset will be

```
$DATA_ROOT/
├──── data_split/
├──── everyday/ (~60G)
│     ├──── BeerBottle/
│     |     |──── 3f91158956ad7db0322747720d7d37e8/
|     │     |     |──── mode_0/
|     |     |     |     |──── piece_0.obj
•     •     •     •     •
•     •     •     •     •
|     |     |     |     |──── piece_n.obj
•     •     •     •
•     •     •     •
|     |     |     |──── mode_19/
|     |     |     |     |──── piece_0.obj
•     •     •     •     •
•     •     •     •     •
|     |     |     |     |──── piece_n.obj
|     │     |     |──── fracture_0/
|     |     |     |     |──── piece_0.obj
•     •     •     •     •
•     •     •     •     •
|     |     |     |     |──── piece_n.obj
•     •     •     •
•     •     •     •
|     |     |     |──── fracture_79/
|     |     |     |     |──── piece_0.obj
•     •     •     •
•     •     •     •
|     |     |     |     |──── piece_n.obj
•     •
•     •
├──── artifact/ (~40G)
├──── other/ (~900G)
```

## Reproduce the benchmark results

**We release the code for reproducing our benchmark results [here](https://github.com/Wuziyi616/multi_part_assembly).**

## Volume Constrained Version (*New!*)

In the initial release of our Breaking Bad Dataset, some fractures contain small chip-like pieces (see examples with 6 or 8 pieces in our gallery).
The imbalance in shape volumes causes difficulty in model learning.
For example, if we sample 1,000 points per piece, the point density of small and large pieces will be very different.

As an attempt to solve this issue, we create a volume constrained version of our dataset.
In the fracture simulator, we set the minimum volume of each piece to be at least 1/40 of the total shape volume, and do rejection sampling to generate valid samples.
We release the volume constrained version of the `everyday` and the `artifact` subset under the same [repo](https://doi.org/10.5683/SP3/LZNPKB).
Note that, due to the constraint, some shapes cannot generate 100 valid fractures.

We also benchmark the baselines on this version of data.
See last part of the [section](https://github.com/Wuziyi616/multi_part_assembly/blob/dev/docs/model.md#geometric-assembly).

## Citation

If you find this dataset useful, please consider citing our paper:

```bib
@inproceedings{sellan2022breaking,
  title      = {Breaking Bad: A Dataset for Geometric Fracture and Reassembly},
  author     = {Sell{\'a}n, Silvia and Chen, Yun-Chun and Wu, Ziyi and Garg, Animesh and Jacobson, Alec},
  booktitle  = {Thirty-sixth Conference on Neural Information Processing Systems Datasets and Benchmarks Track},
  year       = {2022}
}
```
