# Customer Segmentation 

[Orders Data](https://drive.google.com/file/d/1crDRZkG_Pj9EgXcxo9y97vHLVqfuZvPA/view?usp=sharing)

[Notebook](https://github.com/felipebita/customerseg/blob/main/Customer_Segmentation.ipynb)

**Objective:**

Segment customers based on their ordering behavior—frequency, time of day, or days since prior order—to better understand their preferences and optimize marketing strategies.

## Solution

**Summary:** 

To address this task, I employed an **RFM (Recency, Frequency, Monetary Value)** strategy combined with **K-means clustering** for client segmentation. Since Monetary Value data was not available, I used the mean number of products per order (*mean_products*) as a proxy for value. Additionally, I included two more features: whether the client typically orders before or after midday (*b_a_12*) and whether they usually place orders earlier or later in the week (*e_l_week*). The most intesting number of cluster was 5 and it enabled tailored marketing strategies.

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

![image](https://github.com/user-attachments/assets/0f2f2953-a02e-4104-adff-1fe4f1e79b1e)

Cluster 0: Recent, Infrequent Shoppers
- *Frequency:* Low, suggesting these customers do not purchase often.
- *Recency:* Very high, indicating they have made a purchase very recently.
- *Mean Products:* Slightly high, meaning they tend to buy a decent number of items when they do order.
- *Mean Interval:* High, confirming they take a long time between orders.
- *b_a_12:* High, showing a strong preference for ordering after midday.
- *e_l_week:* Low, indicating they tend to order earlier in the week.
- *Summary:* This group consists of recent but infrequent customers. They purchase a relatively large number of products per order but have long gaps between their purchases. They are most active after midday and during the early part of the week. They could be new customers or existing ones who only shop for specific occasions.

Cluster 1: At-Risk Morning Shoppers
- *Frequency:* Below average, indicating they are not frequent shoppers.
- *Recency:* Low, meaning it's been a while since their last purchase.
- *Mean Products:* Below average, suggesting smaller basket sizes.
- *Mean Interval:* Below average, which might indicate they used to purchase in bursts but have since stopped.
- *b_a_12:* Very low, showing a strong preference for ordering before midday.
- *e_l_week:* Low, indicating a preference for ordering early in the week.
- *Summary:* This is a group of lapsing or at-risk customers who are likely to churn. They haven't shopped in a while, and when they did, their orders were infrequent and small. Their defining characteristic is a strong preference for shopping in the morning and early in the week. A re-engagement campaign would be suitable for them.

Cluster 2: Loyal Regulars (Potentially Lapsing)
- *Frequency:* Very high, by far the most frequent shoppers in the dataset.
- *Recency:* Low, indicating it has been some time since their last purchase.
- *Mean Products:* Slightly above average, suggesting healthy order sizes.
- *Mean Interval:* Very low, confirming they make purchases very close together.
- *b_a_12:* Neutral, with no strong preference for ordering before or after midday.
- *e_l_week:* Neutral, with no strong preference for the day of the week.
- *Summary:* This is your core group of loyal and highly engaged customers. They shop far more often than anyone else. However, their low recency is a red flag, suggesting that these normally active customers may be at risk of lapsing. They are prime candidates for a loyalty program or a "we miss you" campaign to bring them back.

Cluster 3: New & Cautious Shoppers
- *Frequency:* Below average, indicating infrequent purchasing behavior.
- *Recency:* Very high, tied for the most recent shoppers.
- *Mean Products:* Very low, indicating they buy very few items per order.
- *Mean Interval:* Very high, showing the longest time between purchases.
- *b_a_12:* Below average, suggesting a slight preference for ordering before midday.
- *e_l_week:* High, indicating a tendency to order later in the week.
- *Summary:* This group likely represents new customers making small, trial purchases. They are very recent but have bought very little. Their high interval between orders confirms their infrequent nature. The goal with this segment is to nurture them and encourage a second, larger purchase, perhaps with a targeted follow-up offer.

Cluster 4: Lapsing Afternoon Shoppers
- *Frequency:* Low, indicating they are infrequent buyers.
- *Recency:* Low, meaning it has been a while since they last shopped.
- *Mean Products:* Average, suggesting typical order sizes.
- *Mean Interval:* Below average, showing relatively short gaps between their past purchases.
- *b_a_12:* Very high, indicating a very strong preference for shopping after midday.
- *e_l_week:* Slightly high, suggesting a tendency to order later in the week.
- *Summary:* This group is similar to Cluster 1 but with a key difference in their shopping time. They are infrequent, lapsing customers with a very distinct habit of ordering in the afternoon and toward the end of the week. A targeted re-engagement campaign sent in the afternoon might be effective at winning them back.

**Marketing Strategies for Customer Segments**

Cluster 0: Recent, Infrequent Shoppers:
*This group consists of recent customers who buy a good number of items but have long gaps between purchases. They are most active after midday and early in the week.*

- *Post-Purchase Nurturing:* Implement an automated email series after their first purchase. The first email should confirm the order, the second could offer tips on using the products, and a third could suggest complementary items to encourage a second purchase sooner.
- *Incentivize the Next Purchase:* A week or two after their order, send a time-sensitive offer (e.g., "15% off your next order, valid for 14 days") specifically designed to shorten their long purchasing interval.
- *Time-Targeted Communication:* Schedule promotional emails and app notifications to be sent on Monday and Tuesday afternoons to align with their established shopping habits.

Cluster 1: At-Risk Morning Shoppers:
*These are lapsing customers who made small, infrequent orders. Their defining trait is a strong preference for shopping in the morning and early in the week.*

- *Win-Back Campaigns:* Launch a "We Miss You!" campaign with a compelling, high-value offer (e.g., a steep discount or a free product with their next purchase) to grab their attention.
- *Precision Timing:* Send all re-engagement communications during their preferred window: morning hours (e.g., 9-11 AM) on Monday or Tuesday.
- *Feedback Collection:* Send a survey asking for feedback on their experience, with a small incentive for completion. This can provide valuable insights into why they stopped purchasing and make them feel heard.

Cluster 2: Loyal Regulars (Potentially Lapsing):
*This is your most valuable group of highly frequent shoppers. However, their low recency is a major concern, indicating they are at high risk of churning.*

- *VIP Treatment & Exclusivity:* Acknowledge their loyalty with exclusive perks. Grant them early access to new product launches or sales events. Send them a personalized "VIP Thank You" offer that is more generous than standard promotions.
- *Proactive Re-engagement:* Don't wait. Send a personalized email or notification that says, "We've noticed you haven't been around lately. Here's a special something to welcome you back."
- *Highlight Loyalty Benefits:* Remind them of their accumulated loyalty points or the benefits of their tier in your rewards program to encourage them to return and use their status.

Cluster 3: New & Cautious Shoppers:
*This group likely represents new customers who made a single, small "trial" purchase. They are recent but have the longest interval between potential purchases and buy few items.*

- *Build Trust & Confidence:* Send content that reinforces their purchase decision. This could include customer testimonials, 5-star reviews, or blog posts showcasing the benefits of the product they bought.
-*Encourage a Second Purchase:* After they've had time to experience their first product, offer a special discount on a related category or a "starter kit" bundle to encourage a larger, more confident second order.
- *Brand Introduction:* Add them to a "Welcome" email flow that introduces the brand's story, values, and what makes your products unique to build a connection beyond just one transaction.

Cluster 4: Lapsing Afternoon Shoppers:
*This is another group of at-risk customers who are lapsing. Their key characteristic is a very strong preference for shopping in the afternoon and towards the end of the week.*

- *Afternoon Flash Sales:* Create limited-time flash sales that run exclusively in the afternoons on Thursdays and Fridays. Promote these heavily to this specific segment to create urgency and align perfectly with their habits.
- *Personalized Reminders:* Use their browsing history or past purchases to send targeted "Still thinking about it?" or "Back in stock!" notifications during their preferred afternoon shopping window.
- *Weekend-Focused Promotions:* Frame offers around the upcoming weekend (e.g., "Get ready for the weekend! Enjoy 20% off."). Send these communications on Thursday or Friday afternoon to capture their attention when they are most likely to be active.
