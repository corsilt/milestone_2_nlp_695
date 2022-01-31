### milestone_2_nlp_695
# Predicting the text dificulting of Wikiepedia comments
## Cory Bilyeu

### Motivation
In many real-world applications, there is a need to make sure textual information is comprehensible/readable by audiences who may not have high reading proficiency. This might include students, children, adults with learning/reading difficulties, and those who have English as a second language. The simple Wikipedia (https://en.wikipedia.org/wiki/Simple_English_Wikipedia), for example, was created exactly for this purpose. Before the editors spend a lot of effort to simplify the text and increase its readability, it would be very useful to suggest to them which parts of an article's text might need to be simplified.

The main reason we chose the Wikipedia dataset was that we felt that there were more opportunities for feature engineering using this dataset, and secondarily we were limited on time given the need to pivot to another dataset this late into our project. Nonetheless, we were very interested in a task that would allow us to get creative with different feature engineering methodologies, and the opportunity to work with unstructured text data more. Our goal with this dataset was to predict the difficulty of text by applying a binary classifier to text from either Wikipedia or simple Wikipedia. Predicting the difficulty of text allows for audiences to be provided with material that better matches their needs. For example, students at different educational levels would benefit from being able to select from material that is tailored to their education level. Similarly, those learning different languages could benefit by selecting documents that have a simpler vocabulary or are easier to understand.

### Data Source

The training data for this task is hosted on Kaggle for the ‘UMich SIADS 695 Fall21: Predicting text difficulty’ competition (https://www.kaggle.com/c/umich-siads-695-fall21-predicting-text-difficulty/overview). This dataset contains .csv files of the training and testing data where every document is a sentence from a Wikipedia article with a corresponding binary label if the articles came from simple Wikipedia or not. The training data contains 416,768 labeled documents and the training data contains 119,092 unlabeled documents. These datasets have a unique id for each string that contains the text from the articles.

Data from additional sources were provided along with the Wikipedia dataset which we used to add additional information about the text for more features. The Dale-Chall word list contains about 3,000 English words that were known to 80% of fifth-graders in the 90s. A reference list from Brysbaert et al. also contained the concreteness ratings for about 40,000 English word lemmas known by at least 85% of raters. Data was also included which provided the age-of-acquisition (AoA) ratings for over 50,000 English words (not including pronouns, number words, adverbs, nouns mostly used as names). 

There are not too many ethical issues to consider when working with these datasets as the data is taken from public domains about public topics. However, as all the documents we analyzed were in English our results would not necessarily be applicable to other languages. 

### Methods and Evaluation

It didn’t take long to realize the dataset contained a high variety of text instances in various contexts, lengths, topics, and structure. There was no central theme to identify across the dataset, and each sample appears to be randomly curated. Wikipedia describes itself as “a free content, multilingual online encyclopedia,” so it is not surprising that a collection of comments or sentences from Wikipedia articles would be of such a high variety. 

The samples of text were clearly not preprocessed and littered with misspellings, errant spacing, extra punctuation, and random symbols. It was even noted that a small minority of the samples did not contain any letters at all, but only punctuation; furthermore, some samples were identified to be duplicates, but had conflicting labels. See the following example:

