# Promotion Effectiveness at a Fashion Retail Chain

---

## B1. Problem Formulation

### (a) ML Problem Definition 

- **Target Variable:**  
  `items_sold` (number of items sold per store per month)

- **Candidate Input Features:**  
  - Store attributes: store size, location type  
  - Customer factors: footfall, demographics  
  - External factors: competition density  
  - Promotion details: promotion type  
  - Temporal features: month, seasonality, festival flag, weekend proportion  

- **Type of ML Problem:**  
  **Supervised Regression**

- **Justification:**  
  Since the target (`items_sold`) is a continuous numerical value, the problem is to predict a quantity rather than classify categories. Hence, regression is appropriate.

---

### (b) Why Items Sold is Better than Revenue

- Revenue is affected by:
  - Pricing differences  
  - Product mix  
  - Discount depth  

- Items sold:
  - Directly reflects customer response to promotions  
  - Removes pricing-related noise  
  - Enables fair comparison across promotions  

- **Broader Principle:**  
  The target variable should closely align with the business objective and minimize the influence of confounding factors.

---

### (c) Alternative Modelling Strategy

- **Approach:**  
  Segmented or hierarchical modelling:
  - Separate models for urban, semi-urban, and rural stores  
  - Or a multi-level model with store-specific effects  

- **Justification:**  
  Different store types respond differently to promotions, and a global model may fail to capture these variations.

---

## B2. Data and EDA Strategy

### (a) Data Joining and Aggregation

- **Joins:**
  - Transactions + Store attributes (on `store_id`)  
  - Transactions + Promotion details  
  - Transactions + Calendar (on date)

- **Final Dataset Grain:**  
  One row = **one store × one month**

- **Aggregations:**
  - Total `items_sold` (target)  
  - Monthly footfall  
  - Promotion type applied  
  - Proportion of weekends/festivals  
  - Optional: transaction count, avg basket size  

---

### (b) EDA Strategy

1. **Distribution of Items Sold**
   - Check skewness and outliers  
   - Apply log transform if needed  

2. **Boxplot: Items Sold vs Promotion Type**
   - Compare effectiveness of promotions  

3. **Time Series Plot**
   - Identify seasonality and trends  

4. **Correlation Heatmap**
   - Detect relationships and multicollinearity  

5. **Scatter Plot: Footfall vs Items Sold**
   - Understand conversion behavior  

---

### (c) Handling Promotion Imbalance 

- **Problem:**  
  80% transactions have no promotion → model bias toward baseline  

- **Solutions:**  
  - Add binary feature: promotion vs no promotion  
  - Use sampling or weighting techniques  
  - Model promotion uplift explicitly  

---

## B3. Model Evaluation and Deployment

### (a) Train-Test Split & Metrics

- **Split Strategy:**  
  Time-based split:
  - Train: first ~2–2.5 years  
  - Test: last 6–12 months  

- **Why Not Random Split:**  
  Causes data leakage and unrealistic evaluation  

- **Evaluation Metrics:**
  - **RMSE:** penalizes large errors  
  - **MAE:** average prediction error (interpretable)  
  - **R²:** variance explained  

---

### (b) Explaining Model Recommendations

- Use **feature importance techniques** (e.g., SHAP)

- **Analysis:**
  - December → Loyalty Points Bonus  
  - March → Flat Discount  

- **Explanation:**
  - December: festive + repeat customers → loyalty works better  
  - March: price-sensitive demand → discounts perform better  

- **Communication:**
  - Visualize feature contributions  
  - Explain in business terms (seasonality and customer intent)

---

### (c) Deployment Process

1. **Save Model:**  
   Use `joblib` or `pickle`

2. **Prepare Monthly Data:**  
   - Update store, calendar, and promotion inputs  
   - Apply same preprocessing pipeline  

3. **Prediction:**
   - Simulate all promotion options per store  
   - Select promotion with highest predicted sales  

4. **Automation:**  
   - Schedule monthly batch job  

5. **Monitoring:**
   - Track prediction vs actual  
   - Monitor error metrics  
   - Detect data drift  

6. **Retraining Trigger:**
   - Performance degradation  
   - Changing patterns or new promotions  