---
layout:     post
title:      Optimizer
toc:        true
categories: [Algorithm]
---
## 1. Regularization & Weight Decay介绍
在深度学习算法中，我们通常使用Regularization和Weight Decay来提高模型在测试集上的准确率，避免过拟合问题。Regularization和Weight Decay目的一致，在某些优化算法中可以通过调整超参的方式实现数学上的等价，但是二者的出发点不同，在框架设计中应作为两种独立的方法存在。

为了后面语义的一致，我们将神经网络的训练从概念上分为三个阶段：
1. Forward \\
  前向计算直到 $$Loss$$，由用户自定义；
2. AutoGrad (Backward) \\
   计算 $$Loss$$ 对各个权值的导数，称为 $$gradient$$，也就是 $$model\_diff$$；
3. Optimizer \\
   根据 $$gradient$$ 使用优化算法(SGD、Adam、...)更新模型；

### 1.1 Regularization
正则化从优化理论上来讲是对目标函数的修改，给原有的目标函数加上待优化变量(variable)的范数（可以简单理解为variable的大小）。作为优化理论的一种，深度学习算法中的正则化定义和优化理论中的完全一致；在工程上，深度学习框架通常仅实现L1正则化和L2正则化。

从定义来说正则化仅修改了前向网络的 $$Loss$$，属于Forward阶段，完全由用户负责，框架仅提供L1/L2范数计算Op即可。

若原Loss为：

$$
\begin{aligned}
  Loss
\end{aligned}
$$

则将其修改为：

$$
\begin{aligned}
  Loss + \alpha\Sigma \|w\|_2^2 + \beta\Sigma \|w\|_1
\end{aligned}
$$

这种理解方式也可以演绎为反向对 $$gradient$$ 的修改。 


### 1.2 Weight Decay
Weight Decay指的是在使用 $$gradient$$ 更新权值的过程中，给权值一个penalty，通常将此模型简化为：

$$
\begin{aligned}
  \theta^{(t+1)} = (1 - \lambda)\theta^{(t)} - lr \cdot \triangledown_\theta Loss(\theta^{(t)})
\end{aligned}
$$

其中 $$1 - \lambda$$就是对权值的penalty。

Weight Decay属于Optimizer阶段，每个optimizer应该处理自己的Weight Decay逻辑，例如SGDW、AdamW。

### 1.3 小结
Regularization机制只负责修改 $$gradient$$，然后将其送入Optimizer，它不关心Optimizer是如何实现的；Weight Decay机制只负责使用 $$gradient$$更新模型，它不关心 $$gradient$$是如何计算的。所以这两种机制从原理上来说是正交的。

## 2. L2 Regularization & Weight Decay的区别与联系
L2 Regularization和Weight Decay经常被混用，在对齐测试时给工程师带来困难，例如PyTorch在框架层面就不存在正则化相关的API，需要用户手动在前向对Loss进行修改，在Optimizer文档（[link](https://pytorch.org/docs/stable/optim.html)）中也混用了 *weight decay* 和 *l2 penalty* 这两个词。原因是在某些优化策略中，二者可以通过修改超参实现数学上的等价。

下面举几个例子。

### 2.1 Naive SGD
在SGD中，L2 Regularization与Weight Decay可以通过修改超参实现等价。

#### a. 原始公式

原Loss为：

$$
\begin{aligned}
  Loss(\theta)
\end{aligned}
$$

原更新策略：

$$
\begin{aligned}
  \theta^{(t+1)} = \theta^{(t)} - lr \cdot \triangledown_\theta Loss(\theta^{(t)})
\end{aligned}
$$

#### b. Naive SGD + L2 Regularization

Loss修改为：

$$
\begin{aligned}
  Loss(\theta) + \frac{\gamma}{2}\Sigma\theta_i^2
\end{aligned}
$$

其中，$$\gamma$$ 为L2 Regularization配置参数。

因为Loss的改变，所以更新策略修改为：

$$
\begin{aligned}
  \theta^{(t+1)} = \theta^{(t)} - lr \cdot (\triangledown_\theta Loss(\theta^{(t)}) + \gamma\theta^{(t)})
\end{aligned}
$$

#### c. Naive SGD + Weight Decay

Loss不变，更新策略修改为：

$$
\begin{aligned}
  \theta^{(t+1)} = (1 - \lambda)\theta^{(t)} - lr \cdot \triangledown_\theta Loss(\theta^{(t)})
\end{aligned}
$$

其中 $$\lambda$$ 为Weight Decay配置参数，当 $$\lambda == lr\cdot\gamma$$时，L2 Regularization与Weight Decay等价。 

### 2.2 SGD with Momentum
#### a. 原始公式

原Loss为：

$$
\begin{aligned}
  Loss(\theta)
\end{aligned}
$$

原更新策略：

$$
\begin{aligned}
  &v^{(t)} = \gamma v^{(t-1)} + lr \cdot \triangledown_\theta Loss(\theta^{(t)}) \\
  &\theta^{(t+1)} = \theta^{(t)} - v^{(t)}
\end{aligned}
$$

#### b. SGD with Momentum + L2 Regularization
Loss修改为：

$$
\begin{aligned}
  Loss(\theta) + \frac{\gamma}{2}\Sigma\theta_i^2
\end{aligned}
$$

其中，$$\gamma$$ 为L2 Regularization配置参数。因为Loss的改变， 所以更新策略修改为：

$$
\begin{aligned}
  &v^{(t)} = \gamma v^{(t-1)} + lr \cdot (\triangledown_\theta Loss(\theta^{(t)}) + \gamma\theta^{(t)}) \\
  &\theta^{(t+1)} = \theta^{(t)} - v^{(t)}
\end{aligned}
$$

#### c. SGD with Momentum + Weight Decay
Loss不变，更新策略修改为：

$$
\begin{aligned}
  &v^{(t)} = \gamma v^{(t-1)} + lr \cdot \triangledown_\theta Loss(\theta^{(t)}) \\
  &\theta^{(t+1)} = (1 - \lambda)\theta^{(t)} - v^{(t)}
\end{aligned}
$$

SGD with Momentum要维护Momentum这个自身状态，L2 Regularization和Weight Decay在维护这个状态上无法实现对齐，原因是二者都使用 $$gradient$$  更新这个状态，而L2 Regularization修改了 $$gradient$$，Weight Decay没有。

#### d. SGDW
论文 [Decoupled Weight Decay Regularization](https://arxiv.org/abs/1711.05101) 为了将L2 Regularization与Weight Decay解耦，提出了SGDW算法。

### 2.3 Adam
Adam的推导方法与前面两个完全相同，结论为与SGD with Momentum类似，Adam也要使用 $$gradient$$ 更新自身状态，所以在Adam优化算法中，L2 Regularization与Weight Decay并不等价。论文 [Decoupled Weight Decay Regularization](https://arxiv.org/abs/1711.05101) 为了将二者解耦，提出了AdamW算法。

### 2.4 小结
根据目前读到的Optimizer算法归纳可以得到下面的结论：凡是Optimizer自身带有状态，且需要使用 $$gradient$$ 更新该状态的优化算法，L2 Regularization和Weight Decay并不等价，因为L2 Regularization修改了 $$gradient$$ 而Weight Decay没有。

## 3. TensorFlow，PyTorch，OneFlow相关API对比

在接口方面，OneFlow和TensorFlow显示地提供了权值的正则化方法；而PyTorch并没有提供正则化方法，而是在更多的Optimizer接口 [torch.optim](https://pytorch.org/docs/stable/optim.html) 中提供weight decay参数，未来OneFlow和PyTorch在Optimizer接口、算法对齐上可能还有一些工作要做（TODO）。