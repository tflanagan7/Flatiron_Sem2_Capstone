# Predicting MLB Player Performance
## Trevor Flanagan
***
### Project Overview
The Statcast Era in baseball has been a renasissance for baseball statisticians, and teams that can take advantage of the overwhelming bounty of data available in the sport give themselves a serious edge above their competition. The goal of this project is to build an ML model in Python capable of predicting MLB hitter performance in future seasons. To do this, we make use of the pybaseball package and import MLB player data from a combination of FanGraphs, Baseball Reference, and Baseball Savant. Through careful predictor selction and model tuning we were able to predict the WAR a player will get in a future season with a RMSE of 1.65, which is within 1 standard deviation for the stat.

![Baseball](https://www.futurity.org/wp/wp-content/uploads/2020/08/baseball-vision-training-batting_1600.jpg)

***

### Value of the Project

So what is the Statcast Era? Statcast was a **modern tracking technology** adopted by all 30 major league ballparks in 2015. It is an extremely high speed, and high accuracy automated tool that analyzes player movement, ball movement, and bat movement. From sprint speed to a 12-6 curveball's rpm Statcast is a very useful data source that is changing the game

Statcast's existence breathes new life into the role of **baseball statisticians who are now able to influence front office decision making, training and condition, and even the plays on the field**. 

To provide a few examples of ways the game has changed with the influence of statisticians:
* **The shift** is a much more common and exagerated phenominon, which will be banned in the 2023 season partially do the fact that it's too effective. The shift is where you'll **move players out of their normal positions to cover a part of the field the batter is statistically much more likely to hit the ball to**.
* It's no longer widely excepted that the best hitter in the lineup hits 3rd, some teams are placing their best hitters 2nd in the lineup now
* **The way managers manage their bullpen has changed**. The stats show that the 3rd time the opposing lineup faces your starting pitcher they start doing better, so in important games managers are pulling their starters earlier. When I was growing up watching the game you'd expect the starter who hasn't given up any runs will stay in and pitch all 9 innings if he keeps pitching well.

An additional business application exists for this project in the **sports betting industry**

According to an article published by Grand View Research in the 2022 Market Analysis Report 2022, the sports betting industry is "anticipated to grow at a comp annual growth rate (CAGR) of 10.2% from 2022 to 2030, which would make it a **182 billion dollar industry**. 

![Sport Bet](https://www.grandviewresearch.com/static/img/research/us-sports-betting-market.png)

For companies posting sports betting lines, you need to make sure you're confident in the liklihood of a certain outcome or a certain player performance.

Finally, I think this project is **useful across other sports**. Your sport might be growing and your an organization that want's to offer players you sponsor a competitive edge. Or you might be coaching a soccer team and the league your in is very stubborn and traditional so theirs an opportunity to be the **first to adapt with the usage of predictive sports analysis**.

***

### Modeling Workflow

#### 1. Identifying Predictors
We don't want our model to have 130 predictor variables for two main reasons
* That would cause some pretty long computing times
* The **Curse of Dimensionality** - as the number of dimensions increase the accompanying volume of the hyperspace explodes

So instead, we fit our data to a Simple Linear model and use a **Sequential Feature Selector** from sklearn to perform forward selection of the **best 15 variables** for determining WAR

**Predictor variables identified:** Age, IBB, SO, SB, BU, BB%, ISO, BABIP, WAR, Spd, PH, CB%, Pull%, Soft%+, Hard%+

#### 2. First Simple Model
For our baseline model we used sklearn's LinearRegression, this model produced a RMSE of 1.649 with validation data, which is less than 1 standard deviation for WAR across the 20 years of data collected. 

#### 3. Using PolynomialFeatures to Reduce Underfitting
The idea behind Polynomial features is that **not every stat in our model is likely to have a perfectly linear relationship with Next_WAR**. 

Take Age for example, which is in our model. One could expect that a player just entering the league is going to have to have some adjustment time to get used to the Major League level, and as they compete at that level for a few years they enter the peak of their career, a positive correlation between Age and Next_WAR. Once the same player passes his prime though, and continues to age you might expect you can start seeing a negative correlation between age and Next_WAR.

Adding polynomial degrees to our model make's it possible to **capture these non-linear complexities, at the risk of overfitting**


As we increased the number of polynomial features, the model performed better with the training data, but was becoming less accurate with the validation set. This is reasonable, because although we are ultimately concerned with producing a model that performs best with blind data, at this stage we're intentionally increasing overfitting at this stage with the hopes that in our third model we can reduce overfitting with with Regularization, while keeping the benefits from the polynomial factors addition to the model.

![degrees](/Images/degrees.png)

Choosing which # of polynomial degrees with does not have an objective answer, but for the sake of being through I tested each degree with the remainder of the model tuning processes we do below and by fractional margins a Polynomial Degree of 1 performs the best. So that's ultimately what was choses for the model. Which means it was decided to revert back to the simple Linear Regression model.

#### 4. Using Regularization to Reduce Overfitting
Although our model didn't show any signs of overfitting, with training and test data performing almost the same, we tested to see if we could reduce any hidden overfitting effects to improve our model's performance using regularization.

The regularization technique we chose was Ridge Regression, which uses an alpha value to add a regularization penalty and adjust minorly adjust our model's coefficients.

![alpha](/Images/alpha.png)

Selecting an alpha value also proved to be inconsequential, because the RMSE of our model with validation error at it's best was only improving by 0.001.

Although a small, and statistically insignificant improvement a change of 0.001 to RMSE with validation data was an improvement all the same. So **for our final model we selected the Ridge Regression model**.  

***

### Evaluating the final model
Testing the final model with our test data, the RMSE increased b y 0.05 up to 1.71. Which is not very significant, which suggests that we did not overfit the model

![highWAR](/Images/highWAR.png)

![lowWAR](/Images/lowWAR.png)

Our model is **performing pretty well at prediction seasons where players are going to have mediocre to poor WARs**.

**Where the model failed was with identifying when players are going to have exceptionally strong seasons**. This could make sense, players have "oddities" of seaasons from time to time, where through some combination of luck and streaky spurts of talent a player makes a name for themselves in the MLB.

In many cases a player doesn't come up to the MLB and make a splash their rookie year, and if they are great in their rookie year, they're still unproven, so they'll be hitting lower in the lineup  and getting less play opportunities. 

That said, the model was still able to predict within 1 standard deviation on average, which should definitely give some level of confidence.

That said, in terms of our business values **the model needs to be more consistent to provide industry value**. 

Whether you're a Team Manager, a front office General Manager, or setting the betting lines for a sport's betting company, **you want to feel like your model has an edge on what conclusions you could come up with with regular obesvations**. So while the model isn't completely failing, there are certainly additional steps that could stengthen it.

***

### Conclusion - Next Steps
1. Since we've identified that our model does poorly at predicting breakout performances, a solution could be that the model needs to find a way to **incorporate their Minor League stats**.

This would be most likely the greatest impact we can make on chipping away at the seemingly irreducible error that to a degree comes from the game's unpredictable nature.

2. Could we expand our model potentially to **track how a pattern of WAR over several seasons can predict Next_WAR**? Rather than predicting next season WAR off just the prior season's stats.

In combination with including minor league stats  this could also add an additional layer, where one bad season where you were coming off an injury causes the model to underrate a players capabilities.

3. **Lets test our model with prediction based off the 2022 season and track it's successes and failures**

![secondbase](https://github.com/tflanagan7/Flatiron_Sem2_Capstone/blob/main/Images/secondbase.PNG)
Interesting here, our model predicts Willy Adames to record the highest WAR in MLB history. We can take this as another sign that the model could still use some tuning.

***

### Repository Navigation
Reproduction or recreation of this model can be done following the workflow in the Final Notebook
* See [the Final Notebook outlining step-by-step processes and their methodology in depth](https://github.com/tflanagan7/Flatiron_Sem2_Capstone/blob/main/Final%20Notebook.ipynb)

For consolidated project material look into the presentation powerpoint files.
* See [the Powerpoint Presenation](https://github.com/tflanagan7/Flatiron_Sem2_Capstone/blob/main/Presentation.pptx)



