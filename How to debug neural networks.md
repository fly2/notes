# How to debug neural networks. Manual.

[转载](https://hackernoon.com/how-to-debug-neural-networks-manual-dc2a200f10f2)

![img](https://cdn-images-1.medium.com/max/1000/1*GuCcL7GGuY7fapptMs-B6A.png)

Debugging neural networks can be a tough job even for field expert. Millions of parameters stuck together where even one small change can break all your hard work. Without debugging and visualization all your actions is popping a coin, and what worse it eating your time. Here i gather practices that will help you find problems earlier.

### Dataset problems

**Try to overfit your model with small dataset**General you neural net should overfit your data in a few hundreds of iterations. If your loss doesn’t go down, then your problem is deeper.

**Use iterative logic in solving problem** 
Try to build the simplest network that solve your main problem and then move step by step to global problem. For example, if you are creating style transfer network, try first train your script to transfer style on one image. If it work, only then create model that will transfer style to any image.

**Use balanced datasets with distortions** 
For example, if you train your network to classify data, your training data should have same number of inputs for each class. In other situation there is possibility of overfitting by class. Neural nets are not invariant to all distortions, and you need to train them specifically for that. So making input distortions will increase your network accuracy.

**Network capacity vs dataset size**Your dataset should be enough for network to learn. If you have small dataset and big network it will stop learning(in some cases this will cause same result for big number of different inputs). If you have big dataset and small network then you will see jumping of loss, cause network capacity can’t store so much information.

**Use mean centering** 
This will remove noise data from your network and increase training performance and also in some cases will help with NaN problem. But remember that if you have time-series data then you should use batch centering and not global.

### Neural network problems

**First try simpler model**I saw many cases when people first tried some standard big network like ResNet-50, VGG19, etc, but then found that their problem can be solved on network with just few layers. So if you have not standard problem first start from small networks. The more stuff you add the more harder to train model to solve your problem, so starting from small network will also save time. Also you should remember that big networks eat much memory and ops.

**Visualization is a must**If you are using Tensorflow then definitely start using Tensorboard. If not, try find some visualization tool for your framework, or write it by yourself. This will help you great in finding problems on earlier stages of training. Things that you should definitely visualize: losses, histograms of weights, variables and gradients. If you working with CV then always visualize filters to understand what actually network is seeing.

**Weights initialization **If you set weights incorrectly your network can become untrainable because of zero gradients, or similar updates for all neurons, etc. Also you should remember that weights is coupled with learning rate, so big learning rate and large weights can lead to NaN problem.

For small networks it’s enough to use some Gaussian distribution initializers around 1e-2–1e-3.

For deep networks this will not help, because your weights will be multiplied with each other many times that will lead to very small numbers which will almost kill gradients on back-propagation step. Thanks to Ioffe and Szegedy we now have [Batch-Normalization](https://arxiv.org/abs/1502.03167) that alleviates a lot of headaches.

**Use standard network for standard problem** 
There are plenty of pre-trained models([1](https://github.com/tensorflow/models))([2](https://github.com/tensorflow/models/tree/master/slim#Pretrained)) that you can use right away. In some cases you can use them right away or you can use fine-tuning technique that will save time on training. The main idea is that most of the network capacity is the same for different problems. For example, if we talking about computer vision than first layers of network will consist from simple filters like lines, dot, angel that same for all images, and you don’t need to retrain them.

**Use decay for learning rate**This will almost always give you a boost. There are plenty of different [decay schedulers in Tensorflow](https://www.tensorflow.org/versions/r0.12/api_docs/python/train/decaying_the_learning_rate)

**Use Grid Search or Random Search or Config file for tuning hyper-parameters** 
Don’t try to check all parameters manually it’s very time consuming and not effective. I usually use global config for all parameters and after run checking results to see in which direction i should investigate further. If this method not helping then you can use Random Search, or Grid Search.

**Activation functions**

![img](https://cdn-images-1.medium.com/max/1000/1*DRKBmIlr7JowhSbqL6wngg.png)

1. **Vanishing gradient problems. **
   Some activation function for example Sigmoid and Tanh are suffering from saturation problem. At their extremes their derivative are close to zero that kill gradient and learning process. So it’s good to check different function. Right now the standard activation function is ReLU. Also this problem can appear in very deep or recurrent networks, for example if you have 150 layers and all activation giving 0.9 result then 0.9¹⁵⁰ = 0,000000137. But as i said above Batch-Normalization will help with this and also residual layers.
2. **Not zero centered activation. **
   For example Sigmoid, ReLU function is not zero centered. This mean that during training all you gradients will be all positive or all negative that will cause problem with learning. This also the reason why we use zero-centered input data.
3. **Dead ReLUs. **
   The standard ReLU function also not perfect. The problem that for negative numbers ReLU giving 0 that mean that they will not be activated, and thus some part of your neurons will be dead and never used. The reasons why this can happen is large learning rate and wrong weights initialization. If parameter tweaking is not helping you can try Leaky ReLU, PReLU, ELU or Maxout that does not have this problem.
4. **Exploding gradients.**
   The problem is same as vanishing problem except with each step gradients became larger and larger. One of the main fixes for this is use gradient clipping, basically set hard limit for gradient.

**Network accuracy degradation on deep networks**The problem that really deep networks from some point starting to behave as a broken phone. Thus adding more layers decrease network accuracy. Fix for this is to use Residual layers that passing some portion of input across all layers. On image bottleneck residual layer.

![img](https://cdn-images-1.medium.com/max/1000/1*M1J-VkbWPBKFTKgqtC29kQ.png)

> If you problem not covered above ask in the comments and i will try to help you . 
> Also i will be appreciated for more debug cases that i can add to this article.

> Next article will be about predicting bitcoin price with FCN network