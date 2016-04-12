<div><center> <h1 style="font-size:50px; color: #a94442;">Welcome to the 89th Academy Awards!</h1> 
<h3 style="margin-bottom: 25px; color: #B19D30;">Using Amazon Movie Reviews to Analyze Oscar Nominations for Best Picture</h3> </center>
<center><h4 style="font-weight:normal">Robert Minnich, Ziyue Jin, Rong Wang, Aoyuan Lio, Josh Dillon</h3></center>
<center><h4 style="font-weight:normal; margin-bottom: 40px;">April 12, 2016</h4></center></div>

<i><h2 style="font-size:25px;">Overview</h2></i>
For our project, BRIEF OVERVIEW OF PROJECT HERE.

<i><h2 style="font-size:25px;">Movie Graph Theory</h2></i>
Give brief explanation of work here.

<div style="text-align: center;">
 <span style="float:center;width: 200px;">
   <IMG SRC="Slide1.jpg" ALT="image" width="400px">
 </span>
</div>

<i><h2 style="font-size:25px;">Network Analysis</h2></i>
Give brief explanation of work here.

<i><h2 style="font-size:25px;">Movies Seen by a Reviewer</h2></i>
Give brief explanation of work here.

<div style="text-align: center;">
 <span style="float:center;width: 200px;">
   <IMG SRC="Slide2.jpg" ALT="image" width="200px">
 </span>
</div>

<i><h2 style="font-size:25px;">Recommended Movies Based on Network Distance</h2></i>
Give brief explanation of work here.

<div style="text-align: center;">
 <span style="float:center;width: 200px;">
   <IMG SRC="Slide3.jpg" ALT="image" width="200px">
 </span>
</div>


<i><h2 style="font-size:25px;">Farther Movies from User Based on Network Distance</h2></i>
Give brief explanation of work here.

<div style="text-align: center;">
 <span style="float:center;width: 200px;">
   <IMG SRC="Slide4.jpg" ALT="image" width="200px">
 </span>
</div>

<i><h2 style="font-size:25px;">Longest Paths Between User and Movie</h2></i>
Give brief explanation of work here.

<div style="text-align: center;">
 <span style="float:center;width: 200px;">
   <IMG SRC="MDS_FINAL.png" ALT="image" width="600px">
 </span>
</div>

<i><h2 style="font-size:25px;">Logistic Regression Model to Predict Oscar Winners</h2></i>
As part of our analysis, we were curious if it would be possible to predict the winner of Best Picture based on information gathered through Amazon movie reviews. We began by joining the Amazon data with our Oscars data, which included variables such as whether or not they won Best Picture, and what year they were nominated in.
After, we also joined the data with movie scores given by Twitter. Then we split into training and testing data.

This predictive model is most likely biased as we had to use reviews posted before and after the Oscar nomination and ceremony dates to have a large enough sample size.



```r
#################### Load Libraries ##################

library(dplyr)
library(data.table)
library(caret)

#################### Prepare Data ####################
movies <- read.csv("movies2.csv", stringsAsFactors=FALSE)
oscar_asin <- read.csv("project4-team-5/data/oscar_nominations.csv", stringsAsFactors=FALSE)
oscar_dates <- read.csv("project4-team-5/data/oscar_dates.csv", stringsAsFactors=FALSE)
twitter_data <- read.csv("project4-team-5/data/oscar_winners_FINAL_twitter2.csv", stringsAsFactors=FALSE)
twitter_data[14,]$Title = "Crouching Tiger, Hidden Dragon"
twitter_data$Year<-NULL
twitter_data$Win<-NULL
twitter_data$ASIN<-NULL
twitter_data<-twitter_data[1:98,]

# Take first genre
for(i in 1:length(twitter_data$Genre)){
  twitter_data$Genre[i]<-strsplit(twitter_data$Genre[i], "|", fixed=TRUE)[[1]][1]
}

oscar_movies <- left_join(oscar_asin, movies, by = c("ASIN" = "product_productid"))
final_data <- subset(left_join(oscar_movies, twitter_data, by = c("Title" = "Title")), select=c(3,8,15,16))

# Split into training and testing data
train<-sample_frac(final_data, 0.7)
sid<-as.numeric(rownames(train))
test<-final_data[-sid,]
```

Creating the logistic regression model was fairly easy. We chose to go with this model as it works well with categorical data, in our case, whether or not a movie wins Best Picture after being nominated.


```r
# Fit logistic regression model
fit <- glm(Win~Score+factor(Genre)+review_score,data=train,family=binomial(link='logit'))
fit.result <- predict(fit, test)
fit.result <- ifelse(fit.result > 0.5,1,0)
error <- mean(fit.result != test$Win)

# Percent correctly classified
print(paste0('Percent classified correctly ', (1-error) * 100, "%"))
summary(fit)

# Statistically significant variables
summary(fit)$coeff[-1,4] < 0.05
```

Our model fits very well, with an average of 81% accuracy in correctly choosing which nominated movies would win Best Picture. In addition, we analyzed the variables within the model to determine statistically significance. Interestingly, the Amazon movie review score was not deemed statistically significant, while the Twitter score and main genre were significant.


<i><h2 style="font-size:25px;">Shiny App</h2></i>
Give brief explanation of work here.



