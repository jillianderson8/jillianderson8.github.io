---
layout: default
---

# My First Graph, ISSP trust

Here I can write some stuff. We will see what that might be. 

```
issp <- read.dta("/Users/jilliananderson/Documents/University/3B/INTEG375/Deliverables/DV01/GESISReligion.dta")

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

<img src="/images/ISSP_Trust.png" alt="image" style = "max-width: 50%" align = "right" >

