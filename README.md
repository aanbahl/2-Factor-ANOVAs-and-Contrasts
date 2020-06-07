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

```options(contrasts = c("contr.sum", "contr.poly"))```

Check your contrasts again, to make sure they're changed: ```options()$contrasts```

Now, you can conduct your ANOVA. 

```Anova(lm(dependent_variable ~ independent_variable1 * independent_variable2, data = name_of_dataset), type = 3)```

Notice how the ANOVA was conducted with a linear model as its input. 

Suppose you're conducting a simple ecology experiment investigating the effects of the pH and temperature of soil on the number of plant species growing in that soil. However, you don't have access to the numerical data, and so all of your data is categorical: you have 3 pH categories: "Acidic", "Neutral", and "Basic", and you have three temperature categories: "High", "Medium", and "Low". You do, however, have the exact number of plant species growing in your various soil samples. Your dataset is called ```plant_species_soil_data```.

Your code would look like this:

```Anova(lm(plant_species ~ pH * temperature, data = plant_species_soil_data), type = 3) ```

Don't forget to test assumptions of the ANOVA!

But suppose you wanted to make sure there were no confounding variables affecting your experiment? You would probably measure other variables, such as wind speed, humidity, the presence of certain ions in the soil. In order to make sure none of these conditions significantly differed between your soil samples, you could conduct a simple one way ANOVA with the ```aov()``` function in the car package to ensure that there are no significant difference between these variables among your samples. Don't forget to test assumptions!

Unfortunately, you find that the wind speed does differ significantly between your soil samples. This could affect your results! 

Luckily, the ```Anova()``` model will allow you to include wind speed as a predictor variable, since it uses ```lm()``` as an input. Your code would then be modified to look something like this:

```Anova(lm(plant_secies ~ pH * temperature + wind_speed, data = plant_species_soil_data), type = 3)```

# ANOVA Table Interpretation 

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
The table shows a significant difference between temperature groups. However, which group is significantly different from the other? Is it ```High``` temperature? What about ```Low``` temperature? To find out, further tests will have to be conducted. The table also shows a significant interaction between the pH groups and the temperature groups. However, like in the case of temperature, there are many possible ways in which this interaction could have occurred. Is there a significant interaction between ```Basic``` pH and ```Low```temperature? What about between ```Neutral``` pH and ```Medium``` temperature? 

A linear model (found with the ```lm()``` function) can show exactly in which groups these interactions are present. However, you first need to decide what your reference groups will be.

# Reference Groups and Linear Model Interpretation

What exactly is a reference group? In a linear model, when more than two groups exist for an independent variable, pair-wise comparisons will be done with one group as the reference group - the group which the other groups will be compared to. For example, in our ecology experiment, ```"Neutral"``` pH or ```"Medium"``` temperature could be our reference groups, and all comparisons will be conducted in reference to these groups. Ideally, our linear model will show if there is a significant difference between ```Basic``` and ```Neutral``` pHs, or if the difference lies between ```Low``` and ```Medium``` temperatures. However, these reference groups will need to be specified. 

R will have already assigned one of your groups are the default group. This can be checked with the following code:

``` contrasts(dataset$independent_variable1) ```  which would be  ```contrasts(plant_species_soil_data$temperature``` in our experiment.

This code would give the following output:
```
          High Low
Medium      0   0
High        1   0
Low         0   1
```
Here, you can see that soils of ```Medium``` temperature are your reference group, since ```Medium``` gives a value of ```0``` when compared across tabled across ```High``` and ```Low``` temperature soils.

So suppose your default reference groups were ```Medium``` temperature and ```Neutral``` pH. Then, you could conduct your linear model. Be sure to change your default contrasts back to ```contr.treatment``` and ```contr.poly``` for your linear model!

``` 
options(contrasts = ("contr.treatment", "contr.poly"))
plant_species_lm <- lm(plant_species ~ pH * temperature + wind_speed, data = plant_species_soil_data)
summary(plant_species_lm) 
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
How is this interpreted? For each line that contains one subgroup, you are comparing that subgroup with the reference subgroup, keeping the other independent variable constant. For example, the second line of the table after the intercept is titled ```pHBasic```. This line is comparing medium temperature soil samples (since medium is our reference group) that have a basic pH to medium temperature soil samples that have a neutral pH (since neutral is our reference group). Keeping wind speed constant, the model finds no significant difference between these two groups.

Let's move on to temperature. The third line of the table is titled ```temperatureHigh```. This line compares neutral pH soil samples (since neutral is our reference group) that have high temperatures to neutral pH soil samples that have medium temperatures (since medium is our reference group). Here, there is a significant interaction as the P value is less than 0.05. The model finds that neutral pH soil samples with higher temperatures house 2.02 more species of plants than neutral pH soil samples with medium temperatures (as indicated by the ```Estimate``` column),  keeping wind speed constant.

What about the interaction term? This is where it gets tricky. Think of the interaction term as the difference of differences. If ```pHBasic``` is looking at the difference in numbers of plant species in acidic soils and neutral soils, and ```temperatureHigh``` is looking at the difference in numbers of plant species in high and medium temperature soils, the interaction term for these values is the difference of the two values. 

In our ecology experiment, we see that the difference in number of plant species between neutral pH high temperature and medium temperature soils is signficantly different than the difference in number of plant species between neutral and acidic soils with medium temperatures. This could mean that in the neutral pH soils, medium temperature soils had more/less plant species than low temperature soils. However, in the basic pH group, medium temperature soils had more/less plant species than low temperature soils. 

This interaction term is often difficult to interpret, so here you should probably create an interaction plot to visually view your data. You can also create bar graphs and look at the vertical distance between the bar heights in question to confirm your interaction "difference of differences" analysis. 

So far, in your pH groups you've only compared neutral and basic pHs and high and medium temperatures. But what if you wanted to compare neutral and acidic pHs? Or medium and low temperatures? Or what about acidic and basic pHs? This is where you'd have to change the reference group, so that the group that all other groups are being compared to is now different. This can be done with the following code:
```
plant_species_soil_data$pH <- factor(plant_species_soil_data$pH, levels = c("Acidic","Neutral","Basic"))
plant_species_soil_data$temperature <- factor(plant_species_soil_data$temperature, levels = c("High","Medium","Low"))
```
You just changed your pH reference group to ```Acidic``` and your temperature reference group to ```High```. Since your ```levels``` vector is ordered, the first element will be the reference group (which, in these two cases, is ```High``` and ```Acidic```.)

As you did previously, check your reference groups with the following code: ```contrasts(dataset$independent_variable)```

Now, you can run your linear model again with the same code as you did with the previous linear model, but the output will be different, as you are now comparing different groups.

```
Call:
lm(formula = SPWM_WME_T1 ~ COMT2 * group_membership + Cancer_Treatments, 
    data = clean_data)

Residuals:
    Min      1Q  Median      3Q     Max 
-37.000 -11.470  -1.055  11.650  54.091 

Coefficients:
                                Estimate Std. Error t value Pr(>|t|)   
(Intercept)                       26.978      8.505   3.172  0.00276 **
pHNeutral                         13.167     11.728   1.123  0.26765   
pHBasic                           19.515     14.239   1.371  0.17747   
temperatureMedium                 26.272     13.247   1.983  0.05360 . 
temperatureLow                     1.989     13.146   0.151  0.88044   
wind_speed                         6.135     11.326   0.542  0.59079   
pHNeutral:temperatureMedium       -9.417     17.310  -0.544  0.58919   
pHBasic:temperatureMedium        -41.765     20.225  -2.065  0.04485 * 
pHNeutral:temperatureLow          -5.224     16.918  -0.309  0.75895   
pHBasic:temperatureLow       8.233     20.334   0.405  0.68751   
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 20.31 on 44 degrees of freedom
Multiple R-squared:  0.2654,	Adjusted R-squared:  0.1152 
F-statistic: 1.766 on 9 and 44 DF,  p-value: 0.1024
```
You can now interpret this linear model as you did the previous one, and it will give you different comparisons, with different results. 

# Works Cited

Fox, J. (2016) Applied Regression Analysis and Generalized Linear Models, Third Edition. Sage.

Fox, J. and Weisberg, S. (2019) An R Companion to Applied Regression, Third Edition, Sage.
