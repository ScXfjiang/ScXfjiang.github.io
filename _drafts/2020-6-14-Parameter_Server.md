---
layout:     post
title:      Parameter Server
toc:        true
categories: [System Programming, Machine Learning]
---
# Ⅰ. Parameter Server



# Ⅱ. 基于Parameter实现Logistic Regression
## 1. 模型定义
Logistic Regression概率模型如下：

$$
\begin{aligned}
p(y\ |\ \boldsymbol{x}, \boldsymbol{\theta}) = Bernoulli(h_\boldsymbol{\theta}(\boldsymbol{x}))
\end{aligned}
$$

where

$$
\begin{aligned}
h_{\boldsymbol{\theta}}(\boldsymbol{x}) = Sigmoid(\boldsymbol{\theta^T\boldsymbol{x}}) = \frac{1}{1 + e^{-\boldsymbol{\theta}^{T}\boldsymbol{x}}}
\end{aligned}
$$

## 2. 学习策略
定义$$y$$的取值为$$1$$和$$0$$，其概率分布为：

$$
\begin{aligned}
p(y=1\ |\ \boldsymbol{x})&=h_{\boldsymbol{\theta}}(\boldsymbol{x}) \\
p(y=0\ |\ \boldsymbol{x})&=1-h_{\boldsymbol{\theta}}(\boldsymbol{x})
\end{aligned}
$$

即

$$
\begin{aligned}
p(y\ |\ \boldsymbol{x})=(h_{\boldsymbol{\theta}}(\boldsymbol{x}))^y(1-h_{\boldsymbol{\theta}}(\boldsymbol{x}))^{1-y} \\
\end{aligned}
$$

Likelihood Function为：

$$
\begin{aligned}
L(\boldsymbol{\theta}) = \prod_{i=1}^N (h_{\boldsymbol{\theta}}(\boldsymbol{x}^{(i)}))^{y^{(i)}}(1-h_{\boldsymbol{\theta}}(\boldsymbol{x}^{(i)}))^{1-y^{(i)}}
\end{aligned}
$$

Negative Log Likelihood Function为：

$$
\begin{aligned}
\ell(\boldsymbol{\theta})=-\sum_{i=1}^N (y^{(i)}\log h_\boldsymbol{\theta}(\boldsymbol{x}^{(i)}) + (1 - y^{(i)})\log (1 - h_\boldsymbol{\theta}(\boldsymbol{x}^{(i)})))
\end{aligned}
$$

这就是我们要优化的目标函数，该问题变成了一个最优化问题：

$$
\begin{aligned}
& \underset{\boldsymbol{x}}{\text{minimize}} && \ell(\boldsymbol{\theta})
\end{aligned}
$$

我们无法直接求得$$\boldsymbol{\theta}$$的解析解，下面采用Gradient Descent算法进行学习。

<br/>
对于单个样本$$(\boldsymbol{x},\ y)$$而言。

$$
\frac{\partial}{\partial\theta_j}\ell(\boldsymbol{\theta})=(y-h_\boldsymbol{\theta}(\boldsymbol{x}))x_j
$$

对于整个训练集来说，Gradicent Descent的迭代步骤为：

$$
\theta_j = \theta_j - \alpha (y^{(i)} - h_\boldsymbol{\theta}(\boldsymbol{x}^{(i)}))x_j^{(i)}
$$

我们可以将计算向量化：

$$
\boldsymbol{\theta} = \boldsymbol{\theta} - \alpha\times (y^{(i)} - h_\boldsymbol{\theta}(\boldsymbol{x}^{(i)}))\boldsymbol{x}^{(i)}
$$

同时我们可以采用Batch Gradicent Descent一次计算多个样本的梯度：

$$
\boldsymbol{\theta} = \boldsymbol{\theta} - \alpha\times \frac{1}{batch\_size}\sum_{batch\_size}(y^{(i)} - h_\boldsymbol{\theta}(\boldsymbol{x}^{(i)}))\boldsymbol{x}^{(i)}
$$

至此，我们完成了Logistic Regression学习算法的推导，下面我们要在parameter server上实现这个计算。

## 3. 基于Parameter Server的实现
