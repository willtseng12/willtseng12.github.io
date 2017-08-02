---
layout: post
title: Credit Card Default Project
---

After the successful conclusion of my previous movie data project, for the past two and a half weeks, 
I have been working on another data science project where I am looking at credit card client payment status, 
history, and default outcomes in Taiwan from April 2005 to September 2005, predicting whether they were 
eventually going to default their loans on the following month, October 2005. The purpose of the project was to 
tie in the theories we learned on classification learning algorithms with potentially practical areas of 
application. Moreover, it was also an opportunity for us to familiarize ourselves with the Amazon Web Service 
(AWS) platform, especially with their EC2 service, and PostgreSql database for data storage and access.

The data is available on the UCI machine learning repository here. Luckily there was only a minimal amount of 
cleaning that was required before it was modeling ready. I read the data from my AWS into Jupyter Notebook 
through sqlalchemy. I did some exploratory data analysis to take a first look at how different features might 
be potentially related to each other and the binary target variable, default.  Three specific features stood 
out to me at first sight. The first one being education, as we can see in the graph below that not surprisingly 
people who have higher education is associated with a lower probability of default as they are in general more 
likely to earn more than someone with a high school degree

[bar chart]

Aside from the education status, a person’s credit limit balance also stood out to be important. People who have 
higher credit limit balance usually indicates that they are either financially affluent or have proven themselves 
credit worthy through their past payment history.

[graph]

In this graph we have on the x-axis the standardized credit limit balance and y-axis whether a person has defaulted or 
not (indicated by 0 and 1). One can see that for credit balance more than 1 standard deviation above the mean, there 
are correspondingly fewer defaults, so it might be a good idea to include it in our model. Lastly, we definitely want 
to take a look at an individual’s payment status in the past 6-months.  I calculated the default rate of each payment 
status for each month. The I took the average of each status and this was the result:

[bar chart]

We can see that for any given month, if you are 1 month late on payment there is around a 33% chance of defaulting at the 
end of the entire period you are taking the average of. What is interesting is that this number climbs significantly after 
1 month where being late for 2 months is associated with over a 50% chance of defaulting. This can in general be a good 
rough guide to evaluating likelihood of default, but the takeaway here is payment status is most likely a good indicator of 
default outcomes because as interests accumulates, they the debtor even more.

Now that I have a set of believably important features, I ran different models with the following three features 
(standardizing the continuous one): credit limit balance, whether a person had a graduate degree, and number of times they 
were late in this 6-month period. Here are the result comparison:

[table]

Notice that in order for these metrics to make sense, we must compare them to a baseline model. In this case, 22% of entire 
dataset are default outcomes, then we can state that our baseline model has an accuracy score of around 22% if we predict 
everyone to default. Therefore, we can see in these metrics that our accuracy score increase by much across all models. 
However, what we are even more interested here are the recall scores. Intuitively, the recall indicates the percentage of 
defaults in our sample we were able to capture with our model while the precision indicates among those we predicted to 
default, how many actually did. Therefore, there is a tradeoff between the two scores in general: being stricter on catching 
defaults will naturally make you catch more non-defaulters as well.  Now for the purpose of this project, I considered recall 
to be a slightly more important metric to pay attention to, although in the real world there is usually an objective function 
that allows data scientist to find an optimal tradeoff point between these metrics.

We can see that the bootstrapped decision tree on the last line yielded the highest recall among the models at the cost of 
incorrectly catching more non-defaulters as indicated by the lower accuracy score compared to others. And surprisingly the 
Naïve Bayes classifier seem to perform just a good as our support vector machine considering the former’s ‘naïve’ conditional 
independence assumption among the features which is typically not true.

The models in general don’t look too bad I suppose. But I think we can still do a bit better in terms of raising that recall 
a bit more. Therefore, I went back to doing some more feature engineering because it occurred to me that perhaps our being 
late on a payment itself was not a very strong predictor whether someone was going to default. I say that because being late 
on payment usually indicates a further underlying problem, that is, a person is financially unable to pay. So what we really 
need is to create derive some variables that capture a person’s ability to pay.

Here are my attempts:
Let us define the gap as the difference between what a person pay and their bill:

$$gap_{i} = billAmt_{i} – payAmt_{i}$$

$$gap_{mean} = \frac{1}{n} * \sum_{i=0}^n gap_{i}$$

$$gap_{std} = \sqrt{\sum_{i=0}^n frac{(gap_{i} – gap_{mean})^2}{n-1}}$$

The $$gap_{mean}$$ is an average measure of how much unpaid leftover a person carries over each month. The $$gap_{std}$$ 
measures how consistent a person was paying relatively to his bill each period. Lastly I want to add another binary variable 
$$lateSuddenLargeGap$$ indicating when:

-	A person possess gaps in this 6-months period that is 0.7 standard deviation above their gap mean  
AND  
- The person has been late for the final 2-months of the period (August, September)

The 0.7 standard deviation and months chosen are arbitrary thresholds; but, the point here is to create a feature that 
constructs profiles of clients who have past histories of not paying off large sum of their bills and have been delaying 
their bill payments 2-months before the result month (October). All these features are in essence derived with the goal of 
trying to gauge a person’s ability to pay.

After putting these new variables into our models, here was the results:

[table]

As you can see, our recalls have increased compared to the first model, some more significantly than the others. As one can 
see, the bootstrapped decision tree is still coming out as the classifier catching the most default in the data set at around 
70% (recall). So depending on firm’s strategy, decision tree is definitely a more aggressive classifier compare to other more 
moderate ones. Therefore, if a firm is worried about under extending loans to these false positive predictions from decision 
tree, it may be better for them to take adopt a more balance model such as the Support Vector Machine (SVM) that has a more 
balanced precision to recall score, catching less defaults but also not predicting too much false positives.

This tradeoff among the models can be seen in the following ROC curve:

[graph]

Not surprisingly, our decision tree classifier took a hit in term of its Area Under Curve (AUC) score, but still better than 
some such as our logistic regression model. On the other hand, SVM came out top followed by KNN and Naïve Bayes. Therefore, 
a more balanced approach might be more desirable than trying to maximize default catches at the cost of our precision score.

Overall, couple of major takeaways here are to initially look for that 2-months late status as seen in the bar chart earlier. 
It gives a very rough initial idea of the likelihood of a person defaulting. Also, look at whether a person is able to 
pay off large bills consistently throughout the period of evaluation. But even better, incorporating both into the analysis 
will provide a more holistic picture of your client’s situation, really allowing decision makers to optimize their lending 
decisions. 

I found this project to be challenging yet rewarding at the same time. A lot of thoughts went into extracting features through 
exploratory data analysis. Model selection and parameter turning were crucial parts of it as well. Some have asked me why I 
did not try random forest. The answer is that I have, but the classifier had somehow not performed better than 
bootstrap aggregating my decision tree, so I did not include it in my report.
