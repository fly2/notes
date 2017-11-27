## nn发展总结

目前来看，很多对 NN 的贡献（特别是核心的贡献），都在于 NN 的梯度流上，比如

sigmoid 会饱和，造成梯度消失。于是有了 ReLU。
ReLU 负半轴是死区，造成梯度变 0。于是有了 LeakyReLU，PReLU。
强调梯度和权值分布的稳定性，由此有了 ELU，以及较新的 SELU。
太深了，梯度传不下去，于是有了 highway。
干脆连 highway 的参数都不要，直接变残差，于是有了 ResNet。
强行稳定参数的均值和方差，于是有了 BatchNorm。
在梯度流中增加噪声，于是有了 Dropout。
RNN 梯度不稳定，于是加几个通路和门控，于是有了 LSTM。
LSTM 简化一下，有了 GRU。
GAN 的 JS 散度有问题，会导致梯度消失或无效，于是有了 WGAN。
WGAN 对梯度的 clip 有问题，于是有了 WGAN-GP。