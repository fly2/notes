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

zhfont1 = matplotlib.font_manager.FontProperties(fname='/tpdata/DATA/private/shangguanxf/sucai/fonts/msyh.ttf')

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

#### plt.show()和plt.imshow()的区别

plt.imshow()是用于对矩阵画图的函数。plt.show()是将图像展示出来的函数。

### 设置seaborn调色板

[官网介绍](http://seaborn.pydata.org/tutorial/color_palettes.html?highlight=cmap)

[Color Brewer](http://colorbrewer2.org/)配色板，可使用已有的配色方案名称

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
x = arange(25).reshape(5, 5)
cmap = sns.diverging_palette(220, 20, sep=20, as_cmap=True)
ax = sns.heatmap(x, cmap=cmap)
```

#### cubehelix_palette

```python
#制作一个颜色系列
seaborn.cubehelix_palette(n_colors=6, start=0, rot=0.4, gamma=1.0, hue=0.8, light=0.85, dark=0.15, reverse=False, as_cmap=False)
#n_colors 颜色数量
#start 起始色相。0<=start<=3
#rot 在调色板的范围内围绕色调轮旋转。
#gamma gamma因子小于1为强调较暗的颜色，大于1为较亮的颜色
#hue 色调
#light 调色板中最亮的颜色的强度。
#dark 调色板中最暗的颜色的强度。
#hue,light,dark 均为[0,1]
#reverse 如果为True,则调色板将从暗到亮


sns.palplot(sns.cubehelix_palette(rot=-.4))

sns.palplot(sns.cubehelix_palette(start=2.8, rot=.1))
```

#### light_palette/dark_palette

```python
#从浅色/深色到指定的颜色的系列颜色
seaborn.light_palette(color, n_colors=6, reverse=False, as_cmap=False, input='rgb')
#color 序列的基础颜色
#n_color 切分的颜色数量
#reverse 是否翻转。默认为由浅色/深色到指定颜色
#input 输入color的方式。 {'rgb','hls','husl','xkcd'}

sns.palplot(sns.dark_palette("purple"))

sns.palplot(sns.dark_palette((260, 75, 60), input="husl"))

from numpy import arange
x = arange(25).reshape(5, 5)
cmap = sns.dark_palette("#2ecc71", as_cmap=True)
ax = sns.heatmap(x, cmap=cmap)
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

热点地图，根据数据的横纵坐标作为图中的横纵坐标，数值作为颜色的深浅进行作图。

```python
seaborn.heatmap(data, vmin=None, vmax=None, cmap=None, center=None, robust=False, annot=None, fmt='.2g', annot_kws=None, linewidths=0, linecolor='white', cbar=True, cbar_kws=None, cbar_ax=None, square=False, xticklabels='auto', yticklabels='auto', mask=None, ax=None, **kwargs)
#data 数据。二维数据集，可以强制到一个ndarray。 如果提供了Pandas DataFrame，索引/列信息将被用来标记列和行的名称。
#vmin，vmax 锚定颜色映射的值，否则从数据和其他关键字参数中推断。
#cmap 从数据值到色彩空间的映射。 如果没有提供，默认将取决于中心是否设置。
#center 设置色彩空间中心对应的值
#robust 如果设置为True，而且vmin或vmax不存在，则使用强分位数计算色彩图范围，而不是极值。
#annot bool或者数据矩阵。如果为True则在每个单元格写入数据值。如果是一个矩阵的形状与data形状相同，那么使用它来标注热图而不是原始数据。
#fmt 字符串格式化添加注释时使用的代码。
#annot_kws 当annot为True时，ax.text的关键字参数。以字典格式输入
#linewidths 分割单元格的线的宽度
#linecolor 分割单元格线的颜色
#cbar 是否显示颜色条，即颜色空间。
#cbar_kws 关于fig.colorbar的关键字参数。一字典格式输入
#cbar_ax 在其中绘制颜色空间的轴，否则从主轴获取空间。
#square 如果为True，则将Axes纵横比设置为“相等”，这样每个单元格将是正方形的。如果为False，则会使最终结果为正方形
#xticklabels, yticklabels 如果为True，则绘制df的列名称。 如果为False，则不要绘制列名称。 如果为list，将这些替代标签绘制为xticklabels。 如果是整数，则使用列名称，但每n个标签绘制一个。 如果“自动”，尝试密集地绘制不重叠的标签。
#mask 如果通过，数据将不会显示在掩码为True的单元格中。 mask需要和data的shape一致。
#ax 绘制图的轴，否则使用当前活动的轴。


sb.heatmap(df2[0:100],vmin=0,vmax=1，cmap=sb.dark_palette("white",as_cmap=True),yticklabels=False,cbar=False)
plt.show()
```

### 箱线图

箱形图提供了一种只用5个点对[数据集](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E9%9B%86)做简单总结的方式。这5个点包括中点、Q1、Q3、分部状态的高位和低位。箱形图很形象的分为中心、延伸以及分布状态的全部范围。

箱形图中最重要的是对相关统计点的计算,相关统计点都可以通过[百分位](https://baike.baidu.com/item/%E7%99%BE%E5%88%86%E4%BD%8D)计算方法进行实现。

箱形图的绘制步骤：

1、画[数轴](https://baike.baidu.com/item/%E6%95%B0%E8%BD%B4)，度量单位大小和数据批的单位一致，起点比最小值稍小，长度比该数据批的[全距](https://baike.baidu.com/item/%E5%85%A8%E8%B7%9D)稍长。

2、画一个矩形盒，两端边的位置分别对应数据批的上下[四分位数](https://baike.baidu.com/item/%E5%9B%9B%E5%88%86%E4%BD%8D%E6%95%B0)（Q3和Q1）。在矩形盒内部[中位数](https://baike.baidu.com/item/%E4%B8%AD%E4%BD%8D%E6%95%B0)（Xm）位置画一条线段为[中位线](https://baike.baidu.com/item/%E4%B8%AD%E4%BD%8D%E7%BA%BF)。

3、在Q3+1.5IQR和Q1－1.5IQR处画两条与中位线一样的线段，这两条线段为[异常值](https://baike.baidu.com/item/%E5%BC%82%E5%B8%B8%E5%80%BC)截断点，称其为内限；在Q3+3IQR和Q1－3IQR处画两条线段，称其为外限。处于内限以外位置的点表示的数据都是异常值，其中在内限与外限之间的异常值为温和的异常值（mild outliers），在外限以外的为极端的异常值(extreme outliers)。四分位距IQR=Q3-Q1。.

4、从矩形盒两端边向外各画一条线段直到不是异常值的最远点，表示该批数据正常值的分布区间。

5、用“〇”标出温和的异常值，用“*”标出极端的异常值。相同值的数据点并列标出在同一数据线位置上，不同值的数据点标在不同数据线位置上。至此一批数据的箱形图便绘出了。[统计软件](https://baike.baidu.com/item/%E7%BB%9F%E8%AE%A1%E8%BD%AF%E4%BB%B6)绘制的箱形图一般没有标出内限和外限。

```python
seaborn.boxplot(x=None, y=None, hue=None, data=None, order=None, hue_order=None, orient=None, color=None, palette=None, saturation=0.75, width=0.8, dodge=True, fliersize=5, linewidth=None, whis=1.5, notch=False, ax=None, **kwargs)
#x,y 为data中的变量名称
#hue 为data中对数据进行分组的变量名称
#data DataFrame, array, or list of arrays, optional绘图数据集。如果x,y缺失，将对每个数值列画图。
#order, hue_order 控制图中分组的顺序。
#orient {'h','v'}控制图为水平或竖直方向。一般会根据数据进行推断
#color 所有元素的颜色或渐变调色板的种子
#palette 用于色调变量不同级别的颜色。 应该是可以由color_palette（）解释的东西，或者是将色调级别映射到matplotlib颜色的字典。
#saturation 在原来的饱和度绘制颜色的比例。 较大的修补程序通常在略去饱和的颜色下看起来更好，但如果要使绘图颜色与输入颜色规范完美匹配，请将其设置为1。
#width 箱线图的宽度
#dodge 当使用hue参数时，是否应该沿着分类轴移动元素。
#fliesize 用于指示离群值观察的标记的大小。
#linewidth 线的宽度
#whis 异常值的定义。Q3+whis(Q3-Q1)
#notch 是否“切入”方框以指示中位数的置信区间。 还有其他几个参数可以控制凹槽的绘制方式。 请参阅plt.boxplot帮助以获取更多信息。
#ax Axes对象绘制图形，否则使用当前的Axes。
#sym 异常值的显示符号。如果不想显示则输入''，如果输入为None，则默认为'b+'。更多操作请使用flierprops属性

import seaborn as sb
sb.set()
sb.boxplot(y='id',x='type',hue='is_scan',data=login,)
plt.show()
```

#### flierprops属性使用

#### [参考代码](https://matplotlib.org/gallery/statistics/boxplot.html?highlight=flierprops)

### 矩阵画图

plt.imshow()用来给数字矩阵画图。

```python
plt.imshow(X, cmap=None, norm=None, aspect=None, interpolation=None, alpha=None, vmin=None, vmax=None, origin=None, extent=None, shape=None, filternorm=1, filterrad=4.0, imlim=None, resample=None, url=None, hold=None, data=None, **kwargs)
'''
X : 矩阵格式, 形状为 (n, m) 或 (n, m, 3) 或 (n, m, 4)。X可能是一个矩阵或者PIL图片。如果X是矩阵，则如下：MxN – values to be mapped (float or int)	
MxNx3 – RGB (float or uint8)	
MxNx4 – RGBA (float or uint8)
MxNx3 和 MxNx4中的值需为0到1的浮点数。MxN 矩阵则会基于矩阵和cmap画图
'''
#cmap : 颜色地图。默认为None。当X为3维时此参数被忽略。

#aspect : 长宽比{'auto', 'equal', scalar}, default: None。如果为'auto'则自动匹配axes，如果为'equal'，且extent=None，则更改轴纵横比以匹配图像的纵横比。 如果extent不是None，则轴宽高比将更改为与范围匹配。如果输入scalar，则将纵横比变为scalar。注意scalar为字符串类型的数字（'10'）。

#interpolation : 插值方式。可用的值有： 'none', 'nearest', 'bilinear', 'bicubic', 'spline16', 'spline36', 'hanning', 'hamming', 'hermite', 'kaiser', 'quadric', 'catrom', 'gaussian', 'bessel', 'mitchell', 'sinc', 'lanczos'.如果参数为None，则默认使用image.interpolation。如果为'none'，对.ps,.Agg,.pdf来源的文件不做插值处理，其他进行'nearest'插值

#norm : 规范化, Normalize实例用于将二维浮点X输入缩放到（0，1）范围以输入到cmap。 如果norm是None，则使用默认的func：normalize。 如果norm是NoNorm的一个实例，那么X必须是一个整数数组，直接索引到cmap的查找表中。

#vmin, vmax : 标量, vmin和vmax与norm结合使用来对数据进行归一化。 请注意，如果您通过标准实例，则vmin和vmax的设置将被忽略。

#alpha : 透明度。标量。alpha的值。介于[0,1]之间。0为透明。

#origin : 原点的位置。{'upper', 'lower'}.'upper'为左上，'lower'为右下

#extent : 标量 (left, right, bottom, top), 坐标轴x，y轴的坐标范围

#shape : scalars (columns, rows),原始缓冲去图像的形状

#filternorm : scalar, optional, default: 1.A parameter for the antigrain image resize filter. From the antigrain documentation, if filternorm = 1, the filter normalizes integer values and corrects the rounding errors. It doesn’t do anything with the source floating point values, it corrects only integers according to the rule of 1.0 which means that any sum of pixel weights must be equal to 1.0. So, the filter function must produce a graph of the proper shape.

#filterrad : 具有半径参数的滤波器的滤波器半径，即当插值是'sinc'，'lanczos'或'blackman'之一时，默认为4.0

import numpy as np
import matplotlib.cm as cm
import matplotlib.mlab as mlab
import matplotlib.pyplot as plt
import matplotlib.cbook as cbook
from matplotlib.path import Path
from matplotlib.patches import PathPatch

delta = 0.025
x = y = np.arange(-3.0, 3.0, delta)
X, Y = np.meshgrid(x, y)
Z1 = mlab.bivariate_normal(X, Y, 1.0, 1.0, 0.0, 0.0)
Z2 = mlab.bivariate_normal(X, Y, 1.5, 0.5, 1, 1)
Z = Z2 - Z1  # difference of Gaussians

im = plt.imshow(Z, interpolation='bilinear', cmap=cm.RdYlGn,aspect='2'
                origin='lower', extent=[-3, 3, -3, 3],
                vmax=abs(Z).max(), vmin=-abs(Z).max())

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



