<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Binomial Tree Option Pricing</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
  </script>
</head>
<body>

<h1>Binomial Tree Option Pricing</h1>

<h2>European Option - Quasi Closed-Form</h2>

<p>The value of a European call option using the Cox-Ross-Rubinstein (CRR) binomial model is given by:</p>

<p>
$$
C_0 = e^{-rT} \sum_{j=0}^{N} \binom{N}{j} q^j (1-q)^{N-j} \max(S_0 u^j d^{N-j} - K, 0)
$$
</p>

<p>Where:</p>
<ul>
  <li>\( C_0 \): Option price at time 0</li>
  <li>\( N \): Number of time steps</li>
  <li>\( S_0 \): Initial asset price</li>
  <li>\( K \): Strike price</li>
  <li>\( T \): Time to maturity</li>
  <li>\( r \): Risk-free interest rate</li>
  <li>\( u = e^{\sigma \sqrt{\Delta t}} \)</li>
  <li>\( d = e^{-\sigma \sqrt{\Delta t}} \)</li>
  <li>\( q = \frac{e^{r \Delta t} - d}{u - d} \)</li>
  <li>\( \Delta t = \frac{T}{N} \)</li>
</ul>

<hr>

<h2>American Option - Backward Induction</h2>

<p>For American options, early exercise prevents a closed-form. Instead, we use backward induction:</p>

<p>
At each node \( (i, j) \), let \( S_{i,j} = S_0 \cdot u^j d^{i-j} \). Initialize terminal payoffs:
</p>

<p>
$$
V_{N,j} = \max(\text{Intrinsic Value at } S_{N,j}, 0)
$$
</p>

<p>Then recursively compute:</p>

<p>
$$
V_{i,j} = \max\left( \text{Intrinsic Value at } S_{i,j},\ e^{-r \Delta t}\left[q V_{i+1,j+1} + (1-q)V_{i+1,j}\right] \right)
$$
</p>

<p>Where:</p>
<ul>
  <li>\( u = e^{\sigma \sqrt{\Delta t}} \)</li>
  <li>\( d = e^{-\sigma \sqrt{\Delta t}} \)</li>
  <li>\( q = \frac{e^{r \Delta t} - d}{u - d} \)</li>
  <li>\( \Delta t = \frac{T}{N} \)</li>
</ul>

<hr>

<h2>Pseudocode</h2>

<pre>
for j in range(N+1):
    V[N][j] = max(S0 * u**j * d**(N-j) - K, 0)  # For call; reverse for put

for i in reversed(range(N)):
    for j in range(i+1):
        S = S0 * u**j * d**(i-j)
        hold = exp(-r*dt) * (q * V[i+1][j+1] + (1-q) * V[i+1][j])
        exercise = max(S - K, 0)
        V[i][j] = max(hold, exercise)
</pre>

<hr>

<h2>Why No Closed-Form for American Options?</h2>

<p>The presence of early exercise introduces path dependency. Unlike the European case, the option value cannot be expressed using symmetric binomial coefficients because the "optimal stopping" problem breaks tree regularity.</p>

<p>This leads to a moving, non-analytic early exercise boundary that must be computed numerically at each node of the tree.</p>

</body>
</html>
