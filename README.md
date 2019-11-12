
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
#### Repository:
[Transformers Repository](https://github.com/paliwal-jatin/Transformers.git)

<div style="text-align: justify">
 First,  we  attempt  to  classify  tweets  into  S,D,  Q,  C  without  the  use  of  parent  or  siblingtweets.  Note that this attempt is substantiatedas  a  tweet  by  itself  could  give  reasonable  in-formation about its class.   A question mark ora  word  like  ’how’,  etc,  immediately  hints  at  aquery,  while  negative  connotations  could  referto a denial.For this approach, we use Transformers:  anattention mechanism that learns contextual relations between words (or sub-words) in a textas  opposed  to  directional  models,  which  readthe text input sequentially (left-to-right or right-to-left), the Transformer encoder reads the en-tire  sequence  of  words  at  once.   Therefore  itis considered bidirectional,  though it would bemore  accurate  to  say  that  its  non-directional.This characteristic allows the model to learn thecontext of a word based on all of its surround-ings (left and right of the word).We  use  SimpleTransformer:   This  library  isbased  on  the  Pytorch  Transformers  library  byHuggingFace. Using this library, you can quicklytrain and evaluate several transformer models.We  first  preprocess  the  data  and  convert  theJSON tree structure to Tab separated values fedin a 2D array to the SimpleTransformer module.The transformer then has two phases: 
 <br>
 1.The  pre-train  phase:   which  has  alreadybeen  done  and  is  available  -  this  is  thephase  where  the  transformer  learns  thestructure of a language after being trainedon a large corpus (eg: Wikipedia). 
 <br>
 
 2.The  fine-tune  phase:  This  is  separate  forevery transformer, this is where we feed inour  tagged  data  to  the  transformer  and  itlearns the S, D, Q, C tags.  For each of the3 transformer models we used, fine tuningtook about 5-6 hours.
</div>

## [Report](IRE_final.pdf)
## [Video](https://youtu.be/UysOJ0RfoMQ)

<p align="center"><iframe width="1040" height="585" src="https://www.youtube.com/embed/UysOJ0RfoMQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>

Project by: Omkar Gurjar, Jatin Paliwal, Dhawal Jain, Mahathi Vempati
