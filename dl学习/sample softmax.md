## sample softmax

[原论文](https://arxiv.org/pdf/1412.2007.pdf)

[tensorflow关于candidate sampling的文档](https://www.tensorflow.org/extras/candidate_sampling.pdf)

### 1.问题背景

在神经机器翻译论文[Neural Machine Translation by Jointly Learning to Align and Translate](https://arxiv.org/pdf/1409.0473v7.pdf)中，带有attention的decoder中权重的公式如下：
$$
\alpha_t=\frac{exp\{a(h_t,z_{t-1})\}}{\sum_kexp\{a(h_k,z{t-1})\}}......(5)
$$
其中的a为一个单层的前馈神经网络，根据αt和输入的因状态，我们就可以得到一个context vector $c_t$。在decoder的t时刻，输出的目标词汇的概率可以使用如下公式计算： 
$$
p(y_t|y_{<t},x)=\frac{1}{Z}exp\{w^T_t\phi(y_{t-1},z_t,c_t)+b_t\}......(6)
$$
其中，$y_{t-1}$是上一次的输出，$z_t$为当前decoder的隐状态，$c_t$为context vector。
$$
Z=\sum_{k:y_k\in V}exp\{w_k^T\phi(y_{t-1},z+t,c_t)+b_k\}......(7)
$$
由于Z的计算，导致每次计算每个词的概率都需要遍历整个词表V。

当输出的词汇表巨大时，会导致计算量巨大，导致训练效率较低。为了提高效率，会选择词频较高的top n个词，除这n个词之外的其他词，使用unk来表示，而当一个句子中unk过多时，会影响模型性能，导致结果较差。

### 2.解决方法

在[这篇文章](https://arxiv.org/pdf/1412.2007.pdf)中，作者所提出的是一个基于模型的改进方法，使得我们在NMT应用时可以使用较大的词表。同时，本文提出的方法能够将训练时的计算复杂度降到一个常数上（而不是随着词表增大而增大），同时降低空间开销（这样在GPU上等地方跑就容易多了）。 
我们前面已经说到了，训练时的开销，主要是集中在式子6之上，式6需要遍历整个词表V，才能获得其用来normalization的项Z。那么本文的解决思想，也就就是让Z的计算不需要遍历整个词表Z，而是遍历一个更小的子集V’。

对于**式6取对数**后，其梯度计算的结果如下： 

![img](/picture/sample_softmax0.png)
对于式8当中的第二项，其本质上，可以看做是如9那样的能量函数的一个期望（词表中所有词的概率*其梯度）。 
第二项的计算需要遍历整个词表，*那么本文提出的方法，就是如何去近似的求解式子9，替换式子8当中的第二项。* 具体的解决方法，就是根据预先给定的分布Q，和一个抽样的子集V’，使用这个V’去近似的表示式9： 
![这里写图片描述](/picture/sample_softmax1.png) 
于是，在计算时，我们仅仅使用这个$V’$大小的词去近似的得到Z，然后更新$W_t$（当前应该预测出来的词）的参数。

在实际的使用中，这个V’当然不会是随便取得，不然这样会让一个句子中所有词对应的参数无法完全更新。为了解决这个问题，本文的做法是根据训练数据划分出很多的V’。简单来说，就是在训练之前，设定一个Sample-Nums来表示V’的大小，然后我们从语料的头开始，每一句所出现的词加入到当前的集合V’当中，直到V’达到了Sample-Nums的大小，那我们从下一句开始，就按照这个方式重新建立一个V’，知道所有预料都遍历完成了。这样就保证了一个句子对应的那个V’，V’中的词能够完整覆盖句子的所有词。

其中Q指定的概率分布如下，如果当前的词yt，在v’,在V’中的所有词的概率一样，不然全部是0： 
![这里写图片描述](/picture/sample_softmax2.png) 
这种Q就能够抵消了（10-11）中的-log的项目，这样我们就能近似的表示出（6）了： 
![这里写图片描述](/picture/sample_softmax3.png)

### 3.建议

在完成了训练之后，在实际解码中，是可以按照传统方法完全利用整个词表的，不过这样同样也是开销很大的。其实在解码过程中，也可以利用整个Sample的机制，只不过是选择V’就成了一个技术活了，因为他不像训练的时候，该有的数据都是有的。

具体来说，可以简单的选择频率前K个词，或者使用一些对齐模型 根据源语句 找出最可能的前K个词等，这里作者没有多说。

### 4.tensorflow代码

#### sampled_softmax_loss

```python
def sampled_softmax_loss(weights,
                         biases,
                         labels,
                         inputs,
                         num_sampled,
                         num_classes,
                         num_true=1,
                         sampled_values=None,
                         remove_accidental_hits=True,
                         partition_strategy="mod",
                         name="sampled_softmax_loss"):
  """Computes and returns the sampled softmax training loss.
  This is a faster way to train a softmax classifier over a huge number of
  classes.
  This operation is for training only.  It is generally an underestimate of
  the full softmax loss.
  A common use case is to use this method for training, and calculate the full
  softmax loss for evaluation or inference. In this case, you must set
  `partition_strategy="div"` for the two losses to be consistent, as in the
  following example:
  ```python
  if mode == "train":
    loss = tf.nn.sampled_softmax_loss(
        weights=weights,
        biases=biases,
        labels=labels,
        inputs=inputs,
        ...,
        partition_strategy="div")
  elif mode == "eval":
    logits = tf.matmul(inputs, tf.transpose(weights))
    logits = tf.nn.bias_add(logits, biases)
    labels_one_hot = tf.one_hot(labels, n_classes)
    loss = tf.nn.softmax_cross_entropy_with_logits(
        labels=labels_one_hot,
        logits=logits)
  ```
  See our [Candidate Sampling Algorithms Reference]
  (https://www.tensorflow.org/extras/candidate_sampling.pdf)
  Also see Section 3 of [Jean et al., 2014](http://arxiv.org/abs/1412.2007)
  ([pdf](http://arxiv.org/pdf/1412.2007.pdf)) for the math.
  Args:
    weights: A `Tensor` of shape `[num_classes, dim]`, or a list of `Tensor`
        objects whose concatenation along dimension 0 has shape
        [num_classes, dim].  The (possibly-sharded) class embeddings.
    biases: A `Tensor` of shape `[num_classes]`.  The class biases.
    labels: A `Tensor` of type `int64` and shape `[batch_size,
        num_true]`. The target classes.  Note that this format differs from
        the `labels` argument of `nn.softmax_cross_entropy_with_logits`.
    inputs: A `Tensor` of shape `[batch_size, dim]`.  The forward
        activations of the input network.
    num_sampled: An `int`.  The number of classes to randomly sample per batch.
    num_classes: An `int`. The number of possible classes.
    num_true: An `int`.  The number of target classes per training example.
    sampled_values: a tuple of (`sampled_candidates`, `true_expected_count`,
        `sampled_expected_count`) returned by a `*_candidate_sampler` function.
        (if None, we default to `log_uniform_candidate_sampler`)
    remove_accidental_hits:  A `bool`.  whether to remove "accidental hits"
        where a sampled class equals one of the target classes.  Default is
        True.
    partition_strategy: A string specifying the partitioning strategy, relevant
        if `len(weights) > 1`. Currently `"div"` and `"mod"` are supported.
        Default is `"mod"`. See `tf.nn.embedding_lookup` for more details.
    name: A name for the operation (optional).
  Returns:
    A `batch_size` 1-D tensor of per-example sampled softmax losses.
  """
  logits, labels = _compute_sampled_logits(
      weights=weights,
      biases=biases,
      labels=labels,
      inputs=inputs,
      num_sampled=num_sampled,
      num_classes=num_classes,
      num_true=num_true,
      sampled_values=sampled_values,
      subtract_log_q=True,
      remove_accidental_hits=remove_accidental_hits,
      partition_strategy=partition_strategy,
      name=name)
  sampled_losses = nn_ops.softmax_cross_entropy_with_logits(
      labels=labels, logits=logits)
  # sampled_losses is a [batch_size] tensor.
  return sampled_losses


def _compute_sampled_logits(weights,
                            biases,
                            labels,
                            inputs,
                            num_sampled,
                            num_classes,
                            num_true=1,
                            sampled_values=None,
                            subtract_log_q=True,
                            remove_accidental_hits=False,
                            partition_strategy="mod",
                            name=None):
  """Helper function for nce_loss and sampled_softmax_loss functions.
  Computes sampled output training logits and labels suitable for implementing
  e.g. noise-contrastive estimation (see nce_loss) or sampled softmax (see
  sampled_softmax_loss).
  Note: In the case where num_true > 1, we assign to each target class
  the target probability 1 / num_true so that the target probabilities
  sum to 1 per-example.
  Args:
    weights: A `Tensor` of shape `[num_classes, dim]`, or a list of `Tensor`
        objects whose concatenation along dimension 0 has shape
        `[num_classes, dim]`.  The (possibly-partitioned) class embeddings.
    biases: A `Tensor` of shape `[num_classes]`.  The (possibly-partitioned)
        class biases.
    labels: A `Tensor` of type `int64` and shape `[batch_size,
        num_true]`. The target classes.  Note that this format differs from
        the `labels` argument of `nn.softmax_cross_entropy_with_logits`.
    inputs: A `Tensor` of shape `[batch_size, dim]`.  The forward
        activations of the input network.
    num_sampled: An `int`.  The number of classes to randomly sample per batch.
    num_classes: An `int`. The number of possible classes.
    num_true: An `int`.  The number of target classes per training example.
    sampled_values: a tuple of (`sampled_candidates`, `true_expected_count`,
        `sampled_expected_count`) returned by a `*_candidate_sampler` function.
        (if None, we default to `log_uniform_candidate_sampler`)
    subtract_log_q: A `bool`.  whether to subtract the log expected count of
        the labels in the sample to get the logits of the true labels.
        Default is True.  Turn off for Negative Sampling.
    remove_accidental_hits:  A `bool`.  whether to remove "accidental hits"
        where a sampled class equals one of the target classes.  Default is
        False.
    partition_strategy: A string specifying the partitioning strategy, relevant
        if `len(weights) > 1`. Currently `"div"` and `"mod"` are supported.
        Default is `"mod"`. See `tf.nn.embedding_lookup` for more details.
    name: A name for the operation (optional).
  Returns:
    out_logits, out_labels: `Tensor` objects each with shape
        `[batch_size, num_true + num_sampled]`, for passing to either
        `nn.sigmoid_cross_entropy_with_logits` (NCE) or
        `nn.softmax_cross_entropy_with_logits` (sampled softmax).
  """

  if isinstance(weights, variables.PartitionedVariable):
    weights = list(weights)
  if not isinstance(weights, list):
    weights = [weights]

  with ops.name_scope(name, "compute_sampled_logits",
                      weights + [biases, inputs, labels]):
    if labels.dtype != dtypes.int64:
      labels = math_ops.cast(labels, dtypes.int64)
    labels_flat = array_ops.reshape(labels, [-1])

    # Sample the negative labels.
    #   sampled shape: [num_sampled] tensor
    #   true_expected_count shape = [batch_size, 1] tensor
    #   sampled_expected_count shape = [num_sampled] tensor
    if sampled_values is None:
      sampled_values = candidate_sampling_ops.log_uniform_candidate_sampler(
          true_classes=labels,
          num_true=num_true,
          num_sampled=num_sampled,
          unique=True,
          range_max=num_classes)
    # NOTE: pylint cannot tell that 'sampled_values' is a sequence
    # pylint: disable=unpacking-non-sequence
    sampled, true_expected_count, sampled_expected_count = (
        array_ops.stop_gradient(s) for s in sampled_values)
    # pylint: enable=unpacking-non-sequence
    sampled = math_ops.cast(sampled, dtypes.int64)

    # labels_flat is a [batch_size * num_true] tensor
    # sampled is a [num_sampled] int tensor
    all_ids = array_ops.concat([labels_flat, sampled], 0)

    # Retrieve the true weights and the logits of the sampled weights.

    # weights shape is [num_classes, dim]
    all_w = embedding_ops.embedding_lookup(
        weights, all_ids, partition_strategy=partition_strategy)

    # true_w shape is [batch_size * num_true, dim]
    true_w = array_ops.slice(all_w, [0, 0],
                             array_ops.stack(
                                 [array_ops.shape(labels_flat)[0], -1]))

    sampled_w = array_ops.slice(
        all_w, array_ops.stack([array_ops.shape(labels_flat)[0], 0]), [-1, -1])
    # inputs has shape [batch_size, dim]
    # sampled_w has shape [num_sampled, dim]
    # Apply X*W', which yields [batch_size, num_sampled]
    sampled_logits = math_ops.matmul(inputs, sampled_w, transpose_b=True)

    # Retrieve the true and sampled biases, compute the true logits, and
    # add the biases to the true and sampled logits.
    all_b = embedding_ops.embedding_lookup(
        biases, all_ids, partition_strategy=partition_strategy)
    # true_b is a [batch_size * num_true] tensor
    # sampled_b is a [num_sampled] float tensor
    true_b = array_ops.slice(all_b, [0], array_ops.shape(labels_flat))
    sampled_b = array_ops.slice(all_b, array_ops.shape(labels_flat), [-1])

    # inputs shape is [batch_size, dim]
    # true_w shape is [batch_size * num_true, dim]
    # row_wise_dots is [batch_size, num_true, dim]
    dim = array_ops.shape(true_w)[1:2]
    new_true_w_shape = array_ops.concat([[-1, num_true], dim], 0)
    row_wise_dots = math_ops.multiply(
        array_ops.expand_dims(inputs, 1),
        array_ops.reshape(true_w, new_true_w_shape))
    # We want the row-wise dot plus biases which yields a
    # [batch_size, num_true] tensor of true_logits.
    dots_as_matrix = array_ops.reshape(row_wise_dots,
                                       array_ops.concat([[-1], dim], 0))
    true_logits = array_ops.reshape(_sum_rows(dots_as_matrix), [-1, num_true])
    true_b = array_ops.reshape(true_b, [-1, num_true])
    true_logits += true_b
    sampled_logits += sampled_b

    if remove_accidental_hits:
      acc_hits = candidate_sampling_ops.compute_accidental_hits(
          labels, sampled, num_true=num_true)
      acc_indices, acc_ids, acc_weights = acc_hits

      # This is how SparseToDense expects the indices.
      acc_indices_2d = array_ops.reshape(acc_indices, [-1, 1])
      acc_ids_2d_int32 = array_ops.reshape(
          math_ops.cast(acc_ids, dtypes.int32), [-1, 1])
      sparse_indices = array_ops.concat([acc_indices_2d, acc_ids_2d_int32], 1,
                                        "sparse_indices")
      # Create sampled_logits_shape = [batch_size, num_sampled]
      sampled_logits_shape = array_ops.concat(
          [array_ops.shape(labels)[:1],
           array_ops.expand_dims(num_sampled, 0)], 0)
      if sampled_logits.dtype != acc_weights.dtype:
        acc_weights = math_ops.cast(acc_weights, sampled_logits.dtype)
      sampled_logits += sparse_ops.sparse_to_dense(
          sparse_indices,
          sampled_logits_shape,
          acc_weights,
          default_value=0.0,
          validate_indices=False)

    if subtract_log_q:
      # Subtract log of Q(l), prior probability that l appears in sampled.
      true_logits -= math_ops.log(true_expected_count)
      sampled_logits -= math_ops.log(sampled_expected_count)

    # Construct output logits and labels. The true labels/logits start at col 0.
    out_logits = array_ops.concat([true_logits, sampled_logits], 1)
    # true_logits is a float tensor, ones_like(true_logits) is a float tensor
    # of ones. We then divide by num_true to ensure the per-example labels sum
    # to 1.0, i.e. form a proper probability distribution.
    out_labels = array_ops.concat([
        array_ops.ones_like(true_logits) / num_true,
        array_ops.zeros_like(sampled_logits)
    ], 1)

  return out_logits, out_labels
```

