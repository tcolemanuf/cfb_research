# cfb_research

I don't know what I am doing, but just trying to jot down some notes and add in some code.

I took all the play-by-play data from cfbscrapr from 2018 to 2020 and placed it in a file named plays. I then filtered it by only year 2020. Then i wrote this code

offense_count <- plays_2020 %>% group_by(year) %>% summarise(rush_attempt = sum(rush), pass_attempt = sum(pass), num.plays = n()) %>% filter(num.plays > 300)

offense_count <- offense_count %>% mutate(
  Ratio = rush_attempt/(rush_attempt + pass_attempt)
)

glimpse(plays_2020)

![Screen Shot 2021-03-10 at 9 52 08 PM](https://user-images.githubusercontent.com/60517757/110728351-e3da5880-81ea-11eb-80b1-f1f1fc1945a7.png)

which showed me that offenses in 2020 ran the ball 51.68% of the time. In my head, i was saying but if running is less EPA efficient than passing shouldnt it be that the should run more. So, i dusted off an old econ game theory text book and then looked at a chapter in Mathletics by Wayne Winston to come up with the following analysis. 

![Screen Shot 2021-03-10 at 9 54 11 PM](https://user-images.githubusercontent.com/60517757/110728494-2f8d0200-81eb-11eb-81ef-7725d2752be0.png)

What this table is shwoing that when offenses chose to run or pass, how may times did they succeed. It showed that offenses succeeded slightly more when passing the football, and again in my head knowing that EPA is higher for that should indicate that they should pass more. But, what is the actual average of a positive EPA pass play and what is its converse of a negative EPA pass play. The same for Rushing. This would give you a Game Theory grid by which we could view all of CFB.

I put in this code after mutating in an epa_succ and epa_fail variable.
offense_epa <- plays_2020 %>% group_by(epa_succ, epa_fail) %>% summarise(
  Pass_epa_S = mean(EPA[pass==1], na.rm = T), 
  Rush_epa_S = mean(EPA[rush==1], na.rm = T), 
  num.plays = n(),
) %>% filter(num.plays > 300)

This was the grid.

![Screen Shot 2021-03-10 at 10 00 10 PM](https://user-images.githubusercontent.com/60517757/110729058-0ae55a00-81ec-11eb-9d59-058e4c3bcb4b.png)

Again this follows intuition. EPA per successful pass is higher, but also failure is a bit lower. EPA per successful rush is lower but it's floor is higher. So, now we can play this stylized game.

So, imagine this is like the old school Tecmo bowl excpet there are only two choices for the offense and defense. The offense can choose to run and the defense can choose to defend run or pass. Conversely the offense can choose to pass and the defense can choose to defend run or pass. If the defense guesses correctly they stuff you. What is the steady state game that both sides should choose? (note: this game was solved in the early 40s by Von Neumann and Morgenstern and they proved that the game would have one final answer, V the value of the game that both sides would adhere to) So, based on the table what is the guess, should we pass 100% of the time, or run 25% of the time, 50/50?

Let's just say my intuition was that passing should be a larger part of the game because it was a) more effective & b) more successful at producing epa's > 0

![unnamed](https://user-images.githubusercontent.com/60517757/110729884-75e36080-81ed-11eb-83ab-8675cd915c98.png)

Here is the final answer, the offense should run the ball over 60% of the time! The intuition is that because of the reasons i listed above and others that the defense will choose to defend the pass much more often than the run because they are so afraid of losing those explosive epa plays. Think of the striker position that did not exist until recent times, or how nickle packages are just the base package against the spread offense of the current era. What the chart is saying is that both the offense and defense can lock themselves into a guaranteed .18962 per play game which will maximize the minimal gain the offense will make while minimizing the maximum gain the defense will give up. I think it was further surprising that the analysis from my toy model indicates that the defense should drop its pass coverage to only 52% of the time to minimize its losses. 

Many grains of salt have to be taken with this analysis. This was for the hypothetical average offense in FBS taking on the hypothetical average defense. But, it does shed an interesting light in how one can think about strategizing for the upcoming season. Take for instance North Carolina who just lost some generational talent in the running backfield. So, you would expect that as their rush EPA drops they would do less of it and have Howell carry the team through the air. What this analysis is saying is the famous not-so-fast-my-friend quote from Lee Corso and that because teams will focus more on Howell and the passing game, they might have better opportunities at running this year if they can take advantage of them.
