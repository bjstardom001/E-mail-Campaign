# Introduction
This project analyzes email marketing campaign performance using **SQLite**.  The dataset consists of multiple tables tracking campaign sends, user engagement, clicks, and unsubscribes. 
# Background
The goal is to answer a series of analytical questions (based on a LinkedIn SQL practice challenge) and extract actionable insights.

**The questions I answered through my SQL queries were:**

1. What is the overall unsubscribe rate?
2. Which campaigns had the highest and lowest unsubscribe rate?
3. Do certain campaign categories(promotion,newsletter,re-arrangement,announcement) have higher unsubscribe rates?
4. What is the relationship between open rate and unsubscribe rate?
5. Which region(s) or device types(s) have the highest unsubscribe rate?
6. How long after signing up do users typically unsubscribe?
7. Does send time(hour of day) impact engagement or unsubscribe rates?
8. What are the common unsubscribe reasons, and do they vary by campaign category?
9. Who are the "at-risk" users?
10. What's your final story?

# Tools I used

**SQL:** The main tool used for my analysis allows me to query the database and generate results.

**SQLite:** The database management system I used.

**Power BI:** The tool used for the visualization of my data.

# The Analysis

The analysis shows how each question was being answered:

**1. What is the Overall_unsubscribe_rate?**

```
SELECT

ROUND(SUM(total_unsubscribes)*100.0/ SUM(total_sent),2) AS Unsubscribe_rate

FROM 

Campaign_Performance;
```

**2. Which Campaign has the highest and Lowest unsubscribe rate?**

```
SELECT 
 Campaigns.campaign_name,
 ROUND(SUM(total_unsubscribes)*100.0/ SUM(total_sent),2) AS Unsubscribe_rate
 
 FROM
 Campaign_Performance
 LEFT JOIN 
 Campaigns ON Campaign_Performance.campaign_id=Campaigns.campaign_id
 GROUP BY 
 campaign_name
 ORDER BY
 unsubscribe_rate DESC;
```

**3. Do certain campaign categories (promotion, newsletter, re-engagement, announcement) have higher unsubscribe rates?**

``` 
 SELECT
 Campaigns.category,
 ROUND(SUM(total_unsubscribes)*100.0/ SUM(total_sent),2) AS Unsubscribe_rate
 
 FROM
 Campaign_Performance
 LEFT JOIN 
 Campaigns ON Campaign_Performance.campaign_id=Campaigns.campaign_id
 GROUP BY 
 category
 ORDER BY
 unsubscribe_rate DESC;
```

**4. Which region(s) or device type(s) have the highest unsubscribe rate?**

 ```
 SELECT
 region,
 device_type,
 SUM(unsubscribe) AS Unsubscribe_rate
  
 FROM
 Users
 LEFT JOIN
 Email_Engagement ON users.user_id=Email_Engagement.user_id
 GROUP BY
 region
 ORDER BY
 Unsubscribe_rate DESC;
```

**5. How long after signup do users typically unsubscribe?**

 ```
 SELECT
 ROUND(AVG(JULIANDAY(Unsubscribes.unsubscribe_date)- JULIANDAY(users.signup_date)),1) AS Avg_days_to_unsubscribe
 
 FROM 
 Users
 LEFT JOIN
 Unsubscribes ON users.user_id=Unsubscribes.user_id;
```

**6. Does send time(hour of day) affect engagement or unsubscribe rate?**

``` 
 SELECT
 send_hour,
 ROUND(SUM(total_opens)*100.0/ SUM(total_sent),2) AS Open_rate,
 ROUND(SUM(total_unsubscribes)*100.0/ SUM(total_sent),2) AS Unsubscribe_rate
 
 FROM
 Campaign_Performance
 LEFT JOIN
 Campaigns ON Campaign_Performance.campaign_id=Campaigns.campaign_id
 GROUP BY
 send_hour;
```

**7. What are the most common unsubscribe reasons, and do they vary by campaign category?**

 ```
 SELECT 
 Campaigns.category,
 Unsubscribes.reason,
 COUNT(*) AS total_unsubscribes
 
 FROM 
 Unsubscribes
 LEFT JOIN
 Campaigns ON Unsubscribes.campaign_id=Campaigns.campaign_id
 GROUP BY 
 category,reason
 ORDER BY 
 total_unsubscribes DESC;
```

**9. Who are "at-risk" users?**

 ```
 SELECT 
    user_id,
    COUNT(campaign_id) AS total_sent,
    SUM(opened) AS total_opened,
    ROUND(
      SUM(opened) * 100.0 / COUNT(campaign_id),
      2
    ) AS open_rate
FROM email_engagement
GROUP BY user_id;
```

# Key Findings

**1. Overall Campaign Performance**

* An open rate of 59.72% indicates strong engagement, suggesting that subject lines and send timing are effective.

* A click rate of 18.07% is a healthy rate, meaning a fair proportion of opened emails lead to user action.

* Unsubscribe Rate of 5.05% is slightly high; ideally, unsubscribe rates should be below 2%. Indicates potential audience fatigue or content misalignment.

**2. Campaign-Level Insights**

* Highest Unsubscribe Rates: Campaign 2 (5.87%), Campaign 1 (5.80%), Campaign 14 & 8 (5.73%), Campaign 18 (5.47%). These campaigns may have had content that didn’t resonate with subscribers or were sent too frequently.

* Lowest Unsubscribe Rates: Campaign 5 (4.73%), Campaign 12 (4.27%), Campaign 11 (4.13%), Campaign 13 (3.40%). These performed better; their tone, design, or targeting could be used as a benchmark.

**3. Category-Level Insights**

* Promotion Emails: Highest unsubscribe rate at 5.36%, implying users might feel oversaturated with marketing offers.

* Announcement Emails: 5.27%  Slightly lower but still high, suggesting these might not always be relevant.

* Re-engagement (4.87%) and Newsletter (4.82%) show better retention, indicating these formats are more appreciated.

**4. Device Type Insights**

* Tablet: 5.19% unsubscribe rate (highest by device).

* Mobile: 5.00%, Desktop: 4.97%. Relatively close figures, but a higher tablet rate may reflect poor optimization for that screen size or user experience issues.

 **5. Time-of-Day Trends**
 
* Peak Unsubscribe Times: Around 9 AM (5.40%) and 7 PM (5.70%).

* Lowest Unsubscribe: Around 7 AM (4.13%) and 8 PM (4.73%). Sending emails later in the evening or early in the morning may lead to higher unsubscribe rates, possibly due to timing fatigue or off-peak relevance.

# Conclusion and Recommendation

Overall, the email campaigns performed fairly well with a 59.72% open rate and an 18.07% click rate, showing that people are generally engaging with the emails. However, the 5.05% unsubscribe rate is on the high side, which suggests that while many people open and click, a good number are also choosing to opt out.

A closer look shows that Campaigns 1, 2, 8, 14, and 18 had the highest unsubscribe rates. This means some of the messages or their timing may not have connected well with the audience. These campaigns might have been too frequent, too promotional, or not relevant enough. It’s important to review them in detail and check what was different about their tone, design, or timing compared to other better-performing campaigns. Learning from these will help improve future results.

Timing also plays a role in people’s reactions. The data shows that unsubscribes peak around 9 AM and 7 PM, while rates are lower around 7 AM and 8 PM. This suggests that people are more likely to unsubscribe when they receive emails at certain times of the day, possibly when they are busy, getting to work, or getting home from work. Adjusting send times to early in the morning or late evening could help reduce unsubscribes and improve engagement.

When it comes to content type, promotional emails had the highest unsubscribe rate (5.36%), followed by announcement emails (5.27%). This indicates that subscribers may feel overwhelmed or uninterested in too many sales or promotional messages. On the other hand, newsletters and re-engagement emails performed better, meaning people respond more positively to informative or relationship-building content. Balancing  email mix with fewer promotions and more value-driven updates will likely keep the audience more interested and loyal.

Device analysis shows that tablet users had the highest unsubscribe rate (5.19%), slightly higher than mobile (5.00%) and desktop (4.97%). This might point to a layout or design issue on tablet screens. Ensuring that emails are mobile-friendly and optimized for all devices,  especially tablets, can create a smoother experience and reduce frustration that leads to unsubscribes.

Interestingly, Campaigns 5, 11, 12, and 13 had the lowest unsubscribe rates. These campaigns clearly struck the right balance  perhaps in message tone, visual appeal, or relevance. Studying these successful campaigns will be valuable. We should try to identify what made them effective,  whether it was the subject line, timing, or the way the message was presented, and use those strengths as a guide for future campaigns.

Lastly, the overall unsubscribe pattern may also reflect that some subscribers are receiving emails that don’t match their interests. This can happen when all users receive the same type of content without proper audience segmentation. To address this, we should consider dividing our subscribers into smaller groups based on behavior, interests, or past engagement (for example, frequent buyers, new users, inactive members). Sending more personalized content to each group can make our emails feel more relevant and reduce opt-outs.

In summary, the campaigns are performing well in terms of reach and engagement, but improvements are needed in content relevance, timing, and audience targeting. By reviewing high-unsubscribe campaigns, adjusting email timing, focusing on value-based content, optimizing for devices, and improving segmentation, future campaigns can achieve higher engagement and lower unsubscribe rates. Continuous testing and monitoring will also help refine the strategy and ensure long-term improvement.


