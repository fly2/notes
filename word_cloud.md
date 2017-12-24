## word_cloud

[官方博客](https://amueller.github.io/word_cloud/)

[github](https://github.com/amueller/word_cloud)

### wordcloud类

词云对象，用来生成词云

```python
wordcloud.WordCloud(font_path=None, width=400, height=200, margin=2, ranks_only=None, prefer_horizontal=0.9, mask=None, scale=1, color_func=None, max_words=200, min_font_size=4, stopwords=None, random_state=None, background_color='black', max_font_size=None, font_step=1, mode='RGB', relative_scaling=0.5, regexp=None, collocations=True, colormap=None, normalize_plurals=True)
```

### 常用方法

| 方法名                                      | 介绍                                       |
| ---------------------------------------- | ---------------------------------------- |
| [`fit_words`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.fit_words)(frequencies) | 生成词云，从一个包含词和词频的元组。例如：[('d', 4), ('c', 3), ('b', 2), ('a', 1)] |
| [`generate`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.generate)(text) | 从文本中生成词云。文本中的词按空格分开                      |
| [`generate_from_frequencies`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.generate_from_frequencies)(frequencies[, ...]) | 生成词云从词和词频的字典中。示例：{'a': 1, 'b': 2, 'c': 3, 'd': 4} |
| [`generate_from_text`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.generate_from_text)(text) | 从文本中生成词云。和`generate(text)`一样。            |
| [`process_text`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.process_text)(text) | 将文本切分成词，并去掉停用词。                          |
| [`recolor`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.recolor)([random_state, color_func, colormap]) | 重新着色现有的词云。                               |
| [`to_array`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.to_array)() | 转换成numpy矩阵                               |
| [`to_file`](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud.to_file)(filename) | 导出为image文件                               |

### 示例

```python
# -*- coding: utf-8 -*-
"""
Created on Thu Feb  9 13:46:38 2017

@author: shangguanxf
"""

import matplotlib.pyplot as plt
import pickle
from wordcloud import WordCloud,STOPWORDS,ImageColorGenerator
from PIL import Image
import numpy as np
import pandas as pd
import random
from os import path
#得到地址
d = path.dirname('.')
#定义色系
def grey_color_func(word, font_size, position, orientation, random_state=None, **kwargs):
    return "hsl(0, 0%%, %d%%)" % random.randint(60, 100)
def get_wc(f,name):
    #读取文本，需分好词（以空格隔开词）
    #text = open('/home/weblogic/DATA/private/shangguanxf/cc_bigdata/create/寿险预约咨询工单.txt', 'r', encoding='utf-8').read()
    #backgroud_Image = plt.imread('/home/weblogic/DATA/private/shangguanxf/pictures/colored_3.png')
    #plt.imread图片只支持.jpg格式
    back_coloring = plt.imread('C:/Users/shangguanxf/Desktop/picture/cloud.jpg')
    #github原版获取图片方法,可获取.png格式
    #back_coloring = np.array(Image.open('/home/weblogic/CODE/shangguan/wordcloud-master/image/alice_color.png'))

    wc = WordCloud( background_color = 'white',    # 设置背景颜色
                    mask = back_coloring,        # 设置背景图片
                    max_words = 100,            # 设置最大显示的字数
                    stopwords = STOPWORDS,        # 设置停用词
                    font_path='C:/Users/shangguanxf/Desktop/fonts/msyh.ttf',
                    max_font_size = 100,            # 设置字体最大值
                    random_state = 1             # 设置有多少种随机生成状态，即有多少种配色方案
                    )
    #wc.generate(text)
    '''
    #得到词频并导出
    words=wc.process_text(text)
    df=pd.DataFrame(columns=('词', '频数规范化'))
    n=0
    for i in words:
        df.loc[n]=[i[0],i[1]]
        n=n+1
    df.to_excel('/home/weblogic/DATA/private/shangguanxf/cc_bigdata/create/词云/'+name+'.xlsx',sheet_name='Sheet1')
    '''
    #用词频画图 f应为字典的list格式
    wc.generate_from_frequencies(f)
    # 从背景图片生成颜色值
    image_colors = ImageColorGenerator(back_coloring)
    # 以下代码显示图片
    plt.imshow(wc)
    plt.axis("off")

    '''
    # 绘制词云
    plt.figure()
    #绘制灰色系的图片
    plt.imshow(wc.recolor(color_func=grey_color_func, random_state=5))
    # 绘制以背景图片颜色为颜色的图片
    # recolor wordcloud and show
    # we could also give color_func=image_colors directly in the constructor
    plt.imshow(wc.recolor(color_func=image_colors))
    plt.axis("off")
    #绘制背景
    plt.figure()
    plt.imshow(backgroud_Image, cmap=plt.cm.gray)
    plt.axis("off")
    '''
    plt.show()
    # 保存图片
    wc.to_file('/home/weblogic/DATA/private/shangguanxf/cc_bigdata/create/词云/'+name+'.png')
    #wc.to_file(path.join(d, name+'.png'))
    #太寿投诉工单售后服务问题
f=pd.read_csv('C:/Users/shangguanxf/Desktop/太寿投诉工单售后服务词频.csv',encoding='gb18030',header=None,names=['词','频数'])
f1={}
for i in range(len(f.词)):
    f1[f.词[i]]=f.频数[i]
get_wc(f1,'太寿投诉工单售后服务问题')
```

