# Data Project

![Logo](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/Screenshot%202024-11-23%20222057.png)

## A/B Testing: Digital Marketing

### Should one use Social Media or Email?
In the fast-paced world of digital marketing, understanding which campaign strategies yield the best results is critical for maximizing ROI and resource efficiency. With numerous channels such as social media, email, and search ads vying for budget allocation, identifying the most effective approach can be challenging. A/B testing emerges as a powerful, data-driven solution to this problem by allowing marketers to compare the performance of different strategies under controlled conditions.

By systematically analyzing metrics like Click-Through Rate (CTR), conversions, and AdSpend, A/B testing helps businesses make informed decisions. This project leverages A/B testing to evaluate the effectiveness of various campaign channels, enabling a digital marketing agency to optimize its strategy, reduce inefficiencies, and achieve measurable outcomes.


This project analyzes the performance of different campaign channels for a digital marketing agency to identify the most effective one using A/B testing. Leveraging the **Digital Marketing Campaign Dataset** from Kaggle, the analysis focuses on user behavior metrics such as Click-Through Rate (CTR), Ad Spend, income generated and conversions.

---

## Table of Contents
1. [Problem Statement](#problem-statement)
2. [Dataset Overview](#dataset-overview)
3. [Methodology](#methodology)
    - [Data Cleaning](#data-cleaning)
    - [Data Type Conversion](#data-type-conversion)
    - [Descriptive Analysis](#descriptive-analysis)
    - [Marketing Channel and Engagement Analysis](#marketing-channel-and-engagement-analysis)
    - [A/B Testing Implementation](#ab-testing-implementation)
4. [Insights and Findings](#insights-and-findings)
5. [Recommendations](#recommendations)
6. [Conclusion](#conclusion)

---

## Problem Statement

Digital marketing agencies invest in multiple channels (social media, email, search engines, etc.) to attract users. Determining which campaign channel yields the best return on investment (ROI) is crucial for optimizing strategies and budgets. This project aims to identify the most effective channel by:
- Analyzing user engagement metrics.
- Implementing A/B testing to compare campaign performance statistically.

---

## Dataset Overview

The **Digital Marketing Campaign Dataset** contains user engagement metrics, including:
- **AdSpend**: Budget spent on ads.
- **TimeOnSite**: Average time users spent on the website.
- **ClickThroughRate (CTR)**: Ratio of clicks to ad impressions.
- **Conversions**: Number of successful user actions (e.g., purchases).

---

## Methodology

### Data Cleaning
- Handled missing values using appropriate techniques (dropping or filling based on context).
- Removed outliers from critical columns (`AdSpend`, `TimeOnSite`) using the Interquartile Range (IQR) method.
```python
# Check for missing values
missing_values = df.isnull().sum()
print("Missing values:\n", missing_values)

# Handling missing values (drop)
df = df.dropna()  # Alternatively, you could use .fillna() with appropriate values

# Identify outliers in 'AdSpend' and 'TimeOnSite'
sns.boxplot(data=df['AdSpend'])
plt.show()

sns.boxplot(data=df['TimeOnSite'])
plt.show()

# Removing extreme outliers
for column in ['AdSpend', 'TimeOnSite']:
    q1 = df[column].quantile(0.25)
    q3 = df[column].quantile(0.75)
    iqr = q3 - q1
    lower_bound = q1 - 1.5 * iqr
    upper_bound = q3 + 1.5 * iqr
    df = df[(df[column] >= lower_bound) & (df[column] <= upper_bound)]
```
![Missing Values](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/missing_values.png)


![AdSpend](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/adspend.png)


![TimeOnSite](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/timeon_site.png)

### Data Type Conversion and Feature Engineering
```python
# Ensure correct data types
df['CustomerID'] = df['CustomerID'].astype(str)
df['Age'] = pd.to_numeric(df['Age'], errors='coerce')

# Feature Engineering: Create CostPerClick
df['CostPerClick'] = df['AdSpend'] / df['ClickThroughRate']
```
### Descriptive Analysis
```python
# Demographic Analysis

# Demographic distributions
plt.figure(figsize=(10, 5))
sns.histplot(df['Age'], bins=20, kde=True)
plt.title('Age Distribution')

# Gender distribution
gender_counts = df['Gender'].value_counts()
plt.figure(figsize=(5, 5))
sns.barplot(x=gender_counts.index, y=gender_counts.values)
plt.title('Gender Distribution')
plt.show()
```
![Age Distribution](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/age_distn.png)
![Gender Distribution](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/gender_distn.png)

### Marketing Channel and Engagement Analysis
```python
# Marketing channels and conversion rates
channel_conversion = df.groupby('CampaignChannel')['ConversionRate'].mean()
print("Conversion rates by channel:\n", channel_conversion)

# Engagement metrics
engagement_metrics = df[['WebsiteVisits', 'PagesPerVisit', 'TimeOnSite', 'SocialShares']].mean()
print("Average engagement metrics:\n", engagement_metrics)
```
![Marketing Channels and Engagements](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/marketing_engaging.png)



### A/B Testing Implementation

Setting Up A/B Test Groups
```python

# Creating test and control groups based on 'CampaignChannel'
group_A = df[df['CampaignChannel'] == 'Email']
group_B = df[df['CampaignChannel'] == 'Social Media']
```
Defining Metrics to Track
```python

# Primary Metric: Conversion Rate
# Secondary Metrics are ClickThroughRate, EmailOpens, PagesPerVisit

primary_metric_A = group_A['ConversionRate'].mean()
primary_metric_B = group_B['ConversionRate'].mean()
print(f"Conversion Rate (Email): {primary_metric_A}")
print(f"Conversion Rate (Social Media): {primary_metric_B}")
```
![Metrics to Track](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/metrics_to_track.png)

Secondary Metrics Comparison
```python
secondary_metrics = { 'ClickThroughRate': [group_A['ClickThroughRate'].mean(),group_B['ClickThroughRate'].mean()],
                     'EmailOpens': [group_A['EmailOpens'].mean(),group_B['EmailOpens'].mean()],
                     'TimeOnSite': [group_A['TimeOnSite'].mean(),group_B['TimeOnSite'].mean()]}
#  Creating a data frame for easy  visualization
secondary_metrics_df = pd.DataFrame(secondary_metrics, index =['group_A', 'group_B'])
print('Secondary Metrics Comparison:\n', secondary_metrics_df)

# Visualizing secondary metrics 
secondary_metrics_df.plot(kind='bar', figsize=(10, 6))
plt.title('Secondary Metrics Comparison Between Groups')
plt.ylabel('Average Values')
plt.show()
```
![Secondary Metrics](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/secondary_met.png)

![Secondary Metrics Visual](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/secondary_metrics.png)

Conduct Statistical Testing
```python
# Hypothesis Testing

# T-test to compare conversion rates between the two groups
t_stat, p_value = stats.ttest_ind(group_A['ConversionRate'], group_B['ConversionRate'])
print(f"T-statistic: {t_stat}, P-value: {p_value}")

# Confidence Interval Calculation
conf_int_A = stats.norm.interval(0.95, loc=primary_metric_A, scale=stats.sem(group_A['ConversionRate']))
conf_int_B = stats.norm.interval(0.95, loc=primary_metric_B, scale=stats.sem(group_B['ConversionRate']))
print(f"95% CI for Email: {conf_int_A}")
print(f"95% CI for Social Media: {conf_int_B}")

# Effect Size Calculation (Cohen's d)
effect_size = (primary_metric_B - primary_metric_A) / np.sqrt((group_A['ConversionRate'].std() ** 2 + group_B['ConversionRate'].std() ** 2) / 2)
print(f"Effect Size (Cohen's d): {effect_size}")
```
![T-Test](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/adf.png)

Analyzing Results
```python
# Conversion Rate Comparison

# Plot conversion rates
conversion_rates = pd.DataFrame({
    'CampaignChannel': ['Email', 'Social Media'],
    'ConversionRate': [primary_metric_A, primary_metric_B]
})

sns.barplot(data=conversion_rates, x='CampaignChannel', y='ConversionRate')
plt.title('Conversion Rate Comparison')
plt.show()
```
![Conversion Rate comparison](https://github.com/SammieBarasa77/ab_testing/blob/main/assets/images/conversion_rate_last.png)

---

## Insights and Findings

Channel Performance

The conversion rates for Email and Social Media channels are nearly identical, indicating similar efficiency in converting users into customers.
Despite comparable conversion rates, other factors like cost-per-click and audience engagement may vary and require deeper analysis.
Demographic Insights:

## Recommendations

Cost Optimization
Since Email and Social Media have comparable conversion rates, prioritize the channel with lower AdSpend per conversion for cost-effective campaigns.
Use A/B testing on Email campaigns to experiment with personalized content and time-based delivery for higher impact.
Audience-Specific Campaigns:

Focus Social Media campaigns on younger audiences, leveraging platforms like Instagram or TikTok to maximize engagement.
Use Email campaigns for professional or older demographics who may prefer direct communication.
Experiment with Underperforming Metrics:

Optimize time-on-site for both channels by improving landing page design and offering targeted content.
Conduct additional A/B tests to evaluate new strategies, such as interactive social media ads or personalized email subject lines.

## Conclusion
This analysis demonstrates that while both Email and Social Media channels perform similarly in terms of conversion rates, further optimization is required to maximize efficiency. By focusing on cost-effective strategies, tailoring campaigns for specific demographics, and continuously testing new approaches, the digital marketing agency can enhance ROI and overall campaign performance. Future efforts should incorporate user feedback, real-time analytics, and refined testing to stay adaptable in a competitive landscape.

