---
title       : Efficiency of the Random Forest classifier
subtitle    : Tested on R Iris dataset library
author      : Daghan Altas
job         : Lifetime R learner
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
ext_widgets : {rCharts: libraries/nvd3}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---


## Overview
Random Forest is a highly accurate, yet easy-to-use classifier.   
In this presentation we apply the random forest to determine the Iris species based on length and width.

We will show that a simple random forest with a simple tree (see below with ntree = 1)

```r
randomForest(training, predicted_species, ntree = 1)
```
yields 99% accurate results.

Iris dataset has 5 columns and 150 samples.  
We will use columns 1 through 4 (sepal width, length, and petal width and length) to predict the Iris subspecies a given sample belongs to.


```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
```

---
## Learning algorithm
We will split the 150 samples to training (75%) and validation (25%) sets.  
To minimize side effect of the sample ordering , we'll randomize the sample selection.

```r
library(RANN)
library(caret)
library(rpart)
library(randomForest)
library(e1071)

iris$Species <- as.factor(iris$Species)
inTrain = createDataPartition(iris$Species, p = 3/4)[[1]]
training = iris[ inTrain,]
testing = iris[-inTrain,] 
rf <- randomForest(training[,-5], training$Species, ntree = input$iTree)
predTraining <- predict(rf, training[,-5])
predTesting<- predict(rf, testing[,-5])
# Accuracy
cmTesting <- confusionMatrix(predTesting, testing$Species) 
```
Notice that ntree parameter can be set using input$iTree.
This parameter is controlled by the slider in our shiny app.

---

## Plotting the accuracy of nTree
We'll retrain the model with 1 to 10 trees and look at the accuracy.  
The objective is to attain the highest accuracy with the lowest number of trees, to minimize computational complexity.   



<div id = 'chart637a69147380' class = 'rChart nvd3'></div>
<script type='text/javascript'>
 $(document).ready(function(){
      drawchart637a69147380()
    });
    function drawchart637a69147380(){  
      var opts = {
 "dom": "chart637a69147380",
"width":    800,
"height":    400,
"x": "tree_vector",
"y": "accuracy_vector",
"type": "scatterChart",
"id": "chart637a69147380" 
},
        data = [
 {
 "tree_vector": 1,
"accuracy_vector": 0.9444444444444 
},
{
 "tree_vector": 2,
"accuracy_vector": 0.9722222222222 
},
{
 "tree_vector": 3,
"accuracy_vector":              1 
},
{
 "tree_vector": 4,
"accuracy_vector": 0.9166666666667 
},
{
 "tree_vector": 5,
"accuracy_vector":              1 
},
{
 "tree_vector": 6,
"accuracy_vector":              1 
},
{
 "tree_vector": 7,
"accuracy_vector": 0.9722222222222 
},
{
 "tree_vector": 8,
"accuracy_vector":              1 
},
{
 "tree_vector": 9,
"accuracy_vector":              1 
},
{
 "tree_vector": 10,
"accuracy_vector": 0.9722222222222 
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

There is not a clear gain for going beyond 3-4 trees



---
## Conclusion 

Random Forest is a powerful and efficient classifier, even with a single tree.  

In this presentation, we have shown that adding trees for simple classification problems are not required.

Check out the interactive Shiny app at   

https://daghan.shinyapps.io/Forest/   

for trying it our yourself.

ps: you may need to wait a little for the results to be recomputed when you change the slider value
