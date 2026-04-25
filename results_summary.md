Problem Statement: I am helping a risk team identify companies that may go bankrupt. This will be done through a binary classification model with an imbalanced dataset. So for the model, missing a bankrupt company weighs more than incorrectly flagging a healthy company.

Dataset Summary:
    - Target Column: Bankrupt?
    - Positive class: 1, meaning company went bankrupt
    - Shape: 6,819 rows and 96 columns

Class Imbalance Statement:
    - The dataset is imbalanced since it has 220 bankrupt companies and 6,599 non-bankrupt companies.
    - The positive class makes up only about 3.2% of the overall data.
    - Because of this imbalance, accuracy is misleading since it would predict the negative (majority) class. Instead the analysis should focus on how well the model identifies the positive (minority) class rather than relying on accuracy alone.

Discrimination Metric Explanation
    - PR-AUC was used as the primary discrimination metric because it focuses on performance for the positive class. This is important since our datset is imbalanced, with much more negative than positive class.

Calibration Metric explanation:
    - Brier score was used as the primary calibration metric because it measures the quality of predicted probabilities by looking at the mean squared difference between predicted probabilties and the actual outcome. It penalizes wrong predictions more, which is importaant for this imbalanced dataset since we don't want to miss a bankrupt company.
    - Precision, recall, F2 do not fully describe calibration because

Feature Selection Explanation
    - I selected feature set b using a feature importance model. The features were compared based on gain, how much each feature reduces the error within the model.
    - I decided to keep 20 features.
    - Overall, the performance got better with fewer features compared to most of the other models.
    - The smaller feature set is easier to explain because there are fewer variables to go oveer. It is also clearer which features are influencing predictions.

Experiment Table
    | exp_id | model | feature_set | main_settings | train_pr_auc | val_pr_auc | overfit_gap | val_roc_auc | val_brier | threshold |val_precision | val_recall | val_f2 | selected_finalist | notes |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 1 | Dummy Classifier | Feature Set A | strategy=prior | 0.0323 | 0.0323 | 0.0000 | 0.5000 | 0.0312 | 0.5 | 0.0000 | 0.0000 | 0.0000 | No | Reference benchmark for comparison |
    | 2 | XGBoost Baseline | Feature Set A | default XGBoost settings | 1.0000 | 0.4889 | 0.5111 | 0.9527 | 0.0238 | 0.5 | 0.6250 | 0.3030 | 0.3378 | No | Baseline tree model on all features.|
    | 3 | XGBoost Imbalance Handling | Feature Set A | scale_pos_weight=30, max_depth=3, n_estimators=250 | 0.9934 | 0.5025 | 0.4910 | 0.9605 | 0.0299 | 0.5 | 0.4423 | 0.6970 | 0.6250 | No | Class imbalance handled with scale_pos_weight=30 and shawoller model |
    | 4 | XGBoost RandomizedSearchCV | Feature Set A | subsample=1.0, n_estimators=250, max_depth=2, subsample=0.06, reg_lambda=5 | 0.7979 | 0.5249 | 0.2731 | 0.9643 | 0.0208 | 0.5 | 0.5833 | 0.2121 | 0.2431 | Yes | Best of 5 RandomizedSearchCV candidates scored by roc_auc |
    | 5 | XGBoost Selected Features | Feature Set B | max_depth=1, n_estimators=200, learning_rate=0.1, subsample=0.8 | 0.5955 | 0.4614 | 0.1341 | 0.9558 | 0.0222 | 0.5 | 0.6667 | 0.2424 | 0.2778 | No | Relative baseline using Feature Set B |

Winning Model and Why It Won
    - The model that was tuned lightly with RandomizedSearchCV won.
    - This model won because by checking with the validation set the model had the best: 
        - PR-AUC: 0.5249
        - Brier Score: 0.0208
        - ROC-AUC: 0.9643

Final Test Results
    - PR AUC: 0.4955343839288016
    - ROC AUC: 0.9465564738292012
    - Brier Score: 0.02280398793600754
    - Test Precision: 0.296875
    - Test Recall: 0.5757575757575758
    - Test F2 Score: 0.4846938775510205

Confusion Matrix:
    [[945  45]
    [ 14  19]]

Calibration Result:
    - Test Set Brier Score: 0.0228
    - Calibration plot appears reasonably calibrated since the curve stays close to the perfect calibration line for most of the range.
    - From the curve we can see high predicted risks respond to higher bankruptcy rates. As  predicted probability increases, the fraction of positives increase too.
    - While the model seems a little overconfident for predicted probabilties below 0.5, with the distance the calibration curve is above the perfection calibration line after 0.5, overall the model seems to be more underconfident.
    - The ranking is useful even if calibration is imperfect because it is ranking risk in the right direction. More risks means higher chance of bankruptcy.
    
Feature Importance Result:
    - Top feature: Net Income to Stockholder's Equity
    - Makes sense?: The top feature makes sense. If they are going bankrupt that means they are short on cash. All the money would be put towards paying off debt rather than giving it back to stockholder's.
    - Limitation of interpretation: Feature importance shows the variables the model relied on the most, but it doesn't prove that those variables directly cause bankruptcy. Features that are correlated are included in feature importance which doesn't necessarily help the model.

Overfitting Discussion:
    - The overfit gap for the winning model is 0.2731, so the model is relatively overfit.
    - It doesn't seem to be that bad because the winning model still performs relatively well on the test set. While PR-AUC on test is 0.5, the Brier score is 0.02.
    - The validation PR-AUC of 0.5249 and the test confusion matrix still shows that the model is able to identify bankrupt companies.
    - Feature set B had the lowest overfit gap at 0.1341. Using only 20 selected features reduced overfitting since the model is simpler.

AI Usage section:
    - AI Tool Used: Codex, ChatGPT
    - What It Helped With: writing the reusable evaluation function, determining optimal threshold that achieves best validation f2 score, and helped fix errors. Also wrote setup instructions for readme.md
    - One mistake or bad suggestion you caught: I asked it to put the feature importance plot png in this file before I decided to get rid of it. The plot was made with a training setup ChatGPT created internally and not the one I specified in the notebook.
