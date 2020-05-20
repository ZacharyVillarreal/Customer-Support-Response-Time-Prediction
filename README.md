<p align='center'>
<img src="images/Google-Playstore-Full.png" width="800" height="400"> 
</p>

# I Need Help Fast! (An Analysis of Customer Support on Twitter)
-----
**Company Support Team Response Time Predictor**<br>
Zachary Villarreal
<br>
<br>

## Table of Contents
* [Background](#Background)
    * [Project Description](#Project-Description)
    * [Motivation](#Motivation)
* [Data](#Data)
    * [Pipeline](#Pipeline)
    * [Attributes](#Attributes)
* [Analysis](#Analysis)
    * [Exploratory Data Analysis](#Exploratory-Data-Analysis-(EDA))
    * [What About Response Times?](#What-About-Response-Times?)
* [Topic Modeling](#Hypothesis-Testing)
    * [Preparation](#Preparation)
    * [T-Distributed Stochastic Neighbor Embedding](#T-Distributed-Stoachastic-Neighbor-Embedding)
* [Predictive Modeling](#Second-Hypothesis-Testing)
    * [Models](#Models)
* [Conclusion](#Conclusion)



<a name="background"></a>
## Background

### Project Description
---
Customer service is the assistance that a company will provide either before, during, or after a transaction is complete. Customer service is usually done through a company's website or via telephone, but as social media platforms become more commonplace, one can find these services available on these various platforms. One platform that has seen an influx of customer service interactions is Twitter. Twitter is an American social media platform on which users interact with others through shortened messages or "tweets." The content in tweets related to these specific inquiries often varies in word usage. However, they seem to be depicting some underlying subject when analyzed further.

<a href="#Table-of-Contents">Back to top</a>

<p align='center'>
<img src="images/customer_service_team.png" width="400" height="300"> 
</p>

### Motivation
---
We, as a society, have had a steady increase in participation in online transactions. As a result, our interactions with customer teams have also increased. We have all experienced longer than wanted wait times for a response from said teams. I, along with what I am sure is a substantial proportion of people, would like to understand why these wait times are so long and why certain people or companies receive a response much faster than the test. My goal is to analyze what goes into obtaining a fast versus a slow response from a customer support team on Twitter. Specifically, I will be looking at the behaviors of text and the relationship between their respective wait times.

Not only are customers seeking knowledge on how to receive a faster response, but customer support teams also try to see how their times match up against other companies within the same operating space. These teams are often under a high load of inbound requests from their customers and would like to know the analytics behind their responses. Are they targetting specific individuals faster than others, are they responding to those who include specific words within their requests, or are they just responding to those in the order they come in? This project will attempt to answer those questions, as well.

<a href="#Table-of-Contents">Back to top</a>

## Data

### Pipeline
---
**Data allocation**: 
* Twitter Customer Support Data: [Customer Support on Twitter](https://www.kaggle.com/thoughtvector/customer-support-on-twitter?)

**Data processing**:<br>
* The data was downloaded as a CSV (comma-separated values file).
* Pandas was used to import the data and aggregate it into one data frame.
* Exploratory Data Analysis and was performed once the data was cleaned.
    * The code to clean the data can be found [here](src/Data_Cleaning.ipynb).
    * The code depicting EDA can be found [here](src/EDA.ipynb)
* Visualization was completed using Matplotlib and Seaborn.
* Text Preprocessing was completed after EDA.
    * The code to conduct Text Preprocessing can be found [here](src/Helper.ipynb)
* Topic Modeling was completed after text was preprocessed.
    * The code to conduct Topic Modeling can be found [here](src/Topic_Modeling.ipynb)
* Classification Models were built using topics generated from topic modeling.
    * The code to conduct Modeling can be found [here](src/predictive_modeling.ipynb)
   

<a href="#Table-of-Contents">Back to top</a>

### Attributes
---
**Twitter Customer Service Data Set:**

| Feature Name      | Description                           | Unique Value Count |
|-------------------|:-------------------------------------:|-------------------:|
|Customer_Tweet_Id  | 6-Digit User I.D. Code for Anonymity  | 502967             |
|     Day_Tweeted   | Day of the Week the Tweet Was Sent  (Mon-Sun)| 7           |
|Customer_Tweet_Text|         Customer's Inquiry Text       | 605716             |
| Cluster Topic     | Text Topic Assigned Using Clustering  | 3                  |
|    Company_Name   | Name of Customer Service Team         | 108                |
|Company_Reponse_Text| Customer Service's Reponse Text      | 605130             |
| Minutes to Respond| How Long it Takes For a Response (Min)| 8536               |
| Response Speed    | Target Variable (Fast or Slow)        | 2                  |



* The data frame contains 689,281 rows and 9 features, explained in the table above.

<a href="#Table-of-Contents">Back to top</a>

## Analysis

### Exploratory Data Analysis (EDA)
---

As we set on this exploration of our data, I believe we should initially take a look at the top ten companies who currently exhibit the highest number of customer service inquiries. <br>
<br>

<p align='center'>
<img src="graphs/Company_Support_Team_Tweet_Counts.png" width="700" height="420"> 
</p>

It can be observed that both AmazonHelp and AppleSupport far outweigh their competition, as far as the total number of inquiries via customers. But, one could argue that these counts are directly related to the total number of customers these companies have. I believe that this assumption is both fair and most likely valid. As a result, I want to look more into the distribution of the total number of tweets related to customer service over one week.

<p align='center'>
<img src="graphs/Tweets_by_Weekday.png" width="700" height="420"> 
</p>

Interesting enough, there is little to no variance in the number of tweets related to customer support between the days of the week. However, let's dive a little deeper because we want to receive a more detailed understanding of these tweets. Next, I am interested in looking into the distribution of tweets over the hours of the days of the week.

<p align='center'>
<img src="graphs/Distribution_of_tweets.png" width="800" height="420"> 
</p>

We can infer that although the hour of the day does seem to have an impact on the number of tweets, the overall distribution of these tweets is constant throughout the week. While this data is all great to understand the magnitudes of the total distribution of tweets over each specific company and how time affects these inquiries, we are more interested in the information surrounding response times.

<a href="#Table-of-Contents">Back to top</a>

### What About Response Times? 

As mentioned above, this project is specifically looking at how we can predict a *fast* versus *slow* response time. As a result, I am interested in looking into the distribution of these response times. How each company specifically holds up to its competitors in terms of response speed on Twitter.

However, I think it is crucial first to determine what makes a customer support tweet response *fast* versus *slow*.  To do this, I need to look at the distribution of these times for all companies.

<p align='center'>
<img src="graphs/Response_time_general.png" width="800" height="420"> 
</p>

Wow! Overall, the distribution of these responses is skewed towards the left, which means that, on average, a relatively short amount of time needs to have waited to receive a response. However, there seems to be several instances where the amount of time exceeds 400,000 minutes, which is 277 days, not good at all! 

As I mentioned before, I want to be able to differentiate between fast and slow response times, to do that I believe the best method I can implore is taking the median of these times and setting that as the divider. Why am I taking the median and not the mean? Because, as I previously mentioned,  there were occasions where the total time waited far exceeded the norm and thus would throw off the total's mean since the mean is not-resilient to large outliers. As a result, I went with the median. 

`The median time for a response from a customer support team on Twitter is 27.0 minutes.` 

One might wonder if 27 minutes would be a good cutoff for these target times or response speed, most customers would be ecstatic with any response time under one hour! I understand that 27 minutes might be too low, however, as we will see later in this project, this cutoff point produced an even split between the number of fast versus slow responses for all companies.

Let's look at how this newly created `response speed` value is distributed over the companies with the ten highest number of customer inquiries.

<p align='center'>
<img src="graphs/Company_Response_Times.png" width="800" height="420"> 
</p>

Interesting, here we can see that the majority of responses by AmazonHelp, fall below the 27.0-minute mark, while AppleSupport's majority response time lands above the cutoff. 
