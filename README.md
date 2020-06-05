# 2 Factor ANOVAs and Contrasts with the Anova() Function in Car
The car package in R 3.6.1 (Fox and Weisberg, 2019) allows you to carry out 2-factor ANOVAs with the ```Anova()``` function. However, certain conditions may require the specification of contrasts. Each contrast is used for a specific condition, as specified in the help page for package. For first time users of this function, these contrasts may get confusing and tricky. The following tutorial shows you how and when to use the contrasts (if at all) in a 2 factor ANOVA using the ```Anova() ``` function.

# What is a 2 factor ANOVA?
A 2 way ANOVA (Analysis of Variance Test) tests the effects of two categorical variables, and the effect of the variables on each other. 

By conducting a 2 way ANOVA, you're testing 3 null hypotheses at the same time:
1) There is no significant difference in the means of the groups of the first independent variable
2) There is no significant difference in the means of the groups of the second independent variable
3) The effect of the first independent variable does not depend on the effect of the second independent variable (interaction affect)

In the car package, the ```aov()``` function will test a one way ANOVA, but in order to test a two way ANOVA, the ```Anova()``` function is used.

