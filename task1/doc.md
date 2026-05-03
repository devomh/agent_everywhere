# Bayes' Theorem: A Foundation of Probabilistic Reasoning

## Abstract

Bayes' theorem describes how to update beliefs in light of new evidence. First
formulated by Thomas Bayes in the 18th century and later developed by Pierre-Simon
Laplace, it has become a cornerstone of statistics, machine learning, and scientific
inference. This document presents the theorem, its derivation, key interpretations,
and several illustrative applications including medical testing and spam filtering.

## 1. Introduction

Probability theory provides a rigorous language for reasoning under uncertainty.
Among its most powerful tools is Bayes' theorem, which relates the conditional
probability of a hypothesis given observed data to the prior probability of the
hypothesis and the likelihood of the data. In an era of data-driven decision-making,
Bayesian reasoning underlies applications ranging from clinical trials to autonomous
vehicles.

The central question Bayes' theorem answers is: *given that we have observed some
evidence, how should we rationally revise our belief about a hypothesis?*

## 2. Mathematical Formulation

### 2.1 Conditional Probability

The conditional probability of event $A$ given event $B$ is defined as:

$$P(A \mid B) = \frac{P(A \cap B)}{P(B)}, \quad P(B) > 0$$

By symmetry, $P(B \mid A) = \frac{P(A \cap B)}{P(A)}$, so:

$$P(A \cap B) = P(A \mid B)\,P(B) = P(B \mid A)\,P(A)$$

### 2.2 Bayes' Theorem

Rearranging the identity above yields **Bayes' theorem**:

$$\boxed{P(A \mid B) = \frac{P(B \mid A)\,P(A)}{P(B)}}$$

In the context of hypothesis testing, we write $H$ for a hypothesis and $D$ for
observed data:

$$P(H \mid D) = \frac{P(D \mid H)\,P(H)}{P(D)}$$

The terms have well-known names:

- $P(H)$ — the **prior**: our belief in $H$ before seeing $D$
- $P(D \mid H)$ — the **likelihood**: how probable the data are if $H$ is true
- $P(H \mid D)$ — the **posterior**: our updated belief after seeing $D$
- $P(D)$ — the **marginal likelihood** (evidence): a normalising constant

### 2.3 The Law of Total Probability

When $H$ takes discrete values $H_1, H_2, \ldots, H_n$ that partition the sample
space, the denominator expands via the law of total probability:

$$P(D) = \sum_{i=1}^{n} P(D \mid H_i)\,P(H_i)$$

This gives the full form:

$$P(H_k \mid D) = \frac{P(D \mid H_k)\,P(H_k)}{\displaystyle\sum_{i=1}^{n} P(D \mid H_i)\,P(H_i)}$$

## 3. Applications

### 3.1 Medical Diagnostic Testing

Consider a disease affecting 1% of a population. A test has 99% sensitivity
(true-positive rate) and 95% specificity (true-negative rate, so 5% false-positive
rate). Given a positive test result, what is the probability the patient has the
disease?

Let $H$ = "patient has disease" and $D$ = "test is positive":

$$P(H) = 0.01, \quad P(D \mid H) = 0.99, \quad P(D \mid \neg H) = 0.05$$

$$P(D) = 0.99 \times 0.01 + 0.05 \times 0.99 = 0.0099 + 0.0495 = 0.0594$$

$$P(H \mid D) = \frac{0.99 \times 0.01}{0.0594} \approx 0.167$$

Despite the accurate test, only about 16.7% of positive results correspond to true
disease. This counterintuitive result is a direct consequence of the low base rate,
illustrating why prior probabilities matter enormously.

### 3.2 Bayesian Spam Filtering

A spam filter estimates $P(\text{spam} \mid \text{words in email})$. Using a naive
Bayes classifier, it assumes word occurrences are conditionally independent given the
class, then applies:

$$P(\text{spam} \mid w_1, \ldots, w_n) \propto P(\text{spam})\prod_{i=1}^{n}P(w_i \mid \text{spam})$$

Training data provide the likelihoods $P(w_i \mid \text{spam})$ and
$P(w_i \mid \text{ham})$, and the prior $P(\text{spam})$ is estimated from the
proportion of spam in the training set.

## 4. The Bayesian vs. Frequentist Debate

Frequentists interpret probability as the long-run frequency of events; they do not
assign probabilities to hypotheses. Bayesians treat probability as a degree of belief,
permitting statements like "there is a 73% chance this hypothesis is true." The two
schools differ in how they handle prior information:

- **Frequentists** rely solely on the likelihood function and avoid priors.
- **Bayesians** explicitly encode prior knowledge, which is then updated by data.

In practice, both approaches are valuable; the Bayesian framework is especially useful
when prior knowledge is available or when sequential updating of beliefs is required.

## 5. Conclusion

Bayes' theorem is a deceptively simple equation with profound implications. It
provides a principled mechanism for learning from evidence: start with a prior belief,
observe data, compute the likelihood, and obtain a posterior belief. Applications span
medicine, finance, natural language processing, robotics, and fundamental science. As
datasets grow and computational power increases, Bayesian methods continue to gain
prominence as a rigorous framework for inference under uncertainty.
