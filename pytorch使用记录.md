## pytorch使用记录	

### torch.Tensor

#### view()

将Tensor转换为指定的形状,当有形状（.view(-1,8)）中-1表示该维度依赖于其他维度情况。

```python
#torch.Tensor.view(*args)
#*args 形状参数

>>> x = torch.randn(4, 4)
>>> x.size()
torch.Size([4, 4])
>>> y = x.view(16)
>>> y.size()
torch.Size([16])
>>> z = x.view(-1, 8)  # the size -1 is inferred from other dimensions
>>> z.size()
torch.Size([2, 8])
```

#### view_as(other)

将张量变成other张量的形状

```python
torch.Tensor.view_as(other)
#other 其他张量
```

#### zero_()

为张量填充0，可以认为返回和张量一样形状的0张量

```python
zero_() 


a=torch.arange(1, 8)
a.zero_()
->tensor([ 0.,  0.,  0.,  0.,  0.,  0.,  0.])
```

 