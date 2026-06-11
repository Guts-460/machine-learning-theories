# Principal Component Analysis (PCA)

Principal Component Analysis (PCA) is a dimensionality reduction technique that transforms a set of possibly correlated variables into a smaller set of orthogonal variables called **principal components**. PCA identifies directions of maximum variance in the data and projects the data onto a lower-dimensional subspace while preserving as much information as possible.

---

## 1. Problem Setup

Let

- $X \in \mathbb{R}^{n \times p}$ be a data matrix,
- $n$ = number of samples,
- $p$ = number of features.

Before applying PCA, the data are typically centered:

$$
\tilde{X}_{ij} = X_{ij} - \frac{1}{n} \sum_{k=1}^{n} X_{kj}
$$

where each feature has zero mean.

Throughout this document, we assume that $X$ has already been centered.

---

## 2. Covariance Matrix

The sample covariance matrix is

$$
\Sigma = \frac{1}{n} X^\top X
$$

where

$$
\Sigma \in \mathbb{R}^{p \times p}.
$$

The covariance matrix is symmetric and positive semidefinite.

---

## 3. Objective of PCA

The goal of PCA is to find a unit vector

$$
w_1 \in \mathbb{R}^{p}
$$

such that the variance of the projected data is maximized.

The projection of the data onto $w_1$ is

$$
t_1 = Xw_1.
$$

Its variance is

$$
\mathrm{Var}(t_1) = \frac{1}{n} (Xw_1)^\top (Xw_1) = \frac{1}{n} w_1^\top X^\top X w_1 = w_1^\top \Sigma w_1.
$$

Therefore, the optimization problem is

$$
\max_{w_1} \quad w_1^\top \Sigma w_1
$$

subject to

$$
w_1^\top w_1 = 1.
$$

---

## 4. Rayleigh Quotient and Eigenvalue Problem

Define the Rayleigh quotient

$$
R(w) = \frac{w^\top \Sigma w}{w^\top w}.
$$

For unit vectors,

$$
R(w)=w^\top\Sigma w.
$$

Using a Lagrange multiplier,

$$
L(w,\lambda) = w^\top\Sigma w \lambda(w^\top w-1).
$$

Taking derivatives gives

$$
\frac{\partial L}{\partial w} = 2\Sigma w - 2\lambda w = 0,
$$

which leads to

$$
\Sigma w = \lambda w.
$$

Thus, PCA reduces to an eigenvalue problem.

The first principal component is the eigenvector corresponding to the largest eigenvalue:

$$
\Sigma w_1 = \lambda_1 w_1,
$$

where

$$
\lambda_1 = \max_i \lambda_i.
$$

---

## 5. Subsequent Principal Components

The second principal component is obtained by solving

$$
\max_w \quad w^\top \Sigma w
$$

subject to

$$
w^\top w = 1,
$$

and

$$
w^\top w_1 = 0.
$$

More generally, the $k$-th principal component solves

$$
\max_w \quad w^\top \Sigma w
$$

subject to

$$
w^\top w = 1,
$$

and

$$
w^\top w_j = 0, \quad j=1,\ldots,k-1.
$$

The solution is the eigenvector associated with the $k$-th largest eigenvalue:

$$
\Sigma w_k = \lambda_k w_k.
$$

The corresponding principal component scores are

$$
t_k = Xw_k.
$$

---

## 6. Dimensionality Reduction

Let

$$
W_d = [w_1,w_2,\ldots,w_d] \in \mathbb{R}^{p\times d},
$$

where $d \ll p$.

The reduced representation is

$$
Z = XW_d.
$$

Each row of $Z$ is the coordinate of a sample in the $d$-dimensional principal-component space.

---

## 7. PCA via Singular Value Decomposition (SVD)

Consider the Singular Value Decomposition (SVD):

$$
X = U S V^\top,
$$

where

- $U \in \mathbb{R}^{n\times n}$ is orthogonal,
- $V \in \mathbb{R}^{p\times p}$ is orthogonal,
- $S$ contains singular values

$$
\sigma_1 \ge \sigma_2 \ge \cdots \ge 0.
$$

Substituting into the covariance matrix gives

$$
\Sigma = \frac{1}{n} X^\top X = \frac{1}{n} V S^\top S V^\top.
$$

Therefore,

- Columns of $V$ are PCA loading vectors.
- Eigenvalues are

$$
\lambda_k = \frac{\sigma_k^2}{n}.
$$

Hence PCA can be computed directly from the SVD of the centered data matrix.

---

## 8. Explained Variance Ratio

The total variance in the dataset is

$$
\sum_{j=1}^{p} \lambda_j = \mathrm{trace}(\Sigma).
$$

The proportion of variance explained by the first $d$ principal components is

$$
\mathrm{EVR}(d) = \frac{\sum_{j=1}^{d}\lambda_j}{\sum_{j=1}^{p}\lambda_j}.
$$

A large value of $\mathrm{EVR}(d)$ indicates that the selected components preserve most of the information in the original dataset.

---

## 9. Reconstruction

Given the reduced representation

$$
Z = XW_d,
$$

the reconstruction in the original feature space is

$$
\hat{X} = ZW_d^\top = XW_dW_d^\top.
$$

If the data were centered before PCA, the feature means should be added back after reconstruction.

---

## 10. Optimality of PCA

PCA provides the best rank - $d$ approximation of the data matrix in the least-squares sense.

Specifically,

$$
W_d = \arg\min_{\mathrm{rank}(Y)=d} \|X-Y\|_F^2,
$$

where $\|\cdot\|_F$ denotes the Frobenius norm.

This result follows from the **Eckart–Young theorem** and explains why PCA is widely used for compression and denoising.

---

## 11. Key Properties

### Orthogonality

The loading vectors satisfy

$$
w_i^\top w_j = \delta_{ij},
$$

where $\delta_{ij}$ is the Kronecker delta.

### Uncorrelated Components

The principal component scores satisfy

$$
\frac{1}{n} t_i^\top t_j = 0, \quad i \neq j.
$$

### Variance Maximization

Each principal component captures the maximum remaining variance after accounting for all previous components.

### Ordered Importance

The eigenvalues satisfy

$$
\lambda_1 \ge \lambda_2 \ge \cdots \ge \lambda_p.
$$

Thus, earlier principal components contain more information than later ones.

---

## 12. Numerical Algorithm

1. Center the data matrix.

$$
X_c = X - \mathbf{1}_n \mu^\top
$$

where $\mu$ is the vector of feature means.

2. Compute the covariance matrix.

$$
\Sigma = \frac{1}{n} X_c^\top X_c
$$

3. Compute the eigenvalues and eigenvectors of $\Sigma$
   (or directly compute the SVD of $X_c$).

4. Sort eigenvectors according to decreasing eigenvalues.

5. Construct the projection matrix.

$$
W_d =
\left[
w_1,\,
w_2,\,
\ldots,\,
w_d
\right]
$$

6. Obtain the reduced representation.

$$
Z = X_c W_d
$$
