# 📊 Amazon Product Financial & Business Analysis

## 📌 Project Overview

The objective of this project is to analyze Amazon product data from a business perspective and uncover actionable insights that can improve pricing strategy, customer satisfaction, and revenue generation.

Instead of performing only traditional Exploratory Data Analysis (EDA), this project answers real business questions that product managers and business analysts commonly face:

- Are discounts actually increasing sales potential?
- Which products are underpriced?
- Which expensive products are failing?
- What factors influence revenue opportunity?
- Where should the company focus to maximize profitability?

---

# 📂 Dataset Features

The dataset contains information about:

- Product Name
- Category
- Actual Price
- Discounted Price
- Discount Percentage
- Customer Rating
- Rating Count
- Revenue Potential *(Engineered Feature)*
- Absolute Discount Amount *(Engineered Feature)*

---

# 🧮 Feature Engineering

## Revenue Potential

Since the dataset does **not contain actual sales quantity or revenue**, a custom business metric was created.

### Formula

Revenue Potential = Discounted Price × Rating Count

### Why?

Rating Count acts as a proxy for customer demand.

By multiplying

- Selling Price
- Customer Demand

we obtain an estimated business opportunity score.

> **Note:** Revenue Potential is **not actual revenue**. It is an estimated metric used to compare products based on popularity and pricing.

Products with

- higher demand
- higher selling prices

naturally achieve larger Revenue Potential values.

---

# 📈 Business Analysis

---

# 1. Pricing Efficiency & Discount Strategy

## Business Question

> Are large discounts actually driving customer demand?

### Analysis Performed

Relationship analyzed between

- Discount Percentage
- Rating Count
- Revenue Potential

using

- Correlation Matrix
- Scatter Plots

---

## Key Findings

The correlation between

Discount Percentage

and

Rating Count

is almost zero.

This indicates that

**larger discounts are NOT automatically increasing customer demand.**

Similarly,

Discount Percentage has a weak negative relationship with Revenue Potential.

---

## Business Interpretation

Customers are not purchasing products simply because they are heavily discounted.

Instead, purchasing decisions appear to depend more on

- Product quality
- Brand trust
- Reviews
- Product usefulness

rather than discount size.

---

## Business Recommendation

Instead of offering larger discounts,

Amazon should

- improve product quality
- strengthen product descriptions
- increase customer trust
- improve marketing visibility

---

# 2. Pricing Relationship

## Business Question

How does Amazon price expensive products after discounts?

---

## Analysis

Correlation

Actual Price

vs

Discounted Price

---

## Key Finding

Correlation = **0.96**

This is an extremely strong positive correlation.

---

## Business Interpretation

Higher-priced products remain expensive even after discounts.

Although premium products receive discounts,

they are still positioned as premium products.

This indicates a

**Premium Pricing Strategy**

rather than aggressive price reduction.

---

## Business Recommendation

Continue protecting premium positioning while using discounts strategically during seasonal campaigns.

---

# 3. Discount Amount Analysis

Instead of only analyzing percentage discounts,

absolute discount amount was calculated.

### Formula

Absolute Discount = Actual Price − Discounted Price

---

## Key Finding

Correlation

Actual Price ↔ Absolute Discount

= **0.91**

Higher-priced products receive significantly larger monetary discounts.

---

## Business Interpretation

Luxury products receive higher discount values in absolute terms,

even if the percentage discount is similar.

---

# 4. Customer Satisfaction Analysis

## Business Question

Does higher price lead to better customer ratings?

---

## Analysis

Scatter Plot

Rating

vs

Selling Price

Bubble Size = Rating Count

---

## Key Findings

Most products cluster between

**4.0 – 4.4**

regardless of price.

Products costing

₹500

and

₹20,000

often receive similar ratings.

---

## Business Interpretation

Higher price does not necessarily create happier customers.

Customer satisfaction appears relatively stable across different price segments.

---

# 5. Underpriced Stars

## Definition

Products satisfying

- Rating ≥ 4.5
- High Rating Count
- Low Discounted Price

---

## Business Meaning

These products are

- Highly rated
- Highly demanded
- Selling at comparatively low prices

---

## Business Opportunity

These products possess strong pricing power.

A small price increase

(5–10%)

could significantly increase profit

without major demand reduction.

---

## Recommendation

- Test incremental price increases
- Monitor customer response
- Increase profit margins

---

# 6. Overpriced Duds

## Definition

Products satisfying

- Low Rating
- High Original Price
- Heavy Discount

---

## Business Meaning

Despite offering large discounts,

customers still rate these products poorly.

This suggests

pricing is NOT the primary problem.

---

## Possible Reasons

- Poor product quality
- Weak customer experience
- Better competing products
- Overpriced positioning

---

## Recommendation

Rather than increasing discounts,

Amazon should

- investigate customer complaints
- improve product quality
- redesign listings
- reconsider suppliers

---

# 7. Customer Friction Points

## Business Question

Which products have

High Revenue Potential

but

Low Rating Count?

---

## Why?

These products may be

- difficult to discover
- poorly marketed
- lacking customer trust
- suffering from visibility issues

---

## Analysis

Products with

Top 25% Revenue Potential

and

Bottom 25% Rating Count

were identified.

---

## Observation

Very few products matched these conditions.

---

## Business Interpretation

Most products with high business potential already possess strong customer engagement.

There are very few hidden opportunities.

This indicates that

successful products are already receiving sufficient visibility.

---

# 8. Correlation Analysis

The correlation matrix provides an overall understanding of relationships between numerical variables.

---

## Strong Positive Relationships

### Actual Price ↔ Discounted Price

Correlation

0.96

Meaning

Premium products remain premium.

---

### Actual Price ↔ Absolute Discount

Correlation

0.91

Meaning

Expensive products receive larger monetary discounts.

---

### Discounted Price ↔ Absolute Discount

Correlation

0.76

Meaning

Higher selling prices naturally create larger discount amounts.

---

### Revenue Potential ↔ Discounted Price

Correlation

0.47

Meaning

Higher-priced products contribute more revenue opportunity.

---

### Revenue Potential ↔ Rating Count

Correlation

0.42

Meaning

Demand is a significant driver of revenue opportunity.

---

## Weak Relationships

### Rating ↔ Revenue Potential

Correlation

0.07

Meaning

Excellent ratings alone do not generate high business value.

Demand matters more.

---

### Discount Percentage ↔ Rating Count

Correlation

≈ 0

Meaning

Discounts alone do not increase customer demand.

---

# Scatter Matrix Interpretation

The scatter matrix reveals several patterns.

### Pricing

Actual Price and Discounted Price follow an almost perfect straight line,

confirming consistent pricing strategy.

---

### Ratings

Ratings remain concentrated around

4.0–4.4

regardless of product price.

---

### Demand

Rating Count varies dramatically,

indicating that

customer demand differs substantially even among similarly priced products.


---

### Revenue Opportunity

Revenue Potential increases primarily because of

- higher customer demand
- higher selling prices

rather than

higher customer ratings.

---

# Overall Business Insights

## ✅ Pricing strategy is consistent.

Premium products remain premium after discounts.

---

## ✅ Heavy discounts are not driving customer demand.

Customers purchase products based on perceived value rather than discount percentage.

---

## ✅ Customer ratings remain relatively stable.

Price has very little influence on satisfaction.

---

## ✅ Demand is the strongest revenue driver.

Products with higher Rating Counts generate larger business opportunities.

---

## ✅ Several products appear underpriced.

These products present immediate pricing optimization opportunities.

---

## ✅ Some expensive products require quality improvement rather than additional discounts.

---

## ✅ Revenue Potential successfully prioritizes products based on both pricing and customer popularity.

---

# Final Business Conclusion

This analysis demonstrates that **customer demand—not discount percentage—is the strongest driver of business opportunity**. While Amazon maintains a consistent premium pricing strategy, simply offering larger discounts does not significantly increase customer engagement or revenue potential.

The analysis identifies high-performing products that are likely underpriced and could support modest price increases, improving profitability without sacrificing demand. It also highlights expensive products with poor customer ratings, indicating that quality, positioning, or customer experience should be improved rather than relying on deeper discounts.

By engineering a **Revenue Potential** metric using **Discounted Price × Rating Count**, the project prioritizes products based on both pricing and customer popularity, enabling data-driven decisions for pricing optimization, product improvement, and revenue growth.

Overall, the findings suggest that long-term growth will come from **improving product quality, strengthening customer trust, increasing visibility, and optimizing pricing strategies**, rather than competing primarily through larger discounts.