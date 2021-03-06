CIMIS
================

``` r
# read CIMIS data via the public API and plot results
## 
## http://et.water.ca.gov/Rest/Index
  
# stuff you need
#
#   1) a CIMIS API key (see http://et.water.ca.gov/Home/Faq)
#   2) write the key to disk in json format:
#        {
#        "CIMIS-API-KEY": "b7aaaaff-zzzz-yyyy-xxxx-4ab4bbbb1b8"
#        }
#   3) adjust the startDate, endDate, and station per your requirements
  
library(jsonlite)

cimisKey <- fromJSON("./PRIVATE-CIMIS-API-KEY.json")
baseURL <- "http://et.water.ca.gov/api/data?appKey="
key <- cimisKey[["CIMIS-API-KEY"]]
station <- "&targets=231"
start <- "&startDate=2018-01-01"
end <- paste("&endDate=", Sys.Date(), sep="")
units <- "&unitOfMeasure=M"
# units <- "&unitOfMeasure=E"   # default = english units
apiString <- paste(baseURL, key, station, start, end, units, sep="")
data <- fromJSON(apiString)
data.df <- as.data.frame(data)
df2.df <- as.data.frame(data.df$Data.Providers.Records)
  
# convert df values to numeric
# 

DayAirTmpAvg.list <- df2.df$DayAirTmpAvg$Value       # save the values
df2.df$DayAirTmpAvg <- NULL                          # delete the column (currently a df)
df2.df$DayAirTmpAvg <- as.numeric(DayAirTmpAvg.list) # re-create the column as numeric

DayAirTmpMin.list <- df2.df$DayAirTmpMin$Value       
df2.df$DayAirTmpMin <- NULL                        
df2.df$DayAirTmpMin <- as.numeric(DayAirTmpMin.list)
  
DayAirTmpMax.list <- df2.df$DayAirTmpMax$Value       
df2.df$DayAirTmpMax <- NULL                        
df2.df$DayAirTmpMax <- as.numeric(DayAirTmpMax.list)
  
Julian.list <- df2.df$Julian       
df2.df$Julian <- NULL                        
df2.df$Julian <- as.numeric(Julian.list)
  
# convert more vars as necessary
```

``` r
library(ggplot2)

ggplot(data = df2.df, aes(x=Julian)) + 
  geom_line(aes(y = DayAirTmpMax), colour = "red") +
  geom_line(aes(y = DayAirTmpAvg), colour = "green") +
  geom_line(aes(y = DayAirTmpMin), colour = "blue") +
  ylim(-10,50) +
  labs(y = "air temp")
```

    ## Warning: Removed 1 rows containing missing values (geom_path).

    ## Warning: Removed 1 rows containing missing values (geom_path).

    ## Warning: Removed 1 rows containing missing values (geom_path).

![](CIMIS-api_files/figure-markdown_github/unnamed-chunk-2-1.png)
