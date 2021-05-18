# CanadianCovidTweetsProject

## New! Tuned Results

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
2020-10-28_2020-10-31/	10	     0.58718
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
2020-10-28_2020-10-31: 120802 tweets. (INCOMPLETE, only 3 days)
```
Note that the final partition is only 3 days long, so you may want to skip analyzing it until I generate the rest of the data.


