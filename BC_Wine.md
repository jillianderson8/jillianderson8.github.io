---
layout: default
---

# BC Wine

<img src="/images/BC_Wine.png" alt="image" style = "max-width: 200%; margin-left: -50%" align = "center">

### The Story
I should also talk about the problem I am addressing here. What needs to be known? 

The story can go here. I should explain what I am trying to tell. How I have highlighted it with this graph. And then provide one or two discussion topics and questions for this graph. 

### The Learning
Here I should discuss what I have learned from this graph. Or what other learning this graph demonstrates. 

### The Process
Talk about the process to make this graph. Talk about where the data is from. 
How I went about wrangling with it (walk through some of my code and talk about the motivations). 
Include the code specific to data wrangling here. 


``` {R}
liq <- read.csv("/Users/jilliananderson/Documents/University/3B/INTEG375/Portfolio/BCLiquorStoreProductPriceListDec2015.csv")
View(wine)
wine <- liq %>%
  rename(Country = PRODUCT_COUNTRY_ORIGIN_NAME) %>%
  filter(ITEM_CATEGORY_NAME == "Wine") %>%
  mutate(L_per_sale = PRD_CONTAINER_PER_SELL_UNIT*PRODUCT_LITRES_PER_CONTAINER) %>%
  mutate(Price_per_Litre = PRODUCT_PRICE/L_per_sale) %>%
  select(Country, Price_per_Litre) %>%
  mutate(Country = paste(substr(Country,1,1),
                         tolower(substr(Country,2,15)),sep="")) %>%
  mutate(Country = ifelse(Country == "United states o", "USA", Country)) %>%
  mutate(Country = ifelse(Country == "New zealand", "New Zealand", Country)) %>%
  mutate(Country = ifelse(Country == "South africa", "South Africa", Country)) %>%
  group_by(Country) %>%
  mutate(total = n()) %>%
  filter(total >= 15)

## This is currently showing the 25th, Median, 75th percentiles. Is this problematic? 
ggplot(wine, aes(x=reorder(Country,Price_per_Litre, FUN=median),
                 y=Price_per_Litre)) + 
  labs(title="Wine Prices by Orgin Country (CAD/L)") + 
  geom_boxplot(fill = "#744357", 
               colour = "white",
               outlier.shape = NA, 
               coef = 0) + 
  annotate("rect", ymin= -5, ymax=310, xmin=13.8, xmax=15.35, fill = "white") + 
  theme_minimal() + 
  coord_flip(ylim=c(0,300)) + 
  annotate("rect", ymin= 90, ymax=210, xmin=14.65, xmax=15.35, fill = "grey30") + 
  annotate("segment", x=14.65, xend=15.35, y=150, yend=150, color="white", size=1) + 
  annotate("text", label="Median", x=14.3,y=150,size=3) + 
  annotate("text", label="25th", x=14.3,y=90,size=3) +
  annotate("text", label="75th", x=14.3,y=210,size=3) + 
  scale_y_continuous(breaks = c(0,50,100,150,200,250), 
                     labels = c("0", "50", "100", "150", "200", "$250")) + 
  theme(axis.title.x=element_blank(), 
        axis.title.y=element_blank(),
        axis.text.x =element_text(size=10),
        axis.text.y =element_text(size=10),        
        panel.grid.major.y=element_blank()) 

```