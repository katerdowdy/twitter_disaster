# Twitter Alert: Mapping Natural Disasters with Social Media

## Executive Summary
Social media platforms such as Twitter have become an integral part of everyday life in the 21st century, connecting users across the world and reducing the virtual distances between them to zero. With the growing volume of people using social media on a daily basis, could Twitter be leveraged to crowdsource information about a rapid-onset disaster, especially under situations unmonitored by official alert systems or when traditional methods of communication fail? 

For this project, we explored the proof of concept for a disaster alert system using Twitter. We used Twitter’s API and traditional web scraping methods to gather data from areas impacted by disasters; cleaned and evaluated trends in text using Natural Language Processing (NLP); and modeled on pre- and post-disaster Twitter content to detect shifts in Twitter content under emergency situations. Finally, we developed a basic prototype for filtering real-time tweets based on disaster-associated words.

## Problem Statement: 
> Design and implement a web-tool or an app for rapid alert and notification about a disastrous event, in close to real time. The tool will alert about the event, similar, for example, to earthquake or tsunami warnings that appear on Google`s home page immediately after a major disaster. While traditional methods for alerting on such events rely on official information derived from official sources (e.g. USGS), this tool will utilize social media activity to identify these events and alert when an event first occurs.

## Contents
In this repo, you will find the following the following notebooks:

A. Data Collection

B. Cleaning/ Natural Language Processing/ Modeling

C. Streaming Twitter Alerts

as well as presentation slides, our list of FEMA disasters we pulled from for our analyses, and the scraped tweets.

This project was developed by [Ben Brown](https://www.linkedin.com/in/benjamin-brown-3aa0925b/), [Ryan Dorris](https://www.linkedin.com/in/ryan-dorris-731151108/), [Kate Dowdy](http://www.katerdowdy.com/), [Ryan Fuller](https://www.linkedin.com/in/ryan-r-fuller/), and [Sam Singh](https://www.linkedin.com/in/simarpreet-singh1/) as part of the Data Science Immersive program at General Assembly in January, 2019.

Free custom access keys required to run these notebooks: Twitter API ([request access here](https://developer.twitter.com/en/apply-for-access.html)); GeoNames ([request access here](http://www.geonames.org/)).

Software/packages required for running these notebooks: Python, Pandas, Scikit Learn, Time, Tweepy, Json, Datetime, NLTK

Webscraping: BeautifulSoup, Selenium

## Proof of Concept
> To what extent can/should social media be trusted to alert on the presence and spread of a natural disaster?

Our approach to this problem began with a few key considerations:

**1. What social media would make sense to use, or would be possible to use, for an alert system?**

At the beginning of the project, we requested API access from Twitter, Facebook, and Instagram. Only Twitter granted us API access and did so with almost no turnaround time. We felt Twitter would be the best platform to use for alerts in realtime given the public nature of Twitter as well as the character limit, and thought it would be less realistic for people in emergency situations to spend the time writing a Facebook post or applying an Instagram filter to their photo of a burning house. 

**2. What disasters would make the most sense for an alert system?**

While weather alerts already exist for major events such as hurricanes and snow storms, the rapid spread of wildfires and unexpected flooding due to severe storms are more difficult to predict and would be an ideal use case for a social media alert system. 

One major limitation of relying on social media to track disasters is the reliance on cell phone towers: in the case of the Woolsey wildfire in November, 2018 in California, towers near Los Angeles and surrounding counties were impacted and as a result, there are no almost no tweets from the first day of the fire in those areas. 

Ultimately, we decided not to filter our data collection based on the type of disaster in order to collect as much data as possible. We limited our research to disasters in the United States and used [FEMA's website](https://www.fema.gov/disasters) to pull a full list of federally declared disasters that had occurred since 2006 (when Twitter was launched), and prioritized collecting data on the most recent disasters.

**3. How would you detect signal from noise to create an effective alert system?**

We pursued two approaches to this part of the project: 

1. The easy way: Guessing words related to disasters and using those to filter real-time tweets. This was one of our strategies for developing our prototype of the realtime alert system.

2. The hard way: Developing a model based on pre-disaster and post-disaster Twitter activity to determine the coefficients associated with post-disaster content. We felt exploring the actual data was important, if for no other reason than to give us an idea of how much noise we would need to filter through to find tweets that might indicate disasters. This part of the project is further explored in our data collection, cleaning/NLP, and modeling sections. With more time to develop this tool,  we would integrate this model into the live tweets filter to better analyze and pick up on patterns in Twitter activity and content.

# Data Collection
We quickly discovered that Twitter's REST API (for historic tweets) only extends 30 days into the past. Thankfully, as of completing this project, no major natural disasters had occurred in the U.S. in the past month, so we needed to find another way to collect information from past disasters. We wrote code to scrape Twitter with Selenium and BeautifulSoup and collected 44,472 tweets from 2 days prior and 2 days after (including the day of) the natural disaster for 497 unique counties affected by major disasters since 2012. You can see a summary of the locations and types of disasters represented by the collected tweets [here.](https://public.tableau.com/profile/simarpreet.singh6953#!/vizhome/Disaster_Predict/Sheet22)

# Cleaning/NLP

After scraping the Tweets, we decided to remove everything that was not words or single whitespaces. Using regex, all links, numbers, punctuation, and multiple spaces were removed, leaving just the words of each tweet.

These “cleaned” tweets were then tokenized and lemmatized, using those tools from `nltk`, we generated both a `CountVectorizer` and `TfidfVectorizer`.

## Modeling

**Baseline**: Approximately 46.9\% of tweets took place after the disaster began, giving that accuracy as our baseline - any model with accuracy above that threshold is an improvement over random assignment.

### Finding Optimal Vectorizer and Model
Each of these were fit to a training subset of our data (two-thirds of all gathered data), and used to transform the testing split of our data.

We then built a pipeline that ran over the five classification models listed below:

- Logistic Regression
- *k* Nearest Neighbors
- Random Forest
- AdaBoost
- Support Vector Machine Classification

After testing various parameter combinations, all models had accuracy scores in the mid-50\% range, with the best performance (and shortest computation time) found with Random Forest, using a TF-IDF Vectorizer.

### Tuning TF-IDF/Random Forest

The optimum accuracy score achieved was 59.6\%, found with the following parameters:

- TF-IDF
    - Features: 30,000
    - *n*-gram Range: (1,1) - (1,4)
- Random Forest
    - Number of Trees: 100
    - Max Depth: None
    - Scoring: Gini

For this type of classification model, accuracy isn't necessarily the best metric to optimize for: the case of a disaster alert system, you would want to minimize the number of false negatives (ex. predicting no disasters when there actually is a developing disaster.) Here are the other metrics for our optimum model on our test set:

**True Negatives:** 5175

**True Positives:** 2726

**False Negatives:** 3515

**False Positives:** 1926

## Product Prototype
> How might a Twitter alert system work in real life?

We secured access to Twitter's Streaming API and built a function to stream live tweets generated from locations within a specified bounding box of geographic coordinates (included in our code is a method to generate the bounding box coordinates using Geopy and GeoNames.) There are several limitations of relying on Twitter's Streaming API that would make this tool a useful supplement to existing disaster alerts but **not** something you would rely on alone. For one: Twitter's Streaming API produces at maximum roughly 1% of tweets generated in realtime. A real alert system would require access to Twitter's Firehose API which is not free. For our prototype, we found using a smaller bounding box (ex. 30 miles around a city) with our limited Streaming API would generate a more concentrated volume of tweets that might better tell a story about a developing situation. Another limitation is that the vast majority of tweets don't have geocodes associated with them (only about 5% of our live tweets did), so the bulk of our location data is an approximation based on the username's hometown. To get specific location information on developing disasters, we would be relying on the user to convey their address in the content of the tweet.

We filtered tweets based on different sets of words intuitively associated with different types of disasters:

```
fire = [' fire ', ' wildfire ', ' blaze ', ' campfire ', ' smoke ']
flood = [' flood ', ' flooding ', ' flooded ', ' surge ', ' torrent ']
storm = [' storm ', ' tornado ', ' wind ', ' snow ', ' hale ', ' rain ', ' deluge ', ' hurricane ', ' avalanche ', ' ice ']
shooting = [' shooting ', ' shooter ', ' shots fired ', ' bullet ', ' gunshot ', ' murder ', ' driveby ']
everything = fire + flood + storm + shooting

```

The prototype displays tweets in the bounding box that contain at least one word associated with the alert type; it is also set to flag tweets that additionally contain words associated with urgency (to provide another filter layer since 'disaster' words can and often are used in other contexts):

```
urgent = [' help ', ' HELP ', ' sos ', ' SOS ', ' police ', ' emt ', ' ems ', ' emergency ', 
          ' assistance ', ' assist ', ' 911 ', ' relief ', ' warning ', ' danger ', ' crisis ']
```
          
The bag-of-words model has several limitations: it doesn't currently account for changes in frequency or trending topics, and wouldn't catch banter that falls outside these word sets (ex. people impacted by a chemical spill may be tweeting about the poor taste or appearance of their water instead of the 'chemical spill' itself). This is an area where a robust model using much more data would significantly improve this system.

## Future Steps
With more time and resources, there are a few extensions to this project we would invest in:

1. Developing our alert prototype into an interactive online dashboard displaying tweets, their frequency, and approximate location (these kind of visuals would be especially helpful to understand any trends our model might catch in addition to our bag-of-words filter).
2. Investing in the Twitter Firehose API to have access to all streaming tweets (which we could use to further train our model)
3. Pulling in official alerts for comparison against our predictions to test our model's accuracy in realtime, improve on classification of tweets related to disasters, and test how much earlier Twitter may be able to alert responders compared to official sources.
