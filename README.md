# Saving Endangered Languages using Reddit Data

Sean Y. Li

### Background

Globally, around 60% of all languages are endangered (children will not speak it in 100 years) and 40% are moribund (children already do not speak it). The Endanged Languages Project (https://www.endangeredlanguages.com/) provides knowledge and resources for speakers of endangered languages to document, preserve, and teach their languages. However, more robust linguistic analysis is difficult without more training, but can be crucial to deeper understanding and teaching of a language. That's why ELP has partnered with linguists at many universities, to have them donate their time to look at work submitted by people who speak endangered languages. However, we soon ran into the problem of linguistic extremists, conlangers, and trolls submitting requests with real looking data which was wasting the time of linguists all over who were donating their time. Thus, there was a need for a way to filter out the fake data. \*

#### Problem Statement

Can we come up with a model that can identify real linguistic data and questions from those of made up languages?

#### Data Source
To train a model we needed a lot more data than what we currently had. We also needed data that looked like it was written by laypeople, so we couldn't use scholarly text data. That's why we turned to Reddit, specifically the subreddits r/linguistics and r/conlangs. Conlang data looks very similar to real language data, but it’s virtually impossible to create a language to the same detail and precision as real natural language, so we should be able to build a model that can pick up on these subtle differences. The same people sending in fake language data could be on r/conlangs too for inspiration, so it was a good idea. Using Pushshift's API, I scraped 1000 posts from each subreddit to use for modelling.

#### Preliminary Models
I did very basic cleaning by dropping posts that had no descriptions. Afterwards I fed the data to GridSearch pipelines that used a TF-IDF Vectorizer with English stopwords followed by either a Logistics regression classifier, a multinomial Naive Bayes classifier, a k-NN classifier, or a Random Forest Classifier. With 1311 samples and a baseline accuracy just above 50%. In general, the models performed well but were overfit. Logistic Regression and Random Forest Classifier performed almost identically on the test data, so it's possible the tests would perform differently with a different random state

|            Model            | Train Score | Test Score |
|:---------------------------:|:-----------:|:----------:|
| k-NN Classifier_            |      99.99% |     83.23% |
| **Logistic Regression**     |  **97.56%** | **92.07%** |
| Multinomial Naive Bayes     |      95.73% |     86.59% |
| Random Forest Classifier    |      99.99% |     91.16% |


#### More Text Processing
Okay so the models do okay, but there are probably some words that make it too easy. After all, what kind of troll tells you they're trolling you. So I took out stopword and the words conlang/conlangs, and stemmed everything using a Porter Stemmer. Afterwards I removed all the posts with less than 300 characters (which comes out to around 40-80 words). 


#### New Models
This cleaning left me with 761 samples which I fed down the same pipeline. Surprisingly, the models that performed better were performed worse in the preliminary models.

|            Model            | Train Score | Test Score |
|:---------------------------:|:-----------:|:----------:|
| k-NN Classifier            |      99.82% |     91.10% |
| Logistic Regression         |      97.02% |     88.48% |
| **Multinomial Naive Bayes** |  **94.74%** | **91.10%** |
| Random Forest Classifier    |     100.00% |     87.96% |

#### Findings and Areas of Further Interest
A working model for filtering out real and fake linguistic data is possible and could be ready to apply to real crowdsourced data. It would be good to tune it more and maximize sensitivity (minimize false negatives). Getting rid of false negatives is crucial since endangered language speaker populations are often the most vulnerable and underserved. It’s much more important to reduce the possibility of them slipping through the cracks than to manually need to throw out the false positives of conlang data marked as real language data.


\*The scenario outlined above is fictional
