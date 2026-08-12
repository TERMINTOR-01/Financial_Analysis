# Amazon E-Commerce Financial & Pricing Analysis

> **A story-driven business analysis of pricing, discounts, demand, revenue potential, product portfolio, profitability signals, and customer friction**

---

## Executive Summary — The Story in One Minute

At first glance, this dataset looks like a typical e-commerce catalog: products, original prices, discounted prices, discount percentages, ratings, and rating counts.

But once the data is cleaned and the numbers are connected, a much more interesting story appears.

The business is not simply selling products. It is operating a portfolio in which **a relatively small group of products creates most of the business value, while a large part of the catalog contributes comparatively little**. At the same time, the company appears to rely heavily on discounts in some of its biggest categories, yet the analysis shows that **more discount does not automatically mean more demand**.

Three ideas dominate the story:

1. **Pricing is strongly connected to discounting, but discount depth alone is not a reliable demand lever.**
2. **Revenue potential is highly concentrated: 168 products account for roughly 80% of the modeled revenue potential.**
3. **The biggest opportunities are not the same for every product: some products may be underpriced, some are expensive but weakly demanded, and some simply need more visibility and social proof.**

The analysis therefore moves from a simple question — *“Are discounts working?”* — to a much bigger business question:

> **Where should the company protect price, where should it reduce discounting, where should it invest in demand generation, and where should it rethink the product itself?**

---

# 1. The Business Question

Imagine the business team sitting in a meeting and asking a seemingly simple question:

> “We are giving discounts across a large product catalog. Are these discounts actually creating business value?”

That question sounds like a pricing question, but it quickly becomes a broader business problem.

A discount can increase the chance of a purchase, but it also reduces the amount of money retained per sale. A high-priced product can generate large revenue from a small number of purchases, while a low-priced product can generate enormous value through volume. A product can have a fantastic rating but almost no visibility. Another product can be discounted by 70–90% and still fail to attract meaningful engagement.

So the analysis was built to connect five things:

**Price → Discount → Customer engagement → Revenue potential → Portfolio strategy**

The notebook develops these ideas in stages, moving from data cleaning and correlation analysis into pricing strategy, discount efficiency, price realization, category dynamics, Pareto concentration, quality-demand validation, and customer friction.

---

# 2. Starting with the Data

The analysis begins with an Amazon product dataset containing **1,465 rows and 16 columns**.

The source contains fields such as:

- `product_id`
- `product_name`
- `category`
- `discounted_price`
- `actual_price`
- `discount_percentage`
- `rating`
- `rating_count`
- review-related fields
- user-related fields
- image and product-link fields

The raw dataset is useful, but not yet ready for business analysis because many important numeric fields are stored as text.

For example, prices are represented like `₹1,099`, discounts like `64%`, and rating counts like `24,269`.

That means the first job is not visualization. The first job is making the data analytically trustworthy.

---

# 3. Data Cleaning — Turning Catalog Data into Analysis-Ready Data

## 3.1 Removing fields that are not needed for this analysis

The notebook removes fields related to detailed review text, users, images, and product links:

- `img_link`
- `product_link`
- `review_content`
- `review_title`
- `review_id`
- `user_name`
- `user_id`
- `about_product`

This leaves the analysis focused on product economics and customer engagement rather than unstructured review content.

## 3.2 Handling missing values

Only **2 rows** have missing `rating_count` values.

They correspond to:

- Amazon Brand - Solimo 65W Fast Charging Braided product
- REDTECH USB-C to Lightning Cable

Because `rating_count` is later used as the proxy for demand and is also part of the Revenue Potential calculation, these rows are dropped rather than trying to invent a value.

The dataset therefore falls from **1,465 rows to 1,463 rows** at this stage.

Later, `rating` is converted using numeric coercion and another `dropna()` is applied. The final dataframe visible in the notebook contains **1,462 rows**.

### Important reporting note

The notebook later describes the Pareto analysis using “1,465 total products.” That label is inconsistent with the cleaned dataframe actually used by the downstream analysis, which contains **1,462 rows**.

For this report, the final analytical population is treated as **1,462 products** because that is the dataset actually present after cleaning.

## 3.3 Converting business fields into numeric values

The analysis converts:

- `discounted_price` → numeric
- `actual_price` → numeric
- `discount_percentage` → numeric
- `rating` → numeric
- `rating_count` → integer

This allows the project to calculate derived metrics and conduct correlation, segmentation, and portfolio analysis.

## 3.4 Simplifying category hierarchy

The original `category` field contains multi-level category strings separated by `|`.

The notebook keeps the first category level using:

```python
df["category"] = df["category"].str.split("|").str[0]
```

This transforms long category paths into broad business categories such as:

- Electronics
- Computers & Accessories
- Home & Kitchen
- Office Products
- Musical Instruments
- Health & Personal Care
- Home Improvement
- Car & Motorbike
- Toys & Games

This is useful for portfolio-level strategy because the analysis is primarily trying to answer **which major business categories matter most**.

---

# 4. The First Important Metric: Revenue Potential

The project introduces a custom metric:

> **Revenue Potential = Discounted Price × Rating Count**

This is the central metric used in much of the notebook.

The logic is intuitive:

- discounted price represents the amount charged for a product
- rating count is treated as a proxy for customer demand / purchase volume
- multiplying the two creates a rough estimate of the product's value contribution

### Why this metric is useful

Actual transaction quantities are not available in the dataset. Therefore, the analysis needs a proxy for demand.

`rating_count` serves that role.

So the metric helps answer:

> “Which products appear capable of generating the greatest value when selling price and customer engagement are considered together?”

### But this is not actual revenue

This is an important analytical limitation.

The dataset does **not** contain actual units sold, actual order quantity, transaction revenue, cost of goods, or profit.

Therefore, Revenue Potential should be interpreted as a **modeled business proxy**, not reported company revenue.

Likewise, rating count is not identical to units sold because not every buyer leaves a rating and rating behavior varies by product.

This distinction matters, especially when this analysis is used in a portfolio or interview setting.

---

# 5. The First Big Discovery — Price and Discount Move Together

The correlation analysis is where the story starts becoming interesting.

The strongest relationship in the dataset is:

### Actual Price vs Discounted Price

**Correlation = 0.962**

That is an extremely strong positive relationship.

In simple terms:

> **Products that are expensive before discount are usually still expensive after discount.**

A ₹50,000 product does not suddenly become a ₹500 product just because it receives a discount.

Instead, the discount generally reduces the price while preserving the broad positioning of the product.

### Business meaning

This suggests the business maintains a relatively consistent pricing ladder:

- low-priced products remain low-priced
- premium products remain premium
- discounts change the selling price, but do not completely erase the underlying price hierarchy

This is useful because it tells us that discounting is operating *within* product price tiers rather than completely reshaping them.

---

# 6. Expensive Products Also Receive Larger Absolute Discounts

Another strong relationship is:

### Discounted Price vs Absolute Discount Amount

**Correlation = 0.764**

And even stronger:

### Actual Price vs Absolute Discount Amount

**Correlation = 0.911**

This means the absolute amount of money given away through a discount tends to become larger as the original product price becomes larger.

A ₹50,000 item receiving a 20% discount gives up ₹10,000.

A ₹500 item receiving the same 20% discount gives up only ₹100.

So even when the percentage discount is identical, the business impact of the discount can be dramatically different in rupee terms.

### Business interpretation

High-ticket products can therefore become major sources of discount exposure.

This does not mean high-ticket discounts are automatically bad. A large discount can be justified if it generates enough additional demand.

The real question becomes:

> **How much business value is created for every unit of discount given away?**

That question leads directly into the next stage of the analysis: discount efficiency.

---

# 7. Does Giving a Bigger Discount Actually Create More Demand?

This is one of the most important questions in the entire project.

The notebook compares:

**Discount Percentage vs Rating Count**

and concludes that higher discounts are **not associated with proportionally higher customer engagement**.

That is a crucial finding.

It challenges a common assumption:

> “If we want more sales, just give a bigger discount.”

The dataset does not support that as a general rule.

A 70% discounted product may still receive little customer engagement, while another product with a 20–30% discount may attract a much larger customer base.

### Business lesson

A discount is an incentive, not a guarantee of demand.

Demand can depend on:

- product usefulness
- brand strength
- product quality
- customer trust
- category demand
- visibility
- competition
- perceived value

Therefore, discounting should be treated as one lever inside the pricing strategy — not the entire strategy.

---

# 8. The Discount Tier Story — Why 10–30% Stands Out

To make discount performance easier to interpret, products are divided into four tiers:

| Discount Tier | Products |
|---|---:|
| 0–10% | 84 |
| 10–30% | 259 |
| 30–50% | 426 |
| 50%+ | 693 |

This distribution itself is revealing.

The **50%+ tier contains 693 products**, meaning almost half of the final analytical catalog is heavily discounted.

That immediately raises a strategic question:

> If deep discounting were the strongest demand strategy, would we expect the 50%+ group to clearly dominate revenue performance?

The notebook's tier analysis suggests the answer is **no**.

### 0–10% discounts

Low-discount products generate reasonable revenue potential, but customer engagement is lower than in the strongest middle tier.

### 10–30% discounts — the strongest balance

The notebook identifies the **10–30% tier as the best-performing discount range** in terms of balancing customer demand and Revenue Potential.

This is the first major pricing recommendation emerging from the analysis.

The strategy does not appear to be:

> “Maximum discount = maximum performance.”

Instead, it appears closer to:

> **“Moderate discount = better balance between demand and retained selling price.”**

### 30–50% discounts

Increasing the discount beyond 30% does not appear to create proportional improvement in engagement and is associated with lower Revenue Potential.

### 50%+ discounts

Deep discounts produce the strongest warning sign.

Despite containing **693 products**, the 50%+ group is associated with the **lowest Revenue Potential** in the notebook's tier comparison.

This suggests that aggressive discounting may be sacrificing too much selling price without creating sufficient additional demand.

---

# 9. A Simple Example of Over-Discounting

The notebook illustrates the problem with a simple hypothetical example.

### Product A

Original Price = ₹100  
Discount = 20%  
Selling Price = ₹80  
Rating Count = 2,000

Revenue Potential:

**₹80 × 2,000 = ₹160,000**

### Product B

Original Price = ₹100  
Discount = 60%  
Selling Price = ₹40  
Rating Count = 2,300

Revenue Potential:

**₹40 × 2,300 = ₹92,000**

Even though Product B has slightly higher engagement, the much deeper discount cuts the price so aggressively that modeled revenue potential becomes substantially lower.

This example captures the core lesson:

> **Demand growth is useful only when the value created by the extra demand compensates for the price sacrificed.**

---

# 10. Discount Leakage — Where Is the Pricing Cost Concentrated?

The project next introduces another derived metric:

> **Total Discount Leakage = Absolute Discount Amount × Rating Count**

This is intended to approximate how much discount value is being given away at scale.

Again, this is not accounting profit or literal lost profit. It is a **discount-exposure proxy** based on the project's demand proxy.

The category-level results show a very strong concentration.

| Category | Total Discount Leakage Proxy |
|---|---:|
| Electronics | ~₹38.84B |
| Computers & Accessories | ~₹6.26B |
| Home & Kitchen | ~₹4.19B |
| Musical Instruments | ~₹0.087B |
| Office Products | ~₹0.015B |
| Health & Personal Care | ~₹0.004B |
| Home Improvement | ~₹0.004B |
| Car & Motorbike | ~₹0.002B |
| Toys & Games | ₹0 |

The total modeled discount exposure is approximately **₹49.41B** across these categories.

### The important part is concentration

Electronics alone accounts for the largest discount exposure by a huge margin.

That is not automatically a bad thing because Electronics is also the largest revenue-potential category. The business may be deliberately spending discount value where demand is strongest.

So the right question is not:

> “Which category gives the most discount?”

The better question is:

> **“Which category creates enough business value to justify the discount exposure?”**

---

# 11. Electronics — The Biggest Engine and the Biggest Discount Exposure

Electronics generates approximately **₹59.18B in modeled Revenue Potential** in the category aggregation.

At the same time, it carries approximately **₹38.84B in modeled discount leakage**.

This creates a fascinating strategic tension.

Electronics is both:

- the biggest opportunity
- and the biggest place to manage pricing carefully

The category is clearly commercially important, but its average Price Realization Ratio is only about **0.49**, meaning customers pay roughly 49% of the original listed price on average.

Its average discount is about **50.83%**.

So the business is heavily discount-dependent in a category that is already its main growth engine.

### What this suggests

Electronics should not simply be “discount less” across the board.

Instead, the right move is to identify:

- products where discounting is driving efficient demand
- products where discounting is destroying price realization
- products with strong demand that may tolerate smaller discounts
- products where large discounts are compensating for weak product-market fit

This is where product-level efficiency becomes more valuable than category-level averages.

---

# 12. Discount Efficiency — The “Was the Discount Worth It?” Metric

The project defines:

> **Discount Efficiency = Revenue Potential / Total Discount Leakage**

The higher the number, the more modeled Revenue Potential is generated relative to the discount exposure proxy.

This creates a useful business lens.

Instead of asking only:

> “How large is the discount?”

we can ask:

> **“How efficiently is the discount being converted into business value?”**

### A striking low-efficiency example

Product **B097C564GC** has a Discount Efficiency of approximately **0.06**.

Its recorded values are approximately:

- Actual Price: ₹4,999
- Absolute Discount: ₹4,705
- Revenue Potential: ₹1.30M
- Discount Leakage Proxy: ₹20.82M
- Discount Efficiency: **0.06**

The product is discounted by roughly **94%**.

This is exactly the kind of product that deserves pricing review.

The issue is not merely “large discount.” The issue is that the modeled business value is tiny relative to the discount exposure created.

### Fire-Boltt smartwatch cluster

Several Fire-Boltt listings appear with a Discount Efficiency around **0.10**.

The notebook highlights approximately:

- Revenue Potential: **₹25M** per listed row pattern
- Total Discount Leakage: **₹254M** per listed row pattern
- Discount Efficiency: **0.10**

The repeated listings also deserve a data-quality / catalog-quality check because they may represent duplicate listings, variants, or repeated SKUs.

### Business lesson

Products with extremely low Discount Efficiency should not automatically receive deeper discounts.

They should instead be investigated for:

- weak demand
- poor positioning
- over-discounting
- duplicate listings
- excessive promotional dependence
- inventory clearance behavior

---

# 13. Price Realization — How Much of the Original Price Survives?

The next metric is:

> **Price Realization Ratio = Discounted Price / Actual Price**

This answers a very practical pricing question:

> “After all discounts are applied, what fraction of the original listed price is the business actually collecting?”

A ratio of 0.80 means 80% of the original price is being realized.

A ratio of 0.40 means only 40% is being realized.

This creates a direct concept of **price integrity**.

---

# 14. Category-Level Price Integrity

The category results tell a strong story.

| Category | Avg. Price Realization | Avg. Discount | Products |
|---|---:|---:|---:|
| Office Products | 0.876 | 12.35% | 31 |
| Toys & Games | 1.000 | 0.00% | 1 |
| Home & Kitchen | 0.598 | 40.17% | 447 |
| Car & Motorbike | 0.585 | 42.00% | 1 |
| Musical Instruments | 0.542 | 46.00% | 2 |
| Electronics | 0.492 | 50.83% | 526 |
| Health & Personal Care | 0.473 | 53.00% | 1 |
| Computers & Accessories | 0.461 | 53.92% | 451 |
| Home Improvement | 0.421 | 57.50% | 2 |

### Office Products — the strongest price integrity

Office Products has a realization ratio of approximately **0.88**.

Customers are paying close to the original listed price, while the average discount is only about **12.35%**.

This is a strong signal of pricing power and low discount dependence.

### Home & Kitchen — a healthier middle ground

Home & Kitchen has a realization ratio of approximately **0.60**, with an average discount around **40%**.

Compared with Electronics and Computers & Accessories, the category appears to retain a larger share of its listed price.

### Electronics — powerful, but discount-dependent

At approximately **0.49**, Electronics realizes less than half of the original listed price on average.

That is a major strategic signal because Electronics is also the strongest revenue category.

The business may therefore be buying demand with significant pricing sacrifice.

### Computers & Accessories — similar pressure

Computers & Accessories has a realization ratio of approximately **0.46**, with an average discount of about **54%**.

This suggests substantial dependence on promotional pricing.

### Small-category caution

Categories such as Car & Motorbike, Toys & Games, Musical Instruments, Health & Personal Care, and Home Improvement have very small sample sizes in this cleaned dataset.

Their category-level averages should therefore be treated as directional rather than definitive.

---

# 15. The Portfolio View — Which Categories Actually Matter?

The category revenue matrix combines:

- Average Selling Price
- Total Revenue Potential
- Product Count

This creates a BCG-style portfolio view.

## Electronics — the clear market leader

Electronics has:

- approximately **₹5,966 average selling price**
- approximately **₹59.18B Revenue Potential**
- **526 products**

It combines scale, pricing, and demand.

This makes Electronics the clear strategic engine of the catalog.

### Strategic role

**Invest and protect.**

That means:

- prioritize availability
- reduce stockout risk
- allocate meaningful advertising budgets
- develop bundles and value-added offers
- monitor discount efficiency at product level

## Home & Kitchen — a strong secondary engine

Home & Kitchen has:

- approximately **₹2,331 average selling price**
- approximately **₹6.26B Revenue Potential**
- **447 products**

The category generates substantial value through a broad product base rather than the very high average selling prices seen in Electronics.

### Strategic role

**Maintain and optimize.**

## Computers & Accessories — broad-volume contributor

Computers & Accessories has:

- approximately **₹845 average selling price**
- approximately **₹6.35B Revenue Potential**
- **451 products**

This is an important volume-driven category.

### Strategic role

**Maintain scale and improve margin efficiency.**

Bundling and cross-selling are especially relevant here because the category contains many relatively affordable products.

## Smaller categories

Office Products, Musical Instruments, Home Improvement, Car & Motorbike, Health & Personal Care, and Toys & Games contribute much less modeled revenue in this dataset.

This does not automatically mean they should be removed. It means the business should ask:

> “Is the strategic or profit contribution of these categories large enough to justify the operational complexity?”

---

# 16. The Biggest Structural Discovery — 80% of Value Comes from Only 168 Products

The Pareto analysis is one of the strongest insights in the project.

After sorting products by Revenue Potential and calculating cumulative contribution, the notebook finds:

> **168 products generate approximately 80% of Revenue Potential.**

Out of the final **1,462-product analytical dataset**, that is approximately:

> **11.49% of the catalog.**

This is a very strong concentration.

In other words:

> **The business does not need every product to perform equally. A small group of products is carrying the portfolio.**

This changes how inventory, marketing, and pricing resources should be allocated.

---

# 17. Who Are These Top Revenue Drivers?

The top products are heavily dominated by Electronics.

Examples include:

- Redmi smartphones
- Samsung smartphones
- Mi smart TVs
- OnePlus smart TVs
- other high-demand consumer electronics

Some of the strongest products have enormous rating counts.

For example, several Redmi products in the top set have rating counts above **300,000**, which makes their Revenue Potential extremely large even when the selling price is not the highest in the dataset.

This is a critical lesson:

> **High price is not enough. High demand multiplied by price is what creates large modeled value.**

A ₹40,000 product with a small customer base may contribute less than a ₹8,000 product with massive demand.

---

# 18. The Long Tail — Hundreds of Products Contribute Very Little

The Pareto curve rises steeply at the beginning and then flattens.

This means the catalog has a classic long-tail shape:

- a small number of high-impact products
- a large number of low-impact products

After the first roughly 170 products, the incremental contribution becomes comparatively small.

From a business perspective, this creates a portfolio optimization question.

Every additional product may create:

- warehouse complexity
- procurement complexity
- catalog management cost
- marketing dilution
- inventory risk

If a product contributes almost no value, the business should not automatically keep treating it as strategically important.

Potential actions for long-tail products include:

- bundle them
- reduce inventory exposure
- limit advertising
- replace them with stronger products
- discontinue consistently weak products

---

# 19. Premium Products Can Still Be Weak Businesses

The notebook also identifies products that are expensive but fall into the lower part of Revenue Potential.

The implemented selection rule is:

- actual price above the 75th percentile
- Revenue Potential below the 25th percentile

Examples include products such as:

- premium projectors
- smart watches
- air fryers
- humidifiers
- water purifiers
- other higher-priced products with comparatively low demand

Several of these products also have substantial discounts.

This reveals an important business paradox:

> **A product can be expensive and heavily discounted and still generate little modeled business value.**

Price alone does not create demand.

Discount alone does not create demand either.

The product must offer something customers actually want and trust.

---

# 20. Quality + Demand Validation — Underpriced Stars

One of the most useful product-level segments is the **Underpriced Star**.

The implemented code defines an Underpriced Star using:

- Rating ≥ **4.5**
- Rating Count in the **top 25%**
- Discounted Price in the **bottom 25%**

These are products with:

- very strong customer satisfaction
- strong customer engagement
- relatively low selling prices

Examples identified in the notebook include:

- AmazonBasics USB cables
- Redgear gaming mousepad
- Dell USB wired mouse
- Duracell batteries
- ELV mobile phone stand

These products are strategically interesting because they combine **quality + demand + affordability**.

### The opportunity

These products may possess pricing power.

The business could test a modest price increase while monitoring engagement and conversion.

The reasoning is straightforward:

If customers already strongly value a product and demand is high, the business may not need to give away as much margin through aggressive discounting.

### Caution

This is a hypothesis, not proof that prices can be increased without hurting sales.

A proper pricing test would be required before making a broad change.

---

# 21. Quality + Demand Validation — Overpriced Duds

The opposite segment is the **Overpriced Dud**.

The implemented code defines these products using:

- Rating ≤ **3.5**
- Actual Price in the **top 25%**
- Discount ≥ **50%**

Examples identified in the notebook include:

- PTron Force X10 Bluetooth Earphones
- Skywall Smart LED TV
- Boult Audio Airbass Earbuds

The important pattern is:

> **High price + heavy discount + poor rating**

This is a dangerous combination.

It suggests the business is already making a large pricing concession, yet customer satisfaction remains weak.

### Why simply discounting more may be the wrong move

If the product problem is quality, performance, expectation mismatch, or weak positioning, another discount may simply reduce margin without fixing the underlying problem.

The right response may instead be:

- improve product quality
- inspect negative review themes
- change positioning
- improve product information
- reconsider the product assortment
- discontinue consistently poor products

This is one of the clearest cases where **product strategy matters more than discount strategy**.

---

# 22. Rating and Price Are Not Strongly Connected

The correlation between:

### Rating vs Revenue Potential

is only **0.069**.

That is essentially negligible.

This means highly rated products do not automatically generate high modeled revenue.

Why?

Because ratings have relatively little variation across much of the dataset, while demand and price vary much more.

Similarly:

### Rating vs Discount Percentage

has a weak negative correlation of **-0.156**.

So a bigger discount does not systematically create a better customer rating.

This reinforces a broader lesson:

> **Price and promotion can influence purchase decisions, but they do not replace product quality.**

---

# 23. Customer Friction — Valuable Products that Lack Social Proof

The final section of the notebook explores a different kind of opportunity.

Instead of finding products that are too expensive or too heavily discounted, it searches for products that may be blocked by insufficient customer visibility.

The concept is:

- high-value products
- relatively low review counts
- strong customer ratings

The business problem is simple:

> A good product can still underperform if customers do not have enough social proof to trust it.

Products with many ratings naturally look more established. A premium product with very few reviews may struggle to convert even if its rating is excellent.

### Why this matters

This is a fundamentally different problem from poor pricing.

For these products, the solution may be:

- advertising
- homepage placement
- recommendation systems
- influencer exposure
- seasonal campaigns
- verified review generation
- post-purchase review reminders

In other words:

> **Some products do not need a bigger discount. They need more visibility.**

### Important implementation note

There is a mismatch between the written explanation in the notebook and the actual filtering code.

The markdown describes Customer Friction Points using conditions such as Rating ≥ 4.2 and a price threshold around the median, while the code that actually creates `customer_friction` filters on:

- discounted price ≥ 75th percentile
- rating count ≤ 25th percentile

without applying the stated rating ≥ 4.2 condition.

Therefore, this segment should be treated as a **high-price / low-review segment based on the executed code**, rather than as a confirmed “high-rated customer friction” segment.

That is an important technical correction for a polished project report.

---

# 24. Putting the Entire Story Together

At this point, the analysis tells a surprisingly consistent story.

## Chapter 1 — The business discounts heavily

There are **693 products** in the 50%+ discount tier.

So deep discounting is not an exception; it is a major part of the catalog strategy.

## Chapter 2 — But bigger discounts do not automatically create bigger demand

The relationship between discount percentage and engagement is weak.

This means the business cannot depend on discount depth as the primary demand engine.

## Chapter 3 — Moderate discounts appear more efficient

The 10–30% discount tier provides the strongest balance between demand and Revenue Potential in the notebook's analysis.

## Chapter 4 — Discount exposure is concentrated

Electronics accounts for the overwhelming majority of modeled discount leakage, but it also generates the largest modeled Revenue Potential.

The category therefore deserves optimization, not blanket discount elimination.

## Chapter 5 — Price realization is uneven

Office Products preserve almost 88% of original price, while Electronics and Computers & Accessories realize roughly 49% and 46% respectively.

This means different categories have very different pricing power.

## Chapter 6 — A small number of products carry the business

Only **168 products, or about 11.49% of the cleaned catalog, generate around 80% of Revenue Potential**.

That means product prioritization is critical.

## Chapter 7 — Different products need different interventions

Some products are strong enough to consider price optimization.

Some need quality improvement.

Some need more visibility.

Some should receive less promotional investment.

That is the final lesson:

> **There is no single “best discount strategy” for the entire catalog. The right strategy is product- and category-specific.**

---

# 25. Strategic Recommendations

## 1. Make 10–30% discounts the default test range

The analysis suggests that moderate discounting delivers a better balance between demand and Revenue Potential.

Instead of starting with 50–70% discounts, the business should test whether lower discount levels can preserve demand while improving price realization.

## 2. Stop treating all discounts equally

A 20% discount on a high-volume winner is fundamentally different from a 90% discount on a weak product.

Discount decisions should consider:

- demand
- Revenue Potential
- discount exposure
- price realization
- rating
- category economics

## 3. Protect the top 168 products

The products responsible for around 80% of modeled Revenue Potential deserve disproportionate attention.

Prioritize:

- inventory availability
- advertising
- placement
- delivery performance
- pricing experiments

A stockout on a top product is likely more damaging than a stockout on a long-tail product.

## 4. Optimize Electronics rather than blindly expanding discounts

Electronics is the strongest business engine but also the biggest discount-exposure center.

Focus on product-level optimization inside Electronics:

- reduce discounts where demand is already strong
- preserve discounts where they demonstrably drive demand
- identify low-efficiency products
- test higher price realization on proven winners

## 5. Use product quality interventions for Overpriced Duds

Do not keep solving a quality problem with a pricing problem.

Products with poor ratings, high prices, and heavy discounts should undergo quality and positioning review before further discount expansion.

## 6. Use visibility interventions for Customer Friction products

Products with strong potential but low review counts may benefit more from:

- promotion
- recommendation placement
- review generation
- social proof

than from additional price cuts.

## 7. Rationalize the long tail

The business should continuously review low-Revenue-Potential products.

Potential actions:

- bundle
- replace
- reduce inventory
- reduce paid promotion
- discontinue consistent underperformers

## 8. Protect price integrity where customers already accept the price

Office Products demonstrate that a category can operate with a high price realization ratio and relatively low discounting.

That suggests the business should actively search for similar pockets of pricing power elsewhere.

---

# 26. A Practical Decision Framework for the Business

The analysis can be translated into a simple operating framework.

| Product Signal | What it means | Suggested action |
|---|---|---|
| High demand + high rating + low price | Strong product-market fit | Test modest price increase |
| High demand + heavy discount | Likely pricing-power opportunity | Test lower discount |
| High price + low demand | Premium product risk | Improve visibility / pricing / positioning |
| Low rating + heavy discount | Discount is not fixing product problem | Review quality or discontinue |
| High value + low review count | Social-proof problem | Increase visibility and reviews |
| Low Revenue Potential + low demand | Long-tail burden | Reduce inventory or rationalize |
| High Revenue Potential + high discount | Important but margin-sensitive | Optimize price carefully |

This framework makes the analysis operational rather than purely descriptive.

---

# 27. What the Analysis Does Well

This project has several strong analytical components.

### Strong business-oriented feature engineering

The project does not stop at raw columns. It creates:

- Revenue Potential
- Absolute Discount Amount
- Discount Tier
- Total Discount Leakage
- Discount Efficiency
- Price Realization Ratio

That makes the analysis much closer to a real business case.

### Strong segmentation mindset

The notebook identifies:

- discount tiers
- top performers
- underperforming premium products
- Underpriced Stars
- Overpriced Duds
- customer friction opportunities

This is much more useful than a generic EDA.

### Strong portfolio thinking

The Pareto analysis and category matrix move the discussion from individual products to portfolio decisions.

### Strong use of visual analysis

Scatter plots, correlation analysis, category matrices, bubble charts, and Pareto charts all support the business narrative.

---

# 28. Analytical Limitations You Should Mention in the Project

A polished report should also be honest about what the data cannot prove.

## Revenue Potential is not actual revenue

The formula uses `discounted_price × rating_count`.

Because actual units sold are unavailable, this is a modeled proxy.

## Rating Count is not units sold

A rating is an engagement signal, not a confirmed transaction count.

It is useful as a proxy, but not equivalent to sales volume.

## Discount Leakage is not profit loss

The leakage metric multiplies discount amount by rating count.

It estimates exposure to discounting but does not account for:

- cost of goods
- shipping
- platform fees
- marketing costs
- returns
- taxes
- actual order volume

Therefore it should not be called “lost profit.”

## Correlation does not establish causation

For example, the dataset shows that deep discounts are not strongly associated with larger rating counts, but this does not prove that discounts never increase demand.

A causal conclusion would require experiments or transaction-level time-series data.

## Rating Count and Revenue Potential are mechanically related

Because Rating Count is literally part of the Revenue Potential formula, their positive correlation is partly expected by construction.

## Some categories are too small for robust inference

Several categories have only one or two products.

Their averages should not be treated as reliable category-wide business rules.

## Duplicate or repeated listings may influence results

Several product IDs / listings appear with highly similar product information.

These should be checked before making operational decisions.

---

# 29. Data Quality / Notebook Quality Notes to Fix Before Publishing

There are a few issues worth cleaning up before this analysis is presented as a final portfolio project.

### Currency labels

The source data uses Indian Rupee values (`₹`), but some notebook markdown and chart labels use `£`.

This report uses **₹** consistently because that matches the underlying dataset.

### Dataset size in the Pareto narrative

The notebook says “1,465 total products” after later cleaning steps. The final dataframe shown in the notebook contains **1,462 rows**.

The report therefore uses 1,462 as the final analytical population.

### Customer Friction criteria mismatch

The explanatory markdown and the actual filtering code do not match exactly.

The code filters high discounted price and low rating count, while the written logic additionally describes a high rating condition.

The code and explanation should be aligned before publishing.

### Discount tier boundary

The project uses:

```python
bins=[0, 10, 30, 50, 100]
```

This should be interpreted carefully around exact boundary values such as 10%, 30%, and 50%.

### Duplicate products / repeated listings

The dataset contains repeated or very similar listings, which can inflate demand proxies and portfolio concentration.

A deeper version of the analysis should consider deduplicating or grouping by product family / SKU where appropriate.

---

# 30. Final Business Conclusion

The most important thing this analysis reveals is that the business does **not** have a simple discount problem.

It has a **portfolio optimization problem**.

The catalog contains products with very different economic behaviors.

Some products are strong enough that the company may be leaving money on the table by discounting too aggressively.

Some products are receiving huge discounts without generating enough value in return.

Some products are expensive but under-demanded.

Some products look strong in quality and price but need more visibility and customer reviews.

And above all, a small group of products drives most of the modeled business value.

The numbers tell a clear story:

- **1,462 products** remain after the cleaning steps used in the notebook.
- **693 products** sit in the 50%+ discount tier.
- The **10–30% discount tier** emerges as the strongest balance of demand and Revenue Potential in the notebook's comparison.
- Electronics generates roughly **₹59.18B** of modeled Revenue Potential and has the largest discount exposure.
- Office Products show the strongest price realization among meaningful multi-product categories, at about **0.88**.
- Electronics and Computers & Accessories realize only about **0.49** and **0.46** of original listed prices on average.
- The top **168 products** generate approximately **80% of Revenue Potential**, representing about **11.49%** of the cleaned catalog.
- The project identifies **Underpriced Stars** that may deserve price optimization and **Overpriced Duds** that may need product or positioning changes instead of deeper discounting.
- The Customer Friction analysis points to another class of opportunity: valuable products that may need more social proof and visibility rather than another price cut.

The strategic message is therefore simple:

> **Do not ask whether the company should discount more or discount less. Ask which products deserve which strategy.**

A winning pricing strategy should:

**Protect price where demand is already strong.**  
**Use moderate discounts where they create efficient demand.**  
**Fix product problems instead of discounting them away.**  
**Increase visibility for strong products that lack social proof.**  
**Concentrate inventory and marketing on the products that create the majority of business value.**

That is the real story hidden inside this dataset.

---

# 31. One-Line Takeaway

> **The biggest opportunity is not to sell more by giving the biggest discount; it is to understand which products already have pricing power, which products truly need demand generation, and which products are consuming discount without creating enough value.**

---

## Appendix — Core Formulas Used

### Revenue Potential

```text
Revenue Potential = Discounted Price × Rating Count
```

### Absolute Discount Amount

```text
Absolute Discount Amount = Actual Price − Discounted Price
```

### Total Discount Leakage Proxy

```text
Total Discount Leakage = Absolute Discount Amount × Rating Count
```

### Discount Efficiency

```text
Discount Efficiency = Revenue Potential / Total Discount Leakage
```

### Price Realization Ratio

```text
Price Realization Ratio = Discounted Price / Actual Price
```

### Price Realization Percentage

```text
Price Realization % = (Discounted Price / Actual Price) × 100
```

---

## Final Note on Interpretation

This report intentionally treats the notebook as the primary source of truth for the analysis and preserves its core logic. Where the notebook contains inconsistencies between narrative text and executed code, those mismatches are explicitly called out rather than silently hidden.

The strongest use of this work is as a **business-oriented analytical case study** demonstrating how raw e-commerce catalog data can be transformed into pricing, portfolio, and product strategy insights.
