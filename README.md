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
temperature               527  2   0.6386   0.03284 *  
wind_speed                121  1   0.2934   0.59079    
pH: temperature          4313  4   2.6131   0.04803 *  
Residuals               18155 44                       
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

```
The table shows a significant difference between temperature groups. However, which group is significantly different from the other? Is it "High"? What about "low"? To find out, further tests will have to be conducted. The table also shows a significant interaction between the pH groups and the temperature groups. However, like in the case of temperature, there are many possible ways in which this interaction could have occurred. Is there a significant interaction between basic pH and low temperature? What about between neutral pH and medium temperature? 

A linear model (found with the ```lm()``` function) can show exactly in which groups these interactions are present. However, you first need to decide what your reference groups will be.

What exactly is a reference group? In a linear model, when more than two groups exist for an independent variable, pair-wise comparisons will be done with one group as the reference group - the group which the other groups will be compared to. For example, in our ecology experiment, "neutral" pH or "medium" temperature could be our reference groups, and all comparisons will be conducted in reference to these groups. Ideally, our linear model will show if there is a significant difference between "basic" and "neutral" pHs, or if the difference lies between "low" and "medium" temperatures. However, these reference groups will need to be specified. 

R will have already assigned one of your groups are the default group. This can be checked with the following code:
```
contrasts(dataset$independent_variable1)``` which would be ```contrasts(plant_species_soil_data$temperature``` in our experiment
```
Suppose your default reference groups were "Medium" temperature and "neutral". Then, you could conduct your linear model:
``` 
plant_species_lm <- lm(plant_species ~ pH * temperature + wind_speed, data = plant_species_soil_data)
summary(COMT_DigitsTotal) 
```
The output would look something like this:
```
Call:
lm(formula = plant_species ~ pH * temperature + wind_speed, 
    data = plant_species_soil_data)

Residuals:
    Min      1Q  Median      3Q     Max 
-3.5455 -1.1429 -0.0712  1.3473  3.4545 

Coefficients:
                                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)                     11.545455   0.571906  20.188   <2e-16 ***
pHAcidic                         1.799105   1.138615   1.580   0.1213    
pHBasic                          0.597403   0.917090   0.651   0.5182    
temperatureHigh                  2.025974   0.917090   2.209   0.0324 *  
temperatureLow                   0.350919   0.978666   0.359   0.7216    
wind_speed                      -1.378238   1.057592  -1.303   0.1993    
pHAcidic:temperatureHigh        -2.120534   1.646172  -1.288   0.2044    
pHBasic:temperatureHigh         -3.668831   1.501497  -2.443   0.0186 *  
pHAcidic:temperatureLow         -0.299105   1.579787  -0.189   0.8507    
pHBasic:temperatureLow           0.008815   1.615211   0.005   0.9957    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 1.897 on 44 degrees of freedom
Multiple R-squared:  0.2509,	Adjusted R-squared:  0.0977 
F-statistic: 1.638 on 9 and 44 DF,  p-value: 0.1342
```
How is this interpreted? The first line of the table after the intercept is titled ```pHAcidic```. This line is comparing medium temperature soil samples (since medium is our reference group) that have an acidic pH to medium temperature soil samples that have a neutral pH (since neutral is our reference group). Keeping wind speed constant, the model finds no significant difference between these two groups.

Let's move on to temperature. The third line of the table is titled ```temperatureHigh```. This line compares neutral pH soil samples (since neutral is our reference group) that have high temperatures to neutral pH soil samples that have medium temperatures (since medium is our reference group). Here, there is a significant interaction as the P value is less than 0.05. The model finds that neutral pH soil samples with higher temperatures house 2.02 more species of plants than neutral pH soil samples with medium temperatures (as indicated by the ```Estimate``` column),  keeping wind speed constant.

What about the interaction term? This is where it gets tricky. Think of the interaction term as the difference of differences. If ```pHAcidic``` is looking at the difference in numbers of plant species in acidic soils and neutral soils, and ```temperatureHigh``` is looking at the difference in numbers of plant species in high and medium temperature soils, the interaction term for these values is the difference of the two values. 

In our ecology experiment, we see that the difference in number of plant species between neutral pH high temperature and low temperature soils is signficantly different than the difference in number of plant species between basic and acidic soils with medium temperatures. 


