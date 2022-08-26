---
title: "Do You Feel Lucky?"
date: 2022-08-26
categories:
  - blog
tags:
  - luck
  - hardwork
  - youtube
---

A few months (rather years) back, I wrote an article titled ["What is Success?"](https://aceking007.github.io/blog/What-Is-Success/) inspired by a [YouTube video.](https://www.youtube.com/watch?v=3LopI4YeC4I&ab_channel=Veritasium)  

**The premise**  
The question addressed in the video and article is simple: How much of success can we attribute to hardwork and how much of it can we attribute to luck?  
By carrying out a simple simulation, Derek Muller (the Veritasium guy) shows in the video that for being one of the 11 top astronauts graduating the class of 2017 in NASA, on an average the candidate needs to have a luck of 95%, assuming that 5% of the selection process is the result of luck. Of course, I have glossed over a few details and simplified the statement a lot. Nevertheless, it is a dramatic statement to make.

The main fact that kind of bugged me about this simulation was how did he come up with the assumption that 5% of the selection process is based on luck. I'm not quite sure, but I don't think anywhere he justifies this 5% limit imposed on the simulation (too lazy to re-watch the video). So, I thought it might be a good opportunity to re-run the simulation and test out a few different cases to see if we can quantify the effect that luck has in various scenarios.

**The simulation**  
I have carried out the simulation in almost the same way (detailed in the highlighted paragraph below), with a few variations. In particular, I have varied the effect of luck at 0.5%, 1%, 3%, 5%, 7%, 10%, and 15% as well as the total number of candidates at 100, 1000, 5000, 10000, 15000, 20000. The original simulation had values fixed at 5% and 18300.

>For the simulation, first we randomly generate skill and luck scores for each applicant out of 100. Then, based on the effect of luck, we take the weighted average of both scores to arrive at a final score for each candidate. The candidates are then ranked according to the final weighted score and the top 10 candidates are selected (deemed successful). This whole process is repeated a 1000 times over to produce a more statistically meaningful result.

Now, before going to the results, one should logically expect that the candidates that are selected when effect of luck is 10% will certainly have higher luck scores compared to candidates selected when the effect of luck is 1%, but the question is, how large of a difference will there be? and will the luck score be significant in both or any of the cases?


**Results and Thoughts**  
Below are tables detailing the results of the trial:  
*Table 1: Average luck score of selected candidates*
![Luck Score](/assets/images/luck_score.JPG)

*Table 2: Average skill score of selected candidates*
![Skill Score](/assets/images/skill_score.JPG)


As expected, we see that the luck scores tend to increase a bit as the effect of luck increases in the trial. But the increase is not as stark (at least for higher number of candidates) as one might expect. On the other hand, one might observe that the average luck score steeply increases as the total number of candidates applying for the same position tend to increase (with almost reaching saturation value of around 97.7% as number of candidates becomes 20000).

Looking at the second table, we notice that the skill score for candidates tends to drop a little as the effect of luck increases, but the drop is nearly insignificant. In almost all cases (apart from the case with total 100 applicants), the average skill score of candidates is equal to or greater than 98%. It is also astonishing that the number of cases where the average skill score is greater than 99.5% is around 73%; indicating exceptionally skilled candidates are selected in most of the combinations.

These observations lead to two important discoveries,
- Most top candidates in any given position tend to have really high skill scores
- The importance of luck tends to increase dramatically as the number of candidates applying for a given position increases

It is often counterintuitive, the amount of effect your luck has on the outcome of a large number of events in your life. Even in cases where we assume that luck plays only 0.5% of the role (which I feel is highly undermining the actual role it plays in our lives, but nevertheless), we see that top 10 candidates out of around 15000 candidates will still need to have a luck greater than 95% to qualify.

**Why does it matter, you ask?**  
Given that the candidates appearing yearly for examinations such as the board exams, entrance exams (NEET, IITJEE, etc), competitive exams (UPSC, etc) far outnumber the meagre 20000 upper limit of the current simulation, the effect that luck plays in the lives of toppers or high performers in these examinations should not really be overlooked. We often say that someone failed mostly because they didn't study enough, but in reality people who work hard and study enough (having really high skill scores) will still tend to not make it to the top mostly because of some unlucky thing that happens to them on the day of the exam. On the other hand, those who get good marks and get their dream jobs and colleges, should always remember the role luck played in their success and have a sense of humility regarding the same.  

**Limitations**  
This experiment is a really simple look at how forces of hardwork and luck play a part in any given selection process. Most real world processes (as mentioned above) are really complex. The simplistic nature of the simulation kind of undermines the effect of other factors such as health, grasping power, preparation time, presence/absence of coaching, experience, gender, etc. One might argue that the effect of such factors is kind of hidden in the broad categories of luck and skill, e.g., catching a cold on the day of the exam can be regarded as bad luck while the product of grasping power and preparation time can be equated to hardwork. Even then one will fail to answer finer questions relating to effects other than those under the broad umbrella of skill and luck like does the presence/absence of coaching have an effect on the outcome?   
I wish I could carry out some calculations to arrive at a specific number for the effect of luck in any of these specific exams mentioned above, maybe take into account the number of people selected, the stages of exams (written/interview), and other factors mentioned above to come up with a complex model for the same. But yeah, that's a simulation for another time. Hope just the thought of it excites you like it excites me! Signing off till next time!
