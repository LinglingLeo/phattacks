# Traffic Sign Classification

### Notes:
Some results in this data are inconsistent, please use different random seeds and do mutliple tests (Please report what you get in the code). 

## Prepare for the experiment 

Download the [Data](https://github.com/tongwu2020/phattacks/releases/tag/Data%26Model) and put into 'sign' file 

Go to experiment folder to run our experiment.
```
cd experiment 
```
## Training Models

If you don't want to train the model by yourself; 
see [Models](https://github.com/tongwu2020/phattacks/tree/master/sign/donemodel) to find a trained model

### 1. Training CNN with original images 

```
python train_model.py
```


### 2. Adversarial Training & Curriculum Adversarial Training

#### L_inf adversarial training 

```
python linf_retrain.py {}.pt 
```
{} name of your model want to retrain, if doing adversarial training, fill in anything you want to run; 
change the hyper-parameter inside the file.

Currently, the model is used to adversarial training. For curriculum adversarial training, 
change the code in if __name__ == "__main__": refer to roughly line 117. 


### 3. Randomized Smoothing 

#### Training the model with Guassian noise
```
python gaussian_train.py {}.pt 
```

(a) {} name of your model want to train from a clean model, right now it is training from xavier_uniform initial weight,
so type something to fill in {}; 
(b) 'name' of your output models can be changed inside the file;
(c) 'sigma' of gaussian can be changed inside the file; 
(d) 'other' hyperparameter is defult, like epochs and learning rate


### 4. Defending against Rectangular Occlusion Attacks
```
python sticker_retrain.py {}.pt -alpha 0.01 -iters 30 -out 99 -search 1 -epochs 5
```
(a) {}.pt is the name of model you want to train with DOA ;
(b) 'alpha' is learning rate of PGD e.g. 0.01;
(c) 'iters' is the iterations of PGD e.g.30;
(d) 'out' is name of your final model e.g.99;
(e) 'search' is method of searching, '0' is exhaustive_search, '1' is gradient_based_search";
(f) 'epochs' is the epoch you want to fine tune your network e.g. 5;

Note that ROA is a abstract attacking model simulate the "physical" attacks
Thus there is no restriction for the mask to be rectangle

## Testing Models

### 1.Testing a model with original images
```
python test_model.py {}.pt
```
where {} is the name of your model want to test


### 2.Testing a model against L_inf & L_2 attacks

Test the L_inf robustness for single model
```
python linf_attack.py {}.pt
```

Test the L_2 robustness for single model
```
python l2_attack.py {}.pt
```
where {} is the name of your model want to test

Note that you cannot attack randomized smoothing using those following commands, please use smooth_l2attack.py

Test the L_2 robustness for randomized smoothing 
```
python smooth_l2attack.py {}.pt -sigma 1
```

(a) {}.pt is the name of gaussian model you need to train by gaussian_train.py
(b) 'sigma' is the sigma of gaussian noise (I use same sigma with the sigma training the gaussian model)


### 3.Testing a model against physically realizable attacks (stickers)

The attack is in digit space (not involved rotation and scale) (fixed eyeglass frame mask),
and untargeted (maximize the loss of (f(x),y) )

Use mask_l1loss_uniform_rectangles.png

Test the robustness against physically realizable attacks for single model
```
python physical_attack.py {}.pt
```
{} is the name of your model want to attack. Note that you cannot attack randomized smoothing in this file, 
please use smooth_glassattack.py.

iterations = [10,100,1000] # this is default numbers we used in experiment, 
which is the iterations of attacks.


Test the robustness against physically realizable attacks for randomized smoothing
```
python smooth_glassattack.py {}.pt -sigma 1 
```

(a) {}.pt is the name of gaussian model you need to train by gaussian_train.py
(b) sigma is the sigma of gaussian noise (I use same sigma with the sigma training the gaussian model)


iterations = [10,100,1000] # this is default numbers we used in experiment, 
which is the iterations of attacks 


### 4.Testing a model against adversarial patch

Test the robustness against adversarial patch for single model. The attack is learned from 15 images, each class have 1 images. Choose the dataset by your own, pick 15 images, each class (expect stop sign) have 1 images. Put it in 'LISA/Train'.
```
python make_patch.py {}.pt
```
{} is the name of your model want to attack.

Note that you cannot attack randomized smoothing in this file, 
please use smooth_patch.py

Test the robustness against adversarial patch for randomized smoothing
```
python smooth_patch.py {}.pt -sigma 1
```
(a) {} is the name of your model want to attack.
(b) 'sigma' is the sigma of randomized smoothing

### 5.Testing a single model against JSMA 

```
python JSMA.py {}.pt
```
{}.pt is the name of model you want to attack by JSMA

Note that the output will be accuracy when changing 10,100,1000,10000 points
refer to line 125, print count to calculate the exact curve 

### 6.Testing a single model against other physical attacks 

```
python physical_attack.py {}.pt
```
{}.pt is the name of your model want to attack, change different masks to run 
use maskdone4.jpg maskdone5.jpg maskdone6.jpg   
