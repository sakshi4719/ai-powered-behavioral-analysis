### AI-Powered Behavioral Analysis for Suicide Prevention, Substance Use, and Mental Health Crisis Detection with Longitudinal Geospatial Crisis Trend Analysis

I used  PRAW, the Python Reddit API Wrapper to get posts from Reddit.   

I decided to target 5 subreddits - r/depression, r/anxiety, r/addiction, r/SuicideWatch, r/mentalhealth.  
- r/depression – A space for individuals to share experiences, seek support, and discuss coping with depression.  
- r/anxiety – Focuses on personal experiences, advice, and support related to anxiety disorders.  
- r/addiction – Covers struggles with substance use, recovery journeys, and treatment discussions.  
- r/SuicideWatch – A support community for individuals experiencing suicidal thoughts and seeking help.  
- r/mentalhealth – Discusses various mental health conditions, coping mechanisms, and general well-being.  

I took a 1000 of the newest posts from these 5 subreddits. So I collected a total of 5000 posts.    
https://praw.readthedocs.io/en/latest/code_overview/models/submission.html  
The information I collected from the posts is: 
- id: ID of the post  
- subreddit: Name of the subreddit where the post was posted  
- title: The title of the post  
- author: The username of the poster  
- created_utc: Time the post was created, represented in Unix Time  
- num_comments: The number of comments on the post  
- upvote_ratio: The percentage of upvotes from all votes on the post  
- score: The number of upvotes for the posts  
- selftext: The posts’ selftext - an empty string if a link post  
- permalink: A permalink for the post   

Then I created a structured .csv file containing all this data.  

The 'selftext' field was processed by me in the following steps 
- emoji removal
- word tokenization
- contraction expansion
- stop word removal
- stemming
- lemmatization
This processed text was saved in the column called proctext.   

I carried out sentiment analysis on this  processed text using TextBlob.

I tried using TF-IDF to detect crisis terms but this approach did not work as it seemed to not be able to capture the emotional "weight" of words, since it only uses frequency to determine criss terms.  
Then, I switched to using BERT tokenizer and model. I computed BERT embeddings of each post which are stored as a numpy array in the column bert_embeddings.  
detected crisis terms and classified the posts into High, Moderate and Low Concern posts.


Deliverables for:  
Task 1  
- [x]  A Python script that retrieves and stores filtered social media posts : reddit-data.ipynb
- [x]  A cleaned dataset ready for NLP analysis. - processed-reddit_data.csv

Task 2
- [x] A script that classifies posts based on sentiment and risk level : data-processing.ipynb
- [x] A table or plot showing the distribution of posts by sentiment and risk category :

Task 3
- [x] A Python script that geocodes posts and generates a heatmap of crisis discussions : crisis-geoloc-mapping.ipynb
- [x] A visualization of regional distress patterns in the dataset : crisis_heatmap.html
