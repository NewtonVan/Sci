# Generative Adversarial Nets

***

* Generative model $G$ is aimed to capture the data distribution

* Discriminative model $D$ estimates the probability that a sample came from the training data rather than $G$.

* Formula

  * *prior* : input noise variable $p_z(z)$
  * $G(z;\theta_g)$ is a differentiable function represented by a multilayer perceptron with parameters $\theta_g$

  * multilayer perceptron $D(x;\theta_d)$, which outputs a single scalar representing the probability that $x$ came from the data rather than $p_g$
  * training target
    * train $D$ to maximize the probability of assigning the correct label to both training examples and samples from $G$
    * simultaneously train $G$ to minimize $log(1-D(G(z)))$
  * so if the value function is $V(G, D)$, our objective is : $$\min\limits_G\max\limits_DV(G, D)=\mathbb{E}_{x\sim p_{data}(x)}[log(D(x))]+\mathbb{E}_{z\sim p_z(z)}[1-log(D(G(z)))]$$
  * Early in learning, when $G$ is poor, $D$ can reject samples with high confidence. $log(1-D(G(z)))$ is saturated in this case. Rather than training $G$ to minimize $log(1-D(G(z)))$, we can train $G$ to maximize $log(D(G(z)))$. This objective function results in the same fixed point of the dynamics of $G$ and $D$ but provides much stronger gradients early in learning

* Theoretical Results

  * Global Optimality of $p_g=p_{data}$
  * For $G$ fixed, the optimal discriminator $D$ is $$D^*_G(x)= \frac{p_{data}(x)}{p_{data}(x)+p_g(x)}$$
  * Then we can use the upper proposition reformulated the original minimax game as$$C(G)=\max\limits_DV(G, D)=\mathbb{E}_{x\sim p_{data}}[log\frac{p_{data}(x)}{p_{data}(x)+p_g(x)}]+\mathbb{E}_{x\sim p_g}[log\frac{p_g(x)}{p_{data}(x)+p_g(x)}]$$, then our minimax game becomes minimum $C(G)$
  * The global minimum of the virtual training criterion $C(G)$ is achieved if and only if $p_g=p_{data}$. At that point, $C(G)$ achieves the value $-log4$
  * If $G$ and $D$ have enough capacity, and at each step of propose Algorithm, the discriminator is allowed to reach its optimum given $G$, and $p_g$ is updates so as to improve the criterion $$\mathbb{E}_{x\sim p_{data}(x)}[log(D^*_G(x))]+\mathbb{E}_{x\sim p_g}[1-log(D^*_G(x))]$$, then $p_g$ converges to $p_{data}$

* Advantages and disadvantages

  * disadvantages
    * no explicit representation of $p_g(x)$
    * $D$ must be synchronized well with $G$ during training
    * "the Helvetica scenario"
  * advantages
    * Markov chains are never needed, only backprop is used to obtain gradients
    * no inference is needed
    * components of the input are not copied directly into the generator's parameters
    * another advantage of adversarial networks is that they can represent very sharp

