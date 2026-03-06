# Exercise Two: Logistic Regression — Answers

## (a) Conditional Log Likelihood

In logistic regression, we maximize the conditional log likelihood of the training data. Given $n$ training examples with $p$ features, the class labels $y^{(i)}$, features $x_1^{(i)}, \dots, x_p^{(i)}$, and parameters $w_0, w_1, \dots, w_p$, the objective function is:

$$f(w_0, w_1, \dots, w_p) = \sum_{i=1}^{n} \left[ y^{(i)} \ln \sigma(z^{(i)}) + (1 - y^{(i)}) \ln(1 - \sigma(z^{(i)})) \right]$$

where:

$$z^{(i)} = w_0 + w_1 x_1^{(i)} + w_2 x_2^{(i)} + \cdots + w_p x_p^{(i)} = w_0 + \sum_{j=1}^{p} w_j x_j^{(i)}$$

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

This is the objective function we maximize via gradient ascent.

---

## (b) Partial Derivatives

### Derivative with respect to $w_0$

Using the chain rule and the fact that $\sigma'(z) = \sigma(z)(1 - \sigma(z))$:

$$\frac{\partial f}{\partial w_0} = \sum_{i=1}^{n} \left[ \frac{y^{(i)}}{\sigma(z^{(i)})} \cdot \sigma(z^{(i)})(1 - \sigma(z^{(i)})) \cdot 1 + \frac{1 - y^{(i)}}{1 - \sigma(z^{(i)})} \cdot (-\sigma(z^{(i)}))(1 - \sigma(z^{(i)})) \cdot 1 \right]$$

$$= \sum_{i=1}^{n} \left[ y^{(i)}(1 - \sigma(z^{(i)})) - (1 - y^{(i)})\sigma(z^{(i)}) \right]$$

$$\boxed{\frac{\partial f}{\partial w_0} = \sum_{i=1}^{n} \left( y^{(i)} - \sigma(z^{(i)}) \right)}$$

### Derivative with respect to $w_j$ (for $j = 1, \dots, p$)

The derivation is identical except that $\frac{\partial z^{(i)}}{\partial w_j} = x_j^{(i)}$ instead of 1:

$$\boxed{\frac{\partial f}{\partial w_j} = \sum_{i=1}^{n} \left( y^{(i)} - \sigma(z^{(i)}) \right) x_j^{(i)}}$$

---

## (c) Training the Logistic Regression Classifier

### Design Decisions

- **Algorithm:** Stochastic Gradient Ascent (SGA) — at each step, one training example is randomly selected and the parameters are updated using only that example's gradient.
- **Learning rate:** $\eta = 0.01$
- **Weight initialization:** All weights initialized to 0
- **Convergence criterion:** Change in log-likelihood between consecutive epochs is less than $10^{-6}$
- **Maximum epochs:** 500
- **Data shuffling:** Training data is randomly shuffled at the beginning of each epoch.

At each SGD step, for a randomly selected sample $i$:

$$w_0 \leftarrow w_0 + \eta \left( y^{(i)} - \sigma(z^{(i)}) \right)$$

$$w_j \leftarrow w_j + \eta \left( y^{(i)} - \sigma(z^{(i)}) \right) x_j^{(i)}, \quad j = 1, \dots, p$$

### Optimal Estimated Parameters

After training for 500 epochs (200,000 total SGD iterations):

| Parameter | Value |
|-----------|---------|
| $w_0$ (bias) | 1.115386 |
| $w_1$ | −5.582483 |
| $w_2$ | 7.895730 |
| $w_3$ | −5.695224 |
| $w_4$ | 7.273293 |
| $w_5$ | −4.291386 |
| $w_6$ | 0.152856 |

### Predictions on Test Set

Using these parameters, the predicted label for each test instance is:

$$\hat{y} = \begin{cases} 1 & \text{if } \sigma(w_0 + \sum_{j=1}^{6} w_j x_j) \geq 0.5 \\ 0 & \text{otherwise} \end{cases}$$

These parameters were applied to all 100 examples in "dataForTestingLogistic.txt" (without using the label column).

---

## (d) Misclassified Examples on Test Set

**Number of misclassified examples in the testing dataset: 0 out of 100**

- Test accuracy: **100.00%**
- Training accuracy (for reference): **100.00%** (0 out of 400 misclassified)

The model achieves perfect classification on both the training and test sets, indicating that the data is linearly separable in the 6-dimensional feature space.

---

## (e) Objective Function vs. Iteration

![Objective Function Convergence](plot_objective_convergence.png)

The plot shows the conditional log-likelihood evaluated at the end of each epoch of stochastic gradient ascent.

- **Total SGD iterations:** 200,000 (500 epochs × 400 samples per epoch)
- **Final objective value:** −3.294749
- **Convergence:** The algorithm did not formally converge within 500 epochs (the epoch-to-epoch change remained above the $10^{-6}$ threshold), but the objective function clearly plateaus, indicating near-convergence. The log-likelihood rises steeply from its initial value of approximately −277 and flattens out close to 0 (the theoretical maximum for perfect classification).

The rapid initial increase followed by a gradual plateau is characteristic of gradient ascent on a convex objective — the early updates make large progress, while later updates yield diminishing improvements as the parameters approach the optimum.

---

## (f) Training and Test Error vs. Training Set Size

![Error vs Training Set Size](plot_error_vs_training_size.png)

For each $k \in \{10, 20, 30, \dots, 380, 390, 400\}$, a random subset of size $k$ was drawn from the training data, and the logistic regression model was retrained on that subset using a learning rate of $\eta = 0.1$ for 200 epochs. Both training error (on the $k$ random instances) and test error (on the original test set) were then computed.

### Observations

Both the training error (blue) and test error (red) remain at **0.00 for all values of $k$** from 10 to 400. This means that even with as few as 10 randomly selected training examples, the classifier learns a decision boundary that perfectly classifies both the training subset and the full 100-example test set.

### Explanation

This behavior occurs because the dataset is **highly (or perfectly) linearly separable** in the 6-dimensional feature space:

1. **Strong discriminative features:** The six features provide enough signal to clearly distinguish between class 0 and class 1. The two classes occupy distinct regions of the feature space with a clear linear boundary between them.

2. **Even small subsets are representative:** Because the classes are well-separated, even a random sample of 10 points captures sufficient information about the class structure for the logistic regression model to learn an accurate decision boundary.

3. **Typical learning curve behavior (for harder problems):** In general, for datasets where classes overlap or the decision boundary is more complex, we would expect:
   - *Training error* to start low for small $k$ (easy to fit a few points) and increase slightly as $k$ grows.
   - *Test error* to start high for small $k$ (overfitting/high variance) and decrease as $k$ grows (improved generalization).
   - Both curves to converge as $k$ becomes large.

   However, since this particular dataset is nearly perfectly linearly separable, the logistic regression model achieves zero error at every training set size, and the typical learning curve pattern does not emerge.
