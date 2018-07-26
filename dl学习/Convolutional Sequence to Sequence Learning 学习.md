# Convolutional Sequence to Sequence Learning 学习

[Convolutional Sequence to Sequence Learning](https://arxiv.org/pdf/1705.03122.pdf)

[Language Modeling with Gated Convolutional Networks](https://arxiv.org/pdf/1612.08083.pdf)

### 位置向量

在fairseq中位置向量是和词向量相加产生新的向量去做后续输入。词向量的[官方实现](https://github.com/facebookresearch/fairseq/blob/c2ac281d98ad2df5cb93af4cadb0f5fa40d176b6/fairseq/models/avgpool_model.lua)方式如下，即生成指定尺寸，初始值为[-0.05,0.05]之间。

```Lua
--位置向量定义
local embedPosition =
            mutils.makeLookupTable(config, 1024, dict:getPadIndex())

--config中对init_range的定义
cmd:option('-init_range', 0.05, 'range for random weight initialization')

--生成size，config.nembed大小的tensor，值为(-config.init_range, config.init_range)范围内
function mutils.makeLookupTable(config, size, pad)
    local lut = nn.LookupTable(size, config.nembed, pad)
    --给weight赋值，在pytorch中使用nn.Embedding得到嵌入向量，可以通过_weight来指定已有词向量或空间分布。
    lut.weight:uniform(-config.init_range, config.init_range)
    return lut
end



--torch中的LookupTable定义
function LookupTable:__init(nIndex, nOutput, paddingValue, maxNorm, normType)
   parent.__init(self)

   self.weight = torch.Tensor(nIndex, nOutput)
   self.gradWeight = torch.Tensor(nIndex, nOutput):zero()
   self.paddingValue = paddingValue or 0
   self.maxNorm = maxNorm or nil
   self.normType = normType or nil

   self:reset()
end
```

下面是[非官方实现](https://github.com/tobyyouup/conv_seq2seq/blob/78a6e4e62a4c57a5caa9d584033a85e810fd726e/seq2seq/encoders/pooling_encoder.py)的词向量
$$
Embedding_{i,j}=1-\frac{j+1}{ls}-\frac{k+1}{le}\times(1-2\times\frac{j+1}{ls})
$$
其中$Embedding$为嵌入矩阵，$i$为序列顺序，$j$为词向量位置顺序。

```python
def position_encoding(sentence_size, embedding_size):
  """
  Position Encoding described in section 4.1 of
  End-To-End Memory Networks (https://arxiv.org/abs/1503.08895).
  Args:
    sentence_size: length of the sentence
    embedding_size: dimensionality of the embeddings
  Returns:
    A numpy array of shape [sentence_size, embedding_size] containing
    the fixed position encodings for each sentence position.
  """
  encoding = np.ones((sentence_size, embedding_size), dtype=np.float32)
  ls = sentence_size + 1
  le = embedding_size + 1
  for k in range(1, le):
    for j in range(1, ls):
      encoding[j-1, k-1] = (1.0 - j/float(ls)) - (
          k / float(le)) * (1. - 2. * j/float(ls))
  return encoding
```

