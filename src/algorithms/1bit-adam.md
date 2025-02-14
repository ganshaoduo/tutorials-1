# 1-bit Adam

## Overview

1-bit Adam is a communication compression algorithm that is specifically intended for Adam optimizer. Although there are lots of SGD-based gradients compression algorithms, e.g., QSGD, 1-bit SGD and so on, none of them can be directly applied to Adam optimizer because Adam is non-linearly dependent on the gradient. Empirical study also shows that Adam with gradient compression could suffer an obvious drop in the training accuracy and cannot converge to the same level as its non-compression counterpart. Motivated by this observation, we proposed 1-bit Adam in this [paper](https://arxiv.org/pdf/2102.02888.pdf) to make it possible for Adam to benefit from communication compression.

## 1-bit Adam algorithm

Let's first have a look of the updating strategy of the original Adam, which can be summaried as:

$\textbf{m}_t = \beta_1 \textbf{m}_{t-1} + (1-\beta_1)\textbf{g}_t$

$\textbf{v}_t = \beta_2 \textbf{v}_{t-1} + (1-\beta_2)\textbf{g}_t^2$

$\textbf{x}_t = \textbf{x}_{t-1} - \gamma \frac{\textbf{m}_t}{\sqrt \textbf{v}_t+\epsilon}$

where $t$ is the index of iteration, $\textbf{x}$ represents model parameters, $\gamma$ is the learning rate, $\textbf{g}_t$ is gradient at step $t$.

As we can see, both $\textbf{m}$ and $\textbf{v}$ are derived from gradients $\textbf{g}$. Compressing $\textbf{g}$ will lead to the diverge of training due to the non-linear dependency of $\textbf{v}$ on $\textbf{g}$. The intuition of 1-bit Adam is that $\textbf{v}$ tends to be very stable after a few epochs in the beginning, so we can set $\textbf{v}$ as constant afterward and only update $\textbf{m}$. Without the effect of $\textbf{v}$, we can compress $\textbf{m}$ without worrying about the drop of training accuracy.

Therefore, 1-bit Adam algorithm consists of two stages: warmup stage and compression stage. 

- **In the warmup stage** (usually takas 20% of the total iterations in the beginning), all workers communicate to average local gradients before updating $\textbf{m}$ and $\textbf{v}$ without compression. 
- **In the compression stage**, $\textbf{v}$ is frozen and not updated anymore. All workers update $\textbf{m}$ with its local gradients and compress it into $\mathcal{C}(\textbf{m})$. Then $\mathcal{C}(\textbf{m})$ will be communicated among workers.

A detailed description and analysis of the algorithm can be found in the [paper](https://arxiv.org/pdf/2102.02888.pdf).

## Benchmark

We provide some benchmark results [here](../benchmark/index.md) to compare the performance of 1-bit Adam of Bagua with other SOTA systems on BERT-Large finetune task.


## Limitation
As we discussed above, the 1-bit Adam is based on an assumption that the value of $\textbf{v}$ in Adam will quickly get stable after a few epochs of training. However, it may not work if this assumption breaks. Although we have tested the correctness of 1-bit Adam on BERT-Large, BERT-Base, ResNet50 and Deep Convolutional Generative Adversarial Networks, it is still possible that 1-bit Adam may fail on some other tasks. The condition of 1-bit Adam is still an interesting open problem.


## Example