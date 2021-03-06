## **基本LaTeX 公式命令**

### **希腊字母**

> | 命令         | 显示   |      | 命令       | 显示   |
> | ---------- | ---- | ---- | -------- | ---- |
> | `\alpha`   | α    |      | `\beta`  | β    |
> | `\gamma`   | γ    |      | `\delta` | δ    |
> | `\epsilon` | ϵ    |      | `\zeta`  | ζ    |
> | `\eta`     | η    |      | `\theta` | θ    |
> | `\iota`    | ι    |      | `\kappa` | κ    |
> | `\lambda`  | λ    |      | `\mu`    | μ    |
> | `\xi`      | ξ    |      | `\nu`    | ν    |
> | `\pi`      | π    |      | `\rho`   | ρ    |
> | `\sigma`   | σ    |      | `\tau`   | τ    |
> | `\upsilon` | υ    |      | `\phi`   | ϕ    |
> | `\chi`     | χ    |      | `\psi`   | ψ    |
> | `\omega`   | ω    |      |          |      |

如果使用大写的希腊字母，把命令的首字母变成大写即可，例如 `\Gamma` 输出的是$\Gamma$。

如果使用斜体大写希腊字母，再在大写希腊字母的*LaTeX*命令前加上var，例如`\varGamma` 生成$\varGamma$ 。

例如：

```latex
$$
 \varGamma(x) = \frac{\int_{\alpha}^{\beta} g(t)(x-t)^2\text{ d}t }{\phi(x)\sum_{i=0}^{N-1} \omega_i} 
$$
```

生成如下结果：

>
>$$
>\varGamma(x) = \frac{\int_{\alpha}^{\beta} g(t)(x-t)^2\text{ d}t }{\phi(x)\sum_{i=0}^{N-1} \omega_i}
>$$
>

### **和号和积分号**

和号和积分号比较常用，这里也列出来：

> | 命令                  | 显示                  |      | 命令                  | 显示                  |
> | ------------------- | ------------------- | ---- | ------------------- | ------------------- |
> | `\sum`              | $\sum$              |      | `\int`              | $\int$              |
> | `\sum_{i=1}^{N}`    | $\sum_{i=1}^{N}$    |      | `\int_{a}^{b}`      | $\int_{a}^{b}$      |
> | `\prod`             | $\prod$             |      | `\iint`             | $\iint$             |
> | `\prod_{i=1}^{N}`   | $\prod_{i=1}^{N}$   |      | `\iint_{a}^{b}`     | $\iint_{a}^{b}$     |
> | `\bigcup`           | $\bigcup$           |      | `\bigcap`           | $\bigcap$           |
> | `\bigcup_{i=1}^{N}` | $\bigcup_{i=1}^{N}$ |      | `\bigcap_{i=1}^{N}` | $\bigcap_{i=1}^{N}$ |

### 箭头符号

>| 命令            | 显示            |      | 命令           | 显示           |
>| ------------- | ------------- | ---- | ------------ | ------------ |
>| ` \uparrow`   | $ \uparrow$   |      | `\downarrow` | $\downarrow$ |
>| `\Uparrow`    | $ \Uparrow$   |      | `\Downarrow` | $\Downarrow$ |
>| `\rightarrow` | $\rightarrow$ |      | `\leftarrow` | $\leftarrow$ |
>| `\Rightarrow` | $\Rightarrow$ |      | `\Leftarrow` | $\Leftarrow$ |
>|               |               |      |              |              |
>

### 数域符号

>| 命令           | 显示           |      | 命令           | 显示           |
>| ------------ | ------------ | ---- | ------------ | ------------ |
>| `\mathbb{R}` | $\mathbb{R}$ |      | `\mathbb{N}` | $\mathbb{N}$ |
>|              |              |      |              |              |
>|              |              |      |              |              |
>|              |              |      |              |              |
>
>

### **其它常用命令**

> | 命令                 | 显示                 |      | 命令                       | 显示                     |
> | ------------------ | ------------------ | ---- | ------------------------ | ---------------------- |
> | `\sqrt[3]{2}`      | $\sqrt[3]{2}$      |      | `\sqrt{2}`               | $\sqrt{2}$             |
> | `x^{3}`            | $x^3$              |      | `x_{3}`                  | $x_{3}$                |
> | `\lim_{x \to 0}`   | $\lim_{x \to 0}$   |      | `\frac{1}{2}`            | $\frac{1}{2}$          |
> | `\alpha\cdot\beta` | $\alpha\cdot\beta$ |      | `2\times 3`              | $2\times 3$            |
> | `\overline`        | $\overline y$      |      | `\underline`             | $\underline x$         |
> | `\neq`             | $ \neq$            |      | `\geq`                   | $\geq$                 |
> | `\leq`             | $\leq$             |      | `\in`                    | $\in$                  |
> | `\notin`           | $\notin$           |      | `$\max \limits_{a<x<b}$` | $\max \limits_{a<x<b}$ |
> | `\infty`           | $\infty$           |      | `\partial`               | $\partial$             |
> | `\nabla`           | $\nabla$           |      | `\Delta`                 | $\Delta$               |
> | `\hat x`           | $\hat x$           |      | `\forall`                | $\forall$              |
> | `\exists`          | $\exists$          |      |                          |                        |
> |                    |                    |      |                          |                        |
> |                    |                    |      |                          |                        |

> **注意**：上标和下标在只有一个字符时，可以不用中括号: `x^2`和`x^{2}`的结果都是 $x^2$
>
### 调整公式大小


> | 命令       | 显示         | 命令            | 显示              |
> | -------- | ---------- | ------------- | --------------- |
> | `\tiny`  | $\tiny D$  | `\scriptsize` | $\scriptsize D$ |
> | `\small` | $\small D$ | `\normalsize` | $\normalsize D$ |
> | `\large` | $\large D$ | `\Large`      | $\Large D$      |
> | `\LARGE` | $\LARGE D$ | `\huge`       | $\huge D$       |
> | `\Huge`  | $\Huge D$  |               |                 |
>
> **注意**：从左到右，从上到下依次变大

###括号用法总结

| 命令     | 显示     |      | 命令                 | 显示                |
| ------ | ------ | ---- | ------------------ | ----------------- |
| `()`   | $()$   |      | `\sqrt{2}`         | $[]$              |
| `\{\}` | $\{\}$ |      | `\langle  \rangle` | $\langle \rangle$ |
| `||`   | $\|\|$ |      | `||a+b||_1`        | $ \|\|a+b\|\|_1$  |
|        |        |      |                    |                   |
大括号
$$
F^{HLLC}=\left\{
\begin{array}{rcl}
F_L       &      & {0      <      S_L}\\
F^*_L     &      & {S_L \leq 0 < S_M}\\
F^*_R     &      & {S_M \leq 0 < S_R}\\
F_R       &      & {S_R \leq 0}
\end{array} \right.
$$
```latex
$$
F^{HLLC}=\left\{
\begin{array}{rcl}
F_L       &      & {0      <      S_L}\\
F^*_L     &      & {S_L \leq 0 < S_M}\\
F^*_R     &      & {S_M \leq 0 < S_R}\\
F_R       &      & {S_R \leq 0}
\end{array} \right.
$$
```



------

>
>[更多详见](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference)


