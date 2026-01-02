# League of Legends Match Outcome Prediction

## Overview
This project builds a **logistic regression model** to predict match outcomes for League of Legends (LoL) players using **end-game per-player statistics**.  

Key goals:
- Predict whether a player is on a winning team
- Maintain **interpretability**
- Include **interaction effects** while respecting feature hierarchy
- Evaluate performance using **5-fold cross-validation** and a held-out test set

---

## Dataset
- **Features**: ~15 per-player end-game stats (kills, deaths, assists, gold, vision score, etc.)
- **Target**: Binary variable indicating whether the player’s team won (1) or lost (0)
- **Constraints**:
  - Only per-player statistics available
  - No match-level alignment or differences (diffs) available

---

## Preprocessing
1. **Feature scaling**  
   All continuous variables are standardized using `StandardScaler` to ensure:
   - Comparable magnitudes
   - Stable regularization
   - Proper distance metrics if using kNN

2. **Interaction features**  
   - Generated via `PolynomialFeatures(interaction_only=True)`
   - Only pairwise interactions are included

3. **Feature hierarchy enforcement**  
   - If an interaction is selected, the corresponding main effects are retained
   - This ensures model interpretability

---

## Model
- **Algorithm**: Logistic Regression
- **Regularization**:
  - **Stage 1**: L1 penalty for feature discovery
  - **Stage 2**: L2 penalty to stabilize coefficients and respect hierarchy
- **Cross-validation**: 5-fold CV on the training set
- **Evaluation metric**: ROC-AUC (ranking performance)

---

## Workflow
1. Split data into **90% training** and **10% test**
2. Generate interactions and scale features
3. Perform **5-fold cross-validation**:
   - L1-regularized logistic regression selects important features
   - Main effects added back if their interactions are selected
   - Refit L2 logistic regression on training folds
   - Evaluate ROC-AUC on validation folds
4. Average CV ROC-AUC to assess model stability
5. Refit final model on full training set with hierarchical features
6. Evaluate on the test set

---

## Results
- **Mean CV ROC-AUC**: ~0.85 (good discriminative ability)
- **Test ROC-AUC**: consistent with CV (~0.82–0.88)
- Coefficients interpreted as **change in log-odds per 1 standard deviation** increase
- Interaction coefficients indicate how one feature **modifies the effect** of another

---

## Comparison with kNN (Optional Baseline)
- k-Nearest Neighbors (kNN) evaluated as a **non-parametric benchmark**
- Despite hyperparameter tuning, kNN underperformed due to:
  - High dimensionality
  - Many categorical or one-hot encoded features
  - Curse of dimensionality
- Confirms logistic regression is superior for interpretability and predictive performance

