# Assignment 3: Support Vector Machine — Report

## 1. Key SVM Concepts

### 1.1 What is an SVM?

A **Support Vector Machine (SVM)** is a supervised machine learning algorithm used for classification (and regression) tasks. Given labeled training data, an SVM finds the **optimal separating hyperplane** — a decision boundary that divides data points of different classes with the **maximum margin**. The margin is the distance between the hyperplane and the nearest data points from each class, which are called **support vectors**.

For a binary classification problem with labels $y \in \{-1, +1\}$ and feature vectors $x \in \mathbb{R}^d$, a linear SVM learns a weight vector $a$ and bias $b$ such that the decision function is:

$$f(x) = a^T x + b$$

A new sample $x$ is classified as:
- Class $+1$ if $a^T x + b \geq 0$
- Class $-1$ if $a^T x + b < 0$

### 1.2 The Hinge Loss

SVMs use the **hinge loss** to penalize misclassified or insufficiently confident predictions:

$$L_{\text{hinge}}(y_k, f(x_k)) = \max(0, 1 - y_k \cdot (a^T x_k + b))$$

- When $y_k \cdot (a^T x_k + b) \geq 1$: the sample is correctly classified with sufficient margin, so the loss is **zero**.
- When $y_k \cdot (a^T x_k + b) < 1$: the sample is either misclassified or within the margin, incurring a **linear penalty** proportional to how far it is from the margin boundary.

### 1.3 Regularization

To prevent overfitting and to control model complexity, SVM adds an **L2 regularization** term on the weight vector:

$$\text{Objective} = \frac{\lambda}{2} \|a\|^2 + \frac{1}{N} \sum_{k=1}^{N} \max(0, 1 - y_k(a^T x_k + b))$$

Here:
- $\frac{\lambda}{2} \|a\|^2$ is the **regularization term** that penalizes large weights, encouraging a wider margin and a simpler model.
- $\lambda$ is the **regularization constant** — a hyperparameter that controls the trade-off between maximizing the margin (model simplicity) and minimizing classification errors on the training data.
  - **Large $\lambda$**: Stronger regularization → wider margin → weights are kept small → the model may **underfit** (too simple to capture the data patterns).
  - **Small $\lambda$**: Weaker regularization → narrower margin → the model can fit the training data more closely → risk of **overfitting** if $\lambda$ is too small.

### 1.4 Stochastic Gradient Descent (SGD)

Instead of computing gradients over the entire training set (batch gradient descent), **Stochastic Gradient Descent** updates the model parameters using **one randomly selected training sample at a time**. This is much more efficient for large datasets (like the Adult dataset with ~44K samples).

The gradients provided in the assignment for a single sample $(x_k, y_k)$ are:

$$\nabla_a = \begin{cases} \lambda a & \text{if } y_k(a^T x_k + b) \geq 1 \\ \lambda a - y_k x_k & \text{otherwise} \end{cases}$$

$$\nabla_b = \begin{cases} 0 & \text{if } y_k(a^T x_k + b) \geq 1 \\ -y_k & \text{otherwise} \end{cases}$$

**Interpretation of the gradients:**
- When the margin condition $y_k(a^T x_k + b) \geq 1$ is satisfied (correctly classified with enough confidence), only the regularization gradient $\lambda a$ applies — the model simply decays the weights slightly to keep them small.
- When the margin condition is violated, the gradient includes both the regularization term and the hinge loss gradient ($-y_k x_k$ for $a$ and $-y_k$ for $b$), pushing the decision boundary to correctly classify this sample.

The parameter update rule for each sample is:

$$a \leftarrow a - \eta \cdot \nabla_a$$
$$b \leftarrow b - \eta \cdot \nabla_b$$

where $\eta$ is the **learning rate**.

### 1.5 Maximum Margin and Support Vectors

The key geometric insight of SVM is that among all possible hyperplanes separating two classes, the one with the **largest margin** generalizes best to unseen data. The **support vectors** — training points lying exactly on the margin boundaries ($y_k(a^T x_k + b) = 1$) — are the critical data points that define this hyperplane. All other points do not influence the decision boundary.

---

## 2. Implementation Approach

### 2.1 Overview

The implementation is a **from-scratch linear SVM** trained with SGD in Python using only NumPy. The workflow consists of the following stages:

```
Data Loading → Missing Value Imputation → Feature Encoding → Normalization → SGD Training → Prediction & Evaluation
```

### 2.2 Data Loading

The Adult dataset is provided in CSV-like format:
- **`train.txt`**: 43,957 labeled samples. Each row contains 14 features followed by a label (`<=50K` or `>50K`).
- **`test.txt`**: 4,885 unlabeled samples (14 features, no label).
- **`test_ground_truth.txt`**: 4,885 labeled rows for evaluating test predictions.

Labels are converted to numeric values:
- `<=50K` → $-1$
- `>50K` → $+1$

### 2.3 Missing Value Imputation

The dataset contains missing values represented by `?` (primarily in the `workclass`, `occupation`, and `native-country` columns). Rather than treating `?` as its own category (which adds noise and uninformative dimensions), missing values are imputed:

- **Continuous features**: Missing values are replaced with the **mean** of that feature computed from the training set.
- **Categorical features**: Missing values are replaced with the **mode** (most frequent category) of that feature computed from the training set.

Importantly, imputation statistics are computed **only from training data** and then applied to both train and test sets, avoiding data leakage.

### 2.4 Feature Encoding

The Adult dataset has 14 features — a mix of continuous and categorical:

| Type | Features |
|------|----------|
| **Continuous** (6) | age, fnlwgt, education-num, capital-gain, capital-loss, hours-per-week |
| **Categorical** (8) | workclass, education, marital-status, occupation, relationship, race, sex, native-country |

Encoding strategy:
- **Continuous features**: Kept as numeric floating-point values.
- **Categorical features**: Converted via **one-hot encoding** — each unique category becomes a binary feature (0 or 1).

After encoding, the feature vector has **105 dimensions**.

### 2.5 Feature Normalization

Since SVM with SGD is sensitive to feature scales (a feature ranging from 0–99999 would dominate features ranging from 0–1), **z-score normalization** is applied:

$$x_{\text{normalized}} = \frac{x - \mu}{\sigma}$$

where $\mu$ (mean) and $\sigma$ (standard deviation) are computed from the **training set only**. Both train and test sets are normalized using these same statistics. This ensures:
- All features have approximately zero mean and unit variance.
- Gradient descent converges more quickly and reliably.
- No data leakage from test set statistics.

### 2.6 SVM Training with SGD

The training procedure:

1. **Initialize** the weight vector $a$ to zeros and bias $b$ to 0.
2. For each **epoch** (full pass through the training data):
   a. **Shuffle** the training indices randomly.
   b. For each sample $(x_k, y_k)$:
      - Compute the margin: $m = y_k \cdot (a^T x_k + b)$
      - If $m \geq 1$ (correctly classified with margin):
        - $\nabla_a = \lambda a$, $\nabla_b = 0$
      - Else (margin violated):
        - $\nabla_a = \lambda a - y_k x_k$, $\nabla_b = -y_k$
      - Update: $a \leftarrow a - \eta \cdot \nabla_a$, $b \leftarrow b - \eta \cdot \nabla_b$
3. Return the learned $a$ and $b$.

**Hyperparameters used:**
- Learning rate: $\eta = 0.001$
- Number of epochs: 20
- Random seed: 42 (for reproducibility)

### 2.7 Prediction

For a test sample $x$:

$$\hat{y} = \begin{cases} +1 & \text{if } a^T x + b \geq 0 \\ -1 & \text{otherwise} \end{cases}$$

### 2.8 Evaluation

Classification accuracy is computed as:

$$\text{Accuracy} = \frac{\text{Number of correct predictions}}{\text{Total number of samples}}$$

---

## 3. Accuracy Results for Different Regularization Constants

The SVM was trained with the following regularization constants: $\lambda \in \{10^{-3}, 10^{-2}, 10^{-1}, 1\}$.

| Regularization Constant ($\lambda$) | Training Accuracy | Test Accuracy |
|:------------------------------------:|:-----------------:|:-------------:|
| $10^{-3}$ (0.001) | **84.90%** | **84.89%** |
| $10^{-2}$ (0.01) | 84.83% | 84.79% |
| $10^{-1}$ (0.1) | 83.78% | 84.16% |
| $1$ | 77.66% | 77.67% |

---

## 4. Analysis: How and Why Accuracy Changes with the Regularization Constant

### 4.1 Observed Trend

There is a clear **monotonically decreasing trend** in accuracy as the regularization constant $\lambda$ increases:

- **Best accuracy** at $\lambda = 0.001$: ~84.9% (both train and test)
- **Worst accuracy** at $\lambda = 1$: ~77.7% (both train and test)

The accuracy drops significantly when $\lambda$ is increased from $0.1$ to $1$ (a drop of ~6 percentage points).

### 4.2 Why Does Accuracy Decrease with Larger $\lambda$?

Recall the SVM objective:

$$\min_{a, b} \; \frac{\lambda}{2} \|a\|^2 + \frac{1}{N} \sum_{k=1}^{N} \max(0, 1 - y_k(a^T x_k + b))$$

The regularization constant $\lambda$ controls the **trade-off between two competing objectives**:

1. **Minimizing the regularization term** $\frac{\lambda}{2}\|a\|^2$: This encourages small weights, which means a simpler model with a wider margin. A wider margin generalizes well but may not classify all training data correctly.

2. **Minimizing the hinge loss** $\sum \max(0, 1 - y_k(a^T x_k + b))$: This encourages the model to correctly classify training samples with confidence.

**When $\lambda$ is large (e.g., $\lambda = 1$):**
- The regularization term dominates the objective.
- The optimizer prioritizes keeping $\|a\|$ small over reducing the hinge loss.
- This aggressively shrinks the weight vector, reducing the model's discriminative power.
- The result is **underfitting** — the model becomes too simple to capture the underlying patterns in the data.
- At the extreme, a very large $\lambda$ would push $a$ toward the zero vector, effectively making the model predict based almost entirely on the bias $b$ (i.e., predicting the majority class), which explains the ~77.7% accuracy — close to the proportion of the majority class (`<=50K`) in the dataset.

**When $\lambda$ is small (e.g., $\lambda = 0.001$):**
- The hinge loss term dominates, so the optimizer focuses on correctly classifying as many training samples as possible.
- The weight vector is allowed to grow larger, giving the model more capacity to draw a precise decision boundary.
- This yields better training and test accuracy (~85%).
- Since train and test accuracy are very close, there is no sign of overfitting — suggesting that $\lambda = 0.001$ provides an appropriate level of regularization for this dataset.

### 4.3 Why the Gradual Decline?

The decline is gradual from $\lambda = 0.001$ to $\lambda = 0.1$ (only ~1% drop) but becomes steep at $\lambda = 1$ (~6% drop). This is because:

- For small values of $\lambda$, the regularization penalty is relatively minor — it prevents extreme weights but doesn't significantly constrain the model. The accuracy differences between $\lambda = 0.001$ and $\lambda = 0.01$ are negligible.
- At $\lambda = 0.1$, the regularization starts to noticeably constrain the weight magnitudes, slightly reducing the model's ability to fit the data.
- At $\lambda = 1$, the regularization overwhelms the hinge loss. The weight vector is forced so small that the model effectively loses its ability to discriminate between classes based on features, leading to a dramatic accuracy drop.

### 4.4 No Overfitting Observed

Notably, training and test accuracy are very close across all $\lambda$ values. This indicates that:
- The model is not overfitting even at the smallest $\lambda = 0.001$.
- The dataset is large enough (43,957 training samples) relative to the model's capacity (linear model with 105 features) that overfitting is not a concern.
- In this case, reducing $\lambda$ further might yield even slightly better accuracy, though the improvements would likely be marginal.

### 4.5 Summary Table

| $\lambda$ | Regularization Strength | Model Behavior | Accuracy |
|:---------:|:----------------------:|:--------------:|:--------:|
| 0.001 | Very weak | Fits data well, weights free to grow | ~85% (best) |
| 0.01 | Weak | Minimal constraint, nearly same as 0.001 | ~85% |
| 0.1 | Moderate | Noticeably constrains weights | ~84% |
| 1 | Strong | Weights heavily shrunk, model too simple | ~78% (worst) |

**Conclusion:** For this dataset, a small regularization constant ($\lambda \leq 0.01$) yields the best performance. The linear SVM with SGD achieves approximately **85% test accuracy** on the Adult income classification task, which is consistent with expected performance for a linear classifier on this well-known benchmark dataset.
