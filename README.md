# Cantonese-Counsellor-Chatbot
* The project  provides a dataset which consist of  1,028 Cantonese post-reply pairs on two common counselling topics for university students: test anxiety and loneliness
* This data set is used to train a retrieval-based chatbot. For details, please refer to our paper [Response Selection for a Virtual Counsellor](https://portland-my.sharepoint.com/:b:/g/personal/baikliang2-c_my_cityu_edu_hk/EaU76D80oOFJhVU6CPaKuckBqtOka_JLayLh1p8RrJschA?e=uAebW4)


## Content
* <a href="#Dataset">Dataset</a>
* <a href="#Approach">Approach</a>
* <a href="#Results">Results</a>

## <a name="#Dataset">Dataset</a>
We go through three stages to build our data set
* Post collection
We recruited 10 undergraduate students to collect sentences from Cantonese social media, such as Facebook and Instagram, that discuss loneliness and academic anxiety in general. The students collected a total of 4,744 posts.
* Post labeling.
 The students labelled each post with a counselling issue, which we refer to as a “symptom category”, addressed in counselling literature. We used the 26 symptom categories for the topic of test anxiety,
and the 20 symptoms for the topic of loneliness. The symptom category of each post was independently labelled by two annotators. In case of disagreement, a third annotator performed the labeling. If all three labels differed, the sentence was excluded from the dataset. Otherwise, the sentence was assigned the symptom category with the majority vote.
* Reply composition. 
Finally, the students composed a reply for each post, typically acknowledging its main point and offering brief advice. Staff at the counselling department at our university offered guidance with example replies.
* Examples

| Post|advice|Topic|Symptom|
| :----------------------------------------------------------- | :--------- | :---------  | :---------  |
|我唔開心，好似真係無人會理咁| 明白呢個感受會令人唔開心，試下對人敞開自己 |Loneliness| I have nobody to talk to|
|有左女朋友之後就無哂朋友| 或者試下多同同學開新話題| Loneliness| I lack companionship|
|我會答錯哂，然後我啊媽會鬧| 不要讓父母成為了你的壓力,盡了力便可| Test anxiety| I worry about what my parents will say|

## <a name="#Approach">Approach</a>
We compare three methods for constructing training data for the response selection task: given a user post, the chatbot is to select the best reply from the dataset.
The three methods are ``Symptom category-based regression``，``Weakly supervised regression`` and  ``Binary classification``
They all use RoBERTa whole-word masking (chinese-roberta-wwm-ext) as the pretrained model
* Symptom category-based regression
Intuitively, reply relevance is often correlated with its symptom category.
The assignment of relevance scores are shown in the below Table. The gold reply is assigned y<sub>i</sub> = 1. To train the model to distinguish between semantically close replies, we included all non-gold replies in the same symptom category, with the assigned relevance score of y<sub>i</sub>= 0.75; and all replies from the same symptom group, with
y<sub>i</sub> = 0.50. Further, we randomly selected M replies from the same topic (y<sub>i</sub> = 0.25), and M replies from a different topic (y<sub>i</sub> = 0).
We set M ={1,2,3,4,5,10}
| Reply type|score|
| :----------------------------------------------------------- | :--------- |
|Gold reply|1|
|Candidate reply in same symptom category |0.75|
|Candidate reply in same symptom group|0.5|
|Candidate reply in same topic|0.25|
|Candidate reply in different topic|0|

* Weakly supervised regression
In Weakly supervised regression, we we used a pre-trained response generation model in the open domain to predict the relevance score for the non-gold replies.
Specifically, we made use of [GPT2-chitchat](https://github.com/yangjianxin1/GPT2-chitchat) , a GPT-2 model that has been pre-trained on over 0.5M Mandarin dialogs in a similar manner as DialoGPT. The gold reply is assigned y<sub>i</sub>= 1. For
non-gold replies, we computed loss (r<sub>i</sub> ), which is the cross-entropy loss between r<sub>i</sub>and the reply generated by the model, and then assigned y <sub>i</sub> = max(0, 1−loss(r<sub>i</sub>)/ loss (gold)), to normalize the bias from different post.
In this Regression method, we used the gold reply and 10 non-gold replies for each post as the training data set.


## <a name="#Results">Results</a>
We used 5-fold cross validation to evaluate all the models We also measured the ceiling performance of the symptom category-based regression model, when it has access to the gold symptom category. 
![results1](/pictures/result1.png)
![results2](/pictures/result2png)



