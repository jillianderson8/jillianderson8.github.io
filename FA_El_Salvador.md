---
layout: default
---

# Saskatchewan Elections

<img src="/images/Child_Poverty.png" alt="image" style = "max-width: 200%; margin-left: -50%" align = "center">

### The Story
I should also talk about the problem I am addressing here. What needs to be known? 

The story can go here. I should explain what I am trying to tell. How I have highlighted it with this graph. And then provide one or two discussion topics and questions for this graph. 

### The Learning
Here I should discuss what I have learned from this graph. Or what other learning this graph demonstrates. 

### The Process
Talk about the process to make this graph. Talk about where the data is from. 
How I went about wrangling with it (walk through some of my code and talk about the motivations). 
Include the code specific to data wrangling here. 

```
# Setting up the Data Set
library(foreign)
library(dplyr)
library(ggplot2)
library(igraph)
library(reshape2)

fa <- read.csv("/Users/.../aiddata2-1_thin.csv")

faelswar<- fa %>%
  filter (recipient == "El Salvador" & year < 1997 & year > 1975 & 
            (donor == "United States" |
            donor == "Inter-American Development Bank (IADB)" |
            donor == "World Bank - International Bank for Reconstruction and Development (IBRD)" |
            donor == "International Monetary Fund (IMF)" | 
            donor == "Japan")) %>%
  group_by (year, donor) %>%
  summarise (total = sum(commitment_amount_usd_constant)/1000000)

ggplot(faelswar, aes(x=year, y=total, colour = donor)) + 
  annotate ("rect", xmin = 1980, xmax = 1992, 
            ymin = 0, ymax = 800, 
            alpha = 0.3, fill = "grey") +
  geom_line(size = 0.7) + theme_minimal() + 
  ggtitle("El Salvador Foreign Aid during the Civil War (1980-1992)") +
  theme(plot.title = element_text(lineheight=.8, 
                                  face="bold", 
                                  size = 15)) +
  theme(legend.position="top") +
  scale_color_manual(name = "",
                     values = c("#E69F00","#999999", "#009E73", 
                                "#56B4E9","#CC79A7"),
                     labels=c("IADB", "IMF", "Japan", 
                              "United States", "IBRD")) + 
  scale_y_continuous(name="Aid in Millions (USD)") +
  scale_x_continuous(name = "")
```