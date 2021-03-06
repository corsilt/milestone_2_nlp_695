### milestone_2_nlp_695
# Predicting the text dificulty of Wikiepedia comments
## Cory Bilyeu

### Motivation
In many real-world applications, there is a need to make sure textual information is comprehensible/readable by audiences who may not have high reading proficiency. This might include students, children, adults with learning/reading difficulties, and those who have English as a second language. The simple Wikipedia (https://en.wikipedia.org/wiki/Simple_English_Wikipedia), for example, was created exactly for this purpose. Before the editors spend a lot of effort to simplify the text and increase its readability, it would be very useful to suggest to them which parts of an article's text might need to be simplified.

Our goal with this dataset was to predict the difficulty of text by applying a binary classifier to text from either Wikipedia or simple Wikipedia. Predicting the difficulty of text allows for audiences to be provided with material that better matches their needs. For example, students at different educational levels would benefit from being able to select from material that is tailored to their education level. Similarly, those learning different languages could benefit by selecting documents that have a simpler vocabulary or are easier to understand.

### Data Source

The training data for this task is hosted on Kaggle for the ‘UMich SIADS 695 Fall21: Predicting text difficulty’ competition (https://www.kaggle.com/c/umich-siads-695-fall21-predicting-text-difficulty/overview). This dataset contains .csv files of the training and testing data where every document is a sentence from a Wikipedia article with a corresponding binary label if the articles came from simple Wikipedia or not. The training data contains 416,768 labeled documents and the training data contains 119,092 unlabeled documents. These datasets have a unique id for each string that contains the text from the articles.

Data from additional sources were provided along with the Wikipedia dataset which we used to add additional information about the text for more features. The Dale-Chall word list contains about 3,000 English words that were known to 80% of fifth-graders in the 90s. A reference list from Brysbaert et al. also contained the concreteness ratings for about 40,000 English word lemmas known by at least 85% of raters. Data was also included which provided the age-of-acquisition (AoA) ratings for over 50,000 English words (not including pronouns, number words, adverbs, nouns mostly used as names). 

There are not too many ethical issues to consider when working with these datasets as the data is taken from public domains about public topics. However, as all the documents we analyzed were in English our results would not necessarily be applicable to other languages. 

### Methods and Evaluation

It didn’t take long to realize the dataset contained a high variety of text instances in various contexts, lengths, topics, and structure. There was no central theme to identify across the dataset, and each sample appears to be randomly curated. Wikipedia describes itself as “a free content, multilingual online encyclopedia,” so it is not surprising that a collection of comments or sentences from Wikipedia articles would be of such a high variety. 

The samples of text were clearly not preprocessed and littered with misspellings, errant spacing, extra punctuation, and random symbols. It was even noted that a small minority of the samples did not contain any letters at all, but only punctuation; furthermore, some samples were identified to be duplicates, but had conflicting labels. See the following example:

![image](https://user-images.githubusercontent.com/52717506/151840115-f896cd2d-62a3-42b6-ab2f-d9a4ba20828e.png)

Because the number of samples with conflicting labels was small (relative to the large dataset), we determined this to be random noise that was unlikely to have a large impact, if any, on our trained machine learning classifiers, and therefore we did not remove the conflicting samples identified; one could even argue that this is an artifact of working real-world data.

We then focused on how we would preprocess the messy text data. We came up with a few methods for parsing and processing the text. The methods we decided to employ were tokenization, lemmatization, parts of speech, and potentially stemming (this is something we would try later in our deep learning workflow); stopwords were also removed during preprocessing, except for certain features that depend on them (like count of stop words). Tokenization forms the basis for lemmatization and stemming, and can be defined as: splitting a phrase, sentence, paragraph, or an entire text document into individual units, such as individual words or terms, and each of these smaller units are called tokens.

### Preprocessing
There are many ways to tokenize text in Python that we were aware of, which include the following methods: we could tokenize the text using regex and searching for word character tokens using a pattern such as \w+, we could use the NLTK’s tokenizer to do the tokenization for us, or we use even use Spacy’s tokenizer to perform the tokenization. We ended up using an amalgamation of all three methods during our processing workflow, but we noted that these three methods produce very similar results, so for purposes it would be acceptable to use any of these three methods.

The next type of preprocessing we focused on was lemmatization. Lemmatization is a method that can be defined as “aiming to remove inflectional endings only and to return the base or dictionary form of a word, which is known as the lemma .” Lemmatization was an important consideration for feature engineering we wanted to do because some of our feature engineering ideas were dependent on using lemmas. This is mostly because most of the supplemental datasets we had for word concreteness rankings were based on word lemmas.

Another type of preprocessing we considered was stemming; stemming is very similar to lemmatization, but instead of reducing the word to its lemma, it is reduced to its stem. Stemming was less important to our original feature engineering ideas, but it was used later in our workflow when testing some deep learning models.

### Briefly exploring POS in the Preprocessing Workflow
We briefly analyzed different parts of speech (POS) processing before moving on to feature engineering. We used NLTK’s POS-tagger which processes a sequence of words and adds the corresponding parts of speech tag to each word. This tagger can apply the default POS tags taken from the Penn Treebank Project with 36 tags or a universal tag set that is simplified to 12 tags. Once these tags have been applied to each word the texts can be tokenized by whitespace and then vectorized using methods like CountVectorizer or TfidfVectorizer in order to convert text to sparse representations of word counts. CountVectorizer counts the appearance of words in a document, which can lead to bias in the data when certain words occur frequently since the counts aren’t normalized. TfidfVectorizer counts the appearance of words and normalizes by the number of documents containing the word over the total number of documents. This normalization allows for the overall frequency of a word to be taken into consideration to give a better representation of the significance of the word. These vectorizers also have parameters for tuning, like min_df and max_df, which allow for more control over the words that are included in the sparse representation. 

To get a better understanding of how these vectorizers compare we looked at the accuracy of the default Multinomial Naive Bayes model at predicting the labels of the original text , text with universal tags, text with universal tags with stop words removed, and text with default tags with stop words removed. We then compared how these accuracies were affected by the min_df parameter which specifies how many documents the words need to be in in order to reduce noise in the data by limiting low frequency words.

![image](https://user-images.githubusercontent.com/52717506/151840262-08f55c8c-3e40-45c1-a743-4bbe41f78d4f.png)

We found that the accuracy scores were mostly similar between tokenizers and POS tagging methods. The original text scored slightly better using a TfidfVectorizer whereas the universal POS tags with stop words scored slightly better with a CountVectorizer. The TfidfVectorizer also performed best on this text when looking at words that appeared in at least 50 documents, while CountVectorizer performed best with words in at least 20 documents. Overall, it appears removing stop words didn’t increase the accuracy and that POS tagging performed about as well as the original text.

This is likely due to the documents being analyzed being relatively short, usually a few sentences, and having many documents that contain misspellings or only punctuation or numbers. Having short documents limits the TfidfVectorizer’s ability to normalize by the document weightage of a word since many words already have a low frequency, especially when POS tags are applied. This low document length leads to little difference between using a CountVectorizer and a TfidfVectorizer. Adding POS tags also didn’t significantly increase the accuracy scores. This is likely due to the distribution of parts of speech being used is fairly consistent between the simple and complex documents (label 0 and 1) for both the default and universal POS tag sets. Nonetheless, we decided to leave POS tagging out of our general feature engineering methods

![image](https://user-images.githubusercontent.com/52717506/151840377-e61e8b36-bd94-45c9-a36d-a1116875db0a.png)

### Feature Engineering
Once we had our preprocessing methods established, we moved on to feature engineering of the text. We wanted to generate as many features as we could think of so we would have many features to pick from when it came time for model development. We also planned on ranking features by their predictive power, and eliminating the poor performers, so having too many features wouldn’t necessarily be a problem. Because of the large number of features generated, I will not describe each in detail, but the features we created can be seen summarized below.

![image](https://user-images.githubusercontent.com/52717506/151840485-d435ca85-94b1-4eec-90da-06b0ad0d4642.png)
![image](https://user-images.githubusercontent.com/52717506/151840521-f1139bd1-6a91-4f0b-b5fa-7efce9d91d36.png)

Once we generated the many features described above via feature engineering, we transitioned to feature importance and model development. We had generated many features that hopefully had robust predictive power for our supervised task, but we needed to focus feature selection and removal on possibly redundant features. Many of the features we generated were similar, but with only subtle differences. For example, average world length of a sentence with and without punctuation; this method of feature generation could certainly lead to redundant features.

For feature selection, we decided to use the mutual information between each feature and the label. Mutual information is useful for feature selection because it can capture non-linear relationships between the predictor and the label that something like Pearson’s correlation cannot. Using mutual information for feature selection is typically done via two methods; select the top n features with the highest mutual information scores, or choose some arbitrary threshold to only select features above this threshold. We chose the threshold method because we did not want to restrict our model to a set number of features. We plotted the mutual information values for each of the features in descending order to help visualize the predictive power and ranking of each generated feature; we determined a threshold of  0.025  would eliminate a subset of features with relatively weak predictive power, but still retain most of the features that were generated.

![image](https://user-images.githubusercontent.com/52717506/151840574-8522df29-6579-44a7-9935-75e77cff00bd.png)

Furthermore, we also used our mutual information plot to remove redundant features. This plot allowed us to directly compare the predictive power of assumed redundant features, and then select the one with a higher mutual information value. For example, concreteness_rank_perc had the highest mutual information of the other variants of this feature, so this feature was selected among the variants, while the others were dropped. This process of feature selection and removal of redundant features helped reduce overfitting of our models, and reduce the computational burden in model development with the reduced feature space; both of these benefits were realized during testing, when comparing the aforementioned feature selection process against using all features.

### Discussion

Once we completed our feature selection process, we transitioned to model selection and development. We chose four different classification models to compare in the model development stage, and these four classifiers were logistic regression, ridge classifier, naïve bayes classifier, and a random forest classifier. The rationale behind choosing these models was to pick a variety of classifiers that varied in run time and model type; for example, naive bayes is a probabilistic model, logistic regression and ridge are two different regression models, and finally random forest for as a decision tree type classifier.

![image](https://user-images.githubusercontent.com/52717506/151840661-ef7ba625-8750-4d97-b571-a13d148ed37a.png)

There was one more method we were interested in trying to use tensorflow to train a neural network on our data. We wanted the opportunity to practice building neural networks (NNs) using tensorflow and thought we may have a chance to achieve a superior accuracy using a NN with one addition to our feature engineering strategy.

The addition to our feature engineering strategy was to fit a tf-idf n-gram vectorizer on the original text data and concatenate these features with the selected features from the previous feature engineering methods; the hypothesis was that we could increase the predictive power by adding the tf-idf n-gram vectorizer features to our existing feature space, which we could then train on a NN. The reason we thought the tf-idf vectorizer would increase the predictive power of a trained model is because tf-idf methods are generally considered one of the more robust NLP methods, and this was something that our generated features did not capture.

Unfortunately, these efforts only produced results that were comparable to the Random Forest classifier, but slightly inferior. Additionally, the NN suffered from an overfitting problem (an issue that was also present in our random forest classifier), but because we prioritized accuracy for our model, we decided to stick with the rf model. The below plots summarize the NN results, and the table summarized the model architecture. 

![image](https://user-images.githubusercontent.com/52717506/151840760-cd2d8e4a-0ea2-40fe-8fa7-0efb03b0f0a3.png)

Once we had our features and model selection finalized, we moved on to hyperparameter tuning of our random forest classifier. Hyperparameter via a gridsearch  would allow us to achieve an increased accuracy score;  this is because the gridsearch tries different combinations of parameters on cross validation folds that we can evaluate for test set accuracy. We initialized our gridsearch to try 36 different combinations of hyperparameters using a 3 fold cross validation strategy for evaluating the results on a test set; we settled on a 3 fold strategy due to computational limitations. The below plot shows the test set accuracy on three different cv folds for the 36 combinations. 

![image](https://user-images.githubusercontent.com/52717506/151840845-f428e030-e718-44fe-ab53-9b2a2c4c51c8.png)

From the hyperparameter tuning we were able to select the best performing hyperparameters for our final model development, although it was noted that the performance between the various hyperparameters combinations were slight and only marginally increased accuracy. One thing we did additionally was add more n_estimators to our final model than we were able to in the gridsearch. This was because adding more n_estimators in our gridsearch would result in dramatically longer run times due to the time complexity of training our random forest classifiers on our large data set. We increased our n_estimator to 600 (which was assumed to be near the limit of what our cpu could handle).

We also created confusion matrices for the training, dev, and test sets to better understand what types of correct and errant predictions our model was making; the results are summarized in the plot below.

![image](https://user-images.githubusercontent.com/52717506/151840899-20f719af-55cd-4813-a42f-c59701a81fc9.png)

Based on the results from the confusion matrices it appears that our classifier has a higher true positive rate than false positive rate, although the rates are comparable. It can be inferred that our model has slightly more accuracy predicting sentences that need to be simplified, rather than ones that do not need to be simplified, but overall, our model is considered quite balanced in terms of predictive power of positive and negative instances.

Once we tuned our random forest classifier to the best performing parameters in our gridsearch, we were ready for our final testing on the Kaggle test sets. The following table summarizes the final accuracy results we were able to achieve on the Kaggle test sets.

![image](https://user-images.githubusercontent.com/52717506/151840959-b5639d86-b4d6-4eb7-918e-e95684f3e7ca.png)

As you can see, the random forest classifier with parameter tuning performed better than the default random classifier. Furthermore, it was also demonstrated to be more stable with a slightly more consistent public and private score, and it also had less overfitting on the training set (although both models are considered to be overfit). 

With more time and resources our model could be further improved by accounting for overfitting and including additional features to select from. Increasing the number of folds while cross validating or using RandomSearch would be able to give us better insight into how the parameters affect model accuracy, but this can be computationally expensive. Reducing the noise and increasing the signal of the data can also help with the model overfitting. This can be done by merging in additional resources to compare the documents, such as word lists common to people at more education levels. Stricter feature selection methods can also be used to try to eliminate features that provide too much noise. Further optimizing the model would allow us to better classify the complexity of new documents which is useful in facilitating students' learning by providing them with material that is more appropriate for their level. 
