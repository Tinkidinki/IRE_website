
![Rumours](rumours.png)

<div style="text-align: justify"> 
Battling the surge of fake news is a multi-faceted, complicated project, a part of which is to detect whether rumours that are spreading are fake or real. One way in which this can be done is to analyse the public reaction to a rumour when broadcasted. The public often have information from a lot of other sources, and their collective reaction brings in valuable information in detecting whether a rumour is real or fake.
In order to analyse public reaction, we take some tweets on a topic and analyse all the replies. We classify the replies into support, deny, query and comment and later use this to verify the statement.
We achieved a 95% accuracy on the first task using branchLSTM, and 54% accuracy on the second task using a Random forest classifier. 
We have been given tagged data for training for this task. Here is a report containing all our work and results as well as a video run through of the same:
 </div> 

##  Description of the Dataset

<div style="text-align: justify"> 
For training purposes,  we have been providedwith 9 categories about topics that have gener-ated  a  lot  of  conversation  in  Twitter  and  Red-dit  -  for  example,  the  Charlie  Hebdo  shootingand the Sydney Seige.  Each category containsaround 120-150 tweets and their replies in theTwitter  tree  structure  or  Reddit  comment  treestructure  and  every  reply  is  tagged  with  Sup-port, Query, Comment, Deny. The test data alsocontains rumours that need to be classified astrue or false.  We have with us the training datafrom the 2017 and 2019 editions of the compe-tition, and the test data from 2019.The posts themseleves can be considered astrees,  with  parent  and  sibling  posts  providingcontext.   The  tagged  data  contains  whether  agiven tweet is S, D, Q or C with respect to itsimmediate ancestor. 
</div>

[2019 Train and Test Data](https://competitions.codalab.org/competitions/19938) 
<br>
[2017 Train Data](https://github.com/kochkinaelena/branchLSTM/tree/master/downloaded_data)
 

## Phase 1: Subtask A
<div style="text-align: justify">
This subtask was completed in the first phase ofthe project.  We write the details here for com-pleteness: The first subtask will deal with track-ing how other sources orient to the accuracy ofthe report in question (that we need to eventu-ally find the veracity of).  We are provided witha tree-structured conversation formed of postsreplying to the original rumourous post, whereeach post presents its own type of support withregard to the rumour.   We frame this in termsof  supporting,  denying,  querying  or  comment-ing on (SDQC) the claim.  The goal is to labelthe  type  of  interaction  between  a  given  state-ment and a reply post.  We attempt two distinctmethods to tackle this task:
</div>

### Context Free Method
#### Repository: [Transformers Repository](https://github.com/paliwal-jatin/Transformers.git)

<div style="text-align: justify">
 First,  we  attempt  to  classify  tweets  into  S,D,  Q,  C  without  the  use  of  parent  or  siblingtweets.  Note that this attempt is substantiatedas  a  tweet  by  itself  could  give  reasonable  in-formation about its class.   A question mark ora  word  like  ’how’,  etc,  immediately  hints  at  aquery,  while  negative  connotations  could  referto a denial.For this approach, we use Transformers:  anattention mechanism that learns contextual relations between words (or sub-words) in a textas  opposed  to  directional  models,  which  readthe text input sequentially (left-to-right or right-to-left), the Transformer encoder reads the en-tire  sequence  of  words  at  once.   Therefore  itis considered bidirectional,  though it would bemore  accurate  to  say  that  its  non-directional.This characteristic allows the model to learn thecontext of a word based on all of its surround-ings (left and right of the word).We  use  SimpleTransformer:   This  library  isbased  on  the  Pytorch  Transformers  library  byHuggingFace. Using this library, you can quicklytrain and evaluate several transformer models.We  first  preprocess  the  data  and  convert  theJSON tree structure to Tab separated values fedin a 2D array to the SimpleTransformer module.The transformer then has two phases: 
 <br>
 1.The  pre-train  phase:   which  has  alreadybeen  done  and  is  available  -  this  is  thephase  where  the  transformer  learns  thestructure of a language after being trainedon a large corpus (eg: Wikipedia). 
 <br>
 
 2.The  fine-tune  phase:  This  is  separate  forevery transformer, this is where we feed inour  tagged  data  to  the  transformer  and  itlearns the S, D, Q, C tags.  For each of the3 transformer models we used, fine tuningtook about 5-6 hours.
 </div>
 
 #### We attempt three different transformer models here:

#### Bert

<div style="text-align: justify">
<b>Details:</b> 12-layer, 768-hidden, 12-heads, 110Mparameters.  Trained on cased English text.Thepre-training  corpus  for  BERT  is  BooksCorpus(800M  words)  and  English  Wikipedia  (2,500Mwords)
<br>
<b>Accuracy:</b> 70.71 %
 </div>
 
 #### Roberta
 <div style="text-align: justify">
<b>Details:</b> 125M parameters RoBERTa using theBERT-base  architecture.   RoBERTa  uses  160GB  of  text  for  pre-training,  including  16GB  ofBooks  Corpus  and  English  Wikipedia  used  inBERT.  The  additional  data  included  Common-Crawl News dataset (63 million articles, 76 GB),Web text corpus (38 GB) and stories from Com-mon Crawl (31 GB). <br>
RoBERTa builds on BERTs language mask-ing strategy, wherein the system learns to pre-dict  intentionally  hidden  sections  of  text  withinotherwise unannotated language examples. <br>
RoBERTa,  which  was  implemented  in  Py-Torch, modifies key hyperparameters in BERT,including  removing  BERTs  next-sentence  pre-training objective, and training with much largermini-batches  and  learning  rates.    This  allowsRoBERTa to improve on the masked languagemodeling  objective  compared  with  BERT  andleads to better downstream task performance
<br>
<b>Accuracy:</b> 73.74 %
</div> 

 #### XLNet

<div style="text-align: justify">
<b>Details:</b> 12-layer, 768-hidden, 12-heads, 110Mparameters.   XLNet was trained with over 130GB of textual data.XLNet  is  a  generalized  autoregressive  pre-training  method. <br>
It  is  bidirectional  and  heav-ily relies on the pretraining phase for languagestructure  compared  to  the  other  two  modelsusing  a  method  called  Permutation  LanguageModelling.
<br>
<b>Accuracy:</b> 77.6 %
</div>

### Contextual Method
#### Repository: [branchLSTM Repository](https://github.com/Tinkidinki/branch-lstm-testing)
<div style="text-align: justify">
Given  the  poor  performance  of  our  context-free models (note that a naive classifier that pre-dicted ’Comment’ – the majority class – all thetime on the test set would have given an accu-racy of 75.58 %),  we move on to a contextualmodel. <br>
The structure of the data in our project is in atree format.  Consider the example in the figurebelow. <br>
Consider three starting tweets:  B, C and D.All other tweets are replies to these. Now, for allthe tweets in the sub-branches B, C and D, all the tweets make up the context.  For example,for tweet G - tweets B, E, F and H make up thecontext.  However, one way to look at this data,as we need to do stance classification, is to ig-nore the context due to siblings and look at onlyparental context.<br>
<div><img align="center" src="rumours.png" alt="Branch Image1"></img></div>
Therefore,  the  context  for  G  is  B→F→G.   The entire tweet tree is converted to suchbranches.  For example, if the tweet tree is asabove, then the branches are:<br>
<div><img align="center" src="rumours.png" alt="Branch Image2"></img></div>
Now, this data is an extremely convenient for-mat to input into a Recurrent Neural Network asevery post is tightly correlated to its immediateancestor, but also needs to learn context fromother posts in the branch.  Consider the branchB→F→G: <br>
Now, F has a tag on its stance with respect toB, and G has a tag with its stance with respectto F. Say, F is tagged support and G is taggedcomment. The branch is given as input in partsat various timesteps, and the loss function canbe calculated based on every output.<br>
Since RNNs are known for forgetting histor-ical context,  we can use an LSTM to get pastthis problem.   Hence,  if we do not care aboutsibling context, a branch-LSTM is an ideal neu-ral network to use. <br>
The  Lasagne  module  constructs  a  suitableLSTM  given  input  hyperparameters.    We  didnot  do  a  hyperparameter  search  but  used  theoptimal values from the research paper in therepository. Once converted to branch structure,it is straightforward to feed it into the LSTM andtrain the model. Then, the test data is also con-verted to branches and tagged.  We convertedthe  data  to  the  2017  contest  format,  and  thelinks to the data are available in the repository- which is the baseline code modified. <br>
On  running  the  model  on  the  2019  (Tweets only) data, the results are as follows:<br>
<b>Accuracy:</b> 95.77 % <br>
<b>F-score:</b> 0.756
</div>

### Model Comparison
<div> <img src="rumours.png"></img></div>
This  heavily  beats  the  84%  accuracy  of  thewinning model.  While the two cannot be com-pared because we have only trained and testedon Twitter data as opposed to both Twitter andReddit done by the winning submission, we areyet to study the reason for this accuracy. <br>
We  have  also  attempted  to  use  Bert  againfor contextual classification - using bigram sen-tences of ONLY a tweet and it’s ancestor, how-ever this also produced poor results with an ac-curacy of around 77%.
</div>

## Subtask B
<div style="text-align: justify"> 
 The goal of the second subtask is to predict theveracity of a given rumour.  The rumour is pre-sented as a post reporting or querying a claimbut deemed unsubstantiated at the time of re-lease. Note that for each of these posts we alsohave  the  public  opinion  we  have  analysed  inTask A. Given such a claim, the system shouldreturn a label describing the veracity of the ru-mour  as  true or  false  along  with  a confidencescore. <br>
 Based  on  the  rumour,  we  have  to  divide  itinto three classes - True (Class 0), False (Class1)  and  Unverified  (Class  2).    Unlike  the  pre-vious data, which had sequential dependence,the data here is in the form of a bag of wordsrepresenting the tweet or the Reddit post itselfand three more features comprising the supportratio, deny ratio and comment ratio from Task A(The query ratio is redundant.) We take two dif-ferent approaches to do this classification. First,we  use  all  available  features,  and  in  the  sec-ond  approach  we  completely  discard  the  fea-tures  describing  the  post  and  use  only  publicreaction.  For each approach, we use differenttypes of classifiers. <br>
 <div>
  
 ### Post Context and Public Reaction
 
 <div style="text-align: justify">
 The  confusion  matrices  when  we  use  all  fea-tures for various classifiers are as follows.  Thecorrect classifications are on the y axes, and thepredicted classifications are on the x axis. For agood result, the diagonal has to have maximumvalues (should be lightest according to the usedcolour scheme.) Thus, we can compare the dif-ferent results in the images given below. <br>
</div>

### Only Using Public Reaction!

<div style="text-align: justify">
The  results  using  only  public  reaction  -  Thesupport, deny and comment percentages - justthree  features  as  opposed  to  1750  in  the  firstcase to a post are given in the images below. <br>
</div>

## Conclusion
<div style="text-align: justify"> 
The  best  accuracy  using  all  the  post  featureswas  53.08  %  with  a  Random  forest,  whereasthe best accuracy using just public reaction is 54.32 % with an MLP Classifier. It turns out theaccuracy that was gotten by using 1750+ fea-tures could be surpassed by the three featuresdescribing public reaction.
</div>

## [Report](IRE_final.pdf)
## [Video](https://youtu.be/UysOJ0RfoMQ)

<p align="center"><iframe width="1040" height="585" src="https://www.youtube.com/embed/UysOJ0RfoMQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>

Project by: Omkar Gurjar, Jatin Paliwal, Dhawal Jain, Mahathi Vempati
