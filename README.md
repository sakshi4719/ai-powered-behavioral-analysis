### AI-Powered Behavioral Analysis for Suicide Prevention, Substance Use, and Mental Health Crisis Detection with Longitudinal Geospatial Crisis Trend Analysis

I used  **PRAW**, the Python Reddit API Wrapper to get posts from Reddit.    
[PRAW Documentation](https://praw.readthedocs.io/en/latest/code_overview/models/submission.html)  

I decided to target 5 subreddits - r/depression, r/anxiety, r/addiction, r/SuicideWatch, r/mentalhealth.  
- r/depression – A space for individuals to share experiences, seek support, and discuss coping with depression.  
- r/anxiety – Focuses on personal experiences, advice, and support related to anxiety disorders.  
- r/addiction – Covers struggles with substance use, recovery journeys, and treatment discussions.  
- r/SuicideWatch – A support community for individuals experiencing suicidal thoughts and seeking help.  
- r/mentalhealth – Discusses various mental health conditions, coping mechanisms, and general well-being.  

I took a 1000 of the newest posts from these 5 subreddits. So a total of 5000 posts.    

The information I collected from the posts is:  
| Field          | Description                                              |
|---------------|----------------------------------------------------------|
| `id`          | ID of the post                                           |
| `subreddit`   | Name of the subreddit where the post was posted          |
| `title`       | The title of the post                                    |
| `created_utc` | Time the post was created, represented in Unix Time      |
| `num_comments`| The number of comments on the post                       |
| `upvote_ratio`| The percentage of upvotes from all votes on the post     |
| `score`       | The number of upvotes for the post                       |
| `selftext`    | The post's selftext - an empty string if a link post     |
| `permalink`   | A permalink for the post                                 |    

(I did not save the user ID to protect users' privacy.)  
Then I created a structured .csv file containing all this data.  

The 'selftext' field was processed by me in the following steps 
- emoji removal
- word tokenization
- contraction expansion
- stop word removal
- stemming
- lemmatization   
  
This processed text was saved in the column called proctext.   

I carried out sentiment analysis on this processed text using **TextBlob**. The posts were classified as Neutral, Postive, Negative.

| Sentiment Label        | Number of Posts |
|-------------------|----------------|
| Positive          | 42             |
| Neutral           | 2019           |
| Negative          | 2867           |   

I was not satisfied with the results I got using TextBlob as around 40% of the posts were classified as neutral, which signalled to me that maybe this model is might not be well-suited for a dataset of this type.
Possibly because uses a lexicon-based approach, so it may struggle with nuanced or domain-specific language.  
So, it often fails to capture sentiment in complex sentence structures or context-dependent phrases.  

Hence I tried sentiment analysis and labeling again using a **BERT-based model** (https://huggingface.co/nlptown/bert-base-multilingual-uncased-sentiment) which significantly reduced neutral classifications from 2019 (TextBlob) to 174, suggesting it captures sentiment more effectively.  

| Sentiment Label        | Number of Posts |
|-------------------|----------------|
| Positive | 270           |
| Neutral      | 174             |
| Negative        | 4484              | 

I tried using **TF-IDF** to detect crisis terms but this approach did not work as it seemed to not be able to capture the emotional "weight" of words, since it only uses frequency to determine criss terms.  
Then, I switched to using **BERT** tokenizer and model. I computed BERT embeddings of each post which are stored as a numpy array in the column bert_embeddings.  
I defined some crisis terms and classified the posts based on their similarity to the crisis terms.   
Lastly, I saved selftext, proctext, risk_level, sentiment and sentiment_label to a .csv file.   

| Risk Level        | Number of Posts |
|-------------------|----------------|
| Moderate Concern | 3970           |
| Low Concern      | 913             |
| High-Risk        | 45              |   

I extracted the locations using **Stanza** and plotted a heatmap using **Folium**.  
![image](https://github.com/user-attachments/assets/6b465a73-f382-44f7-af95-fe0c922f8286)

These are top 5 places where crisis discussions were happening.
![image](https://github.com/user-attachments/assets/2b8fa731-a418-4a5a-b484-53a78ea02df7)


**Future Refinements**  
- This project can be extended to add slang and censored words that come up due to social media censoring.
- A pipeline can also be implemented for continuous tracking and trend forecasting over multiple social media platforms.
- Fine-tune a BERT model on crisis-related labeled data to improve accuracy.
- Experiment with RoBERTa/ DistilBERT which are pre-trained on mental health datasets.
- Make a comprehensive list of crisi terms so the BERT model can perform better.

**Deliverables**  
Task 1  
- [x]  A Python script that retrieves and stores filtered social media posts : **reddit-data.ipynb**
- [x]  A cleaned dataset ready for NLP analysis : **processed-reddit_data.csv**

Task 2
- [x] A script that classifies posts based on sentiment and risk level : **data-processing-and-risk.ipynb** , **sentiment-analysis.ipynb**
- [x] A table or plot showing the distribution of posts by sentiment and risk category : **distribution_of_posts.csv**   

Task 3
- [x] A Python script that geocodes posts and generates a heatmap of crisis discussions : **crisis-geoloc-mapping.ipynb**
- [x] A visualization of regional distress patterns in the dataset : **ner_extracted_location.csv**,  **crisis_heatmap.html**, **top_5_crisis_locations.html**

**Installation Guide**   

Prerequisites 
* Python 3.6 or later
* `pip` (Python package installer)

Steps   
1. Navigate to your project directory and create a virtual environment.

2. Install Required Python Packages:
Install the necessary packages using `pip`.  Run this in a Jupyter Notebook.  
REQUIREMENTS.TXT

4. Reddit API Credentials:
- To use PRAW, you need Reddit API credentials.  
- Create a Reddit developer account.  
- Create a new app, and note down the `client_id`, `client_secret`, and `user_agent`.   

4. Run the Scripts:  
- Run `reddit-data.ipynb`. This will retrieve Reddit posts and save them to `processed-reddit_data.csv`.
- Run `data-preprocessing.ipynb`. This script will perform sentiment analysis, calculate risk levels, and save the results in `distribution_of_posts.csv`.
- Run `crisis-geoloc-mapping.ipynb`. This script will extract locations, geocode them, and generate `crisis_heatmap.html` and `ner_extracted_location.csv`.
- Open `crisis_heatmap.html` in your web browser to view the heatmap.   
