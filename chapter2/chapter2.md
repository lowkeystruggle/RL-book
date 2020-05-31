
# Chapter 1: Markov Processes

This book is about "Sequential Decisioning under Sequential Uncertainty". In this chapter, we will ignore the "sequential decisioning" aspect and focus just on the "sequential uncertainty" aspect.

## The Concept of *State* in a Process

For a gentle introduction to the concept of *State*, let us consider a process that generates a sequence of random outcomes at discrete time steps that we'll index by a time variable $t = 0, 1, 2, \ldots$. To understand and reason about the random evolution of such a process, we are typically interested in the internal representation of the process at each point in time $t$. We refer to this internal representation of the process at time $t$ as the (random) *state* of the process at time $t$ and denote it as $S_t$. Specifically, we are interested in the probability of the next state $S_{t+1}$, given the present state $S_t$ and the past states $S_0, S_1, \ldots, S_{t-1}$, i.e., $Pr[S_{t+1}|S_t, S_{t-1}, \ldots, S_0]$. The internal representation (*state*) could be any data type - it could be something as simple as a single stock price at the end of a day, or it could be something quite elaborate like the number of shares of all publicly traded stocks held by all banks in the U.S. at the end of a week. 

## Markov States

We will be learning about Markov Processes in this chapter and these processes have what are called *Markov States*. So we will now learn about the *Markov Property* of *States*. Let us develop intuition for this property with some examples of random evolution of stock prices over time. 

To aid with the intuition, let us pretend that stock prices take on only integer values and that it's acceptable to have zero or negative stock prices. Let us denote the stock price at time $t$ as $X_t$. Let us assume that from time step $t$ to the next time step $t+1$, the stock price can either go up by 1 or go down by 1, i.e., the only two outcomes for $X_{t+1}$ are $X_t + 1$ or $X_t - 1$. To understand the random evolution of the stock prices in time, we just need to quantify the probability of an up-move $Pr[X_{t+1} = X_t + 1]$ since the probability of a down-move $Pr[X_{t+1} = X_t - 1] = 1 - Pr[X_{t+1} = X_t + 1]$. We will consider 3 different processes for the evolution of stock prices. These 3 processes will prescribe $Pr[X_{t+1} = X_t + 1]$ in 3 different ways.

**Process 1**:
$$Pr[X_{t+1} = X_t + 1] = \frac 1 {1 + e^{-\alpha_1(L - X_t)}}$$
where $L$ is an arbitrary reference level and $\alpha_1 \geq 0$ is a "pull strength" parameter. Note that this probability is defined as a [logistic function](https://en.wikipedia.org/wiki/Logistic_function) of $L - X_t$ with the steepness of the logistic function controlled by the parameter $\alpha_1$.

<div style="text-align:center" markdown="1">
![Logistic Curves](./chapter2/logistic_curves.png "Logistic Curves")
</div>
 The way to interpret this logistic function of $L-X_t$ is that if $X_t$ is greater than the reference level $L$ (making $Pr[X_{t+1} = X_t + 1] < 0.5$), then there is more of a down-pull than an up-pull. Likewise, if $X_t$ is less than $L$, then there is more of an up-pull. The extent of the pull is controlled by the magnitude of the parameter $\alpha_1$. We refer to this behavior as *mean-reverting behavior*, meaning the stock price tends to revert to the "mean" (i.e., to the reference level $L$).

We can model the state $S_t = X_t$ and note that the probabilities of the next state $S_{t+1}$ depend only on the current state $S_t$ and not on the previous states $S_0, S_1, \ldots, S_{t-1}$. Informally, we phrase this property as: "The future is independent of the past given the present". Formally, we can state this property of the states as:
$$Pr[S_{t+1}|S_t, S_{t-1}, \ldots, S_0] = Pr[S_{t+1}|S_t]\text{ for all } t > 0$$
This is a highly desirable property since it helps make the mathematics of such processes much easier and the computations much more tractable. We call this the *Markov Property* of States, or simply that these are *Markov States*. Now let us consider a slightly more complicated process and see if it has the Markov Property.

**Process 2**:
$$Pr[X_{t+1} = X_t + 1] =
\begin{cases}
0.5 (1 - \alpha_2(X_t - X_{t-1})) & \text{if } t > 0\\
0.5 & \text{if } t = 0
\end{cases}
$$
where $\alpha_2$ is a "pull strength" parameter in the closed interval $[0, 1]$. The intuition here is that the direction of the next move $X_{t+1} - X_t$ is biased in the opposite direction of the previous move $X_t - X_{t-1}$ and the extent of the bias is controlled by the parameter $\alpha_2$. 

We note that if we model the state $S_t$ as $X_t$, we won't satisfy the Markov Property because the probabilities of $X_{t+1}$ depend on not just $X_t$ but also on $X_{t-1}$. However, we can perform a little trick here and create an augmented state $S_t$ consisting of the pair
$(X_t, X_t - X_{t-1})$. In case $t=0$, the state $S_0$ can assume the value $(X_0, Null)$ where $Null$ is just a symbol denoting the fact that there have been no stock price movements thus far. With the state $S_t$ as this pair $(X_t, X_t - X_{t-1})$ , we can see that the Markov Property is indeed satisfied:
$$Pr[(X_{t+1}, X_{t+1} - X_t)|(X_t, X_t - X_{t-1}), (X_{t-1}, X_{t-1} - X_{t-2}), \ldots, (X_0, Null)]$$
$$= Pr[(X_{t+1}, X_{t+1} - X_t)|(X_t, X_t - X_{t-1})] = 0.5(1 - \alpha_2(X_{t+1} - X_t)(X_t - X_{t-1}))$$

Note that if we had modeled the state $S_t$ as the entire stock price history $(X_0, X_1, \ldots, X_t)$, then the Markov Property would be satisfied trivially. The Markov Property would also be satisfied if we had modeled the state $S_t$ as the pair $(X_t, X_{t-1})$ for $t > 0$ and $S_0$ as $(X_0, Null)$. However, our choice of $S_t := (X_t, X_t - X_{t-1})$ is the "simplest/minimal" internal representation. In fact, in this entire book, our endeavor in modeling states for various processes will be to ensure the Markov Property with the "simplest/minimal" representation for the state. Now let us look at a more complicated process.

**Process 3**:
$$Pr[X_{t+1} = X_t + 1] =
\begin{cases}
\frac 1 {1 + (\frac t {\sum_{i=1}^t \max(X_{i-1} - X_i, 0)} - 1)^{\alpha_3}} & \text{if } t > 0\\
0.5 & \text{if } t = 0
\end{cases}
$$
where $\alpha_3 \geq 0$ is a "pull strength" parameter. This probability expression looks quite opaque, so let's break it down to make sense of it. The first insight is that $\sum_{i=1}^t \max(X_{i-1} - X_i, 0)$ (denote as $D_t$) is the number of down-moves in the stock price history $X_0, X_1, \ldots, X_t$. Likewise, let us denote $U_t$ as $\sum_{i=1}^t \max(X_i - X_{i-1}, 0)$ so that $D_t + U_t = t$. So, we can write this probability expression as:
$$\frac 1 {1 + (\frac {U_t + D_t} {D_t} - 1)^{\alpha_3}}$$
which we should conceptualize as:
$$f(\frac {D_t} {U_t + D_t}; \alpha_3)$$
where $f: [0, 1] \rightarrow [0, 1]$ is a sigmoid-shaped function
 $$f(x; \alpha) = \frac 1 {1 + (\frac 1 x - 1)^{\alpha}}$$
 whose steepness at $x=0.5$ is controlled by the parameter $\alpha$ (note: values of $\alpha < 1$ will produce an inverse sigmoid as seen in the image for unit-sigmoid functions $f$ for different values of $\alpha$). 
 
<div style="text-align:center" markdown="1">
![Unit-Sigmoid Curves](./chapter2/unit_sigmoid_curves.png "Unit-Sigmoid Curves")
</div>

 So, if number of up-moves in history $U_t$ are greater than number of down-moves in history $D_t$, then there will be more of a down-pull than an up-pull for the next price movement $X_{t+1} - X_t$ (likewise, the other way round when $D_t > U_t$). The extent of the pull is controlled by the "pull strength" parameter $\alpha_3$, governed by the sigmoid-shaped function $f$).

We note again that if we model the state $S_t$ as $X_t$, we won't satisfy the Markov Property because the probabilities of next state $S_{t+1} = X_{t+1}$ depends on the entire history of stock price moves and not just on the current state $S_t = X_t$. However, we can again perform a little trick here and create a compact enough state $S_t$ consisting of the pair $(U_t, D_t)$. With the state $S_t$ as this pair $(U_t, D_t)$, we can see that the Markov Property is indeed satisfied:
$$Pr[(U_{t+1}, D_{t+1})|(U_t, D_t), (U_{t-1}, D_{t-1}), \ldots, (U_0, D_0)]
= Pr[(U_{t+1}, D_{t+1})|(U_t, D_t)]$$
$$=
\begin{cases}
\frac f({D_t} {U_t + D_t}; \alpha_3) & \text{if }U_{t+1} = U_t + 1, D_{t+1} = D_t\\ 
\frac f({U_t} {U_t + D_t}; \alpha_3) & \text{if }U_{t+1} = U_t, D_{t+1} = D_t + 1 
\end{cases}
$$