##激活函数

1.relu  
max(x, 0)

2.relu6  
min(max(x, 0), 6)  
对relu做最大值的约束  

3.softplus  
log(exp(x) + 1)  

4.sigmoid  
y = 1 / (1 + exp(-x))

5.tanh  
y = (exp(x)-exp(-x))/(exp(x)+exp(-x))
