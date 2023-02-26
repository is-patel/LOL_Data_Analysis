# Introduction

If you are reading this and either actively play or want to have an idea of the reletive impact of certain roles on the outcome of a League of Legends match, this data may intrest you. It will also improve your knowledge of the game.

2022_LoL_esports_match_data_from_OraclesElixir.csv is a data set from oracleselixir.com which covers Pro matches from the year of 2022 from most major Splits and Regions.

It contains data from 12436 matches, covering a total of 149232 rows. Each row contains 123 columns of information.

The data set contains statistical information about each match played: some of which include: 

*	datacompleteness : the completion of the statistics in the row, 'partial' if there are columns that are missing when they should not be
*	position : the position played by the player in each row, or 'team' if the row contains team based statistcs
*	result : the outcome of the match, 1 if win, 0 if loss
*	 totalgold : the total gold earned by the player, or in team rows it is total gold earned by the team
*	goldat10 : gold earned at 10 minutes by the player, or in team rows it is the gold earned by the team at 10 minutes
*	opp_goldat10 : similar to the above column, but it is for the enemy play in the same role as the row, or enemy team
*	golddiffat10 : the difference between the players gold, and the enemy player in the same role
*	goldat15 : same as the previous goldat column except for gold at 15 minutes
*	opp_goldat15 : same as the previous goldat column except for gold at 15 minutes
*	golddiffat15 : same as the previous goldat column except for gold at 15 minutes
*	xpat10 : similar to the gold column, however instead of gold it is xp
*	opp_xpat10 : similar to the gold column, however instead of gold it is xp
*	xpdiffat10 : similar to the gold column, however instead of gold it is xp
*	xpat15 : similar to the gold column, however instead of gold it is xp
*	opp_xpat15 : similar to the gold column, however instead of gold it is xp
*	xpdiffat15 : similar to the gold column, however instead of gold it is xp

With all this information we can hope to answer the question of if Jungle is 'OP', as many people say, or if it is no better than the others. That is, does the amount by which jungle is winning increase the tendency for the game to be won compared to other roles. Furthermore, with the steps we take to answer this question, we can look at and observe the same for other roles in League of Legends.


# Cleaning and EDA

## Cleaning

To begin, as we are looking for the performance of specific roles and not their teams we are able to remove the rows with the data on the overall performance and keep only the rows with player data.

Because of this many columns become nan, meaning no data. This is due to some columns representing aspects of the whole game such as the number of dragons, which kind of dragon, and other team styled objectives. Something which player rows did not have any of. As such we are able to remove these columns, I sped up this process by removing all columns with every entry as NaN, as these would not provide any information in any scenario.

Next, we are able to transform several columns from binary integer entries into booleans, these include datacompleteness, playoffs, and result.

*	atacompleteness is either 'complete', which was mapped to True, or 'partial' which was mapped to False

*	playoffs was 1 and 0, representing if it was a playoff or not, this True if 1 and False if 0

*	result, representing the outcome of the game, which is either a win or loss, all columns were 1 or 0, 1 if win and 0 if loss, thus it can be mapped in the same way as playoffs

I also noticed an interesting occurance where 2 games resulted in both teams losing, I removed these games from the data set as 2 games is a very small fraction of 12436, and will provide very little information in the grand sceme of things.

Next, we have to decide which columns will help us answer our question. As we are looking for the performance of each role in each game, we will have to hone in on player statistics. Furthermore, from playing the game I find that a reasonable measure for the performance of role is the amount of gold they make, as performing well usually results in more gold. Another measure is the xp earned, which is gained from spending your time efficiently and having minimal downtime from deaths. As such I kept the following columns:

```'datacompleteness','position','result','totalgold','goldat10',

'opp_goldat10''golddiffat10','goldat15','opp_goldat15','golddiffat15'

,'xpat10','opp_xpat10','xpdiffat10','xpat15','opp_xpat15','xpdiffat15'

```

Position, and result are also necessary to organize the data into their varius catagorise.

With the datacompleteness catagory, entries with the False entry were missing the majority of the columns above to NaN values. This lead to the inevitable need to drop all of these columns. Despite losing roughly 14% of the data from this proceedure, all of the major leauges were left untouched. Furthermore, in reference to the game this should not matter as the completeness of the data has very little to do with which role won the hardest. I suspect it has to do with the url column as all if not almost all games with partial data completeness had he 'https://lpl.qq.com/' url, while there were no 'https://lpl.qq.com/' urls in the complete games.

Finally, we will now create the columns which I believe will be helpfull in assesing our question. We want a way to measure the success of a role within only their role and not the whole game, as such we can compare each player with the opponent. As we are using gold and xp as out measurements, we can place earned gold over the sum of earned gold and earned gold by opponent. This lets us measure the proportion of pooled gold within said role during the game.

A quick example is if I made 10 dollars, and you made 30. My pooled 'gold' proportion is 0.25, while yours is 0.75. As such, if one was more successful within their role they would have a high score, ranging between 0 and 1. Furthermore this lets us keep how hard the rest of the team is winning outside of the role.

Another issue that comes up in games of league is the tendency for winning lanes to help their losing lanes, and this effect is carried throughout the game. An example is if top lane was doing extremely well, in their free time they might help jungle or mid, which would lead to them to begin winning as well. To reduce the influence of actions like this we will be using the gold and xp measurements taken at 10 and 15 minutes, as this is earlier in the game. Furthermore, from my experience around 15 - 20 minutes is when teams begin to group up and helping eachother more often than the previous times in the game.

With this in mind, the columns Pooled Gold Prop and Pooled XP Prop were created for both 10 and 15 minutes.

In the end, we are left with a dataframe that looks like this:

<div class="table-wrapper" markdown="block">
| position   | result   |   totalgold |   goldat10 |   opp_goldat10 |   golddiffat10 |   goldat15 |   opp_goldat15 |   golddiffat15 |   xpat10 |   opp_xpat10 |   xpdiffat10 |   xpat15 |   opp_xpat15 |   xpdiffat15 |   Pooled Gold Prop (10 min) |   Pooled Gold Prop (15 min) |   Pooled XP Prop (10 min) |   Pooled XP Prop (15 min) |
|:-----------|:---------|------------:|-----------:|---------------:|---------------:|-----------:|---------------:|---------------:|---------:|-------------:|-------------:|---------:|-------------:|-------------:|----------------------------:|----------------------------:|--------------------------:|--------------------------:|
| top        | False    |       10934 |       3228 |           3176 |             52 |       5025 |           4634 |            391 |     4909 |         4953 |          -44 |     7560 |         7215 |          345 |                    0.50406  |                    0.52024  |                  0.497769 |                  0.511675 |
| jng        | False    |        9138 |       3429 |           2944 |            485 |       5366 |           4825 |            541 |     3484 |         3052 |          432 |     5320 |         5595 |         -275 |                    0.538051 |                    0.526543 |                  0.533048 |                  0.487403 |
| mid        | False    |        9715 |       3283 |           3121 |            162 |       5118 |           5593 |           -475 |     4556 |         4485 |           71 |     6942 |         6789 |          153 |                    0.512648 |                    0.477827 |                  0.503927 |                  0.505571 |
| bot        | False    |       10605 |       3600 |           3304 |            296 |       5461 |           6254 |           -793 |     3103 |         2838 |          265 |     4591 |         5934 |        -1343 |                    0.521437 |                    0.466155 |                  0.522303 |                  0.4362   |
| sup        | False    |        6678 |       2678 |           2150 |            528 |       3836 |           3393 |            443 |     2161 |         2748 |         -587 |     3588 |         4085 |         -497 |                    0.554681 |                    0.53064  |                  0.440212 |                  0.467614 |
</div>

This concludes us preparing the data for analysis.

## EDA

Let us look at some relevant distributions and shape of our data

### Univariate Fig 1

<iframe src="assets/ufig1_1.html" width=800 height=600 frameBorder=0></iframe>

We can easily see that there is a symetrical nature to this, this is resulting from the creation of this statistic, as in every game each role will have a complementry entry of 1-proportion, and thus the data will always be reflected around 0.5. We can also see that its mean, mode, and median, are all roughly around this point, indicating a tendency for this proportion to usually be 50/50 for each role on each team, and very little games where certain games win hard.

### Univariate Fig 2

<iframe src="assets/ufig2_1.html" width=800 height=600 frameBorder=0></iframe>

We can easily see a very similar trend for this graph, a mirrored graph around 0.5, and the tendency for the data to be centered around 0.5. With the mean, median, and mode all being around this point as well. It tells a similar story to the previous graph.

### Bivariate Fig 1

<iframe src="assets/bfig1.html" width=800 height=600 frameBorder=0></iframe>

Here we can see a very interesting trend where the distribution with winning teams having higher centeral measures than losing teams. This confirms our previous expectation where Pooled Proportion is a measure of how a player performs in their role. Furthermore, we can also see that there are entries that are well below 0.5 but still winning, this means in some games even if a player does worse, they are still able to win through the efforts of their team. However, the overall trend seems to be that winning temas have players with on average, higher pooled gold proportion.

### Bivariate Fig 2

<iframe src="assets/bfig2.html" width=800 height=600 frameBorder=0></iframe>

Another trend can be seen here, especially if you only select 2 roles as it is easier to compare them. The same trend holdes as was in univariate distributions, where all roles are centered around 0.5 for the same reasons. But, we can also observe that some roles are further spread out than others, a greater variance. This means that some roles tend to have a more volitile playstile where one side ends up gaining a significant gold advantage to the other.

## Interesting Aggregates

Lets look at some interesing aggregates after grouping

### Pivot Table 1

| result   |      bot |      jng |     mid |      sup |      top |
|:---------|---------:|---------:|--------:|---------:|---------:|
| False    | 0.479201 | 0.483939 | 0.48416 | 0.484915 | 0.484806 |
| True     | 0.520799 | 0.516061 | 0.51584 | 0.515085 | 0.515194 |

This table highlights the average gold proportions of each role at 15 minutes depending on if they won or lost. Here the earlier explained trends are highly evident, specifically that the totals of each column is one, in reference to the method we created the statistic from the original data. Another trend that is more clearly highlighted is the trend in bivarate fig 1, were we can see how the average win is higher than the average loss. Furthermore a trend in bivarate fig 2 is also evident, where we can see the separation between the means of the 2 rows is directly correlated to the variance of the distribution of the role displayed in the figure.

### Pivot Table 2

| result   |     bot |      jng |     mid |     sup |     top |
|:---------|--------:|---------:|--------:|--------:|--------:|
| False    | 12372.3 |  9863.75 | 11585.2 | 6935.74 | 11285.8 |
| True     | 14858.6 | 11838.1  | 13689   | 8311.77 | 13306.8 |

| position   |   Pooled Total Gold Prop |
|:-----------|-------------------------:|
| bot        |                 0.545653 |
| jng        |                 0.545488 |
| mid        |                 0.541621 |
| sup        |                 0.545123 |
| top        |                 0.541089 |

This is a similar statistic, however with total gold after the game ended, rather than the proportion we created based on 10 and 15 minutes, pair this with its aggregate(calculated by winning team role gold / (winning team + losing team role gold)) and we can more clearly see that the proportions are much closer together, highlighitng the fact that as the games go on the gold advantage of each role tends to become uniform, reducing the evidence of the advantage a specific role had before the grouping of the team. This also highlights the usage of 10 and 15 minute advantages to end game advantages when determining how hard a role had won.

### Grouped DataFrame

|                |   totalgold |   Pooled Gold Prop (10 min) |   Pooled Gold Prop (15 min) |
|:---------------|------------:|----------------------------:|----------------------------:|
| (False, 'bot') |    12372.3  |                    0.487315 |                    0.479201 |
| (False, 'jng') |     9863.75 |                    0.489246 |                    0.483939 |
| (False, 'mid') |    11585.2  |                    0.490277 |                    0.48416  |
| (False, 'sup') |     6935.74 |                    0.490018 |                    0.484915 |
| (False, 'top') |    11285.8  |                    0.490271 |                    0.484806 |
| (True, 'bot')  |    14858.6  |                    0.512685 |                    0.520799 |
| (True, 'jng')  |    11838.1  |                    0.510754 |                    0.516061 |
| (True, 'mid')  |    13689    |                    0.509723 |                    0.51584  |
| (True, 'sup')  |     8311.77 |                    0.509982 |                    0.515085 |
| (True, 'top')  |    13306.8  |                    0.509729 |                    0.515194 |

This is just the general overview of the grouping of the data based on the previous pivot tables for your viewing so you can more easily compare between each table if you would like.


# Assessment of Missingness

Here we will assess the missingness of values and the reason/tendency behind these missing values within our dataset, this will better help us understand where our data is coming from, and the potential issues we may come along using some of the columns.

## NMAR Analysis

Within this particular data set, I do not believe that there is a single NMAR column. This is due to the nature of the data generating process. This process as very little human involvment, the information from the games are taken directly from the game, thus if a game has one column of information they will have all columns of information from that game. If one column of information is missing it seems to have to do with the url and league of the game. 

Breaking down all of the columns, for things such as gameid and datacompleteness there are no missing values. Once we get to things such as url, league and split, they all seem to relate to eachother, where certain leagues seem to post urls, while most major leagues have no url, furhtermore the split is usually stated in major leagues. (I am defining a league to be major based on the amount of games coming from that region, examples being LDL and LPL). The other values all have to do with the game itself, which as explained before either have all the data or are missing most of the data(all from the same columns every time), this is directly related to the value in the data completeness column, which is further related to the league and url.

As such all columns are either MD, MAR, or MCAR.

## Missingness Dependency

As we have established that all missing data is dependant on another column, let's try to highlight this.

We will focus on the Split column, which is a method to define the time of year or what grouping they are playing in. There are several missing values in this column, specifically 46428 missing values, which is no small proportion of out data. 

First I will attempt to highlight a couple columns which Split is more likely than not to be dependant on.

### MAR on League

The League column covers the pro region in which these games are played. Here we will analyse the the relation between the league in which games are played and the tendency for split to be missing.

To do this we will perform many permutation tests and calculate the TVD based on the proportion of each league in missing and non missing respectively. 

But before this, we must calculate the TVD for our data, which is roughly 0.843. 

Then we will shuffle the missingness of these rows and check the proportion 1000 times, after this was done we find that 0 of them where greater than this statistic, this means 0.0%(<5%) of scenarios out of 100 lead to a scenario as or more extream than the one we observed, which is indicative of a high correlation.

As such we reject the possibility that split missingness is likely independant on league.

This leads to the conclusing that split is MAR dependent on league.

<iframe src="assets/mfig1.html" width=800 height=600 frameBorder=0></iframe>

In the above figure we can see the large difference between the proportions of different leagues within their respective missing or existing split. The large difference in distribution indicates that there is a relation.

### MAR on Total Gold

As I have explained the total gold column before we can skip straight to the permuation test.

However this time we will be using the average total gold of all rows as our test statistic.

Then, we again permutate the data and collect each of their means, and see how many of them fall above and below our observered t-statistic. This lead to the aquisition of a p-value of 0.008 or 0.8%(<5%) of the simulated statitics being more extreme than the observed, as such we must assume that it is more likely than not that Split is MAR and depedent on Total Gold.

<iframe src="assets/mfig2.html" width=800 height=600 frameBorder=0></iframe>

Above is the distribution of each missing and existing in the real dataset. I used probability desnity, as evidently there will be less frequency in a sample that is samller than the other; 40 thousand is half as much as 80 thousand.

Here we can see that despite being vary close to eachother, the means are still far enough appart that due to the large number of data points the difference is more than should be expected between the overall popultion and sample to be the same.

### Independant of Gold at 10 minutes

Similarly to the previous tests, and exactly the same as the last one we will run on the average of the total gold column, then permute.

This lead to the p-value of 0.173 or 17.3% of the simulated statistics being as or more extreme than the observed.

Unlike the previous 2 simulations, 17.3% is higher than the standard 5% cutoff, which I will be using in this analysis, and thus we must reject that is it more likely than not for Split to be dependant on gold at 10 minutes. This also means we must see Split as independant of Gold at 10 minutes, rather than dependant.

<iframe src="assets/mfig3.html" width=800 height=600 frameBorder=0></iframe>

Above is the distribution created similarly to the one from Total Gold, they also look very similar; however, this time the means are close enough to produce a higher p-value, one that is above our significance value, and thus we must reject the alternate, and assume that it is not likely that Split is dependant on Gold at 10 minutes.


# Hypothesis Testing

Finally, after doing all of this analysis on the rest of the dataset, we are able to hone in on our question and finally come to some sort of conclusion or a general idea of the trend, and if jungle really is 'OP'. 

First, to begin we must lay out our rules for this hypothesis test.

> Null Hypothesis - the tendency to win games with a winning jungler is the same as winning with another winning role

> Alternate Hypothesis - the tendency to win games with a winning jungle is greater than winning with another winning role

> Test Statistic - the average proportion of gold held by the winning jungler of the total gold of both junglers (defined by Gold Prop at 15 min)

> Significance Level - the standard 5%, as we are looking to see if jungle is signficantly more impactful than other roles

This test statistic was chosen based on the many reasons explained throught this process, but more sepcifically it allows us to isolate how successful a role was in a game, with minimal influence from other roles on their team. This will allow use to compare their semi-isolated success against their oponent to the outcome of the game. This ideally lets us see the tendency to win based on the success of each role.

We selected gold over XP, as it is easier to see a difference in Gold, not only from my experience in playing, but also the many graphical and statistical data displayed earlier. 

With this we will perform a standard hypothesis test, where we resample from the original data set many times, and calculate how many are as or more extreme than our observed value. 

This lead us to get the p-value of 0.8851, or 88.51% simulated samples had a higher average gold proportion at 15 min than our observed statistic. This is much higher than our signficance level of 5%.

<iframe src="assets/htfig1.html" width=800 height=600 frameBorder=0></iframe>

This will drive us to the conclusion that success in the jungle likely does not have a higher tendency to win games than success in other roles does.

Or finally, answering the question of "Is Jungle 'OP'?", with no, it is not.

# Extra Data

As we have already done the work and if you are curious, we can expore the p-values for other similar roles.

<iframe src="assets/htfig2.html" width=800 height=600 frameBorder=0></iframe>

Above we can see the other roles color coded

TOP - pink
JNG - red
MID - green
BOT - orange
SUP - yellow

(we can see bot laners have a clearly higher average gold proportion when winning than the average... the question now is "'Is ADC 'OP'?"), although one must keep in mind, when using the same sample distribution multiple times to come to multiple conclusions using multiple p-values the chance for a type 1 error increases, however, it does seem that BOT is well beyond the curve, and thus is very unlikely that a type one error will be commited when concluding based on only that p-value.

Another interseting figure is one similar to the Bivariate Figure 2, however with only winning teams, as opposed to winning and losing. We can more clearly see the distributions of roles when they are winning.

<iframe src="assets/htfig3.html" width=800 height=600 frameBorder=0></iframe>

And with that I will leave you on your way, hopefully believing that jungle is not as 'OP' as everyone claims. (Atleast in pro, and according to my assumptions and measures)