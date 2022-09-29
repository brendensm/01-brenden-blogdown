---
title: Opioid Data Plotting Practice
subtitle: Making some beautiful looking plots using ggplot2 and ggthemes!
author: Brenden Smith
date: '2022-09-27'
slug: []
categories: 
- R
tags: []
excerpt: A walkthrough of creating some custom figures using ggplot2.
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>
<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

### Introduction

Over the summer, I took a course on public health surveillance. As a culminating project, we were tasked with creating original data visualizations for a fact sheet on a topic of our choosing. I chose to examine local opioid overdose and mortality data for my project.

This is a topic that is near to me. The opioid crisis has impacted many communities across the country. At this point, the topic is well known to most people. Despite awareness, overdoses are still rising.

In the following post, I will demonstrate how easily you can spice up basic ggplot graphics. In particular we will look at:
- a basic ggplot2 line chart
- ggthemes we can use to make a more professional looking figure
- and a brief glimpse at plotly (because interactive graphs are so cool!)

### Data Prep

To begin, we will load in our libraries. Be sure to install them if you haven’t already.

``` r
library(tidyverse)
library(ggthemes)
library(readxl)
library(plotly)
```

Next, we will read in our data using readxl. The data I am using comes from the Michigan Substance Use Disorder Data Repository (SUDDR). You can download the data yourself [here](https://mi-suddr.com/opioids/). Keep in mind that the numbers we are working with in this example are raw counts of opioid overdose deaths by county. I’m interested in looking at trends over time with this dataset.

Because I’m focusing on the three counties in my area, I’m going to create a vector with the names of the capital area counties. This will make subsetting the data a little easier.

``` r
opdeaths <- read_xlsx("Opioid Overdose Deaths.xlsx")

counties <- c("Ingham", "Eaton", "Clinton")
```

### Time to Plot!

From here, we can start our first plot. I will select my target counties using the `filter()` function that comes from the dplyr package. Be sure to specify which aesthetics you want to plot on the respective axes. Here we are putting the variable `Year` on the x-axis and `Opioid Overdose Deaths` on the y.

``` r
opdeaths %>%
  filter(County %in% counties) %>%
  ggplot(aes(x = Year, y = `Opioids Overdose Deaths`)) +
  geom_line()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Oh no! What happened? We didn’t tell ggplot which lines we wanted to see. It is important that within the layer `geom_line()` we specify that we want to plot different lines based on our county variable. To do this, we simply add an `aes()` layer and assign `color` to `County`.

``` r
opdeaths %>%
  filter(County %in% counties) %>%
  ggplot(aes(x = Year, y = `Opioids Overdose Deaths`)) +
  geom_line(aes(color = County))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

That looks a lot better! But we can do more. The lines look a bit skinny to me. I would like them to stand out more. It also might help to adjust the opacity of the lines. This can make points that cross over easier to read. To make these changes, we can specify `size` and `alpha` in `geom_line()` outside of the `aes()` argument.

I think it would be great to add points to our plot, too. Like the `geom_line()` layer, I want these to be large enough and overlap easily. I will pass through similar arguments in the `geom_point()` layer, also specifying the `color`.

``` r
opdeaths %>%
  filter(County %in% counties)  %>%
  ggplot(aes(x = Year, y = `Opioids Overdose Deaths`)) +
  geom_line(size = 1, alpha = 0.8, aes(color = County)) +
  geom_point(size = 2, alpha = 0.8, aes(color = County))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

Lastly, I want to add labels and theme to really polish up our plot. This is surprisingly easy! To add our labels, we add another layer called `labs()`. Here we can add a proper title, and more accurate labels for the axes.

Adding a theme is even easier. We can quickly add on a layer and pick a theme that we like. For my example, I’m using the fivethirtyeight theme that comes from ggthemes. Be sure to check out the other options available in this package.

After our `theme_fivethrityeight()` layer, I’m adding a general `theme()` layer to specify that I want all my main title and axes titles to be shown. I am also adjusting the text size to make the title a bit more readable.

``` r
opdeaths %>%
  filter(County %in% counties)  %>%
  ggplot(aes(x = Year, y = `Opioids Overdose Deaths`)) +
  geom_line(size = 1, alpha = 0.8, aes(color = County)) +
  geom_point(size = 2, alpha = 0.8, aes(color = County)) +
  labs(title = "Figure 1: Opioid Overdose Deaths, 1999 – 2020",
       x = "Year",
       y = "Number of Deaths") +
  theme_fivethirtyeight() +
  theme(axis.title = element_text(),
        plot.title = element_text(size = 14))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

And just like that, we have a very nice looking line chart!

### A Glimpse of Plotly

Next I want to briefly show how easy it is to take a basic ggplot figure and make it interactive with the amazing package `plotly`. If I save the figure we created before as an object, we can pass it through the function `ggplotly()`, and as a result, we get a chart where we can zoom in and hover over points to gain more insight. I will demonstrate this below.

``` r
p1 <- opdeaths %>%
  filter(County %in% counties)  %>%
  ggplot(aes(x = Year, y = `Opioids Overdose Deaths`)) +
  geom_line(size = 1, alpha = 0.8, aes(color = County)) +
  geom_point(size = 2, alpha = 0.8, aes(color = County)) +
  labs(title = "Figure 1: Opioid Overdose Deaths, 1999 – 2020",
       x = "Year",
       y = "Number of Deaths") +
  theme_fivethirtyeight() +
  theme(axis.title = element_text(),
        plot.title = element_text(size = 14))

ggplotly(p1)
```

<div id="htmlwidget-1" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020],"y":[0,0,2,2,0,0,2,3,2,2,1,3,5,1,5,2,3,5,10,8,3,6],"text":["Year: 1999<br />Opioids Overdose Deaths:  0<br />County: Clinton","Year: 2000<br />Opioids Overdose Deaths:  0<br />County: Clinton","Year: 2001<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2002<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2003<br />Opioids Overdose Deaths:  0<br />County: Clinton","Year: 2004<br />Opioids Overdose Deaths:  0<br />County: Clinton","Year: 2005<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2006<br />Opioids Overdose Deaths:  3<br />County: Clinton","Year: 2007<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2008<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2009<br />Opioids Overdose Deaths:  1<br />County: Clinton","Year: 2010<br />Opioids Overdose Deaths:  3<br />County: Clinton","Year: 2011<br />Opioids Overdose Deaths:  5<br />County: Clinton","Year: 2012<br />Opioids Overdose Deaths:  1<br />County: Clinton","Year: 2013<br />Opioids Overdose Deaths:  5<br />County: Clinton","Year: 2014<br />Opioids Overdose Deaths:  2<br />County: Clinton","Year: 2015<br />Opioids Overdose Deaths:  3<br />County: Clinton","Year: 2016<br />Opioids Overdose Deaths:  5<br />County: Clinton","Year: 2017<br />Opioids Overdose Deaths: 10<br />County: Clinton","Year: 2018<br />Opioids Overdose Deaths:  8<br />County: Clinton","Year: 2019<br />Opioids Overdose Deaths:  3<br />County: Clinton","Year: 2020<br />Opioids Overdose Deaths:  6<br />County: Clinton"],"type":"scatter","mode":"lines+markers","line":{"width":3.77952755905512,"color":"rgba(248,118,109,0.8)","dash":"solid"},"hoveron":"points","name":"Clinton","legendgroup":"Clinton","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":0.8,"size":7.55905511811024,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(248,118,109,1)"}},"frame":null},{"x":[1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020],"y":[0,0,0,1,3,5,3,5,8,5,6,10,6,9,16,11,19,20,13,24,13,24],"text":["Year: 1999<br />Opioids Overdose Deaths:  0<br />County: Eaton","Year: 2000<br />Opioids Overdose Deaths:  0<br />County: Eaton","Year: 2001<br />Opioids Overdose Deaths:  0<br />County: Eaton","Year: 2002<br />Opioids Overdose Deaths:  1<br />County: Eaton","Year: 2003<br />Opioids Overdose Deaths:  3<br />County: Eaton","Year: 2004<br />Opioids Overdose Deaths:  5<br />County: Eaton","Year: 2005<br />Opioids Overdose Deaths:  3<br />County: Eaton","Year: 2006<br />Opioids Overdose Deaths:  5<br />County: Eaton","Year: 2007<br />Opioids Overdose Deaths:  8<br />County: Eaton","Year: 2008<br />Opioids Overdose Deaths:  5<br />County: Eaton","Year: 2009<br />Opioids Overdose Deaths:  6<br />County: Eaton","Year: 2010<br />Opioids Overdose Deaths: 10<br />County: Eaton","Year: 2011<br />Opioids Overdose Deaths:  6<br />County: Eaton","Year: 2012<br />Opioids Overdose Deaths:  9<br />County: Eaton","Year: 2013<br />Opioids Overdose Deaths: 16<br />County: Eaton","Year: 2014<br />Opioids Overdose Deaths: 11<br />County: Eaton","Year: 2015<br />Opioids Overdose Deaths: 19<br />County: Eaton","Year: 2016<br />Opioids Overdose Deaths: 20<br />County: Eaton","Year: 2017<br />Opioids Overdose Deaths: 13<br />County: Eaton","Year: 2018<br />Opioids Overdose Deaths: 24<br />County: Eaton","Year: 2019<br />Opioids Overdose Deaths: 13<br />County: Eaton","Year: 2020<br />Opioids Overdose Deaths: 24<br />County: Eaton"],"type":"scatter","mode":"lines+markers","line":{"width":3.77952755905512,"color":"rgba(0,186,56,0.8)","dash":"solid"},"hoveron":"points","name":"Eaton","legendgroup":"Eaton","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","marker":{"autocolorscale":false,"color":"rgba(0,186,56,1)","opacity":0.8,"size":7.55905511811024,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,186,56,1)"}},"frame":null},{"x":[1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020],"y":[5,1,3,7,14,14,15,7,15,19,17,23,31,30,41,46,52,66,63,74,78,99],"text":["Year: 1999<br />Opioids Overdose Deaths:  5<br />County: Ingham","Year: 2000<br />Opioids Overdose Deaths:  1<br />County: Ingham","Year: 2001<br />Opioids Overdose Deaths:  3<br />County: Ingham","Year: 2002<br />Opioids Overdose Deaths:  7<br />County: Ingham","Year: 2003<br />Opioids Overdose Deaths: 14<br />County: Ingham","Year: 2004<br />Opioids Overdose Deaths: 14<br />County: Ingham","Year: 2005<br />Opioids Overdose Deaths: 15<br />County: Ingham","Year: 2006<br />Opioids Overdose Deaths:  7<br />County: Ingham","Year: 2007<br />Opioids Overdose Deaths: 15<br />County: Ingham","Year: 2008<br />Opioids Overdose Deaths: 19<br />County: Ingham","Year: 2009<br />Opioids Overdose Deaths: 17<br />County: Ingham","Year: 2010<br />Opioids Overdose Deaths: 23<br />County: Ingham","Year: 2011<br />Opioids Overdose Deaths: 31<br />County: Ingham","Year: 2012<br />Opioids Overdose Deaths: 30<br />County: Ingham","Year: 2013<br />Opioids Overdose Deaths: 41<br />County: Ingham","Year: 2014<br />Opioids Overdose Deaths: 46<br />County: Ingham","Year: 2015<br />Opioids Overdose Deaths: 52<br />County: Ingham","Year: 2016<br />Opioids Overdose Deaths: 66<br />County: Ingham","Year: 2017<br />Opioids Overdose Deaths: 63<br />County: Ingham","Year: 2018<br />Opioids Overdose Deaths: 74<br />County: Ingham","Year: 2019<br />Opioids Overdose Deaths: 78<br />County: Ingham","Year: 2020<br />Opioids Overdose Deaths: 99<br />County: Ingham"],"type":"scatter","mode":"lines+markers","line":{"width":3.77952755905512,"color":"rgba(97,156,255,0.8)","dash":"solid"},"hoveron":"points","name":"Ingham","legendgroup":"Ingham","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","marker":{"autocolorscale":false,"color":"rgba(97,156,255,1)","opacity":0.8,"size":7.55905511811024,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(97,156,255,1)"}},"frame":null}],"layout":{"margin":{"t":53.7969281859693,"r":19.2,"b":51.8774595267746,"l":58.2535491905355},"plot_bgcolor":"rgba(240,240,240,1)","paper_bgcolor":"rgba(240,240,240,1)","font":{"color":"rgba(60,60,60,1)","family":"sans","size":15.9402241594022},"title":{"text":"<b> Figure 1: Opioid Overdose Deaths, 1999 – 2020 <\/b>","font":{"color":"rgba(60,60,60,1)","family":"sans","size":18.5969281859693},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[1997.95,2021.05],"tickmode":"array","ticktext":["2000","2005","2010","2015","2020"],"tickvals":[2000,2005,2010,2015,2020],"categoryorder":"array","categoryarray":["2000","2005","2010","2015","2020"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.98505603985056,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(60,60,60,1)","family":"sans","size":12.7521793275218},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(210,210,210,1)","gridwidth":0.724555643609193,"zeroline":false,"anchor":"y","title":{"text":"Year","font":{"color":"rgba(60,60,60,1)","family":"sans","size":15.9402241594022}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-4.95,103.95],"tickmode":"array","ticktext":["0","25","50","75","100"],"tickvals":[0,25,50,75,100],"categoryorder":"array","categoryarray":["0","25","50","75","100"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.98505603985056,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(60,60,60,1)","family":"sans","size":12.7521793275218},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(210,210,210,1)","gridwidth":0.724555643609193,"zeroline":false,"anchor":"x","title":{"text":"Number of Deaths","font":{"color":"rgba(60,60,60,1)","family":"sans","size":15.9402241594022}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"transparent","width":0.724555643609193,"linetype":"none"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(240,240,240,1)","bordercolor":"transparent","borderwidth":2.06156048675734,"font":{"color":"rgba(60,60,60,1)","family":"sans","size":12.7521793275218},"title":{"text":"County","font":{"color":"rgba(60,60,60,1)","family":"sans","size":15.9402241594022}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"102553b864e14":{"x":{},"y":{},"colour":{},"type":"scatter"},"102556e702193":{"x":{},"y":{},"colour":{}}},"cur_data":"102553b864e14","visdat":{"102553b864e14":["function (y) ","x"],"102556e702193":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

It’s amazing how quickly you can produce interactive charts with R! The output from this function in an html widget. So it can easily be viewed on a website or a local html file. This makes it ideal for sharing graphics quickly among coworkers.

For my project, I created a few more graphics with the same color palette and arranged them on a pdf for easy distribution. If you want to view the finished product you can find that [here](smith-HM808-factsheet.pdf).

I hope you found this post helpful. Next time I want to focus more on plotly demonstrating its capabilities with spatial data analysis. Until next time!
