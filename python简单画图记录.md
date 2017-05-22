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

