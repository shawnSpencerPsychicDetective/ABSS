# Advanced Biometric Systems and Security - Assignment 1 - User Authentication using Biometric Features

We perform a complete evaluation of the feature vectors in the dataset using 2 similarity measures:

- **Euclidean Distance**
- **Cosine Similarity**

The step-by-step code-by-code along with the explanation is given in the notebook. I have uploaded the plots in the GitHub as well. We do everything from data loading, processing, and analysis.

---

# 1. Loading, Analysis, & Processing of Data

- We load the data
- Transpose and clean the data (there was a NULL column that had to be removed).
- Creating user and sample ids
- We split the data into an enrollment and verification set (the assignment mentioned each user gave 10 images, 5 during enrollment and 5 after, based on that)
- The Training (Enrollment) set has 100 users, 5 each, total 500 samples
- Similarly the Testing (Verification set), has 500 samples as well

---

# 2. Computing Similarity Scores

The notebook computes two different comparison matrices.

We compute 2 comparison matrices, a Euclidean, and a cosine. Every value is a comparison between one enrollment image and one verification image (500x500).

## Euclidean Distance

```python
euclidean_matrix = euclidean_distances(X_test, X_train)
```

Euclidean distance measures the geometric distance between two feature vectors.

Smaller values indicate greater similarity.

If two samples are nearly identical, their Euclidean distance approaches zero.

---

## Cosine Similarity

```python
cosine_matrix = cosine_similarity(X_test, X_train)
```

Cosine similarity measures the angle between two feature vectors.

Its values range from:

- -1 (opposite direction)
- 0 (orthogonal)
- 1 (identical direction)

Higher cosine similarity indicates greater resemblance.

Unlike Euclidean distance, cosine similarity ignores magnitude and focuses only on orientation.

---

The resulting matrices have dimensions:

```
500 × 500
```

Each verification sample is compared with every enrollment sample.

This produces:

```
250,000 total comparisons
```

---

# 3. Separating Genuine and Impostor Scores

Every comparison belongs to one of two categories.

## Genuine Match

If

```
test_user == train_user
```

the score is stored as a **genuine score**.

These represent successful comparisons between different biometric samples of the same person.

---

## Impostor Match

If

```
test_user != train_user
```

the score is stored as an **impostor score**.

These comparisons represent different people.

---

Four score distributions are created:

- Genuine Euclidean
- Impostor Euclidean
- Genuine Cosine
- Impostor Cosine

These distributions form the basis for all later performance metrics.

---

# 4. Visualizing Score Distributions

We plot overlapping histograms for both similarity measures.

For Euclidean Distance:

- Genuine scores cluster at **small distances**
- Impostor scores appear at **larger distances**

For Cosine Similarity:

- Genuine scores cluster near **1**
- Impostor scores appear closer to **0** away from 1

A good biometric system produces minimal overlap between these two distributions.

Less overlap means fewer authentication errors.

---

# 5. Computing Distribution Statistics

We calculate:

- Mean
- Standard deviation

for both genuine and impostor score distributions.

These statistics summarize:

- average similarity,
- average distance,
- spread of each distribution.

Large separation between the means generally indicates better recognition performance.

## Euclidean
- Genuine : mean=**346.858**, std=**173.229**
- Impostor: mean=**732.048**, std=**312.219**

## Cosine
- Genuine : mean=**0.986**, std=**0.013**
- Impostor: mean=**0.957**, std=**0.019**

---

# 5. False Acceptance Rate (FAR)

False Acceptance Rate is the proportion of times it identifies 2 different people as the same.

For Euclidean distance:

```
Impostor accepted
if distance ≤ threshold
```

Therefore,

```
FAR =
fraction of impostor scores
below the threshold
```

As the threshold increases,

FAR also increases because more impostors are incorrectly accepted.

---

# 6. False Rejection Rate (FRR)

False Rejections rate represents the rate at which data of 2 same individuals are classified as not same

For Euclidean distance,

```
Genuine rejected
if distance > threshold
```

Therefore,

```
FRR =
fraction of genuine scores
above the threshold
```

Increasing the threshold decreases FRR because genuine users are less likely to be rejected.

---

# 7. FAR and FRR for Cosine Similarity

Cosine similarity behaves oppositely.

Higher similarity indicates better matches.

Therefore:

## Genuine rejected

```
similarity < threshold
```

## Impostor accepted

```
similarity ≥ threshold
```

We evaluate FAR & FRR for both Euclidean distance and cosine similarity at a range of 200 different threshold values.

---

# 8. FAR–FRR Curves

We plot:

- FAR
- FRR

against the decision threshold, for both Euclidean Distance and Cosine Similarity.

These curves illustrate the trade-off between security and convenience.

A stricter threshold:

- decreases FAR,
- increases FRR.

A more lenient threshold:

- decreases FRR,
- increases FAR.

The optimal operating point lies near the intersection of these curves.

---

# 9. ROC Curve

We construct a Receiver Operating Characteristic (ROC) curves.

Instead of plotting FRR directly, it computes:

```
GAR = 1 − FRR
```

where

**GAR** is the Genuine Acceptance Rate.

The ROC curve plots:

- FAR
- GAR

for both similarity measures.

A better biometric system produces a curve that remains closer to the upper-left corner of the graph, indicating high genuine acceptance with low false acceptance.

From the plot we can see that cosine similarity's curve is closer to the upper-left corner of the graph which indicates a higher GAR and lower FAR at the sametime compared to Euclidean distance.

---

# 10. Equal Error Rate (EER)

Equal Error Rate refers to the threshold where:

```
FAR ≈ FRR
```

We finds the threshold where the difference

```
| FAR − FRR |
```

is minimized.

The EER is then calculated as the average of FAR and FRR at that point.

---

## Interpretation

A lower EER indicates better biometric performance because both types of authentication errors are simultaneously minimized.

The dataset's EER results are:

- Euclidean EER ≈ **0.1558**
- Cosine Similarity EER ≈ **0.1146**

This shows that cosine similarity provides more accurate authentication for this dataset.

---

# 11. Decidability Index

Finally, we measures how well the genuine and impostor score distributions are separated.

The Decidability Index is computed as

\[
d'=\frac{|\mu_g-\mu_i|}
{\sqrt{\frac{\sigma_g^2+\sigma_i^2}{2}}}
\]

where:

- \(\mu_g\) = genuine mean
- \(\mu_i\) = impostor mean
- \(\sigma_g\) = genuine standard deviation
- \(\sigma_i\) = impostor standard deviation

A larger Decidability Index indicates:

- greater separation,
- easier classification,
- lower probability of authentication errors.

We find this value for both Euclidean distance and cosine similarity.

- Euclidean Decidability ≈ **1.526**
- Cosine Decidability ≈ **1.758**

As expected, this shows that cosine similarity achieves the higher Decidability Index, confirming that it provides better discrimination between genuine and impostor comparisons.

---

# Overall Workflow

The notebook follows the complete biometric evaluation pipeline:

1. Load, process, and analyze dataset
2. Compute Euclidean distances and cosine similarities.
3. Separate genuine and impostor comparison scores.
4. Visualize score distributions.
5. Compute descriptive statistics.
6. Evaluate FAR and FRR over multiple thresholds.
7. Plot FAR–FRR curves.
8. Generate ROC curves.
9. Calculate Equal Error Rate (EER).
10. Compute the Decidability Index.
11. Compare Euclidean distance and cosine similarity.

---

# Learning & Conclusion

We have evaluated the biometric data using 2 measures, Euclidean distance and cosine similarity. We have learnt about the idea of Euclidean Distances, Cosine Similarity, FAR, FRR, EER, ROC, and Decidability. Our data consistently shows that, for this dataset atleast, cosine similarity is better at distinguishing identities compared to Euclidean distance.