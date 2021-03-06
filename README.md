# Substance Predication

# Problem Statement
The task is to design and implement a deep learning approach to filter predications belonging to the following subset of predicates from SemMedDB, informally called the "Substance Interactions" group:

 * INTERACTS_WITH
 * STIMULATES
 * INHIBITS
More details about these predicates can be found in the appendix of [this article](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-12-486)

3,000 predications from this group with binary labels indicating whether or not the given predication
is truly asserted in the source sentence are provided in `data/substance_interactions.csv`.


# Approach      
I tried to implement 2 approaches, namely a linear neural network model (Sequential) (model 1) and Convolutional Neural Networks (CNN) (model 2), both using Keras libraries. I have commented the code wherever needed and explained the different strategies I tried during the course of this exercise.  

I prepared 3 datasets:     
1. Just the sentence and label columns     
2. Subject + Predicate + Object and label columns     
3. Both of the above combined.          

I ran all three datasets and #3 performed better as compared to the other 2.          

There were key differences in data preparation for both models.      

In the case of the neural network, I tokenized and vectorized tokens using word2vec from Google News dataset(1x300 array per word) which has information regarding words being contextually relevant. Further, they were weighted by 'term frequency - inverse document frequency'(tf-idf), added together and divided by the count of words. Hence the 'mean' (so-to-speak) of all words of a sentence was calculated forming the word vector of that sentence. Extending this to all rows, the input was of the order:          

(number of input rows x vector dimensionality of word2vec)      

In terms of CNN, the input was also tokenized using the Keras library 'Tokenizer' this time and fit the sentences (row) iteratively. Instead of passing the 'mean' of the word vectors, I passed the vectors of a given sentence. I then zero-padded the vectors of sentences until the length was equal to that of the longest amongst the sentences in the input set.  Hence the input was of the order:          

(number of input rows x vector size of the longest sentence)          

The embedding matrix was the word2vec mapping of all tokens in the input corpus and hence the order of this matrix was:          

(number of unique tokens x vector dimensionality of word2vec)          

In both cases, the train-test data split was 80%-20% respectively.    

# Results     
I plotted the history of accuracy and losses of the model predictions. Both models yielded around 60% (+/- 3%) accuracy with training and testing. There seems to be no overfitting. Metrics for testing sets were as follows: 

1. Accuracy
2. Precision
3. Recall
4. F-score     

These results are not terrible but there's room for improvement with hyperparameter tuning and design tweaks for improved metrics. Also, increasing the data volume may result in better metrics. Transfer learning may also be a good option for data this small.  

# Limitations/Future work

The metrics showing the model performance could be improved by using more data and tuning hyperparameters. One  strategy I skipped is k-fold cross-validation. Implementing k-fold cross-validation may have high variability according to one study and alternatively, they suggest using a new technique called J-K-fold cross-validations to reduce variance during training (https://www.aclweb.org/anthology/C18-1252.pdf). Another strategy I skipped was performing a grid search to arrive at optimized hyperparameter values something that was done by Yoon Kim et.al..     Training deep learning classifiers with a small dataset may not be reliable. Transfer learning may be a better option.  Other libraries like fastai (https://docs.fast.ai/) which is a wrapper over PyTorch could be an alternative. They implement sophisticated techniques like 'LR Finder' that helps users make informed decisions on choosing learning rates for optimizers (SGD, Adam or RAdam). They also implement transfer learning wherein an already trained classifier model (on a variety of corpora) is implemented which proves to be effective. They implement advanced Recurrent Neural Network (RNN) strategies. This could be explored in future work.
