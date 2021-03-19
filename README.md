# GA Project 3 - Web APIs & Classification
## Jetnipat Sarawongsuth 

### Problem Statement
Reddit is a massive global online forum with a heavy focus on its communities. These communities are divided into subreddits with each being specific to a certain subject. The variety of the subreddit topics can range from an educational data science subreddit to art, books, memes and video games. As of 2020, reddit has over [53 million](https://www.redditinc.com/) daily active users worldwide.

Each subreddit usually has many moderators and admins moderating the posts to ensure that they meet the subreddit rules. With an everincrasing userbase, the process of moderating these posts are becoming more and more cumbersome requiring greater amount of manpower to ensure the posts are not offtopics and meet the rules.

In this project, I am proposing the viability of using a machine learning classification model to help distinguishing posts for two different subreddits. We will be looking at applying it to two of the biggest film franchises **r/harrypotter** and **r/lotr**. We will evaluating to see if we can use the texts and language used in the post's title/body(selftext) to classify whether it belongs in r/harrypotter or r/lotr. This project will only be using text features within a post to classify as this is a consistent feature across new posts.

- **r/harrpotter** is the place where fans from around the world can meet and discuss everything in the Harry Potter universe! (992k subscribers)

- **r/lotr** is a subreddit for all things related to Tolkien, Lord of The Rings, and The Hobbit universe. (522k subscribers)

### Datasets
The datasets for r/harrypotter and r/lotr were obtained from reddit API.

The project contains two datasets. These datasets contain the information of the individual posts on each subreddit. The original datasets contain 114 columns. However, we will only be selecting a subset 17 relevant columns.
- harrypotter.csv (865 posts)
- lotr.csv (1134 posts)

| Column                | Type   | Description                                             |
|-----------------------|--------|---------------------------------------------------------|
| id                    | string | post's unique identifier                                |
| title                 | string | post's title                                            |
| selftext              | string | post's text content                                     |
| created_utc           | float  | time post was created in UTC epoch-second               |
| author_premium        | bool   | True if author has Reddit Premium account               |
| link_flair_css_class  | string | tags of the post (eg. discussion, announcement, question, events, news)        |
| score                 | int    | the net-score of the post (ups - downs)                 |
| upvote_ratio          | float  | ratio of upvotes                                        |
| num_crossposts        | int    | number of times the post was shared on other subreddits |
| total_awards_received | int    | number of awards the post received                      |
| num_reports           | float  | number of reports the post received                     |
| ups                   | int    | number of upvotes                                       |
| downs                 | int    | number of downvotes                                     |
| num_comments          | int    | number of comments in the post                          |
| gilded                | int    | number of reddit gold received (award)                  |
| spoiler               | bool   | True if the post contains spoilers                      |
| is_video              | bool   | True if the post contains videos                        |
| subreddit             | string | name of the subreddit                                   |

The datatypes of the columns are as follow:
- **5 strings**: title, selftext, subreddit, ...
- **3 bools**: spoiler, author_premium, is_video
- **3 floats**: upvote_ratio, num_reports, ...
- **7 ints**: ups (upvotes), downs (downvotes), num_comments, ...

### Evaluation

|          Metrics       | Logistic Regression | kNN | Naive Bayes |
|------------------------|---------------------|-----|-------------|
| Accuracy (Train) |          1         |  0.89   |       0.95      |
| Accuracy (Test)               |      0.91               |   0.87  |   0.88          |
| Misclassification Rate (Train)|         0            |   0.11  |    0.05         |
| Misclassification Rate (Test) |           0.09          |   0.13  |       0.12      |
| Sensitivity (Train)             |          1           |  0.95   |      0.98       |
| Sensitivity (Test)            |            0.88         |  0.91   |        0.94     |
| Specificity (Train)            |             1        |   0.80  |          0.91   |
| Specificity (Test)            |               0.95      |   0.82  |      0.80       |
| Precision (Train)              |              1       |    0.87 |        0.94     |
| Precision (Test)              |               0.96      |   0.87  |       0.86      |
| F1 (Train)                     |     1                |   0.91  |    0.96         |
| F1 (Test)                     |         0.92            |   0.89  |      0.90       |




Overall, the three models performed satisfactorily. Their accuracies were all above 85% with Logistic Regression edging ahead the other two at 91% on unseen dataset. However, it can be seen that eventhough both Logistic Regression and Naive Bayes may have low bias, their variance is much higher than kNN. When we look at the misclassification rate, we can clearly see that Naive Bayes and kNN have similar performance at 12-13% while Logistic Regression has under 10%. Here both subreddits have equal importance so incorrectly predicting one class or other doesn't really matter. This is unlike, for example, medical check where predicting whether a patient has cancer or not has grave consequence if the model has high False Negative Rate. This means that here we treat FPR and FNR equally. Therefore, we try to optimise the model for accuracy in general. The ROC Curve and the AUC also helped us verify that Logistic Regression model does indeed perform the best, marginally better than Naive Bayes in terms of the AUC at 0.972 and 0.967 respectively.

In which case, the best performing model is the Logistic Regression Model which score the highest accuracy in both the training and testing datasets at 100% and 91% respectively. The model has lower bias than its counterparts and while it may be overfitted, it still outperforms the other models on unseen data. This had been helped in part by the l2 regularisation which helped to simplify the model's complexity leading to a more acceptable result. GridSearch was done in order to see whether simplifying the model through limiting the number of features obtained from DFIDF Vectorisers would result in a better model. However, GridSearch showed that the best Logistic Model found was one where DFIDF had no feature limits and ngram is set to (1,1).



#### Limitations

The main limitation of the model is that it is currently only performing tokenisation, stopwords removal, stemming and dfidf vectorisation. This is a very simple method that does not seek to understand the context or the meaning of these sentences. We are only looking at word occurances and using that information to classify. Therefore, meanings are lost with this type of process. Improvements could be made by using more NTL techniques that are able to understand the context or able to group relevant words together to provide better context.

### Conclusion

In conclusion, the project showed that we are able to make predictions on whether a post belongs to r/harrypotter or r/lotr. This could be a good start for the future work that could ease the ever increasing burden on the subreddit moderators across the network. Although our current model is only able to classify between two subreddits. Production model would only need to classify whether a post belongs in the subreddit (1) or it doesn't. In which case, the output would be 0. In our case 1 represented a post belonging to r/harrypotter and 0 represented a post belonging to r/lotr. Reddit could employ such strategy to remove spams and irrelavant content through this process in detecting whether a post belong in the subreddit or not.





