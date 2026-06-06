Customer Churn Prediction
─────────────────────────────────────────────────────────────────────────────

PROBLEM─────────────────────────────────────────────────────────────────────────────
Companies lose 20–30% of customers silently. Nobody knows who is about to 
leave until they already have. Retention teams are reactive, 
not proactive — and by the time they act, it's too late.


DATASET─────────────────────────────────────────────────────────────────────────────
Online Retail Dataset (UCI / Kaggle)
541,909 transactions · Dec 2010 – Dec 2011 · 38 countries
Columns: InvoiceNo, StockCode, Description, Quantity, InvoiceDate,
         UnitPrice, CustomerID, Country


KEY INSIGHTS─────────────────────────────────────────────────────────────────────────────

EDA (E_commerce_EDA.ipynb)

  · After cleaning (dropping anonymous buyers, cancellations, bad values),
    397,884 rows remained across 4,338 trackable customers.

  · Revenue peaked in November 2011 — Christmas shopping season — then
    dropped sharply as the dataset ended in December.

  · The United Kingdom drives over 80% of all revenue. Germany and France
    are distant second and third.

  · Thursday is the busiest order day. Sunday has almost zero activity.

  · Peak order hours are 10am–2pm on weekdays, visible clearly in the
    day × hour heatmap.

  · Customer revenue is heavily skewed — most customers spend under £500
    total, while a small number exceed £10,000.

  · New customer acquisition was strongest in the second half of 2011,
    but returning customers consistently made up the majority of monthly
    orders, which is a healthy sign.

RFM Segmentation (RFM_Segmentation.ipynb)

  · Every customer was scored 1–5 on Recency, Frequency, and Monetary
    value, then assigned to one of nine named segments.

  · Champions (highest R, F, M) represent roughly 10% of customers
    but generate approximately 45% of total revenue — a classic 80/20
    pattern in retail.

  · About To Sleep is the largest segment by headcount. These customers
    have not purchased recently and buy infrequently — the highest volume
    churn risk in the business.

  · At Risk customers previously purchased often but have gone quiet
    recently. They represent meaningful revenue and warrant urgent
    win-back campaigns.

  · The RFM heatmap confirmed that most customers cluster at low R and
    low F scores, meaning the majority of the base is disengaged.

  · Can't Lose Them (low recency, high frequency) is small in size but
    high in historical value — losing these customers would be
    disproportionately damaging.

Churn Model (RFM__churn_model.ipynb)

  · Churn was defined as no purchase in the last 90 days, labelling
    1,449 of 4,338 customers as churned (33.4% churn rate).

  · Ten features were engineered from purchase history: recency, order
    count, revenue, average order value, product variety, tenure, and
    three derived ratios (orders per month, revenue per order, items
    per order).

  · Random Forest outperformed Logistic Regression across all metrics.
    Both models were evaluated on a held-out 20% test set.

  · recency_days was the dominant feature by a large margin, which is
    expected given that it directly defines the churn label. In a
    production setting, the model would be trained on historical data
    to predict future churn rather than current status.

  · The churn probability distribution showed clear separation —
    retained customers clustered near 0 and churned customers near 1
    — indicating the model is well calibrated.

  · All 4,338 customers were scored with a churn probability and
    assigned a risk tier: High, Medium, or Low.


APPROACH─────────────────────────────────────────────────────────────────────────────
Transaction-level data was cleaned, aggregated to one row per customer,
and used to build RFM scores and a binary churn label (no purchase in
90 days). Two classification models — Logistic Regression as a baseline
and Random Forest as the primary model — were trained on ten behavioural
features using an 80/20 stratified split. Model performance was evaluated
using precision, recall, F1, AUC-ROC, and a confusion matrix. Every
customer was then scored with a churn probability and bucketed into a
risk tier for downstream action.


RESULTS─────────────────────────────────────────────────────────────────────────────
The Random Forest model identified 33.4% of customers (1,449) as churned
and surfaced a High Risk group whose combined historical revenue represents
a quantifiable retention opportunity. The RFM segmentation revealed that
Champions — roughly 10% of the customer base — generate ~45% of revenue,
giving the business a clear prioritisation framework for retention spend.


LEARNING─────────────────────────────────────────────────────────────────────────────
The most important technical lesson was understanding why a perfect model
score (AUC = 1.0) is a red flag rather than a success — it signals data
leakage, where the answer is hidden inside the features. Here, recency_days
defines churn, so the model trivially learns the rule. In a real production
system, the model would be trained on data from 6+ months ago to predict
which current customers will churn in the next 30–60 days, using past
behaviour rather than current status as the label.

What would be done differently: separate the feature window from the label
window to build a genuine predictive model, and experiment with a
threshold below 0.5 to maximise recall (catching more real churners) at
an acceptable precision cost.


TECHNOLOGIES─────────────────────────────────────────────────────────────────────────────

  pandas          Data loading, cleaning, aggregation, feature engineering
  numpy           Numerical operations and log transformations
  scikit-learn    Model training (Logistic Regression, Random Forest),
                  pipelines, train/test split, evaluation metrics
  Plotly          All interactive charts — chosen over matplotlib because
                  charts render natively in Google Colab with hover,
                  zoom, and filter without any extra setup
  Google Colab    Development environment — free GPU/CPU, Drive
                  integration for persistent file storage across sessions


PROJECT STRUCTURE─────────────────────────────────────────────────────────────────────────────

  E_commerce_EDA.ipynb        Exploratory analysis — 8 interactive charts,
                              customer summary table
  RFM_Segmentation.ipynb      RFM scoring and segmentation — 7 interactive
                              charts, segment summary
  RFM__churn_model.ipynb      Churn model — feature engineering, training,
                              evaluation, risk scoring


HOW TO RUN─────────────────────────────────────────────────────────────────────────────
1. Open Google Colab (colab.research.google.com)
2. Upload each notebook via File → Upload notebook
3. Upload the dataset (Online Retail.csv or .xlsx) when prompted
4. Run notebooks in order: EDA → RFM Segmentation → Churn Model
5. Each notebook saves its output CSV to the session before the next
   notebook reads it

─────────────────────────────────────────────────────────────────────────────
