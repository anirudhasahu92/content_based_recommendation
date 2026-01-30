# Content-Based Recommendation System

This project implements a content-based recommendation system using news article data. The goal is to recommend similar articles to users based on the content of articles they have interacted with or a given query.

## Project Overview

The recommendation system leverages natural language processing (NLP) techniques to understand the textual content of news articles. It identifies key terms and phrases to build a profile for each article. When a user queries for recommendations (either by providing an article ID or a custom text query), the system finds articles with similar content.

## Data

The project uses two primary datasets:

1.  **`platform_content.csv`**: Contains information about the news articles available on the platform, including `item_id`, `title`, `text_description`, `item_url`, `language`, and other metadata.
2.  **`consumer_transanctions.csv`**: Contains records of consumer interactions with the content, such as `event_timestamp`, `interaction_type`, `item_id`, `consumer_id`, and `consumer_device_info`.

Both datasets are located in the `data/` directory.

## Methodology

The content-based recommendation system was built through the following steps:

### 1. Data Loading and Initial Exploration
- The `platform_content.csv` and `consumer_transanctions.csv` files were loaded into pandas DataFrames.
- Initial exploratory data analysis (EDA) was performed to understand the structure, data types, and missing values in both datasets.
- `event_timestamp` columns were converted to datetime objects.
- `item_id`, `producer_id`, `producer_session_id`, `consumer_id` were converted to string type to avoid numerical operations.

### 2. Feature Engineering and Visualization
- **Content Data**: Analyzed `language`, `producer_location`, `producer_country`, and `item_type` distributions. A `source` column was extracted from `item_url` to identify top news sources.
- **Consumer Data**: Explored `consumer_location`, `country`, and `interaction_type` distributions. A new column `device_group` was created by parsing `consumer_device_info` to categorize consumer devices (e.g., Android, iOS, Windows, Macintosh, Linux, X11).
- Interaction types in `consumer_data` were mapped to numerical `rating` values (e.g., 'content_watched': 1, 'content_followed': 2, 'content_saved': 3, 'content_liked': 4, 'content_commented_on': 5).

### 3. Text Preprocessing for Content-Based Filtering
- For content-based recommendations, only English articles (`language == 'en'`) were considered.
- A new `keywords_with_stop` column was created by concatenating `title` and `text_description` for each article.
- A `clean_text` function was implemented to remove punctuation, convert text to lowercase, and eliminate English stop words from the `keywords_with_stop` column, resulting in `keywords_clean`.
- The cleaned keywords were then split into lists of words.

### 4. TF-IDF Model and Similarity Calculation
- **Gensim Dictionary**: A dictionary was created from the `words_list` (cleaned keywords for all articles) to map each unique word to an integer ID.
- **Corpus (Bag-of-Words)**: Each article's cleaned keywords were converted into a Bag-of-Words (BoW) representation, indicating word frequencies.
- **TF-IDF Model**: A TF-IDF (Term Frequency-Inverse Document Frequency) model was trained on the corpus. TF-IDF weighs words based on their importance in a document relative to the entire corpus.
- **Similarity Index**: `MatrixSimilarity` from Gensim was used to build an index that allows for efficient similarity calculations between articles or a query and all articles in the corpus.

### 5. Recommendation Function
- A `news_recommendation_content_based` function was developed to provide recommendations. This function accepts either an `article_id` (to find similar articles to a known one) or a `query_text` (to find articles matching a custom query).
- The function converts the query (article's keywords or custom text) into its TF-IDF representation.
- It then computes cosine similarity scores against all articles in the corpus.
- The top-K most similar articles (excluding the query article itself if an `article_id` was provided) are returned with their titles and URLs.

## Example Usage

```python
# Example 1: Recommend articles based on a text query
news_recommendation_content_based(query_text='ethereum blockchain', top_k=5)
```
```python
# Example 2: Recommend articles similar to a specific article ID
# (Replace with an actual item_id from your content_df)
news_recommendation_content_based(article_id='3353902017498793780', top_k=5)
```

## Technologies Used

- Python 3.x
- Pandas (for data manipulation)
- Matplotlib (for data visualization)
- Seaborn (for data visualization)
- Gensim (for NLP tasks like TF-IDF and similarity calculation)
- Scikit-learn (for stop words)
