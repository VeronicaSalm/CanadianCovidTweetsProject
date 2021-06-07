# CanadianCovidTweetsProject 

# WORK IN PROGRESS

## New! (June 6, 2021): Tuned Results with Extra Stats

The folder `tuned results` now contains the NMF results after tuning the number of topics using coherence (following the steps in the **Tuned Results** section below), but also contains additional statistics.

### `tuned_results`

The folder `tuned_results`contains the results for each 14-day segment. Each subfolder is named using the following format:
```
YYYY-MM-DD_YYYY-MM-DD
```
For example, `tuned_results/2020-01-21_2020-02-03/` contains the results of running topic modelling on all tweets from January 21, 2020 to February 3, 2020 (inclusive).      

Inside each subfolder, you will find the following data:
```
models/
top_15_tweets_per_topic/
wordclouds/
topic_distribution.png
```
where
* `models/`: stores saved models and model data, which I can use to generate more data without needing to re-run all my scripts. You can probably ignore this folder, but feel free to ask me if you have any questions.
* `top_15_tweets_per_topic/`: This folder contains one file called `topic_XX.png` for each topic, which contains the 15 tweets that were most strongly associated with each topic. Since some tweets appear many times (e.g., because many users retweet them), the column *Tweet ID* corresponds to only one instance of that tweet. The column *Weight* indicates the raw weight this tweet received for the given topic. The first row is the tweet that was most strongly associated to the given topic. **Note: Weights are not percentages. They do not necessarily range from 0 to 1!** The column *Total Times Tweeted* counts the total number of times this tweet text was published by any user during the two-week period. Finally, *Tweet Text* indicates the full text of the tweet. 
* `wordclouds/`: **Unchanged.** This folder contains images named `topic_XX.png`, each of which is the word cloud for topic `XX`. 
* `overall_statistics.json`: statistics about the overall dataset (i.e., all tweets for the two-week period, not related to any specific topic), including total tweets, total users, vocabulary size, and overall mean VADER scores (along with standard deviation). Note that here, the vocabulary size is the number of normalized tokens, not the total number of words in the dataset.
*  `token_stats_general.csv`:  You might use this spreasheet to understand which terms are most common in a given period. Each row represents one token (word) in the dataset. Each column indicates information about that token: *Number of Occurrences* is the number of times that token appeared in all tweets, *Number of Tweets* is the number of tweets in which the token appeared at least once, *Percentage of Tweets* is the same as the previous column, but as a percentage of the total number of tweets, *Number of Users* is the number of users who used that tweet at least once, and *Percentage of Users* is the same as the previous column, but represented as a percentage of the total number of users. The rows are sorted in descending order by *Number of Occurrences*, so by default the first row is the most frequent term in the dataset.
*  `token_stats_by_topic.csv`: You might use this spreadsheet to see the statistics for important terms more easily. It doesn't contain any new information. Instead it copies the token stats for each of the top 15 terms of each topic. The first column indicates the topic that that token belongs to. The remaining columns are the same as in `token_stats_general.csv`.
* `topic_distribution.png`: **Unchanged.** An alternate visual summary which plots the weights for each word in each topic. The longer the bar for a word, the more strongly associated it is to the given topic.
* `topic_stats.csv`: You might use this spreadsheet to understand the prevalence of each topic in the overall dataset. Some background - For each tweet, the NMF model produces a set of weights, one weight for each topic. We can classify a document to a topic by choosing the topic that produced the highest weight (presumably the one this tweet is most related to). Each row of the spreadsheet represents a topic. The *Number of Tweets* column indicates how many tweets are classified to the given topic. *Percentage of Tweets* is the same statistic, but as a percentage of the overall number of tweets. *Number of Users* is the number of users that had at least one tweet classified to the given topic. This might be useful if you want to understand whether many users are discussing a topic, or only a few (but who are perhaps writing many tweets). *Percentage of Users* is the same statistic, but as a percentage of the overall number of users in the dataset.
* `user_stats.tsv`: This spreadsheet maps each user (*User ID*) to their screen name (*Screen Name*), the number of tweets they published in this period (*Number of Tweets*), the number of topics they tweeted about (*Number of Topics*, this statistic is based on which topics their tweets classified to), and a list of the topics they discussed (*Topics Discussed*). The rows are sorted in decreasing order by number of tweets, so you can see at a glance which user(s) published the most tweets over each two-week period.
* `vader_scores.csv`: This spreadsheet maps each topic (*Topic Number*) to the mean and standard deviation of the VADER scores for that topic. Reminder: For each tweet, VADER returns four scores: Negative, Neutral, Positive and Compound (computed by normalizing the other three scores). Positive, Negative, and Neutral all range from 0.0 (low) to 1.0 (high). The Compound score ranges from -1.0 to 1.0 and is an aggregate of the other three scores. Practically, a topic with a high Positive score is more likely to be happy or grateful, while a topic with a high Negative score is more likely to be angry, sad, or scared. Please read [About the Scoring](https://github.com/cjhutto/vaderSentiment#about-the-scoring) from the VADER Github repo for more information and thresholds for considering sentiment as "positive", "negative", or "neutral". You can also read more about VADER [in this article](https://towardsdatascience.com/sentimental-analysis-using-vader-a3415fef7664).

## Tuned Results

The folder `tuned_results` now contains the NMF results after tuning the number of topics using coherence. The steps to generate this folder are as follows:
1. **Tuning Process:** I tested topic counts [5,10,15,20,25,30,35,40,45,50] by generating NMF models and computing the average coherence across all produced topics. I used a different implementation of NMF (from the [Gensim library](https://radimrehurek.com/gensim/models/nmf.html)) for the tuning process because it has a built-in way to compute coherence. (We implemented coherence from scratch in our other project and I'm not certain it is trustworthy and bug-free).
2. **Topic Generation:** I then generated topics using the [Scikit Learn library]()'s implementation of NMF (exactly the same as below). But instead of telling it to produce 10 topics, I used the best number of topics produced by the tuning process. This means that some weeks of data have different numbers of topics.

Results:
* `tuned_results`: This folder is structured exactly the same way as the original `topic_model_results` folder, but this one contains the tuned topics instead of defaulting to 10 topics per period.
* `topics_per_period.csv`: This spreadsheet will tell you, at a glance, how many topics were generated for each period. I'm also pasting the counts here for easy reference:
```
PERIOD                  TOPICS   COHERENCE
2020-01-21_2020-02-03/	30	     0.48165
2020-02-04_2020-02-17/	40	     0.54204
2020-02-18_2020-03-03/	15	     0.50949
2020-03-04_2020-03-17/	20	     0.60944
2020-03-18_2020-03-31/	10	     0.49097
2020-04-01_2020-04-14/	20	     0.5172
2020-04-15_2020-04-28/	20	     0.46569
2020-04-29_2020-05-12/	20	     0.49084
2020-05-13_2020-05-26/	5	     0.47675
2020-05-27_2020-06-09/	15	     0.4985
2020-06-10_2020-06-23/	5	     0.53138
2020-06-24_2020-07-07/	10	     0.54948
2020-07-08_2020-07-21/	15	     0.49302
2020-07-22_2020-08-04/	10	     0.5177
2020-08-05_2020-08-18/	25	     0.56848
2020-08-19_2020-09-01/	5	     0.55926
2020-09-02_2020-09-15/	10	     0.54614
2020-09-16_2020-09-29/	15	     0.54167
2020-09-30_2020-10-13/	20	     0.52593
2020-10-14_2020-10-27/	10	     0.53775
2020-10-28_2020-11-10/	15   	 0.54142
2020-11-11_2020-11-24/	15  	 0.55077
2020-11-25_2020-12-08/	25  	 0.52398
2020-12-09_2020-12-22/	25  	 0.54874
2020-12-23_2020-12-31/	15  	 0.57996  (INCOMPLETE, only 8 days)
```
Interestingly, only two periods have a best number of topics that is over 30 - the rest are 20 or below, even though I tested counts up to 50.    

**Note:** These results are considered better by an automatic method, but often the difference in coherence between two topic numbers is quite small (e.g., 15 topics may give coherence = 0.5678, but 10 topics could be almost as good with coherence = 0.5633). Since you will be visually inspecting the topics anyway, you can decide which results are better on a period-by-period basis.

## Download the Data

If you want to download this repository as a .zip file, click the green "Code" button. In the drop-down menu, click "Download ZIP". This will allow you to save the current state of this branch anywhere on your personal machine.   

Note that if familiar with git, you can also clone the repository normally using:
```
git clone https://github.com/VeronicaSalm/CanadianCovidTweetsProject
```

## Methods

I extracted all tweets written by 22,231 users who were classified as Canadian both by spatial label propagation and our Canadian filter. This resulted in 5,344,555 total tweets from Jan 21, 2020 to Oct 31, 2020. I partitioned the data into two-week chunks starting from Jan 21 (inclusive), which gave 20 complete periods and one partial (incomplete) week at the end of October.    

Next, I ran Non-negative Matrix Factorization (NMF) on each two-week period. NMF is a topic modelling algorithm (more information can be found [in this article](https://towardsdatascience.com/topic-modeling-articles-with-nmf-8c6b2a227a45)). My script preprocesses each tweet by removing URLs and stopwords (common words like "and" or "the" which don't contain useful semantic information) and lowercasing all words. I also remove a custom stopword list of covid-related terms that appear in many topics such as "covid", "covid19", "coronavirus", etc. Because these words appear in almost every tweet, they aren't useful indicators of a particular topic.  

The results of running NMF are described below.

Notes:
* For each week, I chose to generate `k=10` topics. If the topics appear uninformative, we can try other values of `k` (e.g., generate 7 topics instead). **For best results, we should try tuning the number of topics to find the one that gives the highest average coherence** (for example, we may find that 15 topics is best for one week and 11 best for another week).  
* I did not filter the tweets in any way. These are the results of running topic modelling on the raw data, which includes (or could include) retweets, replies, images, links, and non-English text. I decided not to filter anything for two reasons: (1) this allowed me to get results out more quickly, and (2) in a sample of 1541 tweets, 1491 were English and only 50 (about 3.2%) were non-English according to a language detection tool. This seemed like a small percentage that likely wouldn't affect the topic modelling results (and indeed, when I looked at the January results, I didn't notice any clear non-English topics).
* These results only go up to the end of October, 2020. If this data seems useful, I can easily generate the results for Nov, 2020 to present.
* I also ran VADER on each Canadian tweet, so I can generate some statistics about how the VADER scores change over time upon request. For example, I could extract all tweets containing mask-related keywords and plot the average VADER scores for each 2-week period over time?
* If you want access to the tweets themselves, please let me know (the tweet folder is nearly 600 MB compressed, or 2.0GB uncompressed, which is why I didn't include it here). 

## Topic Modelling Results

The folder `topic_model_results` you will find the results for each 14-day segment. Each subfolder is named using the following format:
```
YYYY-MM-DD_YYYY-MM-DD
```
For example, `topic_model_results/2020-01-21_2020-02-03/` contains the results of running topic modelling on all tweets from January 21, 2020 to February 3, 2020 (inclusive).      

Inside each subfolder, you will find the following results:
```
wordclouds/
topic_distribution.png
topics.json
```
where
* `wordclouds/` is a folder containing 10 images named `topicXX.png`, each of which is the word cloud for topic XX. 
* `topic_distribution.png` is an alternate visual summary which plots the weights for each word in each topic. The longer the bar for a word, the more strongly associated it is to the given topic.
* `topics.json` is a json file which contains some information about each topic. For each topic, I include the following information: TOPIC_NUM is an integer from 1 to 10, FEATURES maps each of the top 15 words to its weight (a higher number means a stronger association to the given topic), WORDLIST is a list of the top 15 words, and COHERENCE is a float from 0.0 to 1.0 which measures the coherence of the given topic. Note: You probably don't need to look in this file unless you are interested in the exact values of the weights or the coherence results.
```
{
    TOPIC_NUM: {
        "features": FEATURES,
        "words": WORDLIST,
        "coherence": COHERENCE
    },
  ...
}
```

## Tweet Counts

The number of tweets in each 14-day (two week) period can be found below:
```
2020-01-21_2020-02-03: 125636 tweets.
2020-02-04_2020-02-17: 106024 tweets.
2020-02-18_2020-03-03: 179644 tweets.
2020-03-04_2020-03-17: 113843 tweets.
2020-03-18_2020-03-31: 89893 tweets.
2020-04-01_2020-04-14: 117938 tweets.
2020-04-15_2020-04-28: 116415 tweets.
2020-04-29_2020-05-12: 103544 tweets.
2020-05-13_2020-05-26: 101141 tweets.
2020-05-27_2020-06-09: 151807 tweets.
2020-06-10_2020-06-23: 347581 tweets.
2020-06-24_2020-07-07: 377400 tweets.
2020-07-08_2020-07-21: 376705 tweets.
2020-07-22_2020-08-04: 386781 tweets.
2020-08-05_2020-08-18: 382112 tweets.
2020-08-19_2020-09-01: 348202 tweets.
2020-09-02_2020-09-15: 414553 tweets.
2020-09-16_2020-09-29: 465103 tweets.
2020-09-30_2020-10-13: 483907 tweets.
2020-10-14_2020-10-27: 435524 tweets.
2020-10-28_2020-10-31: 390343 tweets.
2020-11-11_2020-11-24: 497878 tweets.
2020-11-25_2020-12-08: 487288 tweets.
2020-12-09_2020-12-22: 409177 tweets.
2020-12-23_2020-12-31: 237110 tweets (INCOMPLETE, only 8 days).
```
Note that the final partition is only 3 days long, so you may want to skip analyzing it until I generate the rest of the data.


