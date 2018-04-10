Assignment 1, Create
================
Erin M. Ochoa
2018/04/10

``` r
library(dplyr)
library(ggplot2)
theme_set(theme_grey())
```

``` r
#Downloaded from:
#https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-present-Dashboard/5cd6-ry5g
crimes <- read.csv('~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/Crimes_-_2001_to_present.csv')
```

``` r
names(crimes)
head(crimes)
```

``` r
keep <- c('Year','Primary.Type','Arrest','Domestic','Community.Area')
crimes.sub <- crimes[,keep]
crimes.sub <- crimes.sub[(crimes.sub$Year < 2018), ]
write.csv(crimes.sub,'~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/crimes_sub.csv',
          row.names = FALSE)
rm(crimes)
```

``` r
crimes.sub <- read.csv('~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/crimes_sub.csv')
```

``` r
crimes.sub <- crimes.sub[(crimes.sub$Year == 2017), ]
```

``` r
crimes.sub %>% 
  group_by(Primary.Type) %>%
  summarise(num_rows = length(Primary.Type)) %>%
  data.frame() -> df_crimes

#Keep the 10 most common crimes (excluding the nebulous 'Other Offense' category)
keep_cats <- df_crimes[ order(-df_crimes[2]), ]
keep_cats <- unlist(list(as.character(keep_cats[(1:11), "Primary.Type"])))
keep_cats <- keep_cats[keep_cats != 'OTHER OFFENSE']

crimes.final <- crimes.sub[as.character(crimes.sub$Primary.Type) %in% keep_cats, ]
crimes.final$Primary.Type <- factor(crimes.final$Primary.Type, levels = keep_cats)
```

``` r
df_crimes$prop <- df_crimes$num_rows / sum(df_crimes$num_rows)
df_crimes.sub <- df_crimes[as.character(df_crimes$Primary.Type) %in% keep_cats, ]
df_crimes.sub$Primary.Type <- factor(df_crimes.sub$Primary.Type, levels = keep_cats)
df_crimes.sub <- df_crimes.sub[ order(-df_crimes.sub[3]), ]
```

``` r
leg_labels <- tools::toTitleCase(sapply(keep_cats, tolower,USE.NAMES = FALSE))
leg_labels <- gsub(" ", "\n", leg_labels, perl=TRUE)
```

``` r
ggplot(df_crimes.sub,aes(Primary.Type,prop)) + geom_bar(stat='identity',aes(fill=Primary.Type)) +
      theme(legend.position = 'none',legend.title = element_blank(),
            panel.border = element_rect(linetype = "solid",
                                        color = "grey70", fill=NA, size=1.1),
            plot.title = element_text(hjust = 0.5),
            axis.title.x=element_blank()) +
      scale_fill_brewer(palette = "Paired") +
      scale_y_continuous(labels = scales::percent_format()) +
      scale_x_discrete(labels=leg_labels) +
      labs(y='Proportion of Total Reported Crimes',
           title='Most Commonly Reported Types of Crime in Chicago, 2017')
```

![](hw1_create_files/figure-markdown_github/viz-1.png)

``` r
#Get prop of each type and arrest status
crimes.sub %>% 
  group_by(Primary.Type,Arrest) %>%
  summarise(num_rows = length(Primary.Type)) %>%
  data.frame() -> df.crimes

df.crimes$prop <- df.crimes$num_rows / sum(df.crimes$num_rows)
df.crimes.sub <- df.crimes[as.character(df.crimes$Primary.Type) %in% keep_cats, ]
df.crimes.sub$Primary.Type <- factor(df.crimes.sub$Primary.Type, levels = keep_cats)
df.crimes.sub <- df.crimes.sub[ order(-df.crimes.sub[3]), ]
```

``` r
ggplot(df.crimes.sub,aes(Primary.Type,prop,Arrest)) +
      geom_bar(stat='identity',aes(alpha=Arrest,fill=Primary.Type)) +
      theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
      labs(y='Proportion of Total Reported Crimes',
           title='Most Commonly Reported Types of Crime in Chicago, 2017') +
      scale_fill_brewer(palette='Paired',guide=FALSE) + guides(fill=FALSE) +
      scale_alpha_discrete(range=c(.4,1),breaks=c('true','false'),
                           labels=c('Arrest','No Arrest')) + 
      scale_y_continuous(labels=scales::percent_format(),limits=c(0,.25)) +
      scale_x_discrete(labels=leg_labels)
```

![](hw1_create_files/figure-markdown_github/viz2-1.png)