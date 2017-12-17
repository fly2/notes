## python简单画图记录

画图使用matplotlib和seaborn

### 问题记录

#### 无法显示label

在画图过程中如果需要显示label，不光需要在plot时设置，还需要执行plt.legend()来添加设置好的图例

#### 无法显示中文图例

无法显示中文图例是因为默认字体不包含中文，需要自行设置字体，在显示字体时使用自行设置的字体。

```python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np

zhfont1 = matplotlib.font_manager.FontProperties(fname='/home/weblogic/DATA/private/shangguanxf/sucai/fonts/msyh.ttf')

ax = plt.subplot(111)
t1 = np.arange(0.0, 1.0, 0.01)
for n in [1, 2, 3, 4]:
    plt.plot(t1, t1**n, label="图例%d"%(n,))
#对标签栏进行设置，loc为设置位置，ncol为列数，mode为，shadow是否为图例后绘制阴影，fancybox是否有边框，prop字体设置
leg = plt.legend(loc='best', ncol=2, mode="expand", shadow=True, fancybox=True,prop=zhfont1)
#为边框设置透明度
leg.get_frame().set_alpha(0.5)

plt.show()
```

#### 无法显示seaborn的设置

使用`sb.set()`来覆盖`matplotlib`的设置

### 设置seaborn调色板

[官网介绍](http://seaborn.pydata.org/tutorial/color_palettes.html?highlight=cmap)

**注意：**调色板需要设置参数为`as_cmap=True`，才可用于调色板使用

```python
seaborn.color_palette(palette=None, n_colors=None, desat=None)
#palette 输入调色板名称或为None，为None时返回当前的调色板。如果输入为序列，则应为颜色的rgb代码的list格式
#n_colors 调色板的颜色数量。如果为None，则默认为6种。对于list格式，默认为list长度。如果n_colors数量大于list长度，则会将其进行按顺序重复。小于则删除list中多余的颜色。
#desat 每种颜色的饱和度

#按list生成调色板
flatui = ["#9b59b6", "#3498db", "#95a5a6", "#e74c3c", "#34495e", "#2ecc71"]
sns.palplot(sns.color_palette(flatui))

#按照名称生成调色板
sns.palplot(sns.color_palette("Blues_d"))
```

#### hls_palette

```python
seaborn.hls_palette(n_colors=6, h=0.01, l=0.6, s=0.65)
#在HLS色调空间中获得一组均匀间隔的颜色。
#n_colors 颜色数量
#h 色相
#l 亮度
#s 饱和度
#h,l,s均为0,1之间的数字


sns.palplot(sns.hls_palette(10, h=.5))
```

#### xkcd_pallette

```python
seaborn.xkcd_palette(colors)
#colors 颜色名称列表


colors = ["windows blue", "amber", "greyish", "faded green", "dusty purple"]
sns.palplot(sns.xkcd_palette(colors))
```

#### diverging_palette

```python
#由一个颜色过渡到浅色，再过渡到另一个颜色
seaborn.diverging_palette(h_neg, h_pos, s=75, l=50, sep=10, n=6, center='light', as_cmap=False)
#h_neg,h_pos 色相的起始，终止范围
#s 饱和度
#l 亮度
#n 颜色数量
#seq 颜色变化步长
#center {'light','dark'}中心颜色白色还是黑色

from numpy import arange
>>> x = arange(25).reshape(5, 5)
>>> cmap = sns.diverging_palette(220, 20, sep=20, as_cmap=True)
>>> ax = sns.heatmap(x, cmap=cmap)
```

cubehelix_palette

```python
#制作一个颜色系列
seaborn.cubehelix_palette(n_colors=6, start=0, rot=0.4, gamma=1.0, hue=0.8, light=0.85, dark=0.15, reverse=False, as_cmap=False)
#n_colors 颜色数量
#start 起始色相。0<=start<=3
#rot
#gamma
#hue
#light
#dark
#reverse

```

#### 交互式调色

```python
#选择来自ColorBrewer的调色方案
seaborn.choose_colorbrewer_palette(data_type, as_cmap=False)
#data_type {'sequential','diverging','qualitative'}sequential为序列，即将一个颜色由浅到深排列。diverging为以由一个颜色过渡到浅色，再过渡到另一个颜色。qualitative为使用已经定义好的主题

#将任意两个颜色进行分割过渡
seaborn.choose_diverging_palette()

#创造一组渐变色
seaborn.choose_cubehelix_palette(as_cmap=False)

#生成一组一边颜色为白色的分割调色板
seaborn.choose_light_palette(input='husl', as_cmap=False)
#input 为调另一边颜色的方式 {'husl','hsl','rgb'}

#生成一组单边颜色为黑色的分割调色板
seaborn.choose_dark_palette(input='husl', as_cmap=False)
#input 为调另一边颜色的方式 {'husl','hsl','rgb'}
```

#### 调色板名字列表

```
Accent, Accent_r, Blues, Blues_r, BrBG, BrBG_r, BuGn, BuGn_r, BuPu, BuPu_r, CMRmap, CMRmap_r, Dark2, Dark2r, GnBu, GnBu_r, Greens, Greens_r, Greys, Greys_r, OrRd, OrRd_r, Oranges, Oranges_r, PRGn, PRGn_r, Paired, Paired_r, Pastel1, Pastel1r, Pastel2, Pastel2r, PiYG, PiYG_r, PuBu, PuBuGn, PuBuGn_r, PuBu_r, PuOr, PuOr_r, PuRd, PuRd_r, Purples, Purples_r, RdBu, RdBu_r, RdGy, RdGy_r, RdPu, RdPu_r, RdYlBu, RdYlBu_r, RdYlGn, RdYlGn_r, Reds, Reds_r, Set1, Set1r, Set2, Set2r, Set3, Set3r, Spectral, Spectral_r, Wistia, Wistia_r, YlGn, YlGnBu, YlGnBu_r, YlGn_r, YlOrBr, YlOrBr_r, YlOrRd, YlOrRd_r, afmhot, afmhot_r, autumn, autumn_r, binary, binary_r, bone, bone_r, brg, brg_r, bwr, bwr_r, cool, cool_r, coolwarm, coolwarm_r, copper, copper_r, cubehelix, cubehelix_r, flag, flag_r, gist_earth, gist_earth_r, gist_gray, gist_gray_r, gist_heat, gist_heat_r, gist_ncar, gist_ncar_r, gist_rainbow, gist_rainbow_r, gist_stern, gist_stern_r, gist_yarg, gist_yarg_r, gnuplot, gnuplot2, gnuplot2_r, gnuplot_r, gray, gray_r, hot, hot_r, hsv, hsv_r, inferno, inferno_r, jet, jet_r, magma, magma_r, nipy_spectral, nipy_spectral_r, ocean, ocean_r, pink, pink_r, plasma, plasma_r, prism, prism_r, rainbow, rainbow_r, seismic, seismic_r, spectral, spectral_r, spring, spring_r, summer, summer_r, terrain, terrain_r, viridis, viridis_r, winter, winter_r
```

#### rgb颜色名称表

[官网](https://xkcd.com/color/rgb/)

### 折线图

#### 2d折线图

使用matplotlib.pyplot中plot函数可以轻松的画出2d折线图。

```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt

x = np.linspace(0, 14, 100)
i=1
y1=np.sin(x + i * .5) * (7 - i) * 1
i=2
y2=np.sin(x + i * .5) * (7 - i) * 1
plt.plot(x, y1, color='green',linestyle='solid')
plt.plot(x, y2, color='blue',linestyle='solid')
plt.show()
```

#### 柱线图

```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as ab

time=[0.5,1.5,2.5,3.5,4.5,5.5,6.5,7.5,8.5,9.5,10.5,11.5,12.5,13.5,14.5,15.5,16.5,17.5,18.5,19.5,20.5,
      21.5,22.5,23.5]
login_value=[16856, 7743, 4780, 3638, 3216, 4119, 7314, 13977, 30631, 47083, 49870, 40226, 31705, 33548, 35584, 35616, 35022, 30347, 25281, 26647, 30021, 31531, 29081, 21809]
trade_value=[4988, 1848, 896, 619, 507, 598, 1325, 2735, 4927, 7316, 11267, 8979, 7175, 7454, 7854, 7691, 7581, 6830, 5699, 6177, 7495, 8322, 8552, 5884]

plt.bar(time, login_value)
plt.plot(time, trade_value,'r')
plt.show()
```

### 柱状图

#### 标准柱状图

```python
from matplotlib.ticker import FuncFormatter
import matplotlib.pyplot as plt
import numpy as np

x = np.arange(4)
money = [1.5e5, 2.5e6, 5.5e6, 2.0e7]


def millions(x, pos):
    'The two args are the value and tick position'
    return '$%1.1fM' % (x * 1e-6)


formatter = FuncFormatter(millions)

fig, ax = plt.subplots()
ax.yaxis.set_major_formatter(formatter)
plt.bar(x, money)
plt.xticks(x, ('Bill', 'Fred', 'Mary', 'Sue'))
plt.show()
```

#### 重叠柱状图

即正常的用同一x来进行bar的画图，会使图像重叠，可以通过颜色区分，易于比较。

```python
import seaborn as sb
import matplotlib
import matplotlib.pyplot as plt

plt.bar(time,login_value,label='login')
plt.bar(time,trade_value,color='gray',label='trade')

leg = plt.legend()
#为边框设置透明度
leg.get_frame().set_alpha(0.5)
plt.savefig('/tpdata/DATA/private/shangguanxf/jd登录行为识别/bar星期分布.png')
plt.show()
```

#### 堆叠柱状图

利用bar函数的bottom参数来画堆叠柱状图。bottom为放在底部的数据。

```python
import numpy as np
import matplotlib.pyplot as plt


N = 5
menMeans = np.array([20, 35, 30, 35, 27])
womenMeans = np.array([25, 32, 34, 20, 25])
other=(10,11,12,5,10)
menStd = (2, 3, 4, 1, 2)
womenStd = (3, 5, 2, 3, 3)
ind = np.arange(N)    # the x locations for the groups
width = 0.35       # the width of the bars: can also be len(x) sequence

p1 = plt.bar(ind, menMeans,width,color='black', yerr=menStd)
#通过bottom来设置底部数据
p2 = plt.bar(ind, womenMeans, width,color='pink',
             bottom=menMeans, yerr=womenStd)
#当底部数据不是单独变量时，可以通过前面数据相加来得到底部
p3=plt.bar(ind, other, width,
             bottom=womenMeans+menMeans, yerr=womenStd)

plt.ylabel('Scores')
plt.title('Scores by group and gender')
plt.xticks(ind, ('G1', 'G2', 'G3', 'G4', 'G5'))
plt.yticks(np.arange(0, 81, 10))
plt.legend((p1[0], p2[0]), ('Men', 'Women','other'))

plt.show()
```

#### 分组柱状图

```python
import matplotlib as mpl
mpl.use('Agg')
import matplotlib.pyplot as plt
import numpy as np

# 必须配置中文字体，否则会显示成方块
# 注意所有希望图表显示的中文必须为unicode格式
custom_font = mpl.font_manager.FontProperties(fname='/Library/Fonts/华文细黑.ttf')

font_size = 10 # 字体大小
fig_size = (8, 6) # 图表大小

names = (u'小明', u'小红') # 姓名
subjects = (u'语文', u'数学', u'英语') # 科目
scores = ((65, 90, 75), (85, 80, 90)) # 成绩

# 更新字体大小
mpl.rcParams['font.size'] = font_size
# 更新图表大小
mpl.rcParams['figure.figsize'] = fig_size
# 设置柱形图宽度
bar_width = 0.35

index = np.arange(len(scores[0]))
# 绘制「小明」的成绩
rects1 = plt.bar(index, scores[0], bar_width, color='#0072BC', label=names[0])
# 绘制「小红」的成绩
rects2 = plt.bar(index + bar_width, scores[1], bar_width, color='#ED1C24', label=names[1])
# X轴标题
plt.xticks(index + bar_width, subjects, fontproperties=custom_font)
# Y轴范围
plt.ylim(ymax=100, ymin=0)
# 图表标题
plt.title(u'企鹅班同学成绩对比', fontproperties=custom_font)
# 图例显示在图表下方
plt.legend(loc='upper center', bbox_to_anchor=(0.5, -0.03), fancybox=True, ncol=5, prop=custom_font)

# 添加数据标签
def add_labels(rects):
    for rect in rects:
        height = rect.get_height()
        plt.text(rect.get_x() + rect.get_width() / 2, height, height, ha='center', va='bottom')
        # 柱形图边缘用白色填充，纯粹为了美观
        rect.set_edgecolor('white')

add_labels(rects1)
add_labels(rects2)

# 图表输出到本地
plt.savefig('scores_par.png')
```

#### 单变量分布

matplotlib和seaborn都有针对单变量分布的函数，可以调用函数直接得出结果。seaborn会多一个核密度估计曲线。

```python
import seaborn as sb
import matplotlib
import matplotlib.pyplot as plt

#matplotlab
plt.hist(login_hour,bins=24)
plt.show()
#seaborn
login_hour=pd.Series(list(login_hour.astype('int32')))
#输入为int类型即可，无需为pd.Series
sb.distplot(login_hour)
plt.show()
```

### heatmap

```python
sb.heatmap(df2[0:100],vmin=0,vmax=1，cmap=sb.dark_palette("white",as_cmap=True),yticklabels=False,cbar=False)
plt.show()
```

### 多子图

绘制多个子图 
一个Figure对象可以包含多个子图（Axes），在matplotlib中用Axes对象表示一个绘图区域，称为子图，可以使用subplot()快速绘制包含多个子图的图表，它的调用形式如下：

```python
subplot(numRows,numCols,plotNum) 
#图表的整个绘图区域被等分为numRows行和numCols列，然后按照从左到下的顺序对每个区域进行编号，左上区域的编号为1。plotNum参数指定创建的Axes对象所在的区域 

import numpy as np  
import matplotlib.pyplot as plt  
plt.figure(1)#创建图表1  
plt.figure(2)#创建图表2  
ax1=plt.subplot(211)#在图表2中创建子图1
#ax1=plt.subplot(2,1,1)#在图表2中创建子图1
ax2=plt.subplot(212)#在图表2中创建子图2  
x=np.linspace(0,3,100)  
for i in xrange(5):  
    plt.figure(1)  
    plt.plot(x,np.exp(i*x/3))
    #plt.subplot(211)
    plt.sca(ax1)  #将当前Axes实例设置为ax1.或者直接设置
    plt.plot(x,np.sin(i*x))  
    plt.sca(ax2)  
    plt.plot(x,np.cos(i*x))  
plt.show()  
```

### 图片保存

plt图片保存有两种方法。

#### plt.savefig(path)

用来保存生成的figure()图像

```python
plt.figure()
lw = 2
plt.plot([0, 1], [0, 1], color='navy', lw=lw, linestyle='--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('Receiver operating characteristic example')
plt.legend(loc="lower right")
#savefig(path)
plt.savefig('/home/weblogic/DATA/private/shangguanxf/cc_txt2/roc.png')
plt.show()
```

#### plt.imsave(path,arr)

用来保存矩阵图像

```python
plt.imsave(path,arr)
#path 保存图像的地址
#arr An MxN (luminance), MxNx3 (RGB) or MxNx4 (RGBA) array.
```



