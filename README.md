# 2 Factor ANOVAs, Contrasts, and Reference Groups with the Anova() Function in Car
The car package in R 3.6.1 (Fox and Weisberg, 2019) allows you to carry out 2-factor ANOVAs with the ```Anova()``` function. However, certain conditions may require the specification of contrasts and reference groups. Each contrast and reference groups is used for a specific condition, as specified in the help page for package. For first time users of this function, these contrasts may get confusing and tricky. The following tutorial shows you how and when to use the contrasts and reference groups (if at all) in a 2 factor ANOVA using the ```Anova() ``` function.

# What is a 2 factor ANOVA?
A 2 way ANOVA (Analysis of Variance Test) tests the effects of two categorical variables, and the effect of the variables on each other. 

By conducting a 2 way ANOVA, you're testing 3 null hypotheses at the same time:
1) There is no significant difference in the means of the groups of the first independent variable
2) There is no significant difference in the means of the groups of the second independent variable
3) The effect of the first independent variable does not depend on the effect of the second independent variable (interaction affect)

In the car package, the ```aov()``` function will test a one way ANOVA, but in order to test a two way ANOVA, the ```Anova()``` function is used.

# Types of 2 factor ANOVAs
In R, there are three types of 2 way ANOVAS; namely Type I, Type II, and Type III. If your data are balanced (all sample sizes are the same), all three types will give you the same results. However, if your data are unbalanced, Type II or Type III ANOVAs are advisable. More information can be found here: https://www.r-bloggers.com/anova-%E2%80%93-type-iiiiii-ss-explained/

I like working with Type III Anovas for unbalanced data. However, Type III Anovas, while capable of testing interesting hypothesis with unbalanced data, require a few considerations:

# Contrasts
The first thing to be certain of in Type III ANOVAs is contrasts. What is a contrast? Contrast are ways of comparing one group to another. Each type of contrast will give you a different sum of squares.

According to the ```help(Anova)``` page in R (Fox, 2016), in Type III ANOVAs, you may need to change the default contrast settings for your Anova in order to obtain "sensible results". The default settings are ```contr.treatment``` (for unordered data) and ```contr.poly``` (for ordered data). 

You can check the default contrasts with the following code:

``` options()$contrasts```

You might, therefore, want to change the default contrast settings. If your data are unordered, you'll want to change the default ```contr.treatment``` to ```contr.sum```. This can be done with the following code:
```options()contrasts = c("contr.sum", "contr.poly")```
Check your contrasts again, to make sure they're changed: ```options()$contrasts```

Now, you can conduct your ANOVA. 

```Anova(lm(dependent_variable ~ independent_variable1 * independent_variable2, data = name_of_dataset), type = 3)```

Notice how the ANOVA was conducted with a linear model as its input. 

Suppose you're conducting a simple ecology experiment investigating the effects of the pH and temperature of soil on the number of plant species growing in that soil. However, you don't have access to the numerical data, and so all of your data is categorical: you have 3 pH categories: "Acidic", "Neutral", and "Basic", and you have three temperature categories: "High", "Medium", and "Low". You do, however, have the exact number of plant species growing in your various soil samples. Your dataset is called plant_species_soil_data.

Your code would look like this:

```Anova(lm(plant_species ~ pH * temperature, data = plant_species_soil_data), type = 3) ```

Don't forget to test assumptions of the ANOVA!

But suppose you wanted to make sure there were no confounding variables affecting your experiment? You would probably measure other variables, such as wind speed, humidity, the presence of certain ions in the soil. In order to make sure none of these conditions significantly differed between your soil samples, you could conduct a simple one way ANOVA with the ```aov()``` function in the car package to ensure that there are no significant difference between these variables among your samples. Don't forget to test assumptions!

Unfortunately, you find that the wind speed does differ significantly between your soil samples. This could affect your results! 

Luckily, the ```Anova()``` model will allow you to include wind speed as a predictor variable, since it uses ```lm()``` as an input. Your code would then be modified to look something like this:

```Anova(lm(plant_secies ~ pH * temperature + wind_speed, data = plant_species_soil_data), type = 3)```

# ANOVA Table Interpretation and Reference Groups

The output from your ANOVA should look something like this:
```
Anova Table (Type III tests)

Response: plant_species
                       Sum Sq Df  F value    Pr(>F)    
(Intercept)             63184  1 153.1307 6.328e-16 ***
pH                        686  2   0.8310   0.44234    
temperature               527  2   0.6386   0.53284    
wind_speed                121  1   0.2934   0.59079    
pH: temperature          4313  4   2.6131   0.04803 *  
Residuals               18155 44                       
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

```
The table shows a significant interaction between the pH groups and the temperature groups. However, there are many possible combinations. Is there a significant interaction between basic pH and low temperature? What about between neutral pH and medium temperature? To find out where the interaction is, further tests will have to be conducted.





