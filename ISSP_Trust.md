---
layout: default
---

# ISSP Religion vs Trust

<img src="/images/ISSP_Trust.png" alt="image" style = "max-width: 200%; margin-left: -50%" align = "center">

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
issp <- read.dta("/Users/...)

isspgraph <- issp %>%
  select(V5, V63, V13) %>%
  # Renaming Variables
  rename(Country  = V5) %>%
  rename(religion = V63) %>%
  rename(trust = V13) %>%
  # Removing the country code in the form (__-). But Great Britain is a special case
  mutate(Country = substring (Country, 4)) %>%
  mutate(Country = gsub("GBN-Great Britain and/or United Kingdom", "Great Britain", Country)) %>%
  # Filtering out "Can't Choose" and "No Answer" as they skew results
  filter(religion!=0 & religion !="Can't Choose") %>%
  filter(trust !=0 & trust !="Can't Choose") %>%
  # Changing religious description as a scale 
  #0->6 = Extremely not religious->Extremely religious)
  mutate(religionnum = ifelse(religion == "Yes, Definitely", 1, religion)) %>%
  mutate(religionnum = abs(religionnum-7)) %>%
  # Changing answers to "religions bring more trust than peace" to a scale
  # 0->3 = No trust -> trust
  mutate(trustnum = ifelse(trust == "People can almost always be trusted", 1, trust)) %>%
  mutate(trustnum = abs(trustnum-4)) %>%
  # Finding the mean religious level and mean trust level for each country
  group_by(Country) %>%
  summarize(mean_religion = mean(religionnum), mean_trust = mean(trustnum))
```

After the data wrangling, I can go on to talk about the plotting. What design decisions were made and for what purpose. 

```
ggplot(isspgraph, aes(x=mean_religion, y=mean_trust, labels=Country)) +
  geom_point(color="orange") + 
  geom_text(aes(label=Country), size=2, 
            hjust=-0.1, color = "grey30") +
  theme_minimal() + 
  # Adding Plot Title
  ggtitle("Religion vs Trusting") + 
  theme(plot.title = element_text(lineheight=0.8, face="bold", size = 18)) + 
  # Removing axis text and titles
  theme(axis.text.x = element_blank()) +
  scale_x_continuous(name ="") + 
  theme(axis.text.y = element_blank()) + 
  scale_y_continuous(name ="") + 
  # Adding arrows to the axis
  annotate("segment", x=1, xend=5, y=1.5, yend=1.5, 
           arrow=arrow(ends = "both", type="closed", length=unit(0.02,"npc")))+ 
  annotate("segment", x=3, xend=3, y=0.5, yend=2.5, 
           arrow=arrow(ends = "both", type="closed", length=unit(0.02,"npc"))) + 
  # Annotating axis ends
  annotate("text", x=5, y=1.5, label="Religious", 
           vjust=-0.5, hjust = 0.7, fontface="bold", size=4) + 
  annotate("text", x=1, y=1.5, label="Non-religious", 
           vjust=-0.5, hjust = 0.2, fontface="bold", size=4) + 
  annotate("text", x=3, y=0.5, label="Un-trusting", 
           vjust=1.5, fontface="bold", size=4) + 
  annotate("text", x=3, y=2.5, label="Trusting", 
           vjust=-0.5, fontface="bold", size=4) + 
  coord_cartesian(ylim=c(0.4,2.6))
```

