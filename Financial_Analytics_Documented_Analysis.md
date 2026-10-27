# Financial Analytics Dashboard — Documented Business Analysis

## 1. Executive summary

This analysis explores customer financial behaviour using a dataset of **6,000 monthly records for 750 distinct customers**, with eight monthly records per customer. It focuses on income, monthly balance, delayed payments, credit utilisation, age, credit-score categories, credit-limit changes, and loan types.

The main signals in the cleaned analysis are:

- Average annual income is approximately **53,339 dataset currency units**.
- Average monthly balance is approximately **414 dataset currency units**.
- Average number of delayed payments is approximately **13.31**; this is a count, not the number of days late.
- Average credit-utilisation ratio is approximately **32.28** on the source's ratio-like scale. Confirm whether the source stores this as a percentage or another scale before formatting it in Power BI.
- Younger customers show higher average delayed-payment counts, more loans on average, and higher credit-inquiry activity than the oldest age group.
- The Pearson correlation between age and `Changed_Credit_Limit` is **-0.2224**, a weak-to-moderate negative association in this cleaned sample. The field's exact business meaning must be confirmed before calling it an actual increase/decrease in a credit limit.

These findings are **descriptive signals, not proof of cause**. They can guide further investigation and customer-support experiments, but should not be used alone for lending, pricing, or credit-approval decisions.

## 2. Dataset and method

### Dataset grain

The source contains 6,000 monthly records representing 750 customers over eight monthly periods. The same customer appears repeatedly, so row counts must not be interpreted as unique-customer counts.

### Cleaning and analysis choices

- Malformed numeric strings and known placeholders were converted to missing values.
- Clearly implausible values were set to missing using documented thresholds, including annual income above 1,000,000, more than 20 loans/cards/accounts, more than 50 delayed payments, and more than 50 credit inquiries. These thresholds should be validated against source documentation.
- Invalid ages outside 14–100 were excluded from age-based analysis; 173 rows were flagged for invalid age.
- Placeholder categories were treated as missing. The source has missing values in fields such as credit mix, payment behaviour, occupation, minimum-payment status, and monthly investment.
- Customer-level comparisons use one record per customer where possible to avoid inflating customer counts with repeated monthly rows.
- For the exploratory LTV calculation, the numeric credit-score mapping was Poor = 0, Standard = 1.5, Good = 3. The source brief did not define Standard = 1.5, so this is an assumption requiring confirmation.
- The LTV calculation uses average days of delay as the delay input. The formula, units, and currency assumptions must be confirmed with the original project brief before publication.
- The brief does not define a promotion for LTV below ₹50,000; no offer or approval should be inferred from the threshold.

## 3. What is happening?

### 3.1 Overall financial indicators

| Indicator | Cleaned analysis value | Interpretation |
|---|---:|---|
| Average annual income | 53,339.16 | Average income in the dataset's currency units; confirm currency/source units |
| Average monthly balance | 414.35 | Relatively small compared with average annual income, but the units and balance definition need validation |
| Average delayed-payment count | 13.31 | Indicates delayed-payment behaviour is present; verify the period represented by this count |
| Average credit utilisation | 32.28 | Confirm whether the source scale is already percentage-like before applying percentage formatting |

The balance and income measures may use different time periods or definitions. They should not be directly compared as if they were the same kind of financial measure.

### 3.2 Differences by age group

| Age group | Customers | Avg. annual income | Avg. monthly balance | Avg. delayed-payment count | Avg. days delayed | Avg. credit inquiries | Avg. loans |
|---|---:|---:|---:|---:|---:|---:|---:|
| Teen (14–18) | 81 | 44,955.99 | 345.69 | 15.89 | 28.78 | 8.11 | 4.77 |
| Young Adult (19–24) | 148 | 52,819.53 | 410.06 | 13.75 | 21.92 | 6.19 | 3.70 |
| Old Adult (25–34) | 233 | 50,680.58 | 407.04 | 13.83 | 22.11 | 6.00 | 3.63 |
| Old1 (35–45) | 251 | 55,058.08 | 414.66 | 13.03 | 21.10 | 5.56 | 3.41 |
| Old2 (46+) | 101 | 62,360.35 | 487.04 | 10.09 | 13.47 | 2.90 | 1.84 |

**Observed pattern:** the 46+ group has the highest average income and monthly balance, and the lowest average delayed-payment count, days delayed, credit inquiries, and loans. The Teen group has the lowest average income and balance and the highest values for delayed-payment count, days delayed, inquiries, and loans.

**Why it might be happening (hypotheses to test):**
- Younger segments may have shorter credit histories or less experience managing several credit products.
- Lower balances could leave less buffer for irregular expenses or payment dates.
- More loan products and credit inquiries may reflect greater credit-seeking activity, but the dataset alone does not establish why customers applied.
- Age may be standing in for other factors, such as income stability, employment, credit history length, product mix, or data-quality differences.

**Business implication:** younger customers could be a useful segment for voluntary payment reminders, financial education, and affordability support. Do not assume every younger customer is high risk; validate results at customer level and check for confounding factors.

### 3.3 Age and credit-limit changes

The cleaned analysis reports a Pearson correlation of **-0.2224** between age and `Changed_Credit_Limit`.

**What it means:** higher ages are associated with lower values of this field in the sample. The association is not strong enough to support a causal conclusion.

**Important limitation:** `Changed_Credit_Limit` is not clearly defined as an absolute increase, decrease, or percentage. The business must confirm its definition, units, and time period. Until then, label the visual “Age vs. Changed Credit Limit field” rather than claiming it measures credit-limit increases.

**Next analysis:** plot the field by age and age group; show sample sizes and distribution, not only averages. Check outliers and repeat the correlation using validated records. If the field is confirmed as a change amount, split positive, negative, and zero changes and investigate other explanatory variables.

### 3.4 Credit score patterns by age

In the customer-level summary, the 46+ group has the highest average numeric credit-score proxy (1.80), while the Teen group has the lowest (0.94). The proxy uses Poor = 0, Standard = 1.5, Good = 3, so it is a simplified ordinal score, not a real credit score.

The monthly category records also show that Poor scores appear in every age group, while the 46+ group has fewer Poor records relative to its other categories. Use percentages within each age group for fair comparisons, because groups have different numbers of customers and monthly records.

**Why it might be happening:** credit history length, debt load, payment consistency, and credit-product mix could contribute. These are hypotheses; the current analysis does not isolate their effects.

**Business next step:** compare credit-score category percentages with delayed payments, utilisation, and number of active loans. Validate the numeric mapping before calculating averages from categories.

### 3.5 Credit mix and payment behaviour

The cleaned cross-tabulation shows different payment-behaviour patterns across credit-mix categories. For example, `Low_spent_Small_value_payments` accounts for about **31.8%** of records in the Bad credit-mix category, compared with about **18.0%** in the Good category. Missing payment-behaviour values also exist, so these percentages describe observed records only.

This pattern may indicate that spending/payment behaviour differs by credit mix, but it does not prove that a particular behaviour causes a credit-mix rating. It may also be affected by missing values and repeated monthly records.

**Business next step:** compare within-category percentages, keep missing data visible, and test whether the pattern remains when looking at unique customers or across multiple months. Avoid treating the labels as financial advice without knowing exactly how the source defines them.

### 3.6 Loan-type popularity

Distinct-customer counts by loan type are relatively close in this sample:

| Loan type | Distinct customers with this loan type |
|---|---:|
| Home Equity Loan | 238 |
| Personal Loan | 238 |
| Mortgage Loan | 234 |
| Student Loan | 231 |
| Credit-Builder Loan | 230 |
| Debt Consolidation Loan | 228 |
| Auto Loan | 223 |
| Payday Loan | 209 |

Home Equity and Personal Loans have the highest counts (238 each); Payday Loans have the lowest (209). Customers may hold multiple loan types, so these counts overlap and should not be summed to estimate the total number of customers.

**Business next step:** segment loan types by age, credit mix, delayed-payment behaviour, and income. Investigate affordability and repayment patterns by product rather than assuming popularity means profitability or suitability.

## 4. Why the patterns might be occurring — working hypotheses

The dashboard can help explore, but cannot prove, the following possible explanations:

1. **Credit-history maturity:** older customers may have longer credit histories and more stable repayment routines.
2. **Affordability and liquidity:** customers with lower balances may have less short-term flexibility when bills fall due.
3. **Credit-product burden:** more concurrent loans or cards may increase payment complexity and the chance of missed due dates.
4. **Credit-seeking behaviour:** higher inquiry counts may reflect active applications, shopping for credit, or other processes; the reason is unknown.
5. **Data limitations:** missing values, repeated monthly rows, malformed source values, and unverified field definitions can distort apparent patterns.

These should be tested with validated data, not presented as established facts.

## 5. What the business could do next

### Priority 1 — Validate the data and metric definitions

- Confirm currency, annual-income units, monthly-balance units, and the credit-utilisation scale.
- Confirm whether delayed-payment count and days delayed are separate measures and what period each represents.
- Define `Changed_Credit_Limit` precisely.
- Confirm the credit-score mapping, especially the Standard category, and the LTV formula.
- Track missing-value rates and document the outlier thresholds.
- Check results with unique-customer counts as well as monthly-record trends.

### Priority 2 — Pilot supportive interventions

- Test opt-in payment reminders and clear due-date notifications for customers with repeated delays.
- Offer budgeting or credit-education content to customers who choose to receive it.
- Provide a simple account view showing upcoming payments and total obligations across products, where permitted.
- Make support accessible across age groups rather than using age alone to label risk.

Evaluate pilots using a holdout or comparison group, and measure changes in on-time payment rate, repeat delays, customer complaints, and opt-outs.

### Priority 3 — Investigate affordability and credit exposure

- Review the relationship among number of loans/cards, credit utilisation, monthly balance, and delayed payments.
- Identify combinations of indicators that merit a human review, not automatic rejection.
- Use transparent, policy-approved affordability checks and comply with applicable privacy, fairness, and lending rules.
- Do not infer loan eligibility or recommend a product from this dashboard alone.

### Priority 4 — Improve reporting and monitoring

- Add a monthly trend view to detect whether balances, utilisation, and delays are improving or worsening.
- Show both customer counts and percentages, with missing values reported.
- Add tooltips defining each metric and the period covered.
- Monitor segment changes over time and refresh the analysis after data definitions are validated.

## 6. Suggested Power BI dashboard narrative

**What is happening?** Younger age groups in this sample show higher average delayed-payment measures, more credit inquiries, and more loans than the 46+ group. The oldest group has higher average income and monthly balance.

**Why might it be happening?** Possible explanations include differences in credit-history length, liquidity, credit-product burden, and financial routines. The data does not establish causation, and several metric definitions require confirmation.

**What should the business do next?** Validate the fields and cleaning assumptions, investigate loan burden and repayment patterns, and pilot supportive payment reminders or financial education. Measure outcomes before scaling. Do not use age or the exploratory LTV proxy as a stand-alone lending decision.

## 7. Limitations and responsible use

- This is exploratory analysis of a supplied dataset, not a causal study.
- The age groups and cleaning thresholds are analytical choices and should be reviewed.
- The dataset contains missing and malformed values.
- Monthly records repeat customers; use distinct-customer measures where the question is customer-level.
- Numeric mapping of credit-score categories is an assumption and should not be treated as a validated scoring model.
- The LTV score is an exploratory proxy based on an assumed formula; it is not a validated lending or lifetime-value model.
- The report does not establish that any customer is eligible for a loan, credit-limit increase, discount, or promotion.
- Validate the findings on more recent data and with business owners before taking action.
