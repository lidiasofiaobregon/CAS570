# CAS 570: Fundamentals in Complex Systems 
## Complex Systems Project: Stress and Social Interaction ~ Lidia Obregon

scientific question, convince them you made progress toward answering the question (i.e. add NetLogo documentation)

#### Big Picture Questions:
1. How do social interaction and stress relate to one another over time?
2. How do social interaction and stress interact to produce disease/health? 
3. How do individual differences contribute to disease/health within the population?

Introduction/Background


Model/Methods
#### NetLogo Model (Interface):
<img width="1227" alt="Screenshot 2023-12-04 at 2 58 53 PM" src="https://github.com/lidiasofiaobregon/CAS570/assets/117681452/870387f5-ce27-4fe9-a4db-e354ee97664f">

##### NetLogo Model (Code):
```
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








Analysis
Conclusion
Future Directions



