# CAS 570: Fundamentals in Complex Systems 
## Computational Project: Stress and Social Interaction ~ Lidia Obregon

scientific question, convince them you made progress toward answering the question

#### Big Picture Questions
1. How do social interaction and stress relate to one another over time?
2. How do social interaction and stress interact to produce disease/health? 
3. How do individual differences in initial parameters contribute to stress level/disease/health within the population?

#### Introduction/Background



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
<img width="1227" alt="Screenshot 2023-12-04 at 2 58 53 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/870387f5-ce27-4fe9-a4db-e354ee97664f">



#### Conclusions



#### Future Directions



#### References



