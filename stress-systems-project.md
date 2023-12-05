# CAS 570: Fundamentals in Complex Systems 
## Computational Project: Stress and Social Interaction ~ Lidia Obregon


#### Big Picture Questions
1. How do social interaction and stress relate to one another over time?
2. How do social interaction and stress interact to produce disease/health? 
3. How do individual differences in initial parameters contribute to stress level/disease/health within the population?

#### Introduction/Background

GUTS theory
social safety theory
PNI
Type "A" personality
stress, chronic inflammation, disease

###### To implement this relationship, I used the agent-based modeling platform, NetLogo. 


#### NetLogo Model (Code)

1. Initializing the world and the parameters of the turtles (people)
###### All turtles begin with a random combination of all 8 parameters, with a stress level of 0, and with number of interactions 0
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
###### Each new turtle has its own random confirguation of 8 parameters

```
to increment-age
  set age (1 + age)
  if age > 95 [die]
end

to reproduce
  if random-float 100 < reproduce-turtles [  ; throw "dice" to see if you will reproduce
    ;set energy (energy / 2)                ; divide energy between parent and offspring
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
###### At every tick (time step), count the number of other turtles in the same area (path). If there is more than 1 turtle around, consider an interaction has been had and adjust stress levels accordingly (based on specific parameters)

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
###### Based off of current stress levels, adjust malleable parameters

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
###### Specify how turtles move in their environment/perform all the above functions; Randomnly turn right or left 45 degrees, and take a step forward; Adjust the color of the turtle based on stress level

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

###### Initial number of turtles and reproduction rate are pre-set using the sliders on the left. Mean number of interactions, mean stress level, and mean number of current turtles are displayed in the plots and output windows to the right of the simulated world.

<img width="1227" alt="Screenshot 2023-12-04 at 2 58 53 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/870387f5-ce27-4fe9-a4db-e354ee97664f">



#### Discussion
_scientific question/convince them you made progress toward answering the question_
_1. How do social interaction and stress relate to one another over time?
2. How do social interaction and stress interact to produce disease/health? 
3. How do individual differences in initial parameters contribute to stress level/disease/health within the population?_


Population-level effects



Individual-level effects





#### Future Directions
The following four ideas suggest ways to improve the agent-based model and further explore the relationship between social interactions and stress in the future. 

First, I'd like to implement probability functions in the stress accumulation portion of the model. As with most questions of nature and nurture, there is a _likelihood_ that certain parameters an individual posesses will lead to increased stress. Adding in probability, and giving more weight to values of certain parameters will more properly capture this model of being "predisposed", versus being "fated". In addition to adding in probability functions in general, I'd like to add in probability functions for the lower range of the parameters. For example, if neuroticism is high (i.e. greater than 70), I'd like to not only add a function to increase the probability of stress during an interaction, but I'd also like to add the probability of decreasing stress, were the neuroticism value to be less than 30, for example.

Second, I'd like to refine the aging variable in the model. Instead of setting a random age between 0-100 for each participant, I'd like to more realistically start individuals out at 0 and age accordingly. Furthermore, when a new turtle is "hatched", they're currently also given a randomnly generated age, instead of properly reflecting their nascency. Similarly, at this point, there is not a proper reflection of genetic similarity between "parent" and "offspring"; An implementation of probabilistic genetics would also more accurately reflect the population. 

Third, I'd like to more intentionally refine each of the parameters to more accurately reflect how they scale with stress. At the moment, I've taken what is currently known in the literature when it comes to positive or negative relationships between variables, and I've semi-arbitrarily decided how much stress will be added onto the individual based off of certain cut offs of their parameters. Furthermore, some of the current parameters (8 total) being used contain some overlapping phenomenon. For example, the parameter "unsafety" theoretically includes things such as health metrics and social context information, which we also see present in other parameters (i.e. health_score and bad_social_history). In theory, I could improve the model by more particularly separating out the overlap within parameters to see the clear influence of certain combinations and values over others. Another potential refinement I'd be interested in including would be for the interactions term. Currently, no matter how many people you're in the interaction with, stress only increases or decreases a fixed amount– The number of people present in the interaction doesn't affect the value changes. A more realistic reflection might be an appropriate averaging of the number of people within an interaction, and an adjustment to increase or decrease in stress accordingly.

Lastly, I'd like to eventually explore the mechanisms by which these parameters are interacting within the individual to "produce stress" during an interaction. As we saw in the discussion, the combination of parameters matters for stress– The contignecies between certain parameters and their outcomes (in isolation) are not as cut and dry. Perhaps I could do a behavior spectrum analysis in R to determine the specific profiles of parameters that contribute to high stress or low stress over time, and design experiments around the formulated hypotheses. 


#### References



