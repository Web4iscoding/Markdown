# Assignment 4: Support Vector Machine — Report

---

## 1. Key SVM Concepts

### 1.1 What is a Support Vector Machine?

A **Support Vector Machine (SVM)** is a supervised learning algorithm for binary classification. Given labeled training data, SVM finds the **optimal separating hyperplane** — a decision boundary that separates the two classes with the **maximum margin**. The margin is the perpendicular distance from the decision boundary to the nearest data points of either class.

### 1.2 Core Concepts

**Decision Boundary (Hyperplane)**
The decision function is:

$$f(x) = a^T x + b$$

where $a$ is the weight vector and $b$ is the bias. A sample $x$ is classified as $+1$ if $f(x) \geq 0$ and $-1$ otherwise.

**Margin**
The geometric margin is $\frac{2}{\|a\|}$. Maximizing the margin is equivalent to minimizing $\|a\|^2$, subject to all samples being correctly classified.

**Support Vectors**
The data points that lie exactly on the margin boundaries ($y_k(a^T x_k + b) = 1$) are called **support vectors**. These are the critical samples that define the position and orientation of the hyperplane. Removing any non-support-vector point would not change the decision boundary.

**Soft-Margin SVM (Hinge Loss)**
Real-world data is rarely linearly separable. The soft-margin SVM allows some misclassifications by introducing a **hinge loss**:

$$\ell(x_k, y_k) = \max\bigl(0,\; 1 - y_k(a^T x_k + b)\bigr)$$

This loss is zero when the sample is correctly classified with margin $\geq 1$, and grows linearly with the degree of violation.

**Regularization (L2 Penalty)**
To prevent overfitting and control model complexity, an L2 regularization term is added. The full optimization objective becomes:

$$\min_{a, b} \;\frac{\lambda}{2}\|a\|^2 + \frac{1}{n}\sum_{k=1}^{n}\max\bigl(0,\;1 - y_k(a^T x_k + b)\bigr)$$

The regularization constant $\lambda$ controls the trade-off:
- **Larger $\lambda$** → bigger margin, more misclassifications tolerated (simpler model)
- **Smaller $\lambda$** → tighter fit to training data, smaller margin (more complex model)

**Sub-Gradients for SGD**
Since the hinge loss is not differentiable at $y_k(a^T x_k + b) = 1$, we use sub-gradients. For a randomly selected sample $(x_k, y_k)$:

$$\nabla_a = \begin{cases} \lambda a & \text{if } y_k(a^T x_k + b) \geq 1 \\ \lambda a - y_k x_k & \text{otherwise} \end{cases}$$

$$\nabla_b = \begin{cases} 0 & \text{if } y_k(a^T x_k + b) \geq 1 \\ -y_k & \text{otherwise} \end{cases}$$

These are the exact update rules given in the assignment hint.

---

## 2. Implementation Approach

### 2.1 Data Loading

- Load `train.txt` (43,957 labeled samples) and `test.txt` (4,885 unlabeled samples).
- Load `test_ground_truth.txt` for evaluation labels.
- Parse each line as a CSV row with 15 fields (14 features + 1 label).

### 2.2 Missing-Value Imputation

The UCI Adult dataset contains missing values encoded as `"?"` in categorical columns (e.g., `workclass`, `occupation`, `native_country`). Before encoding:

- **Categorical features**: replace `"?"` with the **mode** (most frequent value) of that column.
- **Numerical features**: replace `"?"` with the **mean** (not applicable in this dataset, but handled for robustness).

This avoids creating a spurious `"?"` one-hot category and yields cleaner feature vectors.

### 2.3 Feature Encoding

- **Categorical features** (8 columns: workclass, education, marital_status, occupation, relationship, race, sex, native_country): **one-hot encoded**. Each unique category value becomes a binary feature. Feature maps are built from the training set to ensure a consistent encoding between train and test.
- **Numerical features** (6 columns: age, fnlwgt, education_num, capital_gain, capital_loss, hours_per_week): kept as continuous values.
- Final feature dimension: **105**.

### 2.4 Standardization

Numerical features are **z-score standardized** using training-set statistics:

$$x' = \frac{x - \mu_{\text{train}}}{\sigma_{\text{train}}}$$

This ensures zero mean and unit variance, which is critical for gradient-based optimization since features on different scales would cause the gradient to favor large-scale features.

### 2.5 Label Mapping

- `<=50K` → $-1$
- `>50K` → $+1$

### 2.6 SVM Training with SGD

The `SVM_SGD` class implements:

1. **Initialization**: weight vector $a = \mathbf{0}$, bias $b = 0$.
2. **For each epoch** (50 epochs total):
   - Compute a **decaying learning rate**: $\eta_t = \frac{\eta_0}{1 + \eta_0 \cdot \lambda \cdot t}$, which ensures convergence by reducing the step size over time.
   - **Shuffle** the training data randomly.
   - **For each sample** $(x_k, y_k)$:
     - Compute $y_k(a^T x_k + b)$.
     - If $\geq 1$: update $a \leftarrow a - \eta_t \cdot \lambda a$ (only regularization gradient; point is safely classified).
     - If $< 1$: update $a \leftarrow a - \eta_t (\lambda a - y_k x_k)$ and $b \leftarrow b + \eta_t \cdot y_k$ (both regularization and hinge loss gradients).
3. **Prediction**: $\hat{y} = \text{sign}(a^T x + b)$.

### 2.7 Evaluation

- Train separately for each $\lambda \in \{10^{-3}, 10^{-2}, 10^{-1}, 1\}$.
- For each $\lambda$, report both training and test accuracy.
- Random seed fixed at 42 for reproducibility.

---

## 3. Accuracy Results

| Regularization Constant ($\lambda$) | Training Accuracy | Test Accuracy |
|:------------------------------------:|:-----------------:|:-------------:|
| $10^{-3}$ (0.001)                    | 84.84%            | 84.61%        |
| $10^{-2}$ (0.01)                     | 84.63%            | **84.79%**    |
| $10^{-1}$ (0.1)                      | 82.78%            | 82.89%        |
| $10^{0}$ (1)                         | 76.47%            | 76.54%        |

**Best test accuracy: 84.79% at $\lambda = 0.01$.**

---

## 4. Analysis: Accuracy Trends with the Regularization Constant

### 4.1 Observed Trend

Accuracy **decreases as $\lambda$ increases**. Going from $\lambda = 10^{-3}$ to $\lambda = 1$, test accuracy drops from ~85% to ~77% — a loss of roughly 8 percentage points. The sharpest drop occurs between $\lambda = 0.1$ (82.89%) and $\lambda = 1$ (76.54%).

The two smallest values ($\lambda = 10^{-3}$ and $\lambda = 10^{-2}$) produce nearly identical accuracy (~84.6–84.8%), with $\lambda = 10^{-2}$ slightly outperforming on the test set.

### 4.2 Why This Happens

The regularization constant $\lambda$ directly controls the **bias–variance trade-off** in the SVM:

**The objective function is:**

$$J(a, b) = \frac{\lambda}{2}\|a\|^2 + \frac{1}{n}\sum_{k=1}^{n} \max(0, 1 - y_k(a^T x_k + b))$$

- The first term ($\frac{\lambda}{2}\|a\|^2$) is the **regularization term** — it penalizes large weights, encouraging a simpler model with a wider margin.
- The second term is the **empirical hinge loss** — it measures how well the model fits the training data.
- $\lambda$ determines the relative importance of regularization vs. data fitting.

**When $\lambda$ is small (e.g., $10^{-3}$):**
- The regularization penalty is weak, so the optimizer focuses on minimizing the hinge loss.
- The weight vector $a$ is allowed to grow large, enabling a more complex, tightly-fitted decision boundary.
- The model captures finer patterns in the data, producing high accuracy.
- Risk: if $\lambda$ is too small, the model can overfit. However, in this dataset, train and test accuracy are nearly equal, indicating that overfitting is not a problem at these values.

**When $\lambda$ is large (e.g., $1$):**
- The regularization penalty dominates the objective.
- The weight vector $a$ is heavily shrunk toward zero, forcing the model toward a very wide margin and an overly simple decision boundary.
- This causes **underfitting** — the model cannot capture the true structure of the data.
- At $\lambda = 1$, the accuracy drops to ~76.5%, which is close to the baseline accuracy of simply predicting the majority class (~75.2% of the dataset is labeled ≤50K), confirming severe underfitting.

**The "sweet spot" at $\lambda = 10^{-2}$:**
- This value provides the best test accuracy (84.79%), slightly above $\lambda = 10^{-3}$ on the test set.
- This makes sense: a small amount of regularization helps generalization by preventing the model from fitting noise, while still allowing enough model capacity to capture the true decision boundary.
- The fact that $\lambda = 10^{-2}$ slightly outperforms $\lambda = 10^{-3}$ on the test set (despite slightly lower training accuracy) is the classic signature of regularization improving generalization.

### 4.3 Summary

| $\lambda$ | Regularization Strength | Effect | Outcome |
|-----------|------------------------|--------|---------|
| $10^{-3}$ | Very weak | Model fits data closely, minimal margin enforcement | High accuracy; slight risk of overfitting |
| $10^{-2}$ | Mild | Good balance between margin and data fitting | **Best test accuracy** — optimal trade-off |
| $10^{-1}$ | Moderate | Margin grows, some patterns lost | Noticeable accuracy drop |
| $1$       | Very strong | Margin dominates, weights shrunk heavily | Severe underfitting, near-baseline accuracy |

The monotonic decrease in accuracy with increasing $\lambda$ (with the small exception at $10^{-2}$ vs $10^{-3}$) demonstrates the fundamental regularization–complexity trade-off: too much regularization simplifies the model beyond its ability to capture the true class boundary, while too little regularization risks overfitting (though this risk is minimal for this large, well-distributed dataset).
