Interactive chart of headcount in UK Government departments
========================================================





```
## Error: there is no package called 'googleVis'
```

```
## Error: could not find function "gvisMotionChart"
```



```r
print(Motion, "chart")
```

```
## Error: object 'Motion' not found
```



```r
library(plyr)

# this is just showing code, to be copied from above when updated

# load data
RawDeptsFTE <- read.csv("./data/RawDeptsFTE.csv")

# get rid of fluff in colnames, making it suitable for reshape and
# googlevis
names(RawDeptsFTE) <- gsub("[.]", "", names(RawDeptsFTE))
names(RawDeptsFTE) <- gsub("FTE", "", names(RawDeptsFTE))
names(RawDeptsFTE) <- gsub("X", "FTE", names(RawDeptsFTE))

DeptsFTE <- ddply(RawDeptsFTE, .(Dept, Whitehall), summarise, FTE2009Q1 = sum(FTE2009Q1, 
    na.rm = TRUE), FTE2009Q2 = sum(FTE2009Q2, na.rm = TRUE), FTE2009Q3 = sum(FTE2009Q3, 
    na.rm = TRUE), FTE2009Q4 = sum(FTE2009Q4, na.rm = TRUE), FTE2010Q1 = sum(FTE2010Q1, 
    na.rm = TRUE), FTE2010Q2 = sum(FTE2010Q2, na.rm = TRUE), FTE2010Q3 = sum(FTE2010Q3, 
    na.rm = TRUE), FTE2010Q4 = sum(FTE2010Q4, na.rm = TRUE), FTE2011Q1 = sum(FTE2011Q1, 
    na.rm = TRUE), FTE2011Q2 = sum(FTE2011Q2, na.rm = TRUE), FTE2011Q3 = sum(FTE2011Q3, 
    na.rm = TRUE), FTE2011Q4 = sum(FTE2011Q4, na.rm = TRUE), FTE2012Q1 = sum(FTE2012Q1, 
    na.rm = TRUE), FTE2012Q2 = sum(FTE2012Q2, na.rm = TRUE))

# convert to wide to create WH and NWH FTE vars for each

DeptsFTEwide <- reshape(DeptsFTE, idvar = "Dept", timevar = "Whitehall", direction = "wide")

# make names manageable
names(DeptsFTEwide) <- gsub("sFTE", "", names(DeptsFTEwide))
names(DeptsFTEwide) <- gsub(".Core Whitehall Depts.", "WH", names(DeptsFTEwide))
names(DeptsFTEwide) <- gsub(".Non-Whitehall Civil Service", "NWH", names(DeptsFTEwide))

# convert to long
DeptsFTElong <- reshape(DeptsFTEwide, idvar = c("Dept"), varying = list(c(2:15), 
    c(16:29)), v.names = c("FTE_WH", "FTE_NWH"), times = c("2009Q1", "2009Q2", 
    "2009Q3", "2009Q4", "2010Q1", "2010Q2", "2010Q3", "2010Q4", "2011Q1", "2011Q2", 
    "2011Q3", "2011Q4", "2012Q1", "2012Q2"), timevar = "Period", direction = "long")

# Notes: yLambda: 0 is log, 1 is linear timeUnit: originally was _NOTHING
# playduration is in ms need to set xaxisoption to _NOTHING if showing
# bubble by default

```



```r

# this is same code as above, just with bubble chart - again, to be copied
# when code above is updated

suppressPackageStartupMessages(library(googleVis))
```

```
## Error: there is no package called 'googleVis'
```

```r

Motion=gvisMotionChart(DeptsFTElong, idvar="Dept", timevar="Period",
                       options=list(
                       height=400, 
                       width=1000, 
                       state="{\"playDuration\":10000,
                       \"xZoomedDataMin\":0,
                       \"xZoomedIn\":false,
                       \"time\":\"2010-07-01\",
                       \"yZoomedIn\":false,
                       \"orderedByY\":false,
                       \"sizeOption\":\"4\",
                       \"xLambda\":1,
                       \"colorOption\":\"2\",
                       \"yZoomedDataMax\":130000,
                       \"nonSelectedAlpha\":0.4,
                       \"iconType\":\"BUBBLE\",
                       \"dimensions\":{\"iconDimensions\":[\"dim0\"]},
                       \"uniColorForNonSelected\":false,
                       \"yZoomedDataMin\":0,
                       \"xZoomedDataMax\":21,
                       \"duration\":{\"multiplier\":1,\"timeUnit\":\"Q\"},
                       \"xAxisOption\":\"3\",
                       \"orderedByX\":true,
                       \"showTrails\":false,
                       \"yLambda\":1,
                       \"yAxisOption\":\"2\",
                       \"iconKeySettings\":[]};",
                       showSelectListComponent = 1,
                       showHeader=1,
                       showAdvancedPanel=1,
                       showChartButtons=1,
                       showXScalePicker=1,
                       showYScalePicker=1,
                       showXMetricPicker=1,
                       showYMetricPicker=1,
                       showSidePanel=1
                       ))
```

```
## Error: could not find function "gvisMotionChart"
```




```
## Error: object 'Motion' not found
```


Let's see a simple chart which will render right in the preview:

```r

library(ggplot2)
library(grid)
# order levels

DeptsFTEwithtotals$Dept <- reorder(DeptsFTEwithtotals$Dept,
                                   -DeptsFTEwithtotals$sFTE2012Q2) 

plot <-ggplot(DeptsFTEwithtotals,
              aes(x=factor(Dept),
                  y=FTE2012Q2,
                  fill=factor(Whitehall))) + 
  geom_bar(position="stack", binwidth=1) +
  guides(fill = guide_legend(reverse = FALSE, nrow =1,
                             direction="horizontal",
                             title="Legend")) +
  labs(y = "FTE in Q2 2012") +
  theme(legend.position="bottom",
        legend.key.size=unit(6,"pt"),
        panel.background=element_blank(),
        panel.grid.major.x=element_blank(),
        panel.grid.minor.y=element_blank(),
        panel.grid.major.y=element_line(colour="light grey"),
        axis.ticks=element_blank(),
        axis.text.x=element_text(angle=90, hjust=1, vjust=.5),
        axis.ticks.margin = unit(1,"pt"),
        axis.title.x=element_blank()
        )
```



![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 







```r
SumDeptsFTEAdj <- read.csv("./data/SumDeptsFTEAdjusted.csv")
```

```
## Warning: cannot open file './data/SumDeptsFTEAdjusted.csv': No such file
## or directory
```

```
## Error: cannot open the connection
```

```r

# remove total and blank rows
SumDeptsFTEAdj <- subset(SumDeptsFTEAdj, Dept != "TOTAL")
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r
SumDeptsFTEAdj <- subset(SumDeptsFTEAdj, Dept != "TOTAL NWH")
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r
SumDeptsFTEAdj <- subset(SumDeptsFTEAdj, Dept != "TOTAL WH")
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r
SumDeptsFTEAdj <- subset(SumDeptsFTEAdj, Dept != "")
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r

# get rid of mess in colnames, making it suitable for reshape and googlevis
names(SumDeptsFTEAdj) <- gsub("[.]","", names(SumDeptsFTEAdj))
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r
names(SumDeptsFTEAdj) <- gsub("FTE","", names(SumDeptsFTEAdj))
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r
names(SumDeptsFTEAdj) <- gsub("X","FTE", names(SumDeptsFTEAdj))
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r

# reshape
SumDeptsFTEAdjlong <- reshape(SumDeptsFTEAdj, idvar = c("Dept","Whitehall"), 
                  varying = c(3:16),
                  v.names = c("FTE"),
                  times = c("2009Q1","2009Q2","2009Q3","2009Q4",
                            "2010Q1","2010Q2","2010Q3","2010Q4",
                            "2011Q1","2011Q2","2011Q3","2011Q4",
                            "2012Q1","2012Q2"),
                  timevar = "Period",
                  direction = "long")
```

```
## Error: object 'SumDeptsFTEAdj' not found
```

```r

# format date, rebuild dates from quarters
SumDeptsFTEAdjlong$Date <- SumDeptsFTEAdjlong$Period
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r

SumDeptsFTEAdjlong$Date <- gsub("Q1","-03-31",SumDeptsFTEAdjlong$Date)
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r
SumDeptsFTEAdjlong$Date <- gsub("Q2","-06-30",SumDeptsFTEAdjlong$Date)
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r
SumDeptsFTEAdjlong$Date <- gsub("Q3","-09-30",SumDeptsFTEAdjlong$Date)
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r
SumDeptsFTEAdjlong$Date <- gsub("Q4","-12-31",SumDeptsFTEAdjlong$Date)
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r

SumDeptsFTEAdjlong$Date <- as.POSIXct(SumDeptsFTEAdjlong$Date, tz = "GMT")
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r

# plot
library(scales)
plotFTEadj <- ggplot(SumDeptsFTEAdjlong, aes(x=Date,
                                   y=FTE/1000,
                                   fill=factor(Whitehall))) + 
                geom_area() +
                facet_wrap(~ Dept, ncol=3, scales = "free_y") +
                #facet_wrap(~ Dept, ncol=3) +
                theme(legend.position="bottom",
                      legend.key.size=unit(6,"pt"),
                      panel.background=element_blank(),
                      panel.grid.major.x=element_blank(),
                      panel.grid.minor.y=element_blank(),
                      panel.grid.major.y=element_line(colour="light grey"),
                      axis.ticks=element_blank(),
                      axis.text.x=element_text(angle=90, hjust=1, vjust=.5),
                      axis.ticks.margin = unit(1,"pt"),
                      axis.title.x=element_blank()
                      )+
                labs(y = "FTE (thousands)", x = "") + 
                #scale_x_date(breaks = "3 months", labels=date_format("%B")) +
                guides(fill = guide_legend(reverse = FALSE, nrow =1,
                                           direction="horizontal",
                                           title="Legend"))
```

```
## Error: object 'SumDeptsFTEAdjlong' not found
```

```r
plotFTEadj
```

```
## Error: object 'plotFTEadj' not found
```
