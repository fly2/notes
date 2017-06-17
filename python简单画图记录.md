## python简单画图记录

画图使用matplotlib和seaborn

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

