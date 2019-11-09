# Rumour Detection and Stance Classification
<div style="text-align: right"> 
## Introduction

Battling the surge of fake news is a multi-faceted, complicated project, a part of which is to detect whether rumours that are spreading are fake or real. One way in which this can be done is to analyse the public reaction to a rumour when broadcasted. The public often have information from a lot of other sources, and their collective reaction brings in valuable information in detecting whether a rumour is real or fake.
In order to analyse public reaction, we take some tweets on a topic and analyse all the replies. We classify the replies into support, deny, query and comment and later use this to verify the statement.
We have been given tagged data for training for this task. Tasks as given in the website:
### Subtask A
The first subtask will deal with the complementary objective of tracking how other sources orient to the accuracy of the rumourous story. We are provided with a tree-structured conversation formed of posts replying to the originating rumourous post, where each post presents its own type of support with regard to the rumour. We frame this in terms of supporting, denying, querying or commenting on (SDQC) the claim. The goal is to label the type of interaction between a given statement and a reply post.
### Subtask B
The goal of the second subtask is to predict the veracity of a given rumour. The rumour is presented as a post reporting or querying a claim but deemed unsubstantiated at the time of release. Given such a claim, and a set of other resources provided, systems should return a label describing the anticipated veracity of the rumour as true or false along with a confidence score.

## Description of the Dataset
For training purposes, we have been provided with 9 categories about topics that have generated a lot of conversation in Twitter - for example, the Char- lie Hebdo shooting and the Sydney Seige. Each category contains around 120-150 tweets and their replies in the Twitter tree structure and every reply is tagged with Support, Query, Comment, Deny.
The test data also contains rumours that need to be classified as true or false. We have with us the training data from the 2017 and 2019 editions of the competition, and the test data from 2019.
The tweet data can be considered as a tree, with parent and sibling tweets providing context. The tagged data contains whether a given tweet is S, D, Q or C with respect to its immediate ancestor.
- 2019 Train and Test Data 
- 2017 Train Data

## Methodologies Attempted and Relevant Find-ings
### Context Free Method
Repository: Transformers Repository
First, we attempt to classify tweets into S, D, Q, C without the use
of parent or sibling tweets. Note that this attempt is substantiated as a tweet by itself could give reasonable information about its class. A question mark or a word like ’how’, etc, immediately hints at a query, while negative connotations could refer to a denial.
For this approach, we use Transformers: an attention mechanism that learns contextual relations between words (or sub-words) in a text as op- posed to directional models, which read the text input sequentially (left-to- right or right-to-left), the Transformer encoder reads the entire sequence of words at once. Therefore it is considered bidirectional, though it would be more accurate to say that its non-directional. This characteristic allows the
2

model to learn the context of a word based on all of its surroundings (left and right of the word).
We use SimpleTransformer: This library is based on the Pytorch Trans- formers library by HuggingFace. Using this library, you can quickly train and evaluate several transformer models. We first preprocess the data and convert the JSON tree structure to Tab separated values fed in a 2D array to the SimpleTransformer module.
The transformer then has two phases:
- The pre-train phase: which has already been done and is available - this is the phase where the transformer learns the structure of a language after being trained on a large corpus (eg: Wikipedia).
- The fine-tune phase: This is separate for every transformer, this is where we feed in our tagged data to the transformer and it learns the S, D, Q, C tags. For each of the 3 transformer models we used, fine tuning took about 5-6 hours.
We attempt three different transformer models here:
#### Bert
Details 12-layer, 768-hidden, 12-heads, 110M parameters. Trained on cased English text.The pre-training corpus for BERT is BooksCorpus (800M words) and English Wikipedia (2,500M words)
Accuracy: 70.71 %
#### Roberta
Details 125M parameters RoBERTa using the BERT-base architecture. RoBERTa uses 160 GB of text for pre-training, including 16GB of Books Corpus and English Wikipedia used in BERT. The additional data included CommonCrawl News dataset (63 million articles, 76 GB), Web text corpus (38 GB) and stories from Common Crawl (31 GB).
RoBERTa builds on BERTs language masking strategy, wherein the sys- tem learns to predict intentionally hidden sections of text within otherwise unannotated language examples. RoBERTa, which was implemented in Py- Torch, modifies key hyperparameters in BERT, including removing BERTs next-sentence pretraining objective, and training with much larger mini- batches and learning rates. This allows RoBERTa to improve on the masked language modeling objective compared with BERT and leads to better down- stream task performance.
Accuracy: 73.74 %
#### XLNet
Details 12-layer, 768-hidden, 12-heads, 110M parameters. XLNet was trained with over 130 GB of textual data.
XLNet is a generalized autoregressive pretraining method. It is bidi- rectional and heavily relies on the pretraining phase for language structure compared to the other two models using a method called Permutation Lan- guage Modelling.
Accuracy: 77.6 %
### Contextual Method
Repository: branchLSTM Repository
Given the poor performance of our context-free models (note that a
naive classifier that predicted ’Comment’ – the majority clas – all the time on the test set would have given an accuracy of 75.58 %), we move on to a contextual model.
The structure of the data in our project is in a tree format. Consider the example in the figure below.
Consider three starting tweets: B, C and D. All other tweets are replies to these. Now, for all the tweets in the sub-branches B, C and D, all the tweets make up the context. For example, for tweet G - tweets B, E, F and H make up the context. However, one way to look at this data, as we need to do stance classification, is to ignore the context due to siblings and look at only parental context.
Therefore, the context for G is B → F → G.
(Sibling context can be explored later)
The entire tweet tree is converted to such branches. For example, if the
tweet tree is as above, then the branches are:
B→E
B→F→G B→F→H C D→I

Now, this data is an extremely convenient format to input into a Recur- rent Neural Network. Consider the branch B → F → G:
Now, F has a tag on its stance with respect to B, and G has a tag with its stance with respect to F. Say, F is tagged support and G is tagged comment. The branch is given as input in parts at various timesteps, and the loss function can be calculated based on every output.

Since RNNs are known for forgetting historical context, we can use an LSTM to get past this problem. Hence, if we do not care about sibling context, a branch-LSTM is an ideal neural network to use.
The Lasagne module constructs a suitable LSTM given input hyperpa- rameters. We did not do a hyperparameter search but used the optimal values from the research paper in the repository. Once converted to branch structure, it is straightforward to feed it into the LSTM and train the model. Then, the test data is also converted to branches and tagged. We converted the data to the 2017 contest format, and the links to the data are available in the repo - which is the baseline code modified.
On running the model on the 2019 (Tweets only) data, the results are as follows:
 Accuracy: 95.77 % F-score: 0.756


This heavily beats the 84% accuracy of the winning model. While the two cannot be compared because we have only trained and tested on Twitter data as opposed to both Twitter and Reddit done by the winning submission, we are yet to study the reason for this accuracy.
### Model Comparison
### Differences from Original Timeline
According to the original timeline, Deliverable 2 included completion of Sub- task A which we have completed, although the results need to be analysed.
### Further Timeline
- Analysis of Subtask A results, possibly look at sibling context. • Implementing a working model of Subtask B.

</div>
