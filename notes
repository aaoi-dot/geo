https://dl.acm.org/doi/pdf/10.1145/1557019.1557153 

Logistic Regression: This is a simple parametric model for binary classification where examples are classified based on their distance from a hyperplane decision boundary [11]. The decision rule is expressed in terms of the sigmoid function σ(z) = [1 + e −z ] −1 , which converts these distances into probabilities that feature vectors have positive or negative labels. The conditional probability that feature vector x has a positive label y= 1 is the following: P(y= 1|x) = σ(w·x + b), (3) where the weight vector w∈ℜd and scalar bias b are parameters to be estimated from training data. In practice, the right hand side of eq. (3) is thresholded to obtain a binary prediction for the label of the feature vector x

Explaining logistic regression with clear, approachable prose.

This passage describes logistic regression, one of the simplest ways to teach a computer to sort things into two groups (e.g. spam vs. not spam, malignant vs. benign). Here it is piece by piece.

“Parametric model for binary classification”
“Binary classification” means every example gets one of two labels, written as y = 1 (positive) or y = 0 (negative). “Parametric” means the model is fully described by a fixed set of numbers (the parameters w and b) that get learned from data.

Feature vectors and the hyperplane
Each example is described by a list of d numbers, the feature vector x. For an email, these might be word counts; for a patient, test results. You can think of each example as a point in d-dimensional space.

The model draws a flat boundary through that space, a hyperplane (a line in 2D, a plane in 3D, and so on). Points on one side lean positive, points on the other lean negative. The boundary is defined by the equation w·x + b = 0.

What w·x + b means

w is a weight vector with one weight per feature. A large positive weight means that feature pushes strongly toward “positive”; a negative weight pushes toward “negative”.
w·x is the dot product: multiply each feature by its weight and add them up.
b is the bias, a constant that shifts the boundary.

The resulting number z = w·x + b is (proportional to) the signed distance of the point from the boundary. Large positive z means “far on the positive side”, large negative z means “far on the negative side”, and z near 0 means “close to the boundary, uncertain”.

The sigmoid function
z can be any number from −∞ to +∞, but we want a probability between 0 and 1. The sigmoid does that conversion:

σ(z) = 1 / (1 + e^(−z))

It has an S shape:

z very large → σ(z) ≈ 1
z = 0 → σ(z) = 0.5
z very negative → σ(z) ≈ 0

So points far on the positive side get probabilities near 1, points on the boundary get 0.5, and points far on the negative side get probabilities near 0.

Equation (3)
P(y = 1 | x) = σ(w·x + b)

Read it as: “the probability that this example is positive, given its features, equals the sigmoid of its distance from the boundary.” The probability of being negative is just 1 minus that.

“Parameters to be estimated from training data”
w ∈ ℝᵈ just says w is a list of d real numbers. You don’t choose w and b by hand: you show the model many labelled examples, and a training algorithm (usually minimising log-loss, a.k.a. cross-entropy, with gradient descent) adjusts them until the predicted probabilities match the true labels as well as possible.

Thresholding
To get a hard yes/no answer, you compare the probability to a cutoff, typically 0.5: predict positive if σ(w·x + b) ≥ 0.5, otherwise negative. Since σ(z) = 0.5 exactly when z = 0, this is the same as asking which side of the hyperplane the point is on. The threshold can be moved (e.g. to 0.3) if missing a positive is costlier than a false alarm.

A tiny example
Say you predict whether a student passes from hours studied (x₁) and hours slept (x₂), and training gives w = (0.8, 0.5), b = −6. A student with 5 hours of study and 4 of sleep gets z = 0.8×5 + 0.5×4 − 6 = 0, so σ(0) = 0.5: right on the fence. With 8 hours of study instead, z = 2.4, σ(2.4) ≈ 0.92, so the model predicts “pass” with 92% confidence.

If it’d help, I can sketch the sigmoid curve and a 2D decision boundary, or walk through how the training step actually finds w and b.

A hyperplane is just a flat “dividing surface” that splits a space into two halves. It’s always one dimension lower than the space it lives in:

In 2D (a flat sheet of paper), a hyperplane is a straight line. It cuts the page into two sides.
In 3D (the room around you), it’s a flat plane, like a sheet of glass slicing the room in two.
In 4D, 5D, or 1000D, it’s the same idea, a flat cut through the space, but we can’t picture it. “Hyperplane” is simply the general word that covers every dimension.

The key properties are that it’s flat (no curves or bends) and that it separates everything into exactly two sides.

Mathematically, it’s all the points x where

w·x + b = 0

In 2D with x = (x₁, x₂), that’s w₁x₁ + w₂x₂ + b = 0, which is just the equation of a straight line. For any point not on the line, w·x + b comes out either positive or negative, and the sign tells you which side the point is on. The bigger the number, the farther from the line.

That’s why it’s useful in logistic regression: your examples have many features, so they live in a high-dimensional space, and the model draws one flat cut through it. Everything on one side leans “positive”, everything on the other leans “negative”, and the sigmoid turns “how far from the cut” into a probability.

--------------------------------------------------------------------
1 What is d, and in what units? Is it days, such as URL or domain age or time since last seen, or something else? Is d a single number per URL, or an array so he can score many URLs at once?
2 Where do 366 and 23 come from? Is the midpoint an average from his data, a leap year split into 23 periods, or something else?
3 Did he fit k and mid to data, or set them by hand? If they were fitted, the formula is a one-feature logistic regression and the output is a real probability. If he set them, it’s a scoring rule, and it’s worth asking why he chose those values.
4 Is the gentle slope intended? With k = 0.02, the score is already 0.42 at d = 0 and takes about 220 units to rise from 10% to 90%. Did he mean the change to be that gradual? For new URLs to start near 0, k would need to be around 0.3.
5 How is the score used? If it’s only a yes/no cut-off, such as above 0.5, that’s identical to “d > 15.9”, so the curve adds nothing. Is it combined with other features or scores later?
6 What’s the goal of the study? Malware or phishing detection, or link rot? That tells you which literature to compare against: Ma et al. (2009) and domain-age scoring for security, or URL half-life and survival studies for link rot.
7 Check the brackets in his code. The formula should be 1 / (1 + e^(−k·(d − mid))). His written version without brackets would compute something different if typed into code that way.

The logistic sigmoid

Formula: f(x) = 1 / (1 + e^(−k·(x − mid)))
It turns any number into a value between 0 and 1 along a smooth S-shape.
mid is where the output is exactly 0.5.
k is the steepness. Large k gives a sharp step, small k a gentle slope. The output goes from 10% to 90% over about 4.4 ÷ k units.
The curve never actually reaches 0 or 1. What you see at x = 0 depends on k × mid. If that’s small, the curve starts near 0.5. It needs to be around 4 or more to start near 0.

2. Your colleague’s version

f(d) = 1 / (1 + e^(−0.02·(d − 15.9))), with brackets added.
d is probably days. 366/23 is probably an average or a year split into periods.
It’s a gentle curve: 0.42 at d = 0, 0.82 at d = 90, near 1 by d = 300.

3. Logistic regression

Two steps: a weighted sum of features, z = b₀ + b₁x₁ + b₂x₂ + …, then the sigmoid, p = 1 / (1 + e^(−z)).
The weights are learned from labelled data, such as URLs already known to be safe or malicious.
Positive weights push towards “yes”, negative weights towards “no”. The output is a probability.
It’s popular for URL work because it’s interpretable, gives probabilities, scales well, and is the standard baseline. The landmark paper is Ma et al., Beyond Blacklists (KDD 2009).

4. One feature vs many

A one-feature sigmoid is the simplest logistic regression: k is the weight and −k·mid is the intercept.
For yes/no decisions, a single-feature sigmoid is equivalent to a cut-off on the raw value: score > 0.5 means d > 15.9.
Its value comes from graded scores, a common 0–1 scale, real probabilities if fitted, and flattening at the extremes.
With many features, the signals can offset each other. That’s where the real predictive power comes from.

5. Vectors

Batch of values: d as an array with one value per URL, and the same curve applied to each. This is common in code, such as NumPy.
Feature vector: d as several features of one URL. This needs k as a vector of weights and a dot product to combine them: f = 1 / (1 + e^(−(b₀ + k·x))).
His single k and single mid suggest the first: one quantity per URL.

6. Other uses of sigmoids

Activation functions in neural networks.
Growth and adoption curves over time, such as epidemics or technology uptake.
Dose-response curves in medicine.
Soft thresholds in hand-built scoring rules.

7. Related research areas

Malicious URL detection: logistic regression on lexical and host features.
Phishing: domain age is a strong signal, usually with hard cut-offs like 30 days, 45 days or 12 months. A sigmoid is the smooth version.
Link rot: URL half-life studies, survival analysis, and logistic regression on URL lifespan.
