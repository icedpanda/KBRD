## [KBRD: Towards Knowledge-Based Recommender Dialog System](https://arxiv.org/abs/1908.05391)

[![GitHub stars](https://img.shields.io/github/stars/THUDM/KBRD)](https://github.com/THUDM/KBRD/stargazers)
[![GitHub license](https://img.shields.io/github/license/THUDM/KBRD)](https://github.com/THUDM/KBRD/blob/master/LICENSE)

Paper accepted at EMNLP-IJCNLP 2019. Latest version
at [arXiv](https://arxiv.org/abs/1908.05391).

* **New:** code and README are improved.
* We curated a paper list for NLP + Recommender System
  at [THUDM/NLP4Rec-Papers](https://github.com/THUDM/NLP4Rec-Papers).
  Contributions are welcome.

## Prerequisites

- Linux
- Python 3.6
- PyTorch == 1.7.0

## Getting Started

### Installation

Clone this repo.

```bash
git clone https://github.com/THUDM/KBRD
cd KBRD
```

Please install dependencies by

**Update with my workaround pytorch version**

```bash
# install cudatoolkts based on your gpu type
# Latest pytorch is not compatible with this repo and took me a while to find the right version.
conda install pytorch==1.7.1 torchvision==0.8.2 torchaudio==0.7.2 cudatoolkit=11.0 -c pytorch

# depend on your gpu type
# https://pytorch-geometric.readthedocs.io/en/latest/
# https://pytorch-geometric.com/whl/torch-{TORCH_version}+{CUDA}.html
# I was only able to install 2.0.5, 0.6.12, 2.0.3 for my GPU.
# it may take up to 60 minutes to install. :(
pip install torch-scatter==2.0.5 -f https://pytorch-geometric.com/whl/torch-1.7.1+cu110.html
pip install torch-spare==0.6.12 -f https://pytorch-geometric.com/whl/torch-1.7.1+cu110.html
pip install torch-geometric==2.0.3 -f https://pytorch-geometric.com/whl/torch-1.7.1+cu110.html
```

Install dependencies with

```bash
pip install -r requirements.txt
```

### Dataset

- We use the **ReDial** dataset, which will be automatically downloaded by the
  script.
- Download the refined knowledge base (dbpedia) used in this
  paper [[Google Drive]](https://drive.google.com/open?id=1WqRoQAxH_kdoJpbYVsFF0EN4ZJxiiDB2)
  . Decompress it and get the `<path/to/KBRD/dbpedia/>` folder, which should
  contain two files `mappingbased_objects_en.ttl` and `short_abstracts_en.ttl`.
- Download the proprocessed extracted entities
  set [[Google Drive]](https://drive.google.com/open?id=1OG-kNIeUi3i0UDNhJVMEnia9JeRAHVXB)
  and put it under `<path/to/KBRD/data/redial/`.

### Update path

You may need to update the module path in the follow files to match your
environment. (line 10)

- `parlai/tasks/redial/train_kbrd.py`
- `parlai/tasks/redial/train_transformer_rec.py`

### Training

Prequisite: if you are running wsl in windows

```bash
# To convert .sh to Unix line endings on Cygwin, use
dos2unix scripts/both.sh
dos2unix scripts/t2t_rec_rgcn.sh
```

1. To train the recommender part, run:

```bash
bash scripts/both.sh <num_exps> <gpu_id>
(optional) bash scripts/baseline.sh <num_exps> <gpu_id>
```

2. To train the dialog part, run:

```bash
bash scripts/t2t_rec_rgcn.sh <num_exps> <gpu_id>
```

The test results are displayed at the end of training and can also be found
at `saved/<model_name>.test`.

### Logging

Training outputs, TensorBoard logs and models files are be saved in `saved/`
folder.

### Evaluation

1. `scripts/score.py` is used to hypothesis testing the significance of
   improvement between different models. To use, first run multiple experiments
   with `num_exps > 1`, for example:

```
bash scripts/both.sh 2 <gpu_id>
bash scripts/baseline.sh 2 <gpu_id>
```

Then,

```bash
python scripts/score.py --name-1 saved/release_baseline --name-2 saved/both_rgcn --num 2 --metric recall@50
```

where you should remove the trailing `_0`, `_1` automatically added to the
model names, `nums` should be set the same as `num_exps` above, and `recall@50`
can be replaced with other evaluation metrics in the paper.

Sample output:

```
[0.298, 0.2918]
0.2949
0.0031
[0.3417, 0.3369]
0.3393
0.0024
Ttest_indResult(statistic=-11.325204070341204, pvalue=0.007706635327863829)
```

2. `scripts/display_model.py` is used to generate responses.

```bash
python scripts/display_model.py -t redial -mf saved/transformer_rec_both_rgcn_0 -dt test
```

Example output (\[TorchAgent\] is our model output):

```
~~
[eval_labels_choice]: Oh, you like scary movies?
I recently watched __unk__
[movies]:
  37993
[redial]: 
Hello!
Hello!
What kind of movies do you like?
I am looking for a movie recommendation.   When I was younger I really enjoyed the __unk__
[label_candidates: 3|37993|50395||Oh, you like scary movies?
I recently watched __unk__]
[eval_labels: Oh, you like scary movies?
I recently watched __unk__]
   [TorchAgent]: have you seen "The Shining  (1980)" ?
~~
```

3. `scripts/show_bias.py` is used to show the vocabulary bias of a specific
   movie (like the qualitative analysis in Table 4)

```bash
python scripts/show_bias.py -mf saved/transformer_rec_both_rgcn_0
```

## ❗ Common Q&A

1. Understanding model outputs.
   Please see https://github.com/THUDM/KBRD/issues/15#issuecomment-636367468.

2. Adapting this code to other datasets.
   It is not straightforward for this code to be run on other datasets
   currently.
   The main reason is that we cached the entity linking process in KBRD for
   ReDial. Please
   see https://github.com/THUDM/KBRD/issues/10#issuecomment-585261932 for
   details.

3. Why the recommender and the dialog part are trained separatedly?
   Please refer
   to https://github.com/THUDM/KBRD/issues/9#issuecomment-556988541 for
   detailed explanation.

If you have additional questions, please let us know.

## Cite

Please cite our paper if you use this code in your own work:

```
@article{chen2019towards,
  title={Towards Knowledge-Based Recommender Dialog System},
  author={Chen, Qibin and Lin, Junyang and Zhang, Yichang and Ding, Ming and Cen, Yukuo and Yang, Hongxia and Tang, Jie},
  journal={arXiv preprint arXiv:1908.05391},
  year={2019}
}
```
