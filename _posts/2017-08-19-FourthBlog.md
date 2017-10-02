---
layout: post
title: NYT Immigration Project
---

Prior to Metis, although I have read a lot about the groundbreaking findings and methods of natural language processing (NLP) in both academia and business applications, I did not have much hands-on exposure. Since I was required on my fourth project at Metis to conduct analysis with NLP using unsupervised learning methods, I decided to do some self-study, read some research papers, and practice clustering and topic modeling algorithms on New York Times articles. After pondering upon what aspects of NYT articles I want to conduct topic modeling on, I decided to look at articles that were related to immigration. The reason can be summed up by this graph obtained from the Migrant Policy Institute:  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/migrant_policy_institute.png)

As you can see, the number of immigrants as a percentage of US population has been steadily increasing since the 1970s. The number of immigrants on the other hand is also at an all-time high. Naturally along with that increase, especially recently in 2017, there have been many questions and issues regarding immigration that are being raised and heavily addressed politically, socially, and culturally. Therefore, I thought it was important to conduct a study of how the issues have evolved through time. I was interested in discovering what were the questions being asked? What are some timeless themes? What are some new and emerging subtopics, or for better re-emerging topics? But most importantly, I think it is a great way to stay informed about the current state of our society through the lens of a specific hot issue.  

In terms of the data, I had access to the NYT articles API. I had to call NYT to raise my request limit so that I could complete the project in time. Initially, I retrieved 14,000 articles that spanned from 1981 all the way to 2017.  I stored the data in MongoDB and read it into Pandas. However, I soon realized that the result from the API only contained short snippets and abstracts of the articles. Being ambitious, I paid 4 dollars to get the student subscription and used Scrapy to scrape the full articles off NYT's website. However, this was easier said than done. Some of the urls I got from the API that I then scraped from were expired, meaning some articles no longer exist. Moreover, NYT seems to have some anti-scraping measure implemented. After every so many x-path requests, NYT would redirect the request. After multiple iterative process, I was able to get 15,643 articles, so not too bad I suppose.  

In order to topic model across time, I vectorized my documents into a bag of words of frequency counts and ran PCA on it since it had over 2000 dimensions [curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality). Afterwards, I tried clustering with K-means with 4 centroids. The number of centroids was picked based on the inertia curve. The curve informed me at what point did the marginal amount of info captured by adding another centroid decreases.  

Here are my results for K-means clustering:  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/kmeans_cluster.png)

As you can see, one is able to identify certain topics and themes despite some overlaps. Because K-means just returned me the cluster, I could only look at these articles based on their headlines which were not very informative unless I read through each article one by one and decide whether the algorithm clustered well. That would be practically inefficient and would take too long. Therefore, I decided to take other approaches. After trying out many different things, I eventually went with vectorizing my documents through TFIDF and topic modeling with NMF. NMF can be understood roughly as a matrix decomposition algorithm that decompose a document to word (vocabulary) frequency matrix into two matrices: a document to component matrix and a component to words matrix where all values are positive in the decomposed matrices. Below is a simple visualization of the matrix decomposition. Basically every column in $V$, let us call it $v$, can be understood as the linear combination of the columns of $W$ weighted by $h$, the columns of $H$. THe valuesof the decomposed matrix are estimated. The cost function is typically some measure of the Euclidean distance between the estimated and the actual matrix $V$. The update is calculated typically through gradient descent or other numerical estimation methods.  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/nmf.png)

The number of components needs to be manually specified. NMF has been known on average to perform just as well as other topic modeling algorithms; however, when trying to model more and more topics, it can sometimes produce low quality topics with incoherent keyword combinations ([Stevens et al., 2012](https://aclweb.org/anthology/D/D12/D12-1087.pdf)). Therefore, through a lot of trial and error and some subjective evaluations, I went with 7 components. After running NMF on articles separated by year of publication date, I begin matching similar topics evaluated by their [Jaccard index](https://en.wikipedia.org/wiki/Jaccard_index) score through an iterative algorithm, summing the counts of documents in those categories together. For topics that are distinct enough from previous occurring topics (Jaccard index == 0), I created a topic series of their own starting at the given time period they appear, potentially matching it with future similar topics.  

After the matching process is done, I built a D3 visualization of immigration topic changes and evolution through time:  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/topic_distr.png)

There was some design choices that went into the construction of this graph both for visuazliation purposes and on a more philosophical level. For topics that did not emerge prior to a given year, I was hesitant to simply give them a count of 0 which would make their emergence look alot less continuous (more like a sudden blob of color popping up or disappearing), giving our eyes a hard time. Secondly on a more philosophical level: a topic hardly ever emerges out of the blue suddenly in time, but is rather always present in some form or another, slowly rising in importance or slowly fading away, but never completely gone once and for all.

The graph still definitely looks a bit too colorful, but let us walk through this graph a step at a time. 
For the keyword category under terrorism and security, this is what we have:   

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/terror.png)  

One sees that there are two time periods that has much news regarding terrorism and security associated with article searches on immigration, namely the late 90s and early 2000s right after 9/11. One sees that some of the keywords returned by the NMF model are names of extremist leaders.  

Another interesting subtopic is the theme on border and boundary:   
  
![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/border.png)

If you take a close look at the graph, some notable historical events happened during these time periods. On the very left around 1982 is the Latin American debt crisis where many workers then tried to cross the Mexican American border looking for employment in the US. We then have in 1989 the fall of the Berlin Wall. And finally 1994, the ratification of the NAFTA trade agreement. Now you might wonder with all the talks on TV about Donald Trump building a wall these days and the crisis in the Middle east, how is the frequency immigration news related to this subtopic almost nonexistent. I want to argue that it has not disappeared but simply bled into another adjacent topic: asylum and detainment:   

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/detainment.png)

In the asylum and detainment subtopic we also see the keyword “border” appearing but in a slightly different context. While “border” in the previous subtopic had a more economic and historical connotation, it’s connotation in the latter topic is much more relevant to the state of socio-political affairs we are seeing today in 2017. This is confirmed by much more relevant keywords to what is going on recently with all the refugee crises and Donald Trump’s stringent policies against immigrants and travelers to the US. I think this is a good place to emphasize that topics are often times not mutually exclusive and that it is usually as much an art as it is a science to really discern similarities and differences through keywords, leveraging some domain knowledge and interpretational skills to weave a coherent and probable story together.  

What about geographical subtopics outside of the US? We now take a quick peak at Europe:   

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/foreign.png)

Here we have an example of a re-emerging subtopic. One can see quite some coverage on Europe around the early 90s, most likely regarding to the foudning of the European Union under the Maastricht Treaty that had effects on policies regarding open borders, employment, and migration. The topic subsided only to re-emerge again recently with the refugee and migrant crises in the Mediterranean, Brexit, and the recent French election between Le Pen and Macron in 2017, covering more than 10% of the news distribution. Nevertheless, a large sum of NYT’s coverage nowadays regarding immigration has some significant political components:  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/political.png)

When I say “political”, what I really mean is party politics, especially during the recent election where immigration to the US is a topic heavily touched upon during the presidential debates along with Donald Trump’s victory. The subtopic account for more than 20% of the news related to immigration keywords, almost twice as much coverage as it had in 1980. This shows that immigration has become much more controversial of an issue in the political arena. However, what is perhaps most interesting for me to see is this next subtopic on immigrant life and education.  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/social_response.png)


I personally call this the category that encompasses the social responses, education policies, issues related to assimilating to life abroad as an immigrant, raising a family, perhaps even some immigrant’s personal memoire. The distribution of this subtopic has been steadily increasing since 2015. I suspect that in an age where topics such as immigration have been extremely politicized by individuals that may not necessarily know what it is like being an immigrant, there is perhaps a demand for a voice that resonates with people on a more personal level: articles that speak from the perspective of the immigrants themselves, the lived experience and the challenges regarding it.  

The previous graphs seem to be implying that immigration as a topic to consider has become much more political than it was in the past given the change in distribution of the subtopics. It suggests that we currently live in a period where much more is going around us both within the US and outside (as we see in the Europe subtopic). The question is how can we better visualize this increasing complexity in issue surrounding immigration and whether it is truly more complicated nowadays then it was in the past. Below are two word clouds generated from two random documents in my dataset, comparing the keywords emerging from the early 1980s to those in 2017. Let us take a look at the early 1908s first.  
  
![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/1980_wc.png)

Although one can definitely spot the presence of political figures in the word cloud, much of the theme in this text seems to only involved Hispanics. In terms of themes, we see issue regarding rights, representation, education, and solidarity in general. But now if we take a look at another random article in 2017, there seems to be much more going on.  
  
![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog4_images/2017_wc.png)

First thing first, one sees the names of recent political candidates, Trump (bottom right), Mike Pence (top right). Next we see keywords on religious groups such as Islam (bottom left) popping up along with extremist group such as ISIS (right above Trump). In terms of countries, there is China (upper right), Baltic (center top). Economic themes such as the market (bottom left corner) all emerge as interconnected issues in this document specifically. It is probably safe to say that issues regarding immigration has definitely become much more complex, interconnected with everything else. Because of its interconnectivity with other important issues, it has naturally been more heavily addressed in the political arena, making it emerge as a very crucial socio-political question that needs to be raised, addressed, and questioned during this time in history.  

You can see my technical implementations [here](https://github.com/willtseng12/metis_projects_17/tree/master/nyt_immigration_project)
