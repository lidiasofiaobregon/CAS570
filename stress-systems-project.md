# CAS 570: Fundamentals in Complex Systems 
## Computational Project: Stress and Social Interaction ~ Lidia Obregon


#### Big Picture Questions
1. How do social interaction and stress relate to one another over time?
2. How do social interaction and stress interact to produce disease/health? 
3. How do individual differences in initial parameters contribute to stress level/disease/health within the population?

#### Introduction

In the stress literature, there are several competing theories as to how one explains the phenomena of chronic stress and subsequent development of stress-based diseases. Two of the main theories are the theory of "stressors" and the generalized "unsafety" theory of stress. On one end is the model of stressors (Selye, 1955). This model emphasizes discrete events (real or imagined) that threaten the safety of an individual and cause the stress response in the body. On the other hand is the model of generalized "unsafety", also called the GUTS theory (Brosschot et. al, 2018). This theory, rather than emphasize discrete events known as stressors, theorizes that the stress response's default mode is actually to always be "on" and is only inhibted/turned off when safety is perceived. This model seems to capture more of the chronic nature of stress, and is more theoretically compatible with phenomena such as how the instability of one's social contexts can cause prolonged stress, long after any actual social stressor is present. 

Regardless of which theory is used to conceptualize the phenomena of stress, the literature demonstrates that one of the elements that seems to contribute to stress, health, and well-being in general is social interactions (Bernstein et. al 2018, Sun et. al 2019, Milek et. al 2019). For this project, I wanted to simulate an environment where we looked at two levels of the phenomena of stress and social interaction: the individual level and the population level. I wanted to explore how interactions within a population of people with diverse characteristics would contribute to the stress (or lack of stress) of the individual in particular and the population in general. To implement and explore this relationship, I used the agent-based modeling platform, NetLogo. The goal was to explore the Big Picture questions of what stress looked like over time within an individual and within the population as number of interactions between people increased. Additionally, I wanted to explore what kind of individual differences (i.e. combinations of parameters) contributed to being stressed and eventually dying from stress. Each individual (or "turtle" in NetLogo) was configured with eight parameters that, based off the literature, have been shown to relate in one way or another to stress. These eight parameters are unsafety (Brosschot et. al, 2018), neuroticism (Amestoy et. al 2023), extroversion (Xin et. al, 2017), age (Brosschot et. al, 2018), health score (Brosschot et. al, 2018), stress genes (Buchanan & Lovallo, 2019), diet score (Brosschot et. al, 2018), and bad social history (Slavich 2022). These parameters were randomnly configured for each individual and were explored over time within an indiviual as their number of interactions increased.


#### NetLogo Model (Code)

1. Initializing the world and the parameters of the turtles (people)
###### To start, we clarify what terms will be used in this code, in order to establish a "global" reality of sorts. I included terms such as "extroversion, stress, age, etc., in order to encompass the parameters and the outcome variables. In order to formally set up the "turtles" (or people) in this world, I created a function "setup-turtles" and specified eight different parameters for each turtle. Each turtle is given a random configuration of the eight parameters, valued from 0 to 100. I set the color of each turtle to correspond to their stress level, so I could see the changes in stress levels as the simulation ran. 

```
turtles-own[unsafety neuroticism extroversion age health_score stress_genes diet_score bad_social_history n_interactions stress]

to setup-turtles 
  clear-all
  create-turtles num_people [
    set shape "person"
    set unsafety random 100
    set neuroticism random 100
    set extroversion random 100
    set age random 100
    set health_score random 100
    set stress_genes random 100
    set diet_score random 100
    set bad_social_history random 100
    set n_interactions 0
    set stress 0
    set color 19 - ( stress / 10 )
  ]
  ask turtles [setxy random-xcor random-ycor]
  reset-ticks
end

```
2. Creating the functions for aging and reproduction
###### In order to increment the age and replicate the phenomena of dying from old age, I implemented an increment-age function that would take the current age of a turtle (set at a random number between 0 to 100) at every time step and and increase it by a value of 1. Once the turtle reached an "age" of 95, I had that turtle "die". Additionally, I created a reproduction function to replicate the phenomena of reproduction and have growth within the population of turtles. At random intervels, a new turtle would be "hatched" and set up with its own random configuration of the eight parameters. 

```
to increment-age
  set age (1 + age)
  if age > 95 [die]
end

to reproduce
  if random-float 100 < reproduce-turtles [  ; throw "dice" to see if you will reproduce
    hatch 1 [
    set shape "person"
    set unsafety random 100
    set extroversion random 100
    set age random 100
    set health_score random 100
    set diet_score random 100
    set bad_social_history random 100
    set n_interactions 0
    set stress 0
    set color 19 - ( stress / 10 )
      rt random-float 360 fd 1 ]   ; hatch an offspring and move it forward 1 step
  ]
end
```

3. Creating the function for interactions
###### I created a function to be implemented at every time step that would count the number of other turtles in the surrounding area (on the same "patch) and consider other turtles present as an interaction. If an interaction occured ("greater than zero"), I would adjust the stress measure accordingly. I selected six of the eight parameters and, based off the current literature, made assumptions of how each parameter would affect a turtle's stress level (when they had an interaction). Some of the measures increased stress level (i.e. neuroticism >= 60), while other measures decreased stress level (i.e. extroversion >= 50).

```
to interact 
    set n_interactions n_interactions + count other turtles-here

  if n_interactions > 0 [
    if neuroticism >= 60
      [set stress stress + 30]
    if unsafety >= 60
      [set stress stress + 40]
    if extroversion >= 50
      [set stress stress - 25]
    if health_score >= 60
      [set stress stress - 30]
    if bad_social_history >= 50
      [set stress stress + 50]
    if stress_genes >= 70
      [set stress stress + 35]
  ]
    ;show n_interactions
  ;show stress
end
```

4. Creating the function for stress feedback
###### To incorporate the phenomena of feedback, I created a function to add or subtract a certain amount of a parameter's value based of whether or not the the turtle's stress level was above or below a certain amount. For example, if the turtle's stress level was above 300, I would add 20 points on to their 'bad_social_history' parameter. I selected only three parameters for these feedback loops (unsafety, bad_social_history, and health_score), based off the knowledge that these are more malleable parameters than, say, stress_genes or neuroticism. 

```
to feedback_stress ;add stress feedback loops to parameters that can change (how does stress affect their original parameters)
  if stress > 300
    [set unsafety unsafety + 10]
  if stress > 300
    [set bad_social_history bad_social_history + 20]
  if stress > 300
    [set health_score health_score - 10]
  if stress < 100
    [set health_score health_score + 10]
  if stress > 500 [die]
  if health_score <= 0 [die]
  if stress < 0
    [set stress 0 ]

end
```


5. Creating the main "go" function
###### This "go" function merely took all the above functions and moved the simulation forward. At every time step, the turtles would take a right turn a random amount of degrees between 0 to 45, a left turn a random amount of degrees from 0 to 45, and then take a step forward once. The functions for interacting, stress feedback, aging, and reproduction would then proceed, and the color of the turtle would be adjusted based on stress level. 

```
to go 
  ask turtles[
    rt random 45
    lt random 45
    fd 1
    interact
    feedback_stress
    increment-age
    reproduce
    set color 19 - ( stress / 10 ) 
  ]
 tick
end

```


#### NetLogo Model (Interface)

###### The initial number of turtles and the reproduction rate are pre-set using the sliders on the left. The mean number of interactions, mean stress level, and mean number of current turtles are displayed in the plots and output windows to the right of the simulated world. These metrics were manipulated during various runs of the simulation to see the effects for the population and the individual turtles. 

<img width="1227" alt="Screenshot 2023-12-04 at 2 58 53 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/870387f5-ce27-4fe9-a4db-e354ee97664f">



#### Discussion
After having simulated the model with different configurations of starting turtles and reproduction rates, I was able to see some of the dynamics within the population emerge. To start, it was surprising to see how, when the reproduction rate was set at 3% for example, the mean stress level within a population stayed at a dynamic equilibrium point (as can be seen in the screenshot below). Rather than the entire population's stress level increasing exponentially as multiple individuals' stress levels increased, the stress level at the population as a whole stayed around an average point. Though people died from both stress and "old age", it seems like the individual's who were highly stressed and whose stressed continuously increased were balanced out by individual's whose parameters did not make them stressed out easily upon having an interaction. 

<img width="1024" alt="Screenshot 2023-12-05 at 4 59 21 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/45115c1f-c50e-4b30-8b2e-c133f09eadf2">

I further explored indivual-level effects to determine if there were certain configurations of parameters that served as protective against stress, or conversely, detrimental to one's life. It seemed like there were consistent patterns within the turtles that died from stress (i.e. a stress level above 500). These turtles tended to have high levels of the "unsafety" parameter and the "bad social history" parameter, which alert to the influence of lifestyle, social context, and environment on one's stress levels (see screenshot below). 

<img width="997" alt="Screenshot 2023-12-05 at 4 57 21 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/811b362b-20c3-48ab-9d3a-3de87b187ef7">


I believe I've made progress to understanding the Big Picture questions of how social interaction and stress relate to one another over time, how they interact to produce disease, and what kinds of individual differences contribute to stress levels within the population. I've explored how increases in number of social interactions don't necessarily directly contribute to increases or decreases in stress unilaterally, but that it's a matter of an individual's configuration of parameters. I've explored how certain parameters such as levels of "unsafety" and "bad social history" contribute highly to stress-based mortality (within this model). Starting with high levels of these parameters makes it more likely to "die" quickly upon multiple interactions. 



#### Future Directions
The following five ideas suggest ways to improve the agent-based model and further explore the relationship between social interactions and stress in the future. 

First, I'd like to implement probability functions in the stress accumulation portion of the model. As with most questions of nature and nurture, there is a _likelihood_ that certain parameters an individual posesses will lead to increased stress. Adding in probability, and giving more weight to values of certain parameters will more properly capture this model of being "predisposed", versus being "fated". In addition to adding in probability functions in general, I'd like to add in probability functions for the lower range of the parameters. For example, if neuroticism is high (i.e. greater than 70), I'd like to not only add a function to increase the probability of stress during an interaction, but I'd also like to add the probability of decreasing stress, were the neuroticism value to be less than 30, for example.

Second, I'd like to refine the aging variable in the model. Instead of setting a random age between 0-100 for each participant, I'd like to more realistically start individuals out at 0 and age accordingly. Furthermore, when a new turtle is "hatched", they're currently also given a randomnly generated age, instead of properly reflecting their nascency. Similarly, at this point, there is not a proper reflection of genetic similarity between "parent" and "offspring"; An implementation of probabilistic genetics would also more accurately reflect the population. 

Third, I'd like to more intentionally refine each of the parameters to more accurately reflect how they scale with stress. At the moment, I've taken what is currently known in the literature when it comes to positive or negative relationships between variables, and I've semi-arbitrarily decided how much stress will be added onto the individual based off of certain cut offs of their parameters. Furthermore, some of the current parameters (8 total) being used contain some overlapping phenomenon. For example, the parameter "unsafety" theoretically includes things such as health metrics and social context information, which we also see present in other parameters (i.e. health_score and bad_social_history). In theory, I could improve the model by more particularly separating out the overlap within parameters to see the clear influence of certain combinations and values over others. Another potential refinement I'd be interested in including would be for the interactions term. Currently, no matter how many people you're in the interaction with, stress only increases or decreases a fixed amount– The number of people present in the interaction doesn't affect the value changes. A more realistic reflection might be an appropriate averaging of the number of people within an interaction, and an adjustment to increase or decrease in stress accordingly.

Fourth, I'd like to somehow implement a "quality of social interaction" metric into the model. At the moment, the only information about the social interaction that is implemented is the presence of or lack of an interaction (a frequency or quantity), rather than information about the quality of interaction. The literature shows that the quality of the social interaction influences whether or not the interaction contributes to one's well-being, especially considering the characteristics of said individual. A qualitative measure could at first be a random parameter of an interaction, and could advance to be based on the compatibility of a given person's characteristics during an interaction.

Lastly, I'd like to eventually explore the mechanisms by which these parameters are interacting within the individual to "produce stress" during an interaction. As we saw in the discussion, the combination of parameters matters for stress– The contignecies between certain parameters and their outcomes (in isolation) are not as cut and dry. Perhaps I could do a behavior spectrum analysis in R to determine the specific profiles of parameters that contribute to high stress or low stress over time, and design experiments around the formulated hypotheses. 


#### References

Amestoy ME, D'Amico D, Fiocco AJ. Neuroticism and Stress in Older Adults: The Buffering Role of Self-Esteem. Int J Environ Res Public Health. 2023 Jun 12;20(12):6102. doi: 10.3390/ijerph20126102. PMID: 37372689; PMCID: PMC10298711.

Bernstein, M. J., Zawadzki, M. J., Juth, V., Benfield, J. A., & Smyth, J. M. (2018). Social interactions in daily life: Within-person associations between momentary social experiences and psychological and physical health indicators. Journal of Social and Personal Relationships, 35(3), 372–394. https://doi.org/10.1177/0265407517691366

Brosschot JF, Verkuil B, Thayer JF. Generalized Unsafety Theory of Stress: Unsafe Environments and Conditions, and the Default Stress Response. Int J Environ Res Public Health. 2018 Mar 7;15(3):464. doi: 10.3390/ijerph15030464. PMID: 29518937; PMCID: PMC5877009.

Buchanan TW, Lovallo WR. The role of genetics in stress effects on health and addiction. Curr Opin Psychol. 2019 Jun;27:72-76. doi: 10.1016/j.copsyc.2018.09.005. Epub 2018 Sep 21. PMID: 30292777; PMCID: PMC6428629.

Milek A, Butler EA, Tackman AM, Kaplan DM, Raison CL, Sbarra DA, Vazire S, Mehl MR. "Eavesdropping on Happiness" Revisited: A Pooled, Multisample Replication of the Association Between Life Satisfaction and Observed Daily Conversation Quantity and Quality. Psychol Sci. 2018 Sep;29(9):1451-1462. doi: 10.1177/0956797618774252. Epub 2018 Jul 3. PMID: 29969949; PMCID: PMC6139582.

Selye, Hans. “Stress and disease.” Science, vol. 122, no. 3171, 1955, pp. 625- 631, https://doi.org/10.1126/science.122.3171.625.

Slavich GM. Social Safety Theory: Understanding social stress, disease risk, resilience, and behavior during the COVID-19 pandemic and beyond. Curr Opin Psychol. 2022 Jun;45:101299. doi: 10.1016/j.copsyc.2022.101299. Epub 2022 Jan 19. PMID: 35219156; PMCID: PMC8769662.

Sun J, Harris K, Vazire S. Is well-being associated with the quantity and quality of social interactions? J Pers Soc Psychol. 2020 Dec;119(6):1478-1496. doi: 10.1037/pspp0000272. Epub 2019 Oct 24. PMID: 31647273.

Xin, Y., Wu, J., Yao, Z. et al. The relationship between personality and the response to acute psychological stress. Sci Rep 7, 16906 (2017). https://doi.org/10.1038/s41598-017-17053-2.
