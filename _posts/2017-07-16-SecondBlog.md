---
layout: post
title: Predicting movie box office
---

For my second and third week at Metis, I picked up the project of trying to predict movie total domestic box office. 
It was the first project where I had design choices to make from start to finish, collecting raw data, and building 
a predictive model entirely from scratch. 

The question my study was specifically trying to answer was whether there were strong early indicators of how well 
a movie will perform, and what they were. As many industry experts will know, the movie business is a relatively 
unstable one, of which the success and failure of any undertakings can be largely unexpected. 
Yet, I wanted to know whether there were in any way still indicators that can be detected observed in general. 

For collecting data, I decided to build a web scraper using python’s Scrapy that crawled through featured movies list
from 2007 – 2017 on IMDB. Some of the content that I extracted from IMDB were: title, runtime, release date, MPAA rating,
IMDB score rating, number of reviews, production budget, opening weekend gross, total domestic gross, and number of 
Facebook likes. Because FB likes info was dynamically generated on the site (read constantly updates), 
I had to use another tool call Selenium to extract it from the web. Moreover, I also had to get CPI data 
from the Bureau of Labor Statistics to adjust for inflation

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/scrapy.png)
![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/selenium.png)


Although I crawled through a span of 11 years, many of the movies are relatively less well known and had a lot of missing 
information. This slimmed down my sample size to around merely 730 observations. I would have certainly liked to have 
collected more data, but had to kept moving forward due to time constraints.

I split my dataset into a training a test set and decided to build a regression model with the sklearn and 
statsmodel packages. My response variable is total domestic gross. During the feature selection process, 
I used [regularization](https://en.wikipedia.org/wiki/Regularization_(mathematics)) methods (Lasso, Ridge, Elastic Net) 
to eliminate features that were deemed un-predictive to prevent overfitting. 
I also used p-value feature selection to evaluate a variable’s predictive significance.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/train_test.png)

It basically came down to 4 variables: production budget, IMDB score rating, opening weekend gross, and number of FB likes. 
I had to log transform all my variables (including response) to make their distribution look slightly more normally distributed. 
I also had to add potential interaction terms as some of the RHS variables such as FB likes and 
opening weekend gross had interaction effects.

After training my model, my $$R^2$$ turned out to be around 0.76 when tested on my test set. All of my regressors 
were statistically significant although some had p-values that were less consistently below 0.05 when trained 
different times. Production budget was one of them. Although historically it has been known to be a good predictor, 
I think the problem lies in the fact that the numbers on IMDB are merely rough estimates and often times do not 
contain the true cost (excluding marketing and distribution cost), making those numbers not as accurate as they look. 
However, looking at the correlation plot below, there seems to be a relativly strong correlation. Conclusion about 
causal relationship however, may require more in depth studies.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/budget_vs_gross.png)

Some of the key takeaways from my study was that in general the model worked better for genres such as Sci-Fi and Comedy 
likely because we have more data on these genres as they are more receptive among the general audience. Unlike Sci-Fi and 
comedy, horror movies performed worse as they are perhaps receptive among a narrower set of audience to begin with 
no matter what went into the production.

IMDB ratings on first sight by looking at the correlation plot may not seem to have a strong (if any) correlation with 
total domestic gross. However, merely looking at a correlation plot could be misleading as there could be a third variable
that [confounds](https://en.wikipedia.org/wiki/Confounding) the true relationship between them that is not captured 
in the correlation plot. Thankfully, this problem is solved when running a regression since the result gives a 
[*ceteris paribus*](https://en.wikipedia.org/wiki/Ceteris_paribus) interpretation allowing the model to evaluate whether 
there are in fact causal relationships between the variables as reflected  by the p-value. And not surprisingly, 
the model tells us that it is statistically significant as higher rated movies  seems to attract more audience in general, 
holding another inputs constant.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/score_vs_gross.png)

Similar to IMDB ratings, the correlation plot does not quite indicate a strong relationship. But after taking out the confounding 
effect it was quite predictive among all genre types as it seems to be a good indicator of the pre-release
sentiment surrounding a movie that may heavily influence how well it does. The scatter plot shows that data points are quite
spread out but can still make out a linear relationship. Model estimates indicates that a 10% increase in FB Likes will likely 
result in a domestic gross increase by ≈ 6 % on average (holding other inputs constant). Production company may want to 
allocate a larger portion of their budget into marketing as we evidence that it heavily influences consumers’ 
impression of the movie.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/fb_vs_gross.png)

And lastly, opening weekend gross was among the strongest predictor in my model. Not surprisingly, as much of a movie’s
total gross is made during the first week of its release, this number can significantly predict the movie’s
eventual success (most of the time). The scatter plot below shows strong correlation to begin with.
In case anyone is interested in the outlier on the top left corner of the plot, it is Taken (2009) starring Liam Neeson. 

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog2_images/opening_vs_gross.png)

So where do we go from here? A more diverse set of data needs to be collected. I want to emphasize ‘diverse’ because not only
do we want more data point for more accurate inference, many of less well known movies are not included because we simply
have less data on them biasing our data-points toward already-hot releases. 

You can find more technical aspect of my project and my implementations [here](https://github.com/willtseng12/imdb_project)

