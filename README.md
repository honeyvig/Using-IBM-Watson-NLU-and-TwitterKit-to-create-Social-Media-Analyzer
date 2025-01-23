# Using-IBM-Watson-NLU-and-TwitterKit-to-create-Social-Media-Analyzer
To create a Social Media Analyzer that uses IBM Watson Natural Language Understanding (NLU) for analyzing tweets and TwitterKit to interact with the Twitter API, you’ll need to follow a series of steps. This project will allow you to fetch tweets, analyze them for sentiment, emotions, keywords, entities, etc., and visualize or respond accordingly.
Step-by-Step Code for Social Media Analyzer
Step 1: Set Up IBM Watson NLU and TwitterKit

You’ll need the following:

    IBM Watson NLU API credentials (API Key and URL).
    Twitter API credentials (API Key, API Secret, Access Token, and Access Token Secret).

Sign up for both services and retrieve your credentials.
Step 2: Install Required Libraries

To get started, you will need to install the ibm-watson and tweepy libraries. tweepy is a Python library for accessing the Twitter API.

pip install ibm-watson tweepy

Step 3: Set Up IBM Watson NLU

Initialize the IBM Watson NLU client using your API credentials.

from ibm_watson import NaturalLanguageUnderstandingV1
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

# Watson NLU API credentials
nlu_api_key = 'YOUR_WATSON_NLU_API_KEY'
nlu_url = 'YOUR_WATSON_NLU_URL'

# Set up Watson NLU client
authenticator = IAMAuthenticator(nlu_api_key)
nlu = NaturalLanguageUnderstandingV1(
    version='2021-08-01',
    authenticator=authenticator
)
nlu.set_service_url(nlu_url)

Step 4: Set Up Twitter API with TwitterKit (Tweepy)

Now, you will set up Twitter API access using the tweepy library. You will need to authenticate with Twitter's API using your credentials.

import tweepy

# Twitter API credentials
twitter_consumer_key = 'YOUR_TWITTER_API_KEY'
twitter_consumer_secret = 'YOUR_TWITTER_API_SECRET'
twitter_access_token = 'YOUR_TWITTER_ACCESS_TOKEN'
twitter_access_token_secret = 'YOUR_TWITTER_ACCESS_TOKEN_SECRET'

# Authenticate with Twitter API using Tweepy
auth = tweepy.OAuth1UserHandler(
    consumer_key=twitter_consumer_key,
    consumer_secret=twitter_consumer_secret,
    access_token=twitter_access_token,
    access_token_secret=twitter_access_token_secret
)
api = tweepy.API(auth)

Step 5: Fetch Tweets Using Twitter API

You can use the Twitter API to search for tweets based on a query (e.g., a hashtag, keyword, or user).

# Search for tweets containing a specific keyword or hashtag
def fetch_tweets(query, count=10):
    tweets = api.search_tweets(q=query, count=count, lang="en", tweet_mode="extended")
    tweet_texts = [tweet.full_text for tweet in tweets]
    return tweet_texts

# Example: Fetch tweets containing the hashtag #AI
tweets = fetch_tweets('#AI', count=5)
for tweet in tweets:
    print(tweet)

Step 6: Analyze Tweets with Watson NLU

Now, we will send the fetched tweets to IBM Watson NLU for analysis, which will provide insights such as sentiment, entities, keywords, and emotions.

# Function to analyze the sentiment and other features of a tweet using Watson NLU
def analyze_tweet(tweet_text):
    response = nlu.analyze(
        text=tweet_text,
        features={
            'sentiment': {},
            'emotion': {},
            'entities': {'mentions': True},
            'keywords': {}
        }
    ).get_result()
    
    return response

# Example: Analyze one tweet
tweet = tweets[0]  # Take the first tweet
analysis = analyze_tweet(tweet)

# Print analysis result
print("Sentiment:", analysis['sentiment']['document']['label'])
print("Emotion:", analysis['emotion']['document']['emotion'])
print("Entities:", analysis['entities'])
print("Keywords:", analysis['keywords'])

Step 7: Combine the Functions

Now, let’s combine the previous steps into a function that will:

    Fetch tweets.
    Analyze each tweet using Watson NLU.
    Print the results.

def analyze_social_media(query, count=10):
    # Step 1: Fetch tweets
    tweets = fetch_tweets(query, count)

    # Step 2: Analyze each tweet
    for tweet in tweets:
        print(f"\nTweet: {tweet}")
        analysis = analyze_tweet(tweet)
        
        # Print analysis results
        sentiment = analysis['sentiment']['document']['label']
        emotion = analysis['emotion']['document']['emotion']
        entities = [entity['text'] for entity in analysis['entities']]
        keywords = [keyword['text'] for keyword in analysis['keywords']]

        print("Sentiment:", sentiment)
        print("Emotion:", emotion)
        print("Entities:", entities)
        print("Keywords:", keywords)

# Example: Analyze tweets containing the hashtag #AI
analyze_social_media('#AI', count=5)

Step 8: (Optional) Visualization of Emotion Analysis

You can use a library like matplotlib to visualize the emotion analysis results. IBM Watson NLU returns emotions like joy, fear, anger, sadness, and disgust, which you can plot.

import matplotlib.pyplot as plt

# Function to visualize emotion data
def visualize_emotion(emotion_data):
    emotions = ['joy', 'fear', 'anger', 'sadness', 'disgust']
    emotion_values = [emotion_data.get(emotion, 0) for emotion in emotions]

    plt.bar(emotions, emotion_values, color='skyblue')
    plt.xlabel('Emotion')
    plt.ylabel('Intensity')
    plt.title('Emotion Analysis of Tweet')
    plt.show()

# Example: Visualize emotion analysis for a tweet
emotion = analysis['emotion']['document']['emotion']
visualize_emotion(emotion)

Step 9: Run the Social Media Analyzer

Now you can run the analyze_social_media function and pass in any query (e.g., a hashtag or keyword) to analyze the tweets in real-time.

# Run the social media analyzer with a specific query
analyze_social_media("#AI", count=5)

Conclusion

This social media analyzer integrates IBM Watson Natural Language Understanding (NLU) for text analysis with the Twitter API (via Tweepy) to fetch tweets. You can analyze sentiment, emotions, entities, and keywords from the tweets, and even visualize the emotions in the tweets.

This basic implementation can be extended to track trends, analyze user sentiments over time, or even perform more advanced tasks like responding to users based on sentiment analysis.
