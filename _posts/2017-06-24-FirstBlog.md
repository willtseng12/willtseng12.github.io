---
layout: post
title: MTA Turnstile Data Project
---

For this week's Metis project, our team received a hypothetical letter from WTWY (Women Tech Women Yes) with a request for us to leverage the MTA turnstile data in NYC to determine a list of optimal subway stations form their street team to canvas and fundraise at for their upcoming gala event.

The data which is available freely online [MTA turnstile data](http://web.mta.info/developers/turnstile.html#main-content) consists of the cumulative number of entries and exits for each turnstile in a respective subway station up to a given date and time.

Considering that WTWY’s goal isn’t only to increase social awareness but to fundraise for their upcoming gala event, my teammate Matt suggested us to incorporate outside data (average property value from trulia and average household income from zipatlas) in our optimization process. From there we filtered out areas with property values less than 2 million.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog1_images/data.png)

In order to come up with a list of our high potential subway station to canvass at, my teammate Devin weighted the z-score of each of these criteria (subway traffic volume, average household income, average property value) with the ratio (6:2:2) for each station, and ranked them accordingly.

To calculate the individual z-scores of a specfic criteria:  
$$ z_{i} = \frac{x - \mu}{\sigma}$$  

To weigh the composite z_score for each station, we followed the ratio:  
$$z_{composite} = (0.6) z_{traffic} + (0.2) z_{property} + (0.2) z_{income}$$  

This is a map of the location that topped our list in the property value category, entry volume category, and both:  

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog1_images/map.png)


After finding the top stations, we wanted to take a look at the traffic volume during **different days of the week** and **time of the day** to determine when to canvass.

One of my teammate Joseph’s graph shows us that traffic volume is generally higher on the weekdays than the weekends. However, if one was to pursue a weekend strategy, the key takeaway here is that 14th Union Sq. and 34th Street Herald Sq. are where you want to put your canvassers at since not only do they have the highest average traffic volume among the statiosn on weekdays but also on the weekends.

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog1_images/bar_chart_most_appealing.png)

From another one of Joseph’s graphs, we can see, among the top stations we picked out, that the standard deviation of the traffic volume is generally lowest during mid-week, Wednesday and Thursday. From the standpoint of risk management, canvassers are more likely to encounter a consistent level of high entry volume at the station they canvass at during these days. 

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog1_images/line_chart_most_appealing.png)

I know you must be curious about that large spike for Chambers Street on Thursday. It’s one these anomalies and outlier situations we wish we had more time to dig in the data to find out why. Because our data set was taken from around April of 2017, some suggested that it could very likely be because of the Tribeca film festival held during this time of the year. But in general the phenomenon of mid-week drop in standard deviation among our other stations is consistent.

Lastly, we take the analysis to different times of the day in one of my graphs. The corresponding y-values along the x-axis is really the average entries between the following time period and the current one. For example, the corresponding average entries at 8 am is really the average entries between 8 am and 12 pm. We certainly seem to get a lot more traffic at these stations during the afternoon peak hours versus the morning peak. 34th St. Herald Sq. seems to get so much more traffic volume throughout the day such that its average entries at the 12 pm (average entries between 12pm to 4pm) is the peak value for some of the other stations!

![test](https://github.com/willtseng12/willtseng12.github.io/raw/master/images/blog1_images/time_of_day.png)

Strategy wise, mid-week afternoon seems to be the way to go for most stations in our list.  Bigger teams to 14th St. Union Sq. and 34th St. Herald Sq. since they are stations that toped both high property value and traffic volume criteria!

We recognize that there are much more we need to do to improve our analysis, most of which involves more time to clean the dataset: from further de-duplicating stations with similar names to handling observations of stations that resets entry counts once in a while. Given the time constraint, I see it as a good start and learning experience for a first project.

For more details regarding my code and approach, please visit [here](https://github.com/willtseng12/metis_projects_17/tree/master/mta_project)
  
