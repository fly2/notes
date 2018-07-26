## hash使用

### hashlib

#### hashlib的主要方法和常量

| 名称      | 描述               |
| --------- | ------------------ |
| md5(…)    | 利用md5算法加密    |
| sha1(…)   | 利用sha1算法加密   |
| sha224(…) | 利用sha224算法加密 |
| sha256(…) | 利用sha256算法加密 |
| sha384(…) | 利用sha384算法加密 |
| sha512(…) | 利用sha512算法加密 |

#### Hash对象特有的方法

如果你利用`hashlib`生成了一个Hash对象，那么这个Hash对象会包含如下方法：

| 名称         | 描述                                                      |
| ------------ | --------------------------------------------------------- |
| update(arg)  | 可以重复利用指定了特殊加密算法的Hash对象，对`arg`进行加密 |
| digest(…)    | 以字符形式返回加密内容                                    |
| hexdigest(…) | 以16进制形式返回加密内容                                  |
| copy(…)      | 为了达到重复利用Hash对象的目的，而克隆Hash对象            |

#### 示例

##### **1、直接使用hashlib方法**

```python
>>> hashlib.sha224(b"Nobody inspects the spammish repetition")
<sha224 HASH object @ 0x7f99432c5b28>

>>> hashlib.sha224(b"Nobody inspects the spammish repetition").hexdigest()
'a4337bc45a8fc544c03f52dc550cd6e1e87021bc896588bd79e901e2'
```

##### **2、直接使用Hash对象中的方法**

```python
>>> m = hashlib.md5()
>>> m
<md5 HASH object @ 0x7f99432c5468>
>>> m.update(b"Nobody inspects")
>>> m.digest()
'>\xf7)\xcc\xf0\xccV\x07\x9c\xa5F\xd5\x80\x83\xdc\x12'
>>> m.update(b" the spammish repetition")
>>> m.digest()
'\xbbd\x9c\x83\xdd\x1e\xa5\xc9\xd9\xde\xc9\xa1\x8d\xf0\xff\xe9'
>>> m.hexdigest()
'bb649c83dd1ea5c9d9dec9a18df0ffe9'
```

##### 3.转换成二进制

**注意：**取二进制时只取b[2:],因为bin返回值以`'0b'`开头。

```python
s=hashlib.sha224(b"Nobody inspects the spammish repetition").hexdigest()
i=int(s,16)
b=bin(i)
->
'0b10100100001100110111101111000100010110101000111111000101010001001100000000111111010100101101110001010101000011001101011011100001111010000111000000100001101111001000100101100101100010001011110101111001111010010000000111100010'
```

### crypt