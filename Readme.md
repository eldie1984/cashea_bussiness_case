
# Assumptions and Methodological Considerations

The following technical and operational assumptions were established for the construction of
the model and the business analysis:

1. **Definition of FPD (First Payment Default)** : The target variable was defined as any
    loan whose first payment (1st Installment) was delayed by more than 3 days ( _Delay_
    _for 1st Installment Payment - Days_ > 3). This threshold was selected because it is the
    earliest and most sensitive risk indicator in a BNPL model.
2. **Handling Missing Values** : Records with null values were identified in critical
    columns such as _User Credit Score_ and _Delay for 1st Installment_. To ensure the
    integrity of the subsequent distributions of the Bayesian model, a listwise deletion of
    these specific records was performed, ensuring that the analysis is based solely on
    complete and verifiable evidence.
3. **Variable Normalization** : Column names were standardized (removal of spaces and
    special characters) to ensure compatibility with Bambi/PyMC formula syntax,
    avoiding arithmetic interpretation errors in predictors.
4. **Probabilistic Inference (94% HDI)** : A Highest Density Interval (HDI) of 94% was
    assumed as the standard for statistical certainty. The business insights presented are
    derived from coefficients whose intervals do not overlap the zero value, ensuring that
    the impact of the _Product Vertical variable_ is statistically significant and not due to
    chance.
5. **Sample Representativeness** : Due to the volume of the dataset (~400k rows), the
    training of the final model was performed on a representative random sample to
    optimize the convergence times of the MCMC strings without sacrificing the stability
    of the estimated parameters.

# Business Impact Analysis

```
● Risk Cost Optimization (Provisions) : The model identified with 94% certainty
(HDI) that the Health and Pharmacy verticals have a significantly higher than
average probability of FPD.
○ Recommendation: Implement a dynamic down payment scheme for these
categories, moving from the standard to 30-40%, reducing capital exposure in
high-volatility segments.
● Efficiency in Credit Admission : When analyzing the User Credit coefficient Score ,
it was observed that its predictive capacity varies according to the Product Type
Vertical.
○ Insight: In verticals like Home , the score is a robust predictor, but in
Technology , the range is wider, indicating that risk is more closely tied to the
product than to the traditional credit profile. This suggests the need for
category-specific decision rules in the loan origination engine.
● Proactive Reduction of NPL (Non-Performing Loans) : Use the model output (
FPD probability ) as an early collection score.
```

```
○ Strategy : Loans identified with a high probability of initial default can be
referred to a flow of preventative reminders (SMS/Push) 48 hours before the
first installment is due, optimizing the operational capacity of the Collections
team.
● Growth Strategy (GMV) : The model validated that low-risk verticals, such as
Entertainment and Services , have a high certainty of payment.
○ Opportunity : It is recommended to aggressively increase credit limits for
recurring users in these categories ("Follow-up orders"), allowing for an
increase in total GMV without deteriorating portfolio quality.
```
# Summary

For this challenge, I selected First Payment Default (FPD) modeling using a Bayesian
Logistic Regression approach due to its unique ability to quantify uncertainty in a dynamic
credit environment. I chose this problem because FPD is the most critical early indicator of
the health of a BNPL portfolio, allowing intervention before a loan becomes non-performing.
Through this model, I identified significant variations in structural risk by Product Vertical,
enabling me to propose dynamic Down Payment strategies and credit limit optimization
based on probabilistic evidence with a 94% confidence level (HDI).

# Next Steps:

These are the features I would have been interested in testing as iterations of the model, but
given the long training time, I didn't want to add them to the model.

**1. Features of "Customer Engagement" (Skin in the Game)**
● Down Payment Ratio : Down Payment Amount / GMV.
    ○ Insight: Customers who pay a higher percentage upfront tend to have a lower
       default rate. This is a strong predictor of payment intent.
● Loan-to-GMV : _Loan Amount_ / _GMV_. Indicates how much the customer is financing
    relative to the total value.
**2. Features of "Payment Behavior" (Repayment Dynamics)**
● Payment Acceleration/Deceleration : The difference in days between Delay for
    1st Installment and Delay for 2nd Installment.
       ○ Insight: If the delay between installments increases, it is an early warning sign
          that the customer's cash flow is deteriorating.
● Early Payment Flag : If the Completion Date is before the Due Date.
    ○ Insight: Identify "Premium" customers who quickly unlock their credit line to
       make repeat purchases.
**3. Features of "History and Recurrence" (Customer Loyalty)**
● Customer Vintage Age : Difference in days between the Origination Date of the
    current loan and the first time the Customer Id appeared in the system.


○ Insight: Long-standing customers have a drastically lower risk (NPL) than new
customers.
● Previous Order Count : Count of previous Unique Loan IDs for each Customer ID.

**4. Features of "Risk Concentration" (Network & Context)**
● Merchant Risk Score : The average historical FPD or DPD for each Merchant Id.
    ○ Insight: Some businesses attract higher-risk or fraudulent customers. This is a
       key hierarchical feature for the Bayesian model.
● Vertical Volatility : The average Amount in USD per Product Type Vertical.
**5. Temporary Features (Seasonality)**
● Is Month End / Is Payday : Binary variable according to the Origination Date.
    ○ Insight: At BNPL, loans taken out just before payday (mid-month or end of
       month) tend to have different payment behaviors.


