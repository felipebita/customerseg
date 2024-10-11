# Customer Segmentation 

Can you segment customers based on their ordering behavior, such as frequency, time of day, or days since prior order, to better understand their preferences and propose ways to optimize marketing strategies?

[Orders Data](https://drive.google.com/file/d/1crDRZkG_Pj9EgXcxo9y97vHLVqfuZvPA/view?usp=sharing)

[Notebook](https://github.com/felipebita/customerseg/blob/main/Customer_Segmentation.ipynb)
## Solution

**Summary:** 

To address this question, I employed an **RFM (Recency, Frequency, Monetary Value)** strategy combined with **K-means clustering** for client segmentation. Since Monetary Value data was not available, I used the mean number of products per order (*mean_products*) as a proxy for value. Additionally, I included two more features: whether the client typically orders before or after midday (*b_a_12*) and whether they usually place orders earlier or later in the week (*e_l_week*). The most intesting number of cluster was 5 and it enabled tailored marketing strategies.

**Data Processing:** 

To prepare the data for K-means clustering, I worked with the *orders* and *order_products* datasets. The orders dataset was used to derive the *Frequency*, *Recency*, *b_a_12*, and *e_l_week* features, while the *order_products* dataset was used to calculate the *mean_products* feature.

I began by checking for missing data and anomalies using distribution plots, and no issues were found. For the orders dataset, I engineered the *order_dow* and *order_hour_of_day* features into *b_a_12* (before/after midday) and *e_l_week* (early/late week). For the *order_products* dataset, I grouped the data by *order_id* and *aggregated* it to obtain the count of products per order. This information was then merged into the *orders* dataset.

Since the *orders* dataset was structured as an **Order View**, I needed to transform it into a **Client View** by grouping the data by *client_i*d and aggregating the relevant features. Specifically:

- *Frequency* was calculated as the count of *order_id*.
- *Recency* was the last value of *days_since_prior_order*.
- *mean_products*, *mean_interval*, *b_a_12*, and *e_l_week* were computed as the mean values for each client.
  
Next, I examined the distribution of the features to identify any skewness, as skewed data can negatively impact K-means clustering. I found that both *Frequency* and *mean_product*s were skewed, so I applied a log transformation to these features. I then re-plotted the distributions to confirm that the data was less skewed. Finally, I standardized all features, as K-means clustering performs best on standardized data.

**Clustering:**

For clustering, I applied the **K-means** algorithm, exploring a range of 2 to 10 clusters. To determine the optimal number of clusters, I used the **Elbow method**, which evaluates the *within-cluster sum of squares (WCSS)*. The *WCSS* plot didn’t present a clear elbow, so I analyzed both **4** and **5 clusters**, as these points showed a more subtle reduction in WCSS. I compared the results from these cluster counts.

For visualizing the clusters within the data, I employed a **3D PCA** scatterplot. The first three principal components (PCs) captured **70.3%** of the variance, offering a reliable representation of the data distribution. Additionally, I compared the clusters based on feature means and visualized these comparisons through a **Snake plot**. The segmentation with **5 clusters** provided better results compared to 4 clusters. Specifically, the 5-cluster model produced clusters with distinct variations in the *b_a_12* feature. This variation allows for **more targeted marketing strategies** based on this feature.

**Customer segments:**

Here is described the characteristics of each cluster found in the K-means clustering.

![image](https://github.com/user-attachments/assets/18b8143a-f742-4f00-a4d0-f9b1d28d2f1f)

Cluster 0:

- *Frequency:* Very high frequency, indicating frequent ordering behavior.
- *Recency:* High, meaning these customers have ordered recently.
- *Mean Products:* High, indicating they purchase a larger number of products per order.
- *Mean Interval*: Short intervals between orders.
- *b_a_12:* Positive value suggests these customers are more likely to order after midday.
- *e_l_week:* Slightly positive, indicating a tendency to order later in the week.
- *Summary:* High-engagement customers who frequently order a large number of products, mostly after midday and towards the end of the week.

Cluster 1:

- *Frequency:* Low frequency, meaning these customers order less often.
- *Recency:* Very low, indicating they haven't ordered recently.
- *Mean Products:* Very low, meaning they purchase fewer products per order.
- *Mean Interval:* Long intervals between orders.
- *b_a_12:* Negative value suggests these customers tend to order before midday.
- *e_l_week:* Tends towards earlier in the week.
- *Summary:* Low-engagement customers who infrequently order a small number of products, primarily before midday and early in the week.
  
Cluster 2:

- *Frequency:* Moderate frequency, with customers ordering at a balanced rate.
- *Recency:* Moderate, indicating recent but not very recent orders.
- *Mean Products:* Low, with fewer products per order.
- *Mean Interval:* Balanced interval between orders.
- *b_a_12:* Tends to order after midday.
- *e_l_week:* Balanced across the week.
- *Summary:* Moderate customers with balanced ordering patterns, favoring the afternoon with no strong preference for early or late week.
  
Cluster 3:

- *Frequency:* Low frequency, ordering less often.
- *Recency:* Moderate, with somewhat recent orders.
- *Mean Products:* Very high, purchasing a large number of products per order.
- *Mean Interval:* Longer intervals between orders.
- *b_a_12:* Tends to order after midday.
- *e_l_week:* Later in the week.
- *Summary:* Low frequency, high-value customers who order fewer times but purchase many products, usually after midday and late in the week.
  
Cluster 4:

- *Frequency:* Moderate frequency.
- *Recency:* Moderate, indicating recent orders.
- *Mean Products:* Balanced product count per order.
- *Mean Interval:* Short intervals between orders.
- *b_a_12:* Balanced but slightly more after midday.
- *e_l_week:* Balanced across the week.
- *Summary:* Moderately engaged customers with a balanced approach to ordering, slightly favoring afternoons but evenly distributed across the week.

**Marketing Strategies:**

Here are marketing strategies suitable for each customer segment identified in the dataset.

Cluster 0: **High-engagement customers who frequently order a large number of products, mostly after midday and towards the end of the week.**

- *Loyalty Programs:* Implement a rewards program offering points for every purchase or product bought, with exclusive benefits for frequent buyers; Offer tiered rewards where more frequent or higher-value customers receive greater benefits.
- *Time-Sensitive Promotions:* Send targeted promotions in the afternoon, especially on Thursdays and Fridays, to align with their ordering patterns. Introduce flash sales or limited-time offers to encourage additional purchases before the weekend.

Cluster 1: **Low-engagement customers who infrequently order a small number of products, primarily before midday and early in the week.**

- *Re-engagement Campaigns:* Send personalized campaigns reminding them of products they’ve previously purchased and highlighting new arrivals. Offer a special discount or incentive for their next purchase to encourage them to order again.
- *Morning and Early Week Deals:* Since they prefer to order before midday and early in the week, target them with morning promotions or "Monday Motivation" deals to kickstart their buying week.

Cluster 2: **Moderate customers with balanced ordering patterns, favoring the afternoon with no strong preference for early or late week.**

- *Midday Offers:* Offer promotions that go live in the afternoon to capture their attention during their preferred ordering time. Create "Lunch Break Specials" that they can take advantage of while on break.
- *Balanced Promotions:* Rotate between early and late week promotions to cater to their balanced ordering pattern, ensuring they receive offers at times they are likely to order.

Cluster 3: **Low frequency, high-value customers who order fewer times but purchase many products, usually after midday and late in the week.**

- Bulk Purchase Incentives: Offer discounts or promotions for bulk purchases, such as "Buy More, Save More" deals or free shipping for large orders. Create subscription-based services for frequently bought items, offering convenience and discounts for regular large purchases.
- Reminder and Replenishment Programs: Send reminders when it’s time to reorder based on their average interval between purchases.
- Exclusive Deals for High-Value Orders: Offer exclusive discounts or perks for customers who spend above a certain threshold, such as a complimentary product or VIP customer service.
- Evening and Late Week Promotions: Launch promotions that go live in the evening and are valid through the weekend to align with their shopping habits. Encourage them to complete their week with a special offer available late in the week.

Cluster 4: **Moderately engaged customers with a balanced approach to ordering, slightly favoring afternoons but evenly distributed across the week.**

- Consistency and Variety: Maintain consistent communication with these customers, offering a mix of product recommendations and new product introductions. Introduce variety in your promotions, alternating between percentage discounts, bundle offers, or free shipping to keep them interested.
- Loyalty Building: Encourage loyalty by rewarding them for consistent purchasing behavior, even if it’s moderate. Offer a rewards program that accumulates points for every purchase. Provide tiered rewards, allowing them to unlock greater benefits with continued engagement.
- Balanced Timing Offers: Since their ordering patterns are spread throughout the week, ensure promotions and communications are timed evenly, so they always have an offer that aligns with their schedule.
