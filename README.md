# Person-reid-GAN-pytorch
A Pytorch Implementation of ["Unlabeled Samples Generated by GAN Improve the Person Re-identification Baseline in vitro"(ICCV17)](http://openaccess.thecvf.com/content_ICCV_2017/papers/Zheng_Unlabeled_Samples_Generated_ICCV_2017_paper.pdf), the official code is available [here](https://github.com/layumi/Person-reID_GAN)(in matlab).


We arrived **Rank@1=88.24%, mAP=70.68%** only with softmax loss.

**What's new:** You may use '--use_dense' to use `DenseNet-121`. It can easily arrive **Rank@1=89.91% mAP=73.58%**. Trained DenseNet-121 model can be found at [GoogleDrive](https://drive.google.com/open?id=1NgZWnYBCzESgKNzLeoWUMxggZ6SSEaZL).(Note that ResNet-50 is a more common choice as the baseline.)

**What's new:** Re-ranking is added to evaluation. The re-ranked result is **Rank@1=90.20% mAP=84.76%**.

**What's new:** Random Erasing is added to train. the source is available [here](https://github.com/zhunzhong07/Random-Erasing)

## Model Structure（we simply alter the model from Residual Networks and DenseNet）
You may learn more from `model.py`. 
We add one linear layer(bottleneck), one batchnorm layer and relu.

## Prerequisites

- Python 2.7
- GPU 
- Numpy
- Pytorch
- Torchvision

## Getting started
### Installation
- Install Pytorch from http://pytorch.org/
- Install Torchvision from the source
```
git clone https://github.com/pytorch/vision
cd vision
python setup.py install
```
## Dataset & Preparation
Download [Market1501 Dataset](http://www.liangzheng.org/Project/project_reid.html)

Preparation: change the name of the folder to 0 to n-1(where n is the number of class, i.e. the number of person),  the folder name is the label of each person (all the pictures under the same folder are the same person) 
```bash
python changeIndex.py
```
the usage of the DCGAN model is under the DCGAN folder,  first use market1501 to train the dcgan model,  then generated some pictures using the trained DCGAN model,  then you can use different numbers of generated images to help train the model using LSRO. 

Our baseline code is only finetuned from resNet or DenseNet,  
we use pretained DenseNet as baseline to train our model, the archieved result are as follows:

|Rank@1 | mAP | Note|
|0.921  |   0.793| ----|
|0.934   |  0.907|re-rank|

re-rank strategy is available [here](https://github.com/zhunzhong07/person-re-ranking)

using LSRO loss and added some image generated by DCGAN model, the achieved result are as follow:

 |Rank@1 | mAP | Note|
 | ----- | ---- | ---- |
 | 86.67 | 68.19 | add 6000 generated image|
 | 87.98 | 69.38 | after re-rank|
 | **88.24** | **70.68** | add 12000 generated image|
 | 87.98 | 69.38 | after re-rank|
 | 86.67 | 68.19 | add 18000 generated image|
 | 87.98 | 69.38 | after re-rank|
 | 86.67 | 68.19 | add 24000 generated image|
 | 87.98 | 69.38 | after re-rank|
 | 86.67 | 68.19 | add 30000 generated image|
 | 87.98 | 69.38 | after re-rank|

To save trained model, we make a dir.
```bash
mkdir model 
```


## Train
Train the baseline by
```bash
python train_baseline.py --use_dense
```
or train the LSRO based model by
```bash
python train_LSRO.py --use_dense
```

`--name` the name of model.(ResNet or DesNet)

`--data_dir` the path of the training data.

`--batchsize` batch size.

`--erasing_p` random erasing probability.


## Test
Use trained model to extract feature by
```bash
python test.py   --which_epoch 99  --use_dense
```
`--gpu_ids` which gpu to run.

`--name` the dir name of trained model.

`--which_epoch` select the i-th model.

`--data_dir` the path of the testing data.

`--batchsize` batch size.


## Evaluation
```bash
python evaluate.py
```
It will output Rank@1, Rank@5, Rank@10 and mAP results.

For mAP calculation, you also can refer to the [C++ code for Oxford Building](http://www.robots.ox.ac.uk/~vgg/data/oxbuildings/compute_ap.cpp). We use the triangle mAP calculation (consistent with the Market1501 original code).

### re-ranking
```bash
python evaluate_rerank.py
```
**It may take more than 10G Memory to run.** So run it on a powerful machine if possible. 

It will output Rank@1, Rank@5, Rank@10 and mAP results.



## Thanks

* Many, many thanks to [layumi](https://github.com/layumi/Person-reID_GAN) for his Great work!
  
### p.s. If you have any questions, you can open an issue!
