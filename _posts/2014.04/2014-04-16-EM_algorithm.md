---
layout: post
title:  EM算法小结
category : algorithm
tagline: 
tags : [EM]
---

为了将论文的Formulation写成Exceptation-Maximization (EM)的形式，前前后后看了大约一个月的EM算法，虽然最后发现无法将论文写成EM的形式，同时也发现了之前发的ICME文章中EM算法的不严谨。现在对EM算法做一些小结，希望对以后的学习与工作有帮助。

什么是EM算法？
----

EM算法是一种迭代算法，1977年由Dempster等人总结提出，用于含有隐变量(hidden variable)的概率模型参数的极大似然估计，或极大后验概率估计。EM算法分的每次迭代由两步组成，

* E-step: 求期望(Exceptation)
* M-step: 求极值(Maximization)

隐变量的引入
----

为了能够准确地去model一个关于变量<script type="math/tex">\mathbf{x}</script>的更加复杂的概率密度函数，我们引入了隐变量<script type="math/tex">\mathbf{h}</script>，这里只以连续情况为例子，离散的情况可以方便的通过连续的情况得到。为了充分利用隐变量的信息，我们可心求<script type="math/tex">\mathbf{x,h}</script>的联合概率<script type="math/tex">Pr(\mathbf{x,h})</script>关于<script type="math/tex">\mathbf{h}</script>的边缘密度，记为

<script type="math/tex; mode=display">
  Pr(\mathbf{x}) = \int Pr(\mathbf{x,h}) d\mathbf{h}.
</script>

无论联合概率<script type="math/tex">Pr(\mathbf{x,h})</script>的形式是怎样的，总存在一些模型参数，记为<script type="math/tex">\mathbf{\theta}</script>，那么上式我们可以写成，

<script type="math/tex; mode=display">
  Pr(\mathbf{x|\theta}) = \int Pr(\mathbf{x,h|\theta}) d\mathbf{h}.
</script>

对于训练数据 <script type="math/tex">\\{\mathbf{x}_i\\}, (i=1,\dots,I)</script>, 有两种不同的方法可以去拟合概率模型，一种是不考虑隐变量<script type="math/tex">\mathbf{h}</script>，利用最大似然估计的方法，但是这种方法在我们将要考虑的模型上是没有简洁的 close form solution.

<script type="math/tex; mode=display">
  \hat{\mathbf{\theta}} = \arg \max\limits_{\mathbf{\theta}} \sum\limits_{i=1}^{I} \log[Pr(\mathbf{x}_i)|\mathbf{\theta}].
</script>

另外一种方法是充分考虑隐变量的信息，利用EM迭化算法求解最优解，

<script type="math/tex; mode=display">
  \hat{\mathbf{\theta}} = \arg \max\limits_{\mathbf{\theta}} \sum\limits_{i=1}^{I} \log[\int Pr(\mathbf{x}_i,\mathbf{h}_i|\theta) d\mathbf{h}_i]. \qquad (1)
</script>

EM算法
---------

要最大化(1)式，即要对(1)式进行求导，但由于(1)式的对数函数里有个积分，很难对(1)式直接求导，EM算法的神奇之处就是在于找到了(1)式的一个下界，通过最大化这个下界<script type="math/tex">\mathcal{B}[\\{q_i(\mathbf{h}i)\\},\mathbf{\theta}]</script>，不断地去逼近(1)式的最大值。在EM算法中，下界是通过式(2)得到的，其中<script type="math/tex">q_i(\mathbf{h}i)</script>表示隐变量<script type="math/tex">\mathbf{h}_i</script>的概率。

<script type="math/tex; mode=display">
\begin{align}
\mathcal{B}[\{q_i(\mathbf{h}_i)\},\mathbf{\theta}] =& 
\sum\limits_{i=1}^{I} \int q_i(\mathbf{h}_i) \log \left[ \frac{Pr(\mathbf{x}_i,\mathbf{h}_i|\mathbf{\theta})}{q_i(\mathbf{h}_i)}\right] d\mathbf{h}_i  \\
\leq & \sum\limits_{i=1}^{I} \log \left[ \int Pr(\mathbf{x}_i,\mathbf{h}_i|\mathbf{\theta}) d\mathbf{h}_i\right]  \qquad (2)
\end{align}
</script>

在<script type="math/tex">t+1</script>次迭代中，令<script type="math/tex">q_i(\mathbf{h}_i)</script>为关于前一个迭代时参数<script type="math/tex">\theta[t]</script>的后验概率，那么由Baye准则可得 Eq. (3) 

**E-step**:

<script type="math/tex; mode=display">
\hat{q}_i(\mathbf{h}_i) = Pr(\mathbf{h}_i|\mathbf{x}_i,\mathbf{\theta}^{[t]}) =
\frac{Pr(\mathbf{x}_i|\mathbf{h}_i,\mathbf{\theta}^{[t]}) Pr(\mathbf{h}_i|\mathbf{\theta}^{[t]})}{Pr(\mathbf{x}_i)} \qquad (3)
</script>

**M-step**: 

<script type="math/tex; mode=display">
\hat{\mathbf{\theta}}^{[t+1]} = \arg\max\limits_{\mathbf{\theta}} \left[
\int \hat{q}_i(\mathbf{h}_i) \log[Pr(\mathbf{x}_i,\mathbf{h}_i)|\mathbf{\theta}] d\mathbf{h}_i
\right] \qquad (4)
</script>
