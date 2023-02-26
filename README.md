# Introduction

If you are reading this and either actively play or want to have an idea of the reletive impact of certain roles on the outcome of a League of Legends match, this data may intrest you. It will also improve your knowledge of the game.

2022_LoL_esports_match_data_from_OraclesElixir.csv is a data set from _____ which covers Pro matches from the year of 2022 from most major Splits and Regions.

It contains data from 12436 matches, covering a total of ____ rows. Each row contains ____ columns of information.

The data set contains statistical information about each match played: some of which include: 

> datacompleteness : the completion of the statistics in the row, 'partial' if there are columns that are missing when they should not be
> position : the position played by the player in each row, or 'team' if the row contains team based statistcs
> result : the outcome of the match, 1 if win, 0 if loss
> totalgold : the total gold earned by the player, or in team rows it is total gold earned by the team
> goldat10 : gold earned at 10 minutes by the player, or in team rows it is the gold earned by the team at 10 minutes
> opp_goldat10 : similar to the above column, but it is for the enemy play in the same role as the row, or enemy team
> golddiffat10 : the difference between the players gold, and the enemy player in the same role
> goldat15 : same as the previous goldat column except for gold at 15 minutes
> opp_goldat15 : same as the previous goldat column except for gold at 15 minutes
> golddiffat15 : same as the previous goldat column except for gold at 15 minutes
> xpat10 : similar to the gold column, however instead of gold it is xp
> opp_xpat10 : similar to the gold column, however instead of gold it is xp
> xpdiffat10 : similar to the gold column, however instead of gold it is xp
> xpat15 : similar to the gold column, however instead of gold it is xp
> opp_xpat15 : similar to the gold column, however instead of gold it is xp
> xpdiffat15 : similar to the gold column, however instead of gold it is xp

With all this information we can hope to answer the question of if Jungle is 'OP', as many people say, or if it is no better than the others. That is, does the amount by which jungle is winning increase the tendency for the game to be won compared to other roles. Furthermore, with the steps we take to answer this question, we can look at and observe the same for other roles in League of Legends.


# Cleaning and EDA

## Cleaning

To begin, as we are looking for the performance of specific roles and not their teams we are able to remove the rows with the data on the overall performance and keep only the rows with player data.

Because of this many columns become nan, meaning no data. This is due to some columns representing aspects of the whole game such as the number of dragons, which kind of dragon, and other team styled objectives. Something which player rows did not have any of. As such we are able to remove these columns, I sped up this process by removing all columns with every entry as NaN, as these would not provide any information in any scenario.

Next, we are able to transform several columns from binary integer entries into booleans, these include datacompleteness, playoffs, and result.

> datacompleteness is either 'complete', which was mapped to True, or 'partial' which was mapped to False

> playoffs was 1 and 0, representing if it was a playoff or not, this True if 1 and False if 0

> result, representing the outcome of the game, which is either a win or loss, all columns were 1 or 0, 1 if win and 0 if loss, thus it can be mapped in the same way as playoffs

I also noticed an interesting occurance where 2 games resulted in both teams losing, I removed these games from the data set as 2 games is a very small fraction of 12436, and will provide very little information in the grand sceme of things.

Next, we have to decide which columns will help us answer our question. As we are looking for the performance of each role in each game, we will have to hone in on player statistics. Furthermore, from playing the game I find that a reasonable measure for the performance of role is the amount of gold they make, as performing well usually results in more gold. Another measure is the xp earned, which is gained from spending your time efficiently and having minimal downtime from deaths. As such I kept the following columns:

'datacompleteness','position','result','totalgold','goldat10','opp_goldat10','golddiffat10','goldat15','opp_goldat15','golddiffat15','xpat10','opp_xpat10','xpdiffat10','xpat15','opp_xpat15','xpdiffat15'

Position, and result are also necessary to organize the data into their varius catagorise.

With the datacompleteness catagory, entries with the False entry were missing the majority of the columns above to NaN values. This lead to the inevitable need to drop all of these columns. Despite losing roughly 14% of the data from this proceedure, all of the major leauges were left untouched. Furthermore, in reference to the game this should not matter as the completeness of the data has very little to do with which role won the hardest. I suspect it has to do with the url column as all if not almost all games with partial data completeness had he 'https://lpl.qq.com/' url, while there were no 'https://lpl.qq.com/' urls in the complete games.

Finally, we will now create the columns which I believe will be helpfull in assesing our question. We want a way to measure the success of a role within only their role and not the whole game, as such we can compare each player with the opponent. As we are using gold and xp as out measurements, we can place earned gold over the sum of earned gold and earned gold by opponent. This lets us measure the proportion of pooled gold within said role during the game.

A quick example is if I made 10 dollars, and you made 30. My pooled 'gold' proportion is 0.25, while yours is 0.75. As such, if one was more successful within their role they would have a high score, ranging between 0 and 1. Furthermore this lets us keep how hard the rest of the team is winning outside of the role.

Another issue that comes up in games of league is the tendency for winning lanes to help their losing lanes, and this effect is carried throughout the game. An example is if top lane was doing extremely well, in their free time they might help jungle or mid, which would lead to them to begin winning as well. To reduce the influence of actions like this we will be using the gold and xp measurements taken at 10 and 15 minutes, as this is earlier in the game. Furthermore, from my experience around 15 - 20 minutes is when teams begin to group up and helping eachother more often than the previous times in the game.

With this in mind, the columns Pooled Gold Prop and Pooled XP Prop were created for both 10 and 15 minutes.

This concludes us preparing the data for analysis.

## EDA

Let us look at some relevant distributions and shape of our data

### Univariate Fig 1
~ insert here (pooled gold 15 minutes)

We can easily see that there is a symetrical nature to this, this is resulting from the creation of this statistic, as in every game each role will have a complementry entry of 1-proportion, and thus the data will always be reflected around 0.5. We can also see that its mean, mode, and median, are all roughly around this point, indicating a tendency for this proportion to usually be 50/50 for each role on each team, and very little games where certain games win hard.

### Univariate Fig 2
~ insert here (pooled xp 15 minutes)

We can easily see a very similar trend for this graph, a mirrored graph around 0.5, and the tendency for the data to be centered around 0.5. With the mean, median, and mode all being around this point as well. It tells a similar story to the previous graph.

### Bivariate Fig 1
~ insert here (Pooled Gold Prop, Win vs Loss)

Here we can see a very interesting trend where the distribution with winning teams having higher centeral measures than losing teams. This confirms our previous expectation where Pooled Proportion is a measure of how a player performs in their role. Furthermore, we can also see that there are entries that are well below 0.5 but still winning, this means in some games even if a player does worse, they are still able to win through the efforts of their team. However, the overall trend seems to be that winning temas have players with on average, higher pooled gold proportion.

### Bivariate Fig 2
~ insert here (Pooled Gold Prop, Win vs Loss)

Another trend can be seen here, especially if you only select 2 roles as it is easier to compare them. The same trend holdes as was in univariate distributions, where all roles are centered around 0.5 for the same reasons. But, we can also observe that some roles are further spread out than others, a greater variance. This means that some roles tend to have a more volitile playstile where one side ends up gaining a significant gold advantage to the other.

## Interesting Aggregates

Lets look at some interesing aggregates after grouping

### Pivot Table 1
~ insert here (pv1)

This table highlights the average gold proportions of each role at 15 minutes depending on if they won or lost. Here the earlier explained trends are highly evident, specifically that the totals of each column is one, in reference to the method we created the statistic from the original data. Another trend that is more clearly highlighted is the trend in bivarate fig 1, were we can see how the average win is higher than the average loss. Furthermore a trend in bivarate fig 2 is also evident, where we can see the separation between the means of the 2 rows is directly correlated to the variance of the distribution of the role displayed in the figure.

### Pivot Table 2
~ insert here (pv2)
~ insert here (pv2.agg)
This is a similar statistic, however with total gold after the game ended, rather than the proportion we created based on 10 and 15 minutes, pair this with its aggregate(calculated by winning team role gold / (winning team + losing team role gold)) and we can more clearly see that the proportions are much closer together, highlighitng the fact that as the games go on the gold advantage of each role tends to become uniform, reducing the evidence of the advantage a specific role had before the grouping of the team. This also highlights the usage of 10 and 15 minute advantages to end game advantages when determining how hard a role had won.

### Grouped DataFrame
~ insert here(grp1)

This is just the general overview of the grouping of the data based on the previous pivot tables for your viewing so you can more easily compare between each table if you would like.



