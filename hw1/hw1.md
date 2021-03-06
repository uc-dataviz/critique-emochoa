Assignment 1
================
Erin M. Ochoa
2018/04/10

Part II: Critique
=================

Consider a visualization of "'The Office' Characters' Most Distinguishing Words", found at [Reddit](https://www.reddit.com/r/dataisbeautiful/comments/8a4gbr/the_office_characters_most_distinguishing_words_oc/) and attributed to [/u/RyBread7](https://www.reddit.com/user/RyBread7)): ![](https://i.redd.it/3k2o82q966q01.png)

Truthful?
---------

The visualization does not cite a source for the text that was analyzed; given what I assume to be the uncontroversial nature of the subject material, however, I have no reason to doubt that the analyses were conducted with scripts of the show. So yes, the visualization is likely truthful.

Functional?
-----------

The visualization is reasonably functional. I find the use of two colors per character to be unnecessary, however; a single color per character would have sufficed. Better yet, a character-specific color could have been used for each character's right-side bar, and then a common color could have been used for the below-character per-season charts. The repeating 'total words' text under each character could have been replaced with the character name (this would have been useful for those of us who have never seen the show to facilitate learning more by looking up certain characters). The word 'legend' in the legend is a poor use of space, especially because the explanation of the equation could have been clearer. The visualization would also benefit from listing a source and an attribution.

(Also, it is worth mentioning that I find the list of each character's most idiosyncratic words to be of no interest, despite it arguably being the main point of the visualization.)

What does this visualization do well? It succinctly and elegantly summarizes each character's proportional contribution to the total number of words spoken in what appears to be the entirety of 'The Office'. It also shows when characters enter and exit the show's cast (by using gaps in the below-character seasonal speaking summary).

Beautiful?
----------

The visualization is aesthetically pleasing, although, as someone who has not seen this show, I wonder whether there are really nine main characters or whether minor characters are being used to pad the number represented to an even (so to speak) nine (or, conversely, whether major characters have been dropped to bring the total to nine). I also wonder where the designer obtained the character illustrations. Though the choice of a serif font is likely a stylistic choice to line up with the branding of the show, a sans-serif font would have rendered a more elegant presentation. Additionally, the designer could have taken more care in copy-editing the text; there are misspellings, mis-capitalizations, and an errant comma.

Insightful?
-----------

The main thing I learned from this visualization is that women speak very little in 'The Office'. I also learned that all the characters in 'The Office' are white (or at least the characters the designer found most important). I suppose that neither of these facts are surprising, especially given that American entertainment tends to favor characters that are both white and male, but they are certainly disheartening.

Enlightening?
-------------

It is likely that this visualization will add very little value, if any at all, to the world. The only way I can imagine that it could do some good is if it opened people's minds to the overwhelming disparities in racial and gender representations in popular media (and the overwhelming real-world racial and gender biases that such disparities reflect). I doubt, however, that this graphic will have much impact.

To investigate my prediction that this visualization will have little or no such impact, I searched through all the comments on the Reddit thread (as of 6:40 a.m. today, there were nearly 1,500) to find any that related to the racial and gender disparities I perceive. (I searched for 'men', 'women', 'man', 'woman', 'guy', 'guys', 'girl', 'girls', 'ethnicity', 'race','color', 'white', and 'black'.) There was a single comment addressing the gender disparity; it had been downvoted (this is how Reddit participants anonymously show their disapproval of other participants' comments). Similarly, one of the two comments relating to the racial disparity had been downvoted, while the other had seemingly been ignored. Only a few Reddit participants cared—enough to comment, that is—that this graphic points out overwhelming racial and gender disparities that amount to unequal and inequitable representation. Not even the designer of the visualization seems to care or even have noticed that the words of dialogue in 'The Office' are spoken overwhelmingly by white men.

A recent [study](http://assets.pewresearch.org/wp-content/uploads/sites/13/2016/02/PJ_2016.02.25_Reddit_FINAL.pdf) found that Reddit users are mostly white, male, and young. Their collective choice to ignore the most obvious—at least, to me—aspect of this visualization is especially telling of their white male privilege (of which they are likely unaware): A graphic showing that nine white characters comprise what is apparently the most noteworthy portion of the cast of 'The Office' (if not the entire cast of characters) merited only two comments; despite appearing to comprise one-third of the cast (something that is itself a disparity), women characters spoke a mere 16.3% of the words uttered in the entirety of the series (this means that a woman spoke one word for every 5.14 words a man spoke), only one user commented on the gender disparity. If Reddit participants do not notice or care about racial and gender disparities in a television show, can we expect that they will notice or care about such matters in real life? Pardon my unrepentant skepticism.

How much does the Internet talk about 'The Office'? A Google search for "'The Office' television" yields approximately 533,000,000 results. In comparison, a Google search for 'gender disparity television' yields approximately 216,000 results. This is a conversation that relatively few people are having. This graphic could contribute to the conversation, but only time will tell if it raises questions in the public sphere about the relative emphasis on male characters (and the seemingly complete absence of characters of color) in 'The Office' specifically and in Western media, especially American media, overall.

Part II: Create
===============

``` r
library(dplyr)
library(ggplot2)
theme_set(theme_grey()) #Because cowplot sets a different default theme
```

``` r
#I ran this once to read in the file (1.55 GB), then wrote a subset of it to another file for knitting purposes.

#Downloaded from:
#https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-present-Dashboard/5cd6-ry5g
crimes <- read.csv('~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/Crimes_-_2001_to_present.csv')
```

``` r
#Run once after initial load of full dataset.
names(crimes)
head(crimes)
```

``` r
#Run once after initial load of full dataset.
keep <- c('Year','Primary.Type','Arrest','Domestic','Community.Area')
crimes.sub <- crimes[,keep]
crimes.sub <- crimes.sub[(crimes.sub$Year < 2018), ]
write.csv(crimes.sub,'~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/crimes_sub.csv',
          row.names = FALSE)
rm(crimes)
```

``` r
#This command reads in the subset of the data, which I further subset later.
crimes.sub <- read.csv('~/Google Drive/School/2018 Spring/Data Viz/Assignments/HW1/crimes_sub.csv')
```

``` r
#Let's visualize a single year of data.
crimes.sub <- crimes.sub[(crimes.sub$Year == 2017), ]
```

``` r
#Count the number of crimes of each type
crimes.sub %>% 
  group_by(Primary.Type) %>%
  summarise(num_rows = length(Primary.Type)) %>%
  data.frame() -> df_crimes

#Keep the 10 most common crimes (excluding the nebulous 'Other Offense' category)
keep_cats <- df_crimes[ order(-df_crimes[2]), ]
keep_cats <- unlist(list(as.character(keep_cats[(1:11), "Primary.Type"])))
keep_cats <- keep_cats[keep_cats != 'OTHER OFFENSE']
```

``` r
#Get count of each type by arrest status
crimes.sub %>% 
  group_by(Primary.Type,Arrest) %>%
  summarise(num_rows = length(Primary.Type)) %>%
  data.frame() -> df.crimes

#Calc prop for each row relative to the whole, then keep 10 most common types
df.crimes$prop <- df.crimes$num_rows / sum(df.crimes$num_rows)
df.crimes.sub <- df.crimes[as.character(df.crimes$Primary.Type) %in% keep_cats, ]
df.crimes.sub$Primary.Type <- factor(df.crimes.sub$Primary.Type, levels = keep_cats)
df.crimes.sub <- df.crimes.sub[ order(df.crimes.sub[3]),]
```

``` r
#Title-case the category labels
leg_labels <- tools::toTitleCase(sapply(keep_cats, tolower,USE.NAMES = FALSE))
leg_labels <- gsub(" ", "\n", leg_labels, perl=TRUE)
```

``` r
ggplot(df.crimes.sub,aes(Primary.Type,prop,Arrest)) +
      geom_bar(stat='identity',aes(alpha=as.factor(Arrest),fill=Primary.Type)) +
      theme(legend.position='bottom',legend.title=element_blank(),
            plot.title=element_text(hjust=0.5),
            plot.subtitle = element_text(hjust = 0.5),
            axis.title.x=element_blank(),
            panel.background=element_blank(),
            panel.grid.major.y=element_line(colour='grey50',size=.25),
            panel.grid.major.x=element_blank(),
            axis.ticks=element_blank()) +
      labs(y='Proportion of All 2017 Crimes',
           title='Most Commonly Reported Types of Crime in Chicago, 2017',
           subtitle='Representing 88% of all crimes known to the police. Source: City of Chicago Data Portal') +
      scale_fill_brewer(palette='Paired') + guides(fill=FALSE) +
      scale_alpha_discrete(range=c(.4,1),breaks=c('true','false'),
                           labels=c('Arrest','No Arrest')) + 
      scale_y_continuous(labels=scales::percent_format(),limits=c(0,.25)) +
      scale_x_discrete(labels=leg_labels)
```

![](hw1_files/figure-markdown_github/viz-1.png)

What is the story?
------------------

This graph shows the ten most common types of crime known to police in Chicago in the year 2017. With homicide and weapons violations absent from the categories, we see that despite what is portrayed in the media, murder and gun-related offenses must have been relatively rare last year. Robbery, a type of crime about which students, faculty, and staff at the University of Chicago are regularly alerted, was only the seventh most-common crime type. Even less common were known narcotics offenses, though, in contrast to every other represented category, all or nearly all of these resulted in arrest.

Why did you select this graphical form?
---------------------------------------

I chose to use a bar chart for three key reasons: First, a pie chart would have been inappropriate and hard to read; it would have required me to use all crime categories or else present data in a misleading form and the slices would have been tiny. Second, a line chart seemed inappropriate because it would have given the impression that the data were longitudinal, whereas they are cross-sectional. Third, a bar chart lends itself well to splitting categories into subcategories; this feature allowed me to use transparency to represent the share of known crimes per category that did not result in arrest, a decision I will discuss more fully below.

Why did you use these specific channels to encode the data (e.g. spatial position, colors, scale)?
--------------------------------------------------------------------------------------------------

### Spatial position

I chose to make a stacked bar chart instead of a dodged chart because representing crimes in which there was an arrest and those in which there was not as part of the same whole was important. Additionally, using a dodged chart causes a gap in the 'Narcotics' section, something that is distracting and aesthetically displeasing.

### Colors

I removed a lot of chart junk (background color, vertical gridlines, and crime types from the legend) and used aesthetics and custom labels to convey important information. I consulted several sources before deciding that the 'Paired' color scale was best for color-blindness. I added transparency to distinguish cases in which there was an arrest from cases in which there was no arrest, though this was not my first inclination: I originally tried the 'fill' aesthetic to add borders to each section of each bar; the idea was that the border would separate the arrests from the non-arrests. The result was disappointing, so I added transparency, this was an improvement, but the best iteration is the one presented here. Finally, I removed horizontal gridlines and all tick marks, added percent signs to the y-axis, fixed the casing on the category labels, and included descriptive labels for the y-axis, title, and legend categories.

### Scale

The scale was intentionally chosen to represent the relative proportions of the represented crime types to all crimes known to the police in 2017 as opposed to a count of such crimes. Providing a raw count of crimes would draw the reader's attention to the sheer number of crimes reported in 2017, which might seem outrageously large to naive readers who fail to take into account the city's total population when considering the number of crimes. The goal is not to show that Chicago does or does not have a lot of crime, which a count of crimes would suggest; the goal is to show that the vast majority of crimes are not the ones about which residents might spend the most time worrying.

Why did you make any other data transformations?
------------------------------------------------

Data were aggregated to calculate the percentage of crimes in each category relative to all crimes. This is because the goal was to show that the vast majority of crimes known to the police in 2017 were the ones about which we tended to hear the least.

How do these decisions facilitate effective communication?
----------------------------------------------------------

This graph is insightful. We immediately grasp that all or nearly all narcotics cases known to the police resulted in an arrest. Criminal trespass was the only other category in which a majority of reports resulted in an arrest. The vast majority of cases in every other category resulted in no arrests, which means that in 2017, Chicago had a very low clearance rate for the most common types of crimes. After pondering the graph, we realize that the impact of the Chicago police in 2017 was mostly focused on making arrests in cases related to drugs. One wonders whether this is a vital function that justifies the level of funding one imagines the police force receives to support such arrests, not to mention the costs of prosecuting such cases and housing or monitoring those convicted of such offenses.

This graph is enlightening. We see that homicides are so rare that they are not present in the top ten most common types of crime. This might ease residents' anxiety about safety in Chicago.
