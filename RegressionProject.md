# Mileage and Transmission
  


***  
## Executive Summary
This report analyses motor vehicle data to find out the difference in average mileage between vehicles with automatic transmission and those with manual transmission. While a difference in mean mileage between the two was found, the difference was due to other vehicle factors, the number of cylinders, and the weight of the vehicle. After adjusting the for the two, there was insignificant difference between the mileage of the two transmission systems.    


## Objective
In this report, we are going to analyse the data collected by Motor Trend about various motor vehicles to answer two questions :   

1. Automatic or manual transmission, which one gives better mileage?   
3. How much is the difference?   

    
Let us perform some initial processing of our data first.   

```r
data(mtcars)
df <- mtcars  ## copying to make changes to dataset
## the column id of the factor variables in the dataset
fac <- c(2, 8, 9, 10, 11)

## converting factor variables to factor from numeric
for (i in fac) {
    df[, i] <- as.factor(df[, i])
}
```

## Basic Exploration of the data

A boxplot of the mileage versus transmission [see appendix](#boxplot) seems to indicate a very clear difference between the two transmission modes. Let us just build a very basic regression model between mileage and transmission and see what result we get.   


```r
fit <- lm(mpg ~ am, df)
pander(summary(fit)$coef, digits = 3)
```


--------------------------------------------------------------
     &nbsp;        Estimate   Std. Error   t value   Pr(>|t|) 
----------------- ---------- ------------ --------- ----------
 **(Intercept)**     17.1        1.12       15.2     1.13e-15 

     **am1**         7.24        1.76       4.11     0.000285 
--------------------------------------------------------------
   
From this we can infer that am1, which refers to manual transmission leads to significantly higher mileage on average (p-value <0.001). Or more specifically, manual transmission on average offers 7.24 miles higher per US gallon than automatic transmission. So automatic transmission, whose average is represented by the intercept here is 17.1 miles/gallon, and thus our average mileage for manual transmission becomes 24.39 miles/gallon.   

To see the effects of other variables, let us build a more complex model which includes various other variables that are significant. After more exploration of the dataset, and building models with more variables (which can be seen in the appendix), the model with cylinders, transmission was found to be the best, with an adjusted R-squared of ~82%. Let us look at this model.   

```r
fittest <- lm(mpg ~ cyl + am + wt, df)
pander(summary(fittest))
```


--------------------------------------------------------------
     &nbsp;        Estimate   Std. Error   t value   Pr(>|t|) 
----------------- ---------- ------------ --------- ----------
    **cyl6**        -4.26        1.41       -3.02    0.00551  

    **cyl8**        -6.08        1.68       -3.61    0.00123  

     **am1**         0.15        1.3        0.115     0.909   

     **wt**         -3.15       0.908       -3.47    0.00177  

 **(Intercept)**     33.8        2.81        12      2.5e-12  
--------------------------------------------------------------


-------------------------------------------------------------
 Observations   Residual Std. Error   $R^2$   Adjusted $R^2$ 
-------------- --------------------- ------- ----------------
      32                2.6           0.838       0.813      
-------------------------------------------------------------

Table: Fitting linear model: mpg ~ cyl + am + wt
   
The coefficient of transmission(am1) is now insignificant! It seems the difference in transmission seen earlier, was mostly the result of other variables, most prominently the number of cylinders in the vehicle and its weight. The mileage is inversely proportional to both of them (decreases with increase in them).  

Let us look at the residuals to see if there are still any more patterns.  

```r
require(ggfortify)  ## extends ggplot for other types of objects.
autoplot(fittest)
```

![](RegressionProject_files/figure-html/resid-1.png)\
  
There does not seem to any more discernible pattern or increasing variation evident from these plots.  

## Result
In conclusion, the choice of transmission does not affect mileage significantly. Though it seems to do so at first sight, it is due to the number of cylinders and weight of the vehicle.
Since there is no significant difference between the two choices of transmissions, our second question becomes void.   

## Appendix
Extra figures of exploratory data analysis.  

A boxplot of mileage vs transmission.  <a name = "boxplot"></a> 

```r
require(ggplot2)
ggplot(df, aes(x = am, y = mpg, group = am, fill = am)) + 
     geom_boxplot() + 
     scale_x_discrete(labels = c("Automatic","Manual")) + 
     scale_fill_discrete(breaks = c(0,1),
                         labels = c("Automatic","Manual"),name = "Transmission") + 
     labs(x = "",y = "Mileage",title = "Boxplot of Mileage vs Transmission")
```

<div class="figure">
<img src="RegressionProject_files/figure-html/boxplot-1.png" alt="boxplot"  />
<p class="caption">boxplot</p>
</div>
