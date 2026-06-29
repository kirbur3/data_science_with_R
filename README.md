# data_science_with_R

**Repository Overview**
Five R projects from STAT364 (Linear Regression Analysis) at Portland State University, covering the full applied regression workflow: estimation, inference, diagnostics, collinearity, transformation, model selection, and dimensionality reduction. All analysis is written in R Markdown using the faraway, ellipse, car, leaps, pls, and MASS packages, and a real-world materials science dataset.
Projects in this repo:
•	**Statistical Modeling and Data Analysis**
•	**Regression Diagnostics and Model Refinement in R**
•	**Model Comparison and Statistical Inference in Regression Analysis with R**
•	**Linear Regression Modeling for Material Density Prediction**
•	**Applied Regression Analysis Across Real-World Datasets**

**Statistical Modeling and Data Analysis**
Four-problem analysis covering non-constant error variance, response transformation, model selection, and principal component regression using pipeline, ozone, prostate, seatpos, and kanga datasets.
Tools: R, faraway, MASS, leaps, pls
Weighted Least Squares - pipeline data (Ch. 8)
•	Diagnosed non-constant variance via residuals vs. fitted plot on Lab ~ Field.
•	Split the Field range into 12 groups of 9 using gl(); computed group-level variance (varlab) and mean (meanfield) via lapply() and split().
•	Regressed log(varlab) ~ log(meanfield) to estimate the variance–predictor power relationship; estimated WLS weights as 1/sqrt(varlab) for use in a WLS fit of Lab on Field.
•	Evaluated nine transformations (sqrt, log, inverse applied to Lab and/or Field individually and together); log(Lab) ~ log(Field) produced the most stable variance across all combinations.
Box-Cox Transformation - ozone data (Ch. 9)
•	Fit O3 ~ temp + humidity + ibh; applied MASS::boxcox() with lambda = seq(−2, 3, by=0.1) to identify optimal lambda.
•	Re-fit with Box-Cox transformed response; R² improved from 0.684 to 0.716.
Variable Selection - prostate data (Ch. 10)
•	Backward elimination: iteratively removed the highest p-value predictor; final model retained lcavol, lweight, and svi.
•	AIC: step() in backward direction selected a 5-predictor model (lcavol, lweight, age, lbph, svi).
•	Adjusted R²: leaps::regsubsets() identified 7-parameter model as having the highest adjusted R².
•	Mallows Cp: no model cleanly met the Cp ≤ p+1 threshold; documented and interpreted.
PCR and AIC Model Selection - seatpos data (Ch. 10 & 11)
•	AIC backward selection chose Age + HtShoes + Leg; 95% prediction interval at mean predictor values narrowed vs. the full 8-predictor model.
•	PCR (HtShoes, Ht, Seated, Arm, Thigh, Leg): prcomp() summary and scree plot both indicated 1 PC (94.53% of variance); pls::pcr() with ncomp=1 predicted hipcenter = –205.09.
•	PCR (all 8 predictors): 3 PCs selected (99.23% variance); predictions reported at ncomp = 1, 2, and 3.
PCA on Skull Measurements - kanga data (Ch. 11)
•	Removed missing values via na.omit(); ran prcomp() on 18 numeric skull measurements (removing the first two non-numeric columns via [,-c(1,2)]). PC1 explained 90.03% of variance; prominent loadings on basilar.length, occipitonasal.length, and mandible.length.
•	Re-ran with scale=TRUE; PC1 variance dropped to 69.31% and no single variable dominated (all loadings < 0.3), illustrating the effect of standardization on PCA structure.
•	PC2 interpretation: crest.width (positive, > 0.4) and foramina.length (negative) were the dominant opposing variables.

**Regression Diagnostics and Model Refinement in R**
Two-chapter analysis covering regression diagnostics (Ch. 6) and collinearity detection (Ch. 7) using sat, teengamb, divusa, fat, and seatpos datasets.
Tools: R, faraway, car
Regression Diagnostics - sat data (Ch. 6)
•	Constant variance: residuals vs. fitted plot showed no pattern; variance judged constant.
•	Normality: Q-Q plot and histogram of residuals indicated approximate normality with slight skew.
•	Leverage: halfnorm() plot of hat values flagged Utah and California as large leverage points.
•	Outliers: rstudent() identified the observation with the largest absolute studentized residual.
•	Influential points: cooks.distance() halfnorm flagged Utah and West Virginia.
•	Structure: car::avPlots() showed positive partial relationships for expend and salary; negative for ratio and takers.
Regression Diagnostics - teengamb data (Ch. 6)
•	Constant variance: residuals vs. fitted plot showed a megaphone pattern; non-constant variance confirmed.
•	Normality: Q-Q plot indicated heavy-tailed distribution; histogram showed potential outlier near residual = 100.
•	Leverage: halfnorm() flagged observations 42 and 35.
•	Outliers: rstudent() returned 6.02 for observation 24.
•	Influential points: Cook’s distance flagged observations 39 and 24.
•	Structure: avPlots() showed negative partial relationships for sex and verbal; positive for income; slight positive for status.
Collinearity - divusa data (Ch. 7)
•	Computed eigenvalues and condition numbers via eigen(t(x) %*% x); large values indicated potential collinearity.
•	VIF via car::vif(): all values below 5, suggesting no actionable collinearity despite large condition numbers.
•	Removed insignificant predictors (military, unemployed) and recomputed condition numbers and VIF; collinearity structure unchanged.
Collinearity - fat data (Ch. 7)
•	13-predictor model: several VIF values elevated, flagging potential collinearity.
•	Removed cases 39 and 42; weight VIF increased substantially, indicating the unusual cases had been masking collinearity rather than causing it.
•	Reduced to age + weight + height: all VIF values dropped to acceptable levels. Computed 95% prediction intervals at median predictor values, at age=40/weight=200/height=73, and at age=40/weight=130/height=73; the last produced a negative lower bound, flagged as physically implausible.

**Model Comparison and Statistical Inference in Regression Analysis with R**
Three-chapter analysis (Ch. 3–5) covering confidence intervals, prediction, hypothesis testing, and explanatory modeling across prostate, cheddar, punting, fat, teengamb, and happy datasets.
Tools: R, faraway, ellipse, ggplot2
Confidence Intervals and Hypothesis Testing - prostate data (Ch. 3)
•	Computed 90% and 95% CIs for age via confint(); 90% CI excluded 0, 95% CI included it, indicating the p-value is between 0.05 and 0.10.
•	Plotted 95% joint confidence ellipse for age and lbph using ellipse::ellipse(); origin fell inside both the ellipse and the marginal intervals, so neither the joint nor the individual hypotheses were rejected.
•	Removed predictors with p > 0.05 (age, lbph, lcp, gleason, pgg45); anova() F-test returned p = 0.217, confirming the reduced model (lcavol, lweight, svi) was preferred.
Log-Scale vs. Original-Scale Predictors - cheddar data (Ch. 3)
•	Fit taste ~ Acetic + H2S + Lactic (log scale): H2S and Lactic significant at 5%, R² = 0.652. Refit on original scale via exp(): only Lactic significant, R² = 0.575. Models are non-nested; compared by R² since F-test is not applicable.
•	Interpreted H2S coefficient on log scale: a 0.01 increase in H2S corresponds to a 0.039 increase in predicted taste score.
F-tests and Symmetry Constraints - punting data (Ch. 3)
•	No individual predictor significant at 5%; global F-test vs. null model returned p = 0.019, confirming collective predictive value.
•	Tested equal effects for right vs. left leg strength (p = 0.468) and right vs. left flexibility (p = 0.202) individually, then simultaneously (p = 0.337); all symmetry constraints supported.
•	Confirmed I(RStr + LStr) sufficient vs. individual strengths (p = 0.598).
Prediction Intervals - prostate and fat data (Ch. 4)
•	Predicted lpsa for new patient at age=65: point estimate 2.389, 95% CI (2.172, 2.606). Re-predicted at age=20: CI widened to (2.260, 4.285), demonstrating interval expansion as predictor values move away from the training mean.
•	Full 13-predictor vs. reduced 4-predictor fat model: anova() favored the full model (p = 0.003), but 95% prediction intervals at median predictor values were nearly identical in width, suggesting practical equivalence despite statistical difference.
•	Identified cases 47 and 50 (rows 25–50) as anomalous via negative predicted body fat percentages; excluded and recomputed intervals with minimal change.
Coefficient Stability - teengamb data (Ch. 5)
•	Fit all 8 submodels including sex as a predictor (sex alone, plus each combination of status, income, verbal). Sex coefficient ranged from –21.6 to –35.7 but remained negative and significant (0.01–0.05 level) in all eight models.
•	Visualized gamble ~ income with separate lm regression lines by sex using ggplot2 geom_smooth(method='lm', aes(group=factor(sex))).
Categorical Recoding - happy data (Ch. 5)
•	Created clove: ifelse(love <= 2, 0, 1). Coefficient increased from 1.92 (love in full model) to 2.30 (clove in full model) to 2.72 (clove alone), illustrating how aggregating low-love categories sharpens the estimated happiness effect.

**Linear Regression Modeling for Material Density Prediction**
Midterm project: linear regression analysis of a 465-observation materials science dataset (p3.txt) with density as the response and three lab-measured properties (Prop1, Prop2, Prop3) as predictors.
Tools: R, ggplot2
Model and Results
•	density ~ Prop1 + Prop2 + Prop3. All three predictors significant at p < 0.001. Multiple R² = 0.9738. Residual standard error = 0.123.
•	Coefficient estimates: Prop1 = –1.055 (negative), Prop2 = 0.104 (positive), Prop3 = –0.001 (negative).
•	95% CIs: Prop1 (–1.230, –0.879), Prop2 (0.101, 0.106), Prop3 (–0.002, –0.0005). Prop2 has the narrowest CI; Prop1 the widest.
•	Global F-test via anova(null_model, full_model): p < 2.2e–16, confirming all three predictors collectively explain density.
Single-Predictor Comparison (cost-constrained selection)
•	Fit density ~ Prop1, density ~ Prop2, density ~ Prop3 separately and compared R² values. Prop1 had the lowest individual R².
•	Scatterplots with ggplot2 geom_smooth(method='lm') confirmed Prop2 had the tightest data-to-line fit across all three. Prop2 selected as the most informative single predictor.
Diagnostics
•	Non-constant variance observed across all three individual predictor scatterplots; variance higher at lower predictor values.
•	One observation near density = 2 appeared as a mild outlier across all plots but did not substantially influence the Prop2 regression line.
Extra Files: **p3.txt**

**Applied Regression Analysis Across Real-World Datasets**
Foundational regression analysis across four datasets (teengamb, uswages, prostate, cheddar) covering estimation, significance testing, model comparison, and prediction.
Tools: R, faraway, ggplot2
Teen Gambling - teengamb data
•	Fit gamble ~ sex + status + income + verbal. R² = 52.67%. Observation 24 had the largest positive residual (identified via which(residuals == max(residuals))).
•	Verified OLS properties: residual mean = –1.56e−16 (effectively 0), residual–fitted correlation ≈ 0, residual–income correlation ≈ 0, all confirming standard lm() behavior.
•	Sex coefficient = –22.12: male respondents predicted to spend £22.12 more per year on gambling than females, holding all else constant.
Wage Modeling - uswages data
•	Fit wage ~ educ + exper: each additional year of education associated with ≈51 more per week in predicted wages.
•	Fit log(wage) ~ educ + exper: R² improved from 0.135 to 0.175. Log model preferred on fit grounds and produces a more interpretable proportional education coefficient.
Incremental Predictor Analysis - prostate data
•	Starting from lpsa ~ lcavol, added lweight, svi, lbph, age, lcp, pgg45, and gleason one at a time (8 models total). Stored residual standard error and R² in vectors at each step.
•	Plotted both trendlines using plot(type='b') to visualize how fit improves and where it levels off as predictors accumulate.
Cheddar Cheese Regression - cheddar data
•	Fit taste ~ Acetic + H2S + Lactic. Confirmed that cor(fitted, response)^2 equals Multiple R² (0.652), verified programmatically.
•	Fit without intercept (0 + Acetic + H2S + Lactic): reported R² = 0.888. Documented as a denominator artifact of the no-intercept formula, not a genuine improvement in fit.
