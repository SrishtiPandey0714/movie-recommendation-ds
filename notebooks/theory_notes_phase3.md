📘 THEORY NOTES
Phase 3: Correlation Analysis & Hypothesis Testing

(Movie Recommendation System)

1️⃣ Why Phase 3 Exists (Conceptual Motivation)

After Exploratory Data Analysis (EDA), we often observe patterns visually, such as:

Popular movies appearing more frequently

Some genres seeming to have higher ratings

Extreme ratings appearing for less-rated movies

However, visual patterns alone are not reliable.
They may arise due to:

Random chance

Sample size imbalance

Noise in the data

Phase 3 answers:

Are the observed patterns statistically valid, or are they coincidental?

This phase transforms intuition into evidence.

2️⃣ Correlation Analysis (Popularity vs Rating)
Concept

Correlation measures the strength and direction of a linear relationship between two numerical variables.

In this project:

Variable 1: rating_count (movie popularity)

Variable 2: avg_rating (movie quality proxy)

We use Pearson correlation, which assumes:

Linear relationship

Continuous variables

Statistical Formulation

Correlation coefficient (r) ranges from -1 to +1

r ≈ 0 → no linear relationship

r > 0 → positive relationship

r < 0 → negative relationship

p-value indicates whether the observed correlation is statistically significant

Observation

Correlation between popularity and average rating is weak

Even if statistically significant, the effect size is small

Interpretation

Popular movies are not necessarily better-rated movies

Popularity and quality should be treated as separate signals.

3️⃣ Hypothesis Testing – Genre vs Rating (t-test)
Objective

To test whether different genres receive significantly different average ratings.

Test Used: Independent Two-Sample t-test (Welch’s t-test)

We compare:

Ratings of Genre A (e.g., Drama)

Ratings of Genre B (e.g., Comedy)

Hypotheses

Null hypothesis (H₀):
Mean ratings of the two genres are equal

Alternative hypothesis (H₁):
Mean ratings of the two genres are different

Why Welch’s t-test?

Does not assume equal variance

More robust for real-world data

Observation

p-value < 0.05

Interpretation

Genre has a statistically significant impact on movie ratings

Thus, genre is not a neutral feature.

4️⃣ Hypothesis Testing – Genre vs Rating Category (Chi-Square Test)
Objective

To test whether rating categories (Low / Medium / High) are independent of genre.

Test Used: Chi-Square Test of Independence

This test checks association between two categorical variables:

Genre

Rating category

Hypotheses

H₀: Genre and rating category are independent

H₁: Genre and rating category are dependent

Observation

p-value < 0.05

Interpretation

Rating distributions vary significantly across genres

This confirms systematic genre bias, not random fluctuation.

5️⃣ Core Statistical Conclusions (Phase 3 Summary)

Popularity has weak influence on ratings

Ratings without sufficient sample size are unreliable

Genre significantly influences:

Average ratings

Rating distributions

Recommendation systems must:

Control popularity bias

Account for genre effects

🔧 CONVERTING FINDINGS INTO FEATURE ENGINEERING RULES

This is the most important part.
Here we convert theory → system design.

🔹 RULE 1: Separate Popularity from Quality
Statistical Finding

Popularity and rating are weakly correlated.

Engineering Rule

❌ Do NOT treat popularity as quality
✅ Use popularity as a confidence signal, not a similarity signal

Implementation Idea

Use rating_count to:

Filter unreliable movies

Weight ratings

Do not directly include popularity in cosine similarity

🔹 RULE 2: Penalize Low-Sample Ratings
Statistical Finding

Movies with very few ratings show extreme averages.

Engineering Rule

❌ Do NOT trust raw average rating for low-count movies
✅ Apply a minimum rating threshold or weighting

Implementation Options

Ignore movies with rating_count < k

Or compute weighted rating:

weighted_rating
=
𝑣
𝑣
+
𝑚
𝑅
+
𝑚
𝑣
+
𝑚
𝐶
weighted_rating=
v+m
v
	​

R+
v+m
m
	​

C

Where:

𝑅
R = movie’s average rating

𝑣
v = rating count

𝐶
C = global average rating

𝑚
m = minimum votes threshold

🔹 RULE 3: Treat Genre as a Strong Feature
Statistical Finding

Genre significantly affects ratings and distributions.

Engineering Rule

✅ Genre must be:

Explicitly encoded

Properly weighted

Normalized to avoid dominance

Implementation Ideas

One-hot encode genres

Normalize genre vectors

Assign higher weight to genre similarity than popularity

🔹 RULE 4: Prevent Dominant Genre Bias
Statistical Finding

Some genres dominate the dataset.

Engineering Rule

❌ Do NOT let dominant genres overpower recommendations
✅ Normalize genre frequency effects

Implementation Ideas

TF-IDF–like weighting for genres

Down-weight extremely common genres (e.g., Drama)

Encourage genre diversity in recommendations

🔹 RULE 5: Rating as a Supporting Feature, Not the Core
Statistical Finding

Ratings are biased and context-dependent.

Engineering Rule

✅ Use ratings as:

A secondary feature

A re-ranking signal

❌ Do NOT rely on rating alone for similarity

🧠 BIG PICTURE CONNECTION (VERY IMPORTANT)

After Phase 3, your system philosophy becomes:

Similarity should be based on content and genre,
while ratings and popularity control confidence and ranking.

This is exactly how real-world recommenders are designed.