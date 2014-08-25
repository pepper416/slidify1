---
title : Predict the probability of being admitted by graduate school
subtitle : Logistic Regression
author : pepper416
job         : 
    framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [quiz, bootstrap]            # {mathjax, quiz, bootstrap}
ext_widgets : {rCharts: ["libraries/highcharts","libraries/polycharts", "libraries/nvd3", "libraries/morris"]}
mode        :  standalone # {standalone, draft,selfcontained}
---
    
## Data Description

    A researcher is interested in how variables, such as GRE (Graduate Record Exam scores),
GPA (grade point average) and prestige of the undergraduate institution, effect admission
into graduate school. The response variable, admit/don't admit, is a binary variable.

The dataset can be downloaded from [http://www.ats.ucla.edu/stat/data/binary.csv](http://www.ats.ucla.edu/stat/data/binary.csv)

```r
mydata <- read.csv("http://www.ats.ucla.edu/stat/data/binary.csv")
head(mydata)
```

```
##   admit gre  gpa rank
## 1     0 380 3.61    3
## 2     1 660 3.67    3
## 3     1 800 4.00    1
## 4     1 640 3.19    4
## 5     0 520 2.93    4
## 6     1 760 3.00    2
```

--- &radio
## Question

To predict the probality of being admitted, which of the following regression model should we use?

1. linear regression model
2. _logistic regression model_
3. randome effect model

*** .hint 
what is the response variable?

*** .explanation 
The response variable admin is binary, therefore, we should use logistic regression model.

---

## Logistic regression model
Since the response variable `admit` is binary, we can fit a logistic regress model to predict the probability of being admitted by graduate school by using `glm()` function.

```r
mydata$rank <- factor(mydata$rank)
mylogit <- glm(admit ~ gre + gpa + rank, data = mydata, family = "binomial")
```

## Prediction

```r
GRE = 1600;GPA = 4;Rank = 1;
# predict the probablity of being admitted
pred = predict(mylogit,newdata=list(gre=GRE,gpa=GPA,rank=as.factor(Rank)),
interval=("confidence"),type = "link", se = TRUE)
PredictedProb <- plogis(pred$fit)
```
Based on our model, if a student has GRE = 1600, GPA= 4, and graduates from the university with prestige rank 1, the predicted probability of being admitted is 0.95.


---
## Average GRE score grouped by GPA and Rank.


```r
mydata$gpa_group = cut(mydata$gpa, breaks = c(2,3,3.5,4))
df_mydata = aggregate(gre ~ gpa_group + rank, data=mydata, mean)
p1 <- nPlot(gre ~ gpa_group, group = 'rank', data = df_mydata, type = 'multiBarChart')
p1$chart(color = c('brown', 'blue', '#594c26', 'green'))
p1$print('chart1')
```


<div id = 'chart1' class = 'rChart nvd3'></div>
<script type='text/javascript'>
 $(document).ready(function(){
      drawchart1()
    });
    function drawchart1(){  
      var opts = {
 "dom": "chart1",
"width":    600,
"height":    400,
"x": "gpa_group",
"y": "gre",
"group": "rank",
"type": "multiBarChart",
"id": "chart1" 
},
        data = [
 {
 "gpa_group": "(2,3]",
"rank": "1",
"gre":          522.5 
},
{
 "gpa_group": "(3,3.5]",
"rank": "1",
"gre": 573.6363636364 
},
{
 "gpa_group": "(3.5,4]",
"rank": "1",
"gre":  661.935483871 
},
{
 "gpa_group": "(2,3]",
"rank": "2",
"gre":            565 
},
{
 "gpa_group": "(3,3.5]",
"rank": "2",
"gre": 580.5633802817 
},
{
 "gpa_group": "(3.5,4]",
"rank": "2",
"gre": 633.8461538462 
},
{
 "gpa_group": "(2,3]",
"rank": "3",
"gre": 473.3333333333 
},
{
 "gpa_group": "(3,3.5]",
"rank": "3",
"gre": 560.8333333333 
},
{
 "gpa_group": "(3.5,4]",
"rank": "3",
"gre": 628.8461538462 
},
{
 "gpa_group": "(2,3]",
"rank": "4",
"gre": 515.7142857143 
},
{
 "gpa_group": "(3,3.5]",
"rank": "4",
"gre":        571.875 
},
{
 "gpa_group": "(3.5,4]",
"rank": "4",
"gre": 603.8095238095 
} 
]
  
      if(!(opts.type==="pieChart" || opts.type==="sparklinePlus" || opts.type==="bulletChart")) {
        var data = d3.nest()
          .key(function(d){
            //return opts.group === undefined ? 'main' : d[opts.group]
            //instead of main would think a better default is opts.x
            return opts.group === undefined ? opts.y : d[opts.group];
          })
          .entries(data);
      }
      
      if (opts.disabled != undefined){
        data.map(function(d, i){
          d.disabled = opts.disabled[i]
        })
      }
      
      nv.addGraph(function() {
        var chart = nv.models[opts.type]()
          .width(opts.width)
          .height(opts.height)
          
        if (opts.type != "bulletChart"){
          chart
            .x(function(d) { return d[opts.x] })
            .y(function(d) { return d[opts.y] })
        }
          
         
        chart
  .color([ "brown", "blue", "#594c26", "green" ])
          
        

        
        
        
      
       d3.select("#" + opts.id)
        .append('svg')
        .datum(data)
        .transition().duration(500)
        .call(chart);

       nv.utils.windowResize(chart.update);
       return chart;
      });
    };
</script>
