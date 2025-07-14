1.  **Regularization**:

    - Use the `diabetes` dataset from `sklearn.datasets`.
    - Compare the performance (Mean Squared Error) of `LinearRegression`, `Ridge`, and `Lasso` models.
    - Tune the `alpha` parameter for `Ridge` and `Lasso` using `GridSearchCV` with cross-validation to find the optimal regularization strength.

    ```python
    from sklearn.datasets import load_diabetes

    # Load the diabetes dataset
    diabetes = load_diabetes()
    ```
    ```Python
    from sklearn.datasets import load_diabetes
    from sklearn.linear_model import LinearRegression, Ridge, Lasso
    from sklearn.model_selection import train_test_split, GridSearchCV
    from sklearn.metrics import mean_squared_error
    import numpy as np
```Python
   # Load dataset
   diabetes = load_diabetes()
   X = diabetes.data
   y = diabetes.target

  # Split into train and test sets
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

  #Linear Regression
  lr = LinearRegression()
  lr.fit(X_train, y_train)
  y_pred_lr = lr.predict(X_test)
  mse_lr = mean_squared_error(y_test, y_pred_lr)
  print(f"Linear Regression MSE: {mse_lr:.4f}")

  # 2. Ridge Regression with GridSearchCV

  ridge = Ridge()
  param_grid_ridge = {'alpha': np.logspace(-4, 4, 50)}
  ridge_cv = GridSearchCV(ridge, param_grid_ridge, cv=5, scoring='neg_mean_squared_error')
  ridge_cv.fit(X_train, y_train)
  best_ridge = ridge_cv.best_estimator_
  y_pred_ridge = best_ridge.predict(X_test)
  mse_ridge = mean_squared_error(y_test, y_pred_ridge)
  print(f"Ridge Regression MSE: {mse_ridge:.4f}, Best alpha: {ridge_cv.best_params_['alpha']}")
    
  # 3. Lasso Regression with GridSearchCV

  lasso = Lasso(max_iter=10000)
  param_grid_lasso = {'alpha': np.logspace(-4, 1, 50)}
  lasso_cv = GridSearchCV(lasso, param_grid_lasso, cv=5, scoring='neg_mean_squared_error')
  lasso_cv.fit(X_train, y_train)
  best_lasso = lasso_cv.best_estimator_
  y_pred_lasso = best_lasso.predict(X_test)
  mse_lasso = mean_squared_error(y_test, y_pred_lasso)
  print(f"Lasso Regression MSE: {mse_lasso:.4f}, Best alpha: {lasso_cv.best_params_['alpha']}")
```
Output:
Linear Regression MSE: 2821.7510
Ridge Regression MSE: 2820.0936, Best alpha: 0.0020235896477251557
Lasso Regression MSE: 2816.4394, Best alpha: 0.005428675439323859

Findings: 
1. Linear Regression MSE: 2821.7510
This is the Mean Squared Error (MSE) of the plain linear regression model.
MSE tells you how far off your model’s predictions are from the actual values.
The lower, the better.

2. Ridge Regression
MSE: 2820.0936 — slightly better (lower) than regular linear regression.
Best alpha: 0.0020 — this is the regularization strength chosen by GridSearchCV.
Ridge adds L2 penalty → it shrinks coefficients to avoid overfitting.

3. Lasso Regression
MSE: 2816.4394 — lowest error among the three models.
Best alpha: 0.0054
Lasso adds L1 penalty → it shrinks and can zero out irrelevant features (feature selection).

| Model  | Regularization | Best Alpha | MSE ↓ (Error) | Effect                               |
| ------ | -------------- | ---------- | ------------- | ------------------------------------ |
| Linear | ❌ None         | –          | 2821.7510     | Basic model                          |
| Ridge  | ✅ L2           | 0.0020     | 2820.0936     | Penalizes large coefficients         |
| Lasso  | ✅ L1           | 0.0054     | 2816.4394     | Shrinks + selects important features |


Lasso performed best in this case — with the lowest MSE, meaning it made the most accurate predictions on the test set.
And tuning alpha helped improve both Ridge and Lasso over plain linear regression.


2.  **Ensemble Methods**:

    - Use the `breast_cancer` dataset from `sklearn.datasets`.
    - Compare the performance (F1 Score and AUC) of `DecisionTreeClassifier`, `RandomForestClassifier`, and `GradientBoostingClassifier`.
    - Tune the hyperparameters of each classifier using `GridSearchCV` with cross-validation.

    ```python
    from sklearn.datasets import load_breast_cancer

    # Load the breast cancer dataset
    breast_cancer = load_breast_cancer()
    ```
```Python
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.metrics import f1_score, roc_auc_score
from sklearn.preprocessing import StandardScaler
import numpy as np

# Load dataset
data = load_breast_cancer()
X = data.data
y = data.target

# Split data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Optional: scale data for boosting
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Results dictionary
results = {}

# -------------------------
# 1. Decision Tree Classifier
# -------------------------
param_grid_dt = {
    'max_depth': [3, 5, 7, None],
    'criterion': ['gini', 'entropy']
}
grid_dt = GridSearchCV(DecisionTreeClassifier(random_state=42), param_grid_dt, cv=5, scoring='f1')
grid_dt.fit(X_train, y_train)
best_dt = grid_dt.best_estimator_
y_pred_dt = best_dt.predict(X_test)
results['Decision Tree'] = {
    'F1': f1_score(y_test, y_pred_dt),
    'AUC': roc_auc_score(y_test, best_dt.predict_proba(X_test)[:, 1])
}

# -------------------------
# 2. Random Forest Classifier
# -------------------------
param_grid_rf = {
    'n_estimators': [50, 100],
    'max_depth': [3, 5, 7, None],
    'max_features': ['sqrt', 'log2']
}
grid_rf = GridSearchCV(RandomForestClassifier(random_state=42), param_grid_rf, cv=5, scoring='f1')
grid_rf.fit(X_train, y_train)
best_rf = grid_rf.best_estimator_
y_pred_rf = best_rf.predict(X_test)
results['Random Forest'] = {
    'F1': f1_score(y_test, y_pred_rf),
    'AUC': roc_auc_score(y_test, best_rf.predict_proba(X_test)[:, 1])
}

# -------------------------
# 3. Gradient Boosting Classifier
# -------------------------
param_grid_gb = {
    'n_estimators': [50, 100],
    'learning_rate': [0.05, 0.1, 0.2],
    'max_depth': [3, 5]
}
grid_gb = GridSearchCV(GradientBoostingClassifier(random_state=42), param_grid_gb, cv=5, scoring='f1')
grid_gb.fit(X_train_scaled, y_train)
best_gb = grid_gb.best_estimator_
y_pred_gb = best_gb.predict(X_test_scaled)
results['Gradient Boosting'] = {
    'F1': f1_score(y_test, y_pred_gb),
    'AUC': roc_auc_score(y_test, best_gb.predict_proba(X_test_scaled)[:, 1])
}

# Print results
for model, metrics in results.items():
    print(f"{model} - F1 Score: {metrics['F1']:.4f}, AUC: {metrics['AUC']:.4f}")

```
Output:
Decision Tree -     F1 Score: 0.9772, AUC: 0.9762
Random Forest -     F1 Score: 0.9772, AUC: 0.9968
Gradient Boosting - F1 Score: 0.9677, AUC: 0.9951

🎯 Metric Breakdown
✅ F1 Score
Measures precision + recall balance.
Best when dealing with imbalanced classes.
Higher = Better

✅ AUC (ROC Curve)
Measures model’s ability to distinguish between classes (ranking confidence).
Ranges from 0.5 to 1.0.
Higher = Better

       Model	                   Type	                             Strength
DecisionTreeClassifier	       Base learner	             Simple, interpretable, overfits easily
RandomForestClassifier	       Bagging	Reduces          overfitting, good accuracy
GradientBoostingClassifier	   Boosting	                 Learns from mistakes, high accuracy


| Scenario                             | Recommended Model            | Why?                                                       |
| ------------------------------------ | ---------------------------- | ---------------------------------------------------------- |
| ✅ **High accuracy needed**           | `GradientBoostingClassifier` | Best performance, learns from previous errors              |
| ✅ **Fast and robust generalization** | `RandomForestClassifier`     | Easy to train, good performance, lower risk of overfitting |
| 🧪 **Simple, interpretable model**   | `DecisionTreeClassifier`     | Easy to explain, but can overfit                           |
| 🐢 **Small dataset & quick model**   | `DecisionTreeClassifier`     | Lightweight and interpretable                              |
| 🧠 **Complex patterns in data**      | `GradientBoostingClassifier` | Handles nuanced relationships                              |

| Model             | Accuracy (Usually) | Speed to Train | Tuning Required?  |
| ----------------- | ------------------ | -------------- | ----------------- |
| Decision Tree     | 🟨 Moderate        | 🟢 Fast        | 🔴 Minimal        |
| Random Forest     | 🟩 Good            | 🟡 Moderate    | 🟡 Medium         |
| Gradient Boosting | 🟩 Excellent       | 🔴 Slower      | 🟠 Yes, sensitive |

✅ Final Recommendation:
🎯 If you want the best performance and you're okay with longer training and tuning, go with Gradient Boosting.

🛡️ If you want strong performance with low risk of overfitting and easier setup, choose Random Forest.

🧾 Use Decision Tree for explainability, learning, or as a starting point.



## Submission