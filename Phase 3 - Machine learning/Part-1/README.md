# Ship Mode Classification — Order-Level Machine Learning

## Overview

This project builds a machine learning model to classify the **shipping mode (`ShipModeKey`)** of an order using historical sales, customer, geographic, product, and order-level information.

The main objective was **not simply to maximize accuracy**, but to build a more methodologically sound classification pipeline while minimizing the risk of **data leakage**.

A key design decision was to aggregate the data to the **order level before splitting the dataset into training and validation sets**. This ensures that different line items belonging to the same order cannot appear in both datasets.

---

## Dataset

The original dataset contains several fact and dimension tables:

* `FactSales.csv`
* `DimCustomer.csv`
* `DimGeography.csv`
* `DimProduct.csv`
* `DimDate.csv`
* `DimOrderPriority.csv`

After joining these tables, the dataset contained:

* **49,670 sales rows**
* **63 columns**

The data was then aggregated from sales-line level to **order level**, resulting in:

* **25,033 unique orders**
* **20 model features**
* **4 shipping-mode classes**

The target distribution was:

| ShipModeKey | Orders |
| ----------- | -----: |
| 1           |  3,718 |
| 2           |  1,311 |
| 3           |  4,993 |
| 4           | 15,011 |

This relatively imbalanced target distribution is an important limitation of the problem. In particular, class 4 represents a substantially larger portion of the data than class 2.

---

## Methodology

### 1. Data Integration

The fact and dimension tables were joined using their corresponding keys.

The resulting dataset was transformed into an order-level dataset by grouping all sales lines belonging to the same `OrderID`.

Before aggregation, consistency checks were performed to verify that attributes expected to remain constant within an order were actually consistent.

For example:

* Customer
* Geography
* Order date
* Segment
* Region
* Market
* Order priority
* Season

No inconsistent orders were found in these checks.

---

### 2. Feature Engineering

Several order-level features were created to provide additional information about the economics and shipping characteristics of each order:

* `ShippingCostPerSales`
* `ShippingCostPerItem`
* `SalesPerItem`
* `ProfitMargin`
* `DiscountAmount`

Additional temporal features included:

* `OrderMonth`
* `OrderDayOfWeek`
* `OrderSeason`
* `IsMonthBoundary`

The final feature set contained 20 features.

---

## Preventing Data Leakage

One of the main goals of this project was to **avoid data leakage**, even if doing so potentially resulted in lower accuracy.

The train/validation split was performed **after aggregating all line items to the order level**:

```python
X_train, X_val, y_train, y_val = train_test_split(
    X,
    y,
    test_size=0.2,
    random_state=42,
    stratify=y
)
```

This means an `OrderID` cannot have some of its line items in the training set and other line items in the validation set.

This is important because splitting the original line-level dataset before aggregation could allow information from the same order to appear in both datasets and produce overly optimistic results.

The final split contained:

* **20,026 training orders**
* **5,007 validation orders**

We also deliberately excluded `ShipDateKey` because shipping date is only known **after the shipping process has occurred**, making it inappropriate for a model intended to predict the shipping mode beforehand.

---

## Model

The classifier used in this project is **CatBoostClassifier** with a multiclass objective.

Main parameters:

```text
Iterations:      300
Learning rate:   0.05
Depth:           7
L2 leaf reg:     5
Random state:    42
Loss function:   MultiClass
Evaluation:      TotalF1
```

Class weights were also applied to reduce the effect of the imbalanced target distribution.

---

## Results

Two feature configurations were evaluated.

| Experiment          |   Accuracy |   Macro F1 | Weighted F1 |
| ------------------- | ---------: | ---------: | ----------: |
| Full feature set    | **0.5874** | **0.4441** |  **0.6096** |
| Reduced feature set |     0.5734 |     0.4318 |      0.5973 |

The full feature set performed better and was selected as the final model.

### Final Performance

**Accuracy:** 58.74%

**Macro F1:** 44.41%

**Weighted F1:** 60.96%

**Macro ROC-AUC:** 77.83%

**Weighted ROC-AUC:** 79.27%
The classification report shows that the model performs substantially better on the dominant class (class 4) than on the minority classes, which is consistent with the highly imbalanced target distribution.

---

## Why Isn't the Accuracy Higher?

A major consideration in interpreting these results is that **the goal of the project was not to artificially maximize accuracy at the cost of methodological validity**.

With more aggressive feature engineering, especially features that could indirectly encode the target or information unavailable at prediction time, it would be possible to obtain higher apparent performance. However, such a model could suffer from **data leakage** and would not provide a reliable estimate of real-world predictive performance.

In other words:

> **We prioritized a leakage-free evaluation over achieving the highest possible accuracy.**

Another important limitation is the **predictive power of the available data itself**.

Although the dataset contains around 49,670 sales rows and 25,033 orders, the effective information available for distinguishing the four shipping modes is relatively limited. A large dataset does not necessarily imply a highly informative classification problem.

The available features describe aspects such as sales, quantity, profit, discount, geography, product category, order priority, and seasonality, but these variables do not necessarily contain enough direct information to uniquely determine the shipping mode.

Therefore, increasing the number of rows alone would not necessarily lead to a proportional increase in classification performance.

---

## Important Data Limitation

The most significant limitation is that some potentially useful variables may also be closely related to the shipping process itself.

For example, `ShippingCost` was retained in the model **only under the assumption that it is available at the time the customer selects the shipping option**.

If `ShippingCost` is calculated or becomes available only after the shipping mode has already been selected, it should also be removed from the feature set to maintain a strictly leakage-free prediction scenario.

This distinction is important when interpreting the reported performance.

---

## Output

The model generates order-level predictions containing:

* Actual shipping mode
* Predicted shipping mode
* Probability for each class
* Prediction correctness

The validation output contains **5,007 order-level predictions**.

---

## Confusion Matrix

The validation confusion matrix was:

| Actual / Predicted |   1 |   2 |   3 |    4 |
| ------------------ | --: | --: | --: | ---: |
| **1**              | 333 | 141 | 184 |   86 |
| **2**              | 101 |  69 |  72 |   20 |
| **3**              | 141 | 157 | 379 |  322 |
| **4**              |  68 |  58 | 716 | 2160 |

The model correctly identifies a substantial number of class-4 orders, while classes 2 and 3 remain considerably more difficult to distinguish.

---

## Project Structure

A suggested project structure is:

```text
project/
│
├── data/
│   ├── FactSales.csv
│   ├── DimCustomer.csv
│   ├── DimGeography.csv
│   ├── DimProduct.csv
│   ├── DimDate.csv
│   └── DimOrderPriority.csv
│
├── notebooks/
│   └── ship_mode_classification.ipynb
│
├── outputs/
│   └── ShipMode_OrderLevel_Predictions.csv
│
└── README.md
```

---

## Technologies

* Python
* Pandas
* NumPy
* Scikit-learn
* CatBoost
* Matplotlib

---

## Key Takeaways

1. The problem was formulated as an **order-level multiclass classification task**.
2. The data was aggregated **before** train/validation splitting to prevent order-level leakage.
3. `ShipDateKey` was excluded because it is known after shipping.
4. Class imbalance was addressed using class weights.
5. The best model achieved approximately **58.7% accuracy** and **44.4% Macro-F1**.
6. The model achieved a considerably stronger **77.8% Macro ROC-AUC**, indicating useful class-discrimination capability despite the moderate classification accuracy.
7. The relatively modest accuracy is partly attributable to the limited predictive information contained in the available features.
8. The project intentionally prioritizes **honest, leakage-aware evaluation over artificially inflated accuracy**.

---

## Conclusion

This project demonstrates a practical approach to multiclass classification on transactional data while explicitly considering **data leakage, class imbalance, feature availability, and dataset limitations**.

The results should therefore be interpreted as a realistic baseline for the available information rather than as an attempt to maximize a single performance metric.

The main objective was to answer a more meaningful question:

**How well can shipping mode be predicted using information that is legitimately available, without allowing leakage from the same order or from future shipping information?**

Under this constraint, the model provides a reasonable baseline while also highlighting that improving performance will likely require **more predictive features**, rather than simply increasing the volume of existing data.
