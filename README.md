# Olympic-Games-Data-Pipeline

Introduction
__________________________________________________________________

The Olympic Games are the most watched sporting event in the world, drawing billions of viewers annually. The objective of our project was to aid the Olympic CTO and their staff find the country that scored the most points, the country with the most gold medals, the team with the most medals, and finally the athlete with the most medals in both All-Time and Last 5 Games categories. With this data we could determine which countries, teams, and athletes are the most dominant and if in recent years there has been a shift in the most dominant countries, teams, and athletes.
  
To accomplish this goal we first had to trace out the data provided and make sense of it. Once we had established an ER diagram of the data we created our staging tables and ingested the data. Our next step was to take the ER model and transform it into a Star Schema model for easier analysis. Once that was accomplished we used the Star Schema to create data products to answer the CTO’s questions. During that time we decided to provide an extra bit of information, how many medals every country had earned overall. With the data products made, all we needed to do was create some effective visualizations and write our report to the CTO.

Data Ingestion
__________________________________________________________________

- Documentation for source data

- SQL to create staging tables

- Load script with \copy commands


Data Transformation
__________________________________________________________________

- Transformation to Star Schema

The project required us to find the values of points, gold medals, and all medals for the countries, teams, and athletes in our dataset. The potential facts in our tables were age, weight, height, year, medal score, and medal. Since age, weight, and height were not indivisible, unique, and valuable information for answering our questions, we kept them in the dimensional tables. That left us with medal score and medal. Medal is not numerical, so it could not be considered an atomic fact because we would not be able to perform operations on it other than count. However, it provides the exact same information as the medal score, so we were able to drop it which also allowed us to reduce redundancy. In the end, we decided to use the medal score as our fact because it is numeric, additive, indivisible, unique, and represents the value of an event.

We chose the olympic events, athletes, olympiad, teams, and olympic committee tables to use as dimensions all linking back to our fact table called result_facts along with each row’s designated medal score. Similarly to medal, we also decided to drop the sport variable because it was not relevant to the questions we needed to answer and would have to be connected by a many to many relationship creating the need for a new table. 

- dbt models transforming to star schema

Transformation to Data Products
__________________________________________________________________

- top_score_overall & top_score_last_5: These two data products are variations of each other, both of them were created to answer the questions of which countries have the highest total score across every Olympic game ever (1896-2016), and which countries have the highest total score in the last 5 Olympic games. We defined medal score according to the following criteria: 0 points for no medal; 1 point for a bronze medal; 2 points for a silver medal; and finally, 3 points for a gold medal. The dataset top_score_overall sums up the total medal score of every country since the beginning of the Olympics in 1896 until 2016. The other data product top_score_last_5 sums up a country’s medal score in the last 5 Olympic games. 

- teams_most_medals_top_three.sql & teams_most_medals_last_five_games.sql: These two data products also answer the two parts of question three. They were created to identify the teams with the most medals of any kind, the first for the top three of all time, and the second for the top team for each country for the olympics games in the last five years. We did this by counting all of the medals, grouping by team, and then using either a limit or where clause with a descending order by to show the top values. 

- What country has take the most gold medals: As mentioned above, the data products created to answer the first questions helped immensely with answering this question. The aim with this data product was to find the top performing countries in the Olympics, both historically and recently. We grouped by country and the medal count to identify the top performing team by gold medal count. Similarly, when looking at the past 5 olympics, we changed that result to add a filter where we only selected olympics that took place after 2007, which included both summer and winter games. For the top 3 countries, we counted the number of medals, grouped by country and count, and put our result in descending order. For the last 5 olympics, we summed up the medal score, and followed similar steps to the last data product, this time we limited our query result to 5 after putting everything in descending order. The results that followed helped answer our questions of the countries that had won the most gold medals overall, and in the last 5 games.

- What athletes have won the most medals? (top 10) Related to the other data products that we chose, this data product was important to identify the most decorated athletes to have ever competed. It helped answer the final question about the top 10 athletes who had won the most medals across every olympic event. After we identified and assigned medal score values in the first data product, the medal tally was calculated by selecting the count of the number of medals won by each athlete across all olympics, grouping them by name, ordering them in descending order and limiting the results to 10. The top 10 following this were the most decorated olympians of all time according to the values in our dataset.

dbt model files to create and load the data into data product(s)


Data Analysis and Visualization
__________________________________________________________________

We used bar graphs to answer the questions that asked for the top teams, athletes, and countries because we felt that it was the clearest way to describe the relationship between the categorical variable of the team, player, or country and the numerical variable of the number of medals earned. The number of medals was counted by using the sum metric. We had considered pie graphs for these but decided that it only made sense if we showed the whole. Because of how we choose to limit the data to only top performers for easier readability, bar graphs made more sense for showing off the discrete data points and comparing them against each other. 

For the questions that asked for information about the relationship between countries we used the world map because it allowed for an intuitive understanding of where the medals were concentrated without having to compare the particular medal score totals while of course allowing the viewer to compare more directly by hovering their mouse over the country they’d like to examine. 
We tended to use pie graphs in addition to the country visualizations because it was able to proportion all of the values against each other, so we could see how often countries, teams, or athletes are winning in relation to others in their category. 

We chose an incrementally fading color scheme to emphasize how concentrated the scores are in certain regions, particularly for the global maps for the total medals for each country, total gold medals for each country, and top scoring countries in the last five games and the pie chart for the top country scores overall. We usually used color schemes with varied colors rather than a gradient to further highlight the difference between the thresholds of the different values.


Reflections and Challenges Addressed
__________________________________________________________________

Overall, this project gave us the opportunity to implement a data pipeline from beginning to end. We gained an understanding of how to ingest data from multiple sources, make data products from them, and then visualize them using graphs to address stakeholder needs. We realized how practical and well-built Superset and Jupyter Notebook were, and how much more difficult this project would be if we used less intuitive tools. The dataset given to us also helped us learn that sometimes just because we are given a table, in this case the sport table, it doesn’t necessarily have value to us as a dimension because it increases redundancy, reducing the efficiency of our pipeline. When we started to consider the specific goals we were required to meet when creating the pipeline, we came across identification and categorizing issues. One issue was that one of our datasets had thousands of duplicate keys, so we had to consider why the relationships between tables were creating overlap and whether the duplicates needed to be removed or if they were truly different data points. When we realized they were different data points, we learned how to troubleshoot faulty tables and how to produce unique keys to replace defunct keys. Also we realized just how ambiguous data could be, and had to ask ourselves things that required further research, like if we should count USSR and Russia as two separate national committees. This made us think that, as data engineers, having the skills to objectively view data in context and problem solve is essential. We are proud of our ability to problem solve as a team when the data wasn’t conducive to our needs and to take advantage of optimizing our workload when the dataset was a better fit for our needs.

