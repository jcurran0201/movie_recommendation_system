# movie_recommendation_system  
The purpose of the project is to create a usable movie recommendation system for uses based on movies that they previously watched and rated highly. 

## About the data  
https://www.kaggle.com/datasets/abhikjha/movielens-100k   
This dataset describes 5-star rating and free-text tagging activity from [MovieLens](http://movielens.org). It contains 100,836 ratings and 3,683 tag applications across 9,742 movies. These data were created by 610 users between March 29, 1996 and September 24, 2018. All users rated a minimum of 20 movies.  Each user is represented by an id, and no other information is provided. The data are contained in the files links.csv, movies.csv, ratings.csv and tags.csv.

### Limitations of the Data  
No demographic information is included in the dataset, which means that all recommendations are based on only how users rated the movies and which types of movies they tend to watch.  
## Data Cleaning Process  
The first step in this process was to discover the shape of each file in order to find out how each file should be merged together in order to create a clean, combined dataset to effectively create a model to recommend moviews to users. Given that the links.csv file and the movies.csv files were the same shape, an inner merge was done on the 'movieId' column that's in both files. It was noticed that ratings.csv and tags.csv each had a unique shape to them and could not be merged together right away. Another issue with ratings.csv and tags.csv is that the timestamp setting needed to be fixed to a proper datetime datatype. A very simple function was created to translate the timestamp column into a proper datetype datatype. After the datatype issue was solved, an outer merge was done on ratings.csv and tags.csv and the merged dataframe was sorted by user_id. Any NaNs in the tag column in the fully combined dataframe were later changed to 'no_tag' string in order to elimiate NaNs

Now that we have two combined dataframes, one being links and movies combined and the other being ratings and tags combined, these two combined dataframes need to brought together into one. Since both combined dataframes have the movieId column, and inner merge was done on the movieId column to create one combined dataframe. The values were then sorted by user_id and rating_timestamp. tag_timestamp was dropped from the combined datframe because a signifigant number of the datapoints did not have tags. 
## Exploratory Data Analysis  
### Distribuion of Movie Ratings
<img width="893" height="520" alt="Screenshot 2026-02-20 at 11 54 36 AM" src="https://github.com/user-attachments/assets/173c1839-5c60-476d-8b75-4bcbadb05986" />

### Top 20 Most Common Genres
<img width="1134" height="555" alt="Screenshot 2026-02-20 at 12 02 32 PM" src="https://github.com/user-attachments/assets/9f994292-29b7-4781-87a2-925e40d24987" />

### Top 20 Most Common Tags Used 
<img width="1160" height="562" alt="Screenshot 2026-02-20 at 12 03 28 PM" src="https://github.com/user-attachments/assets/1d914c0b-8718-4d2c-9541-a3e85ef9fdb8" /> 

### Average Rating Per Genre
<img width="1150" height="562" alt="Screenshot 2026-02-20 at 12 04 55 PM" src="https://github.com/user-attachments/assets/3324c8fb-d0d0-486c-9d2b-0540eecd547e" />

### Most common movie titles 
<img width="893" height="611" alt="Screenshot 2026-02-20 at 12 06 33 PM" src="https://github.com/user-attachments/assets/02601a1e-77e7-40c2-a8c8-d8e1064aca7d" />

## Natural Language Processing  
In order to handle 'title', 'genres', 'tag' columns the use of TfidfVectorizer, hstack, and normalize were needed. The first step in this process was to properly separate the different rows in the 'genres' column that had multiple genres. They were originally separated with | (i.e. Action|Adventure|Sci-Fi). Any stop words in the data needed to be filtered out as well. Next step is to fit_transform tag and genres to convert the text into computer readable numbers. hstack() is then used to combine tag and genres together so model can recommend using both columns. Lastly, normalize() is used to scale each movie vector so vector length = 1. This is important because Cosine similarity compares direction, not magnitude. Without normalization Movies w/ more tags will have larger vectors and unfairly dominate similarity. Normalization makes comparisons these comparisions fair since we are looking for similarity, not magnitude. 

## Recommendation System  
The get_recommendations_for_multiple_movies function implements a content-based “because you watched” recommendation system. It generates recommendations by comparing the features of movies a user has already watched with all other movies in the dataset. Movie features are represented as TF-IDF embeddings of genres and tags, and cosine similarity is used to measure closeness between movies. When a user provides multiple watched movies, their feature vectors are averaged to create a single user preference vector, which is then used to retrieve the most similar movies while excluding already-watched titles.

### Evaluation Approach 
Recommendations were evaluated using average cosine similarity between the user’s watched movies and the recommended movies. This provides a proxy measure of relevance: higher similarity scores indicate that recommended movies share more features with the movies the user enjoyed. Metrics such as Precision@K and Recall@K were not used because they require true user preferences for each user, such as ratings, clicks, or watch history, which were not incorporated into this content-based model. 

​​The recommendations generated by get_recommendations_for_multiple_movies were evaluated using average cosine similarity between the watched movies and the recommended movies. This metric measures how closely the recommended movies match the content features of the movies the user has already seen, based on genres and tags. On a test set of movies, the average_similarity_to_test function produced an average similarity of 0.828, while evaluating specific watched movies yielded an average cosine similarity of 0.891. These high values indicate that the system is effectively recommending movies that are content-wise similar to what the user enjoyed.

### Why Content-Based Recommender Was Used
Although rating data existed in the dataset, it was not used to model user preferences, so a collaborative filtering approach was not feasible. A content-based “because you watched” recommender was chosen instead, as it works without historical user behavior, avoids cold-start issues for new movies, and produces interpretable recommendations based on similarity. This approach allows the system to generate meaningful, deployable recommendations while demonstrating feature engineering and similarity-driven recommendation techniques.

## Planned Deployment
