# Customer Churn Prediction

Predicts which subscription customers are likely to cancel and turns the predictions into a retention plan.
Built on IBM's public *Telco Customer Churn* sample dataset as a practice case; the same approach applies to the
renewal data of a subscription software business.

## Problem
Subscription businesses lose revenue when customers cancel, and keeping a customer is usually cheaper than winning
one back. The goal is to identify who is most likely to leave and what could be done about it.

## Data
- IBM Telco Customer Churn: 7,043 customers, 21 columns, target `Churn` (Yes/No)
- Source: https://github.com/IBM/telco-customer-churn-on-icp4d
- Overall churn rate: 26.5% (1,869 customers)

## Approach
1. Cleaned the data: converted `TotalCharges` to numeric (11 blank values belong to new customers with zero tenure),
   encoded the target and dropped the customer ID.
2. Explored churn by contract type, tenure, monthly charges, internet service and payment method.
3. Split the data 80/20 (stratified) and built a preprocessing pipeline (scaling and one-hot encoding).
4. Trained logistic regression and random forest models with balanced class weights.
5. Evaluated both on the test set (ROC-AUC, precision, recall, F1, confusion matrix) and with 5-fold cross-validation.
6. Analysed churn drivers and ranked customers into five risk groups to measure targeting value.

## Results
| Model | ROC-AUC (test) | Precision | Recall | F1 | ROC-AUC (5-fold CV) |
|---|---|---|---|---|---|
| Logistic Regression | 0.842 | 0.504 | 0.783 | 0.614 | 0.845 ± 0.013 |
| Random Forest | 0.842 | 0.552 | 0.749 | 0.636 | 0.844 ± 0.012 |

- Both models rank customers equally well, so **logistic regression is preferred**: simpler and interpretable.
- The 20% of customers with the highest predicted risk contain **50% of all churners** (2.5x lift).

![ROC curves](images/06_roc_curves.png)
![Risk groups](images/09_risk_groups.png)

## Key findings
- Month-to-month contracts churn at 42.7% (one-year: 11.3%, two-year: 2.8%) and make up 88.6% of all churners.
- Churn is highest in the first six months of tenure (54.3%); churned customers have a median tenure of 10 months
  against 38 for those who stayed.
- Fiber optic customers (41.9%) and electronic check payers (45.3%) churn far more than average.
- Churned customers pay more per month (median 79.65 vs 64.43).
- The highest-risk group is entirely month-to-month, 90% fiber optic, 75% electronic check, with a median tenure of 6 months.

## Recommendations
1. Target new month-to-month customers first.
2. Offer incentives to move them to one-year or two-year contracts.
3. Strengthen onboarding in the first six months.
4. Review the electronic check experience and the fiber optic service.
5. Promote online security and tech support add-ons.

## Limitations
- Single snapshot from a telecom company; patterns will differ for a software business.
- The analysis shows association, not cause; an A/B test would be needed to prove an offer reduces churn.
- No hyperparameter tuning; decision threshold fixed at 0.5.

## How to run
1. Open `customer_churn_prediction.ipynb` in Google Colab or Jupyter.
2. Run all cells. The dataset loads from a public URL.
3. Charts are saved to the `images/` folder.

## Next steps
- Tune both models and test gradient boosting.
- Choose the decision threshold from business costs.
- Run a controlled retention experiment on the highest-risk group.

## Author
Tamanna Akter Swarna
