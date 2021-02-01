---
layout:     post
title:      Optimizer
toc:        true
categories: [Algorithm]
---
## 正则化的理论基础
正则化从优化理论上来讲是对目标函数的修改，给原有的目标函数添加variable的范数（可以简单理解为variable的大小）避免过拟合问题。

$$
\begin{aligned}
  Loss
\end{aligned}
$$

则将其修改为

$$
\begin{aligned}
  Loss + \alpha\Sigma \|w\|_2^2 + \beta\Sigma \|w\|_1
\end{aligned}
$$
