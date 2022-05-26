# VisDA 2022 Challenge 

**[[Challenge website]](https://ai.bu.edu/visda-2022/)**
## Datasets preparation

The pytorch implementation of the ZeroWaste dataset can be found in [here](zerowaste_dataloader.py).

## Baseline (DAFormer)
### Set up the environment
We use the official implementation of the state-of-the-art transformer-based domain adaptation methods [DAFormer](https://arxiv.org/abs/2111.14887) as our baseline. 
To get started, please follow the instructions on how to set up the environment on the DAFormer [github page](https://github.com/lhoyer/DAFormer). 
### Download checkpoints
To download all relevant checkpoints, please use our script.
```shell
sh tools/download_checkpoints.sh
```



## Setup Datasets
**ZeroWaste V1:** Please, download zerowaste-f.zip (7 Gb) from [here](http://csr.bu.edu/ftp/recycle/) and extract them to data/zerowaste-f.

**ZeroWaste V2 (unlabeled)** Please, download zerowaste-v2-trainval.zip (12 Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract
it to `data/zerowaste-v2/`.

**SynthWaste:** Please, download synthwaste_splits.zip (51 Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract it to `data/synthwaste`.

**SynthWaste-aug:** Please, download 	synthwaste_aug.zip (33Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract it to `data/synthwaste-aug` (this folder contains only the augmented train set).



## Training

We provide the following config files:
 
1. `configs/daformer/zerowaste_to_zerowastev2_daformer_mit5.py` for training DAFormer on ZeroWaste V1 as source domain and ZeroWaste V2 as target domain.
2. `configs/source_only/zerowaste_to_zerowastev2_segformer.json` to train SegFormer on ZeroWaste V1 and test on ZeroWaste V2 (source-only). 
3. `configs/daformer/transfer_synthwaste_zerowaste_to_zerowastev2_daformer_mit5.py` to train DAFormer with a SegFormer backbone pretrained on SynthWaste on ZeroWaste V1 as source domain and ZeroWaste V2 as target domain.
4. `configs/source_only/transfer_synthwaste_zerowaste_to_zerowastev2_segformer.json` to train SegFormer pretrained on SynthWaste on ZeroWaste V1 and test on ZeroWaste V2 (source-only). 

To train the model with a desired configuration, run
```shell
python -m tools.train /path/to/config/file --work-dir /path/to/experiment/folder
```
The checkpoints, full config file and other relevant data will be stored in the experiment folder. By default, the experiments will be saved to the `work_dirs` folder.
For more details on how to use the code, please see the [official DAFormer guide](https://github.com/lhoyer/DAFormer). 


## Testing & Predictions

To evaluate the model and output the visual examples of the predictions, run the following command:

```shell
python -m tools.test /path/to/config/file /path/to/checkpoint.pth --eval mIoU --show-dir /path/to/output/predictions --opacity 1
```
To produce final predictions in the original label mapping (0 = 'background', 1 = 'rigid_plastic', 2 = 'cardboard', 3 = 'metal', 4 = 'soft_plastic'), use the following script:
```shell
python -m tools.convert_visuals_to_labels /path/to/output/predictions /output/label/path/
```

## Submission format
For both phases, we ask the participants to submit their predictions in the following format: the submitted file should be a zip archive containing two folders: **source_only** and **uda** containing predictions of the source-only and adaptation version of their solution. Each folder should contain the predicted label maps in the original label mapping (0 = 'background', 1 = 'rigid_plastic', 2 = 'cardboard', 3 = 'metal', 4 = 'soft_plastic') that should have the same name and file extension as the corresponding input images. 

## Baseline results
The baseline results on the test set of ZeroWaste V2 are as follows:
| Experiment            |    mIoU     |    Acc      |
| -----------           | ----------- | ----------- |
| SegFormer V1          |       45.49      |     91.64       |
| SegFormer Synthwaste->V1 (transfer)|         |         |
| DAFormer V1->V2       |       52.26      |      91.2       |
| DAFormer V1->V2 (SynthWaste-pretrained)|           |         |