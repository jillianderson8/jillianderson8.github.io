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

```{R}
# The Set Up

library(foreign)
library(dplyr)
library(ggplot2)
library(igraph)
library(reshape2)


fao <- read.csv("/Users/.../Production_Crops_E_Americas_1.csv")

# The Data
area <- fao %>%
  filter(Country == "Canada") %>%
  filter(Item == "Chick peas" | 
           Item=="Peas, dry" | 
           Item == "Beans, dry" | 
           Item == "Lentils") %>%
  filter(Element == "Area harvested") %>%
  melt(id.vars = c("Country.Code", "Country", "Item.Code", 
                   "Item", "Element.Code", "Element", "Unit")) %>%
  select(Item,variable, value) %>%
  rename(Year = variable) %>%
  rename(Area = value) %>%
  filter(Area != "" & 
           Area != "*" &
           Area != "F" &
           Area != "M" &
           Area != "Im" &
           !is.na(Area)) %>%
  mutate(Year = as.numeric(substr(Year, 2, 5))) %>%
  mutate(Area = as.numeric(Area)) %>%
  mutate(Item = gsub("Peas, dry", "Dry Peas", Item))%>%
  mutate(Item = gsub("Beans, dry", "Dry Beans", Item))%>%
  mutate(Item = gsub("Chick peas", "Chickpeas", Item))


# The Plot
ggplot(area, aes(x=Year, y=Area, colour=Item)) + 
  geom_line(size = 0.8) + 
  geom_text(aes(label=ifelse(Year==2014,as.character(Item), ""),
                vjust=ifelse(Item=="Chickpeas", 0.5 ,0.1)), 
            hjust=-0.1) + 
  theme_minimal() + 
  ggtitle("Harvested Pulse Crops in Canada") + 
  scale_y_continuous(name="Area Harvested (in millions)", 
                     breaks = c(0, 500000, 1000000, 1500000), 
                     labels = c(0, 0.5, 1, "1.5 Ha")) + 
  scale_x_continuous(name="", 
                     breaks = c(1960, 1970, 1980, 1990, 2000, 2010)) + 
  scale_colour_manual(values=c("#5DA5DA","#FAA43A",
                               "#60BD68","#F17CB0")) + 
  guides(colour = FALSE) + 
  coord_cartesian(xlim=c(1960,2020))
```