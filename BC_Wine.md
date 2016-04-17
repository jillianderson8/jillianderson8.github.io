---
layout: default
---

<img src="/images/BC_Wine.png" alt="image" style = "max-width: 150%; margin-left: -25%" align = "center">

### The Story
I should also talk about the problem I am addressing here. What needs to be known? 

The story can go here. I should explain what I am trying to tell. How I have highlighted it with this graph. And then provide one or two discussion topics and questions for this graph. 

### The Learning
Here I should discuss what I have learned from this graph. Or what other learning this graph demonstrates.

### The Process
This graph was produced using the "BC Liquor Store Product Price List"[^1] made available on [opendatabc.ca](https://www.opendatabc.ca/). The dataset I used can be found [here](https://www.opendatabc.ca/dataset/bc-liquor-store-product-price-list-current-prices). This dataset contains the prices of 5,563 products sold in BC Liquor Stores, as well as the characteristics of the products. These characteristics included category (ie. Wine, Spirit, Beer), alcohol content, and origin country. 

Once I had acquired this dataset, I was able to begin exploring it. I soon found this dataset listed over 3,500 wines, originating from a wide variety of countries. I was intrigued by this and decided to compare the prices of wine based on their origin country. 

To begin, I needed to find the price per Litre of each wine which was sold. This was done by first finding the number of litres with each sale, by multiplying the number of containers per sale by the number of litres per container. From here, I was able to divide the price of the product by the total litres of wine to get the final price per litre. 

After I found the price per litre of wine, I began comparing measures of centrality across countries of origin. I began by simply comparing the means between countries. This showed wines originating in France had an average price/L of almost $300, while all other countries had means below $120. After continuing to explore the raw data, I found these simple means were unable to tell the full story of wine prices. For example, France has a total of nine wines which cost over $3000 per liter, making their average wine price seem like a bargain. I decided there was perhaps a better story tell using the price distributions rather than a simple mean. 

Thus, I decided to create a boxplot to present this data. I soon realized there were multiple countries from which only a few wines originated from. I chose to exclude those countries which supplied fewer than 15 wines to the BC Liquor Stores. This was done in an effort to ensure the distributions were representative of wine industries in these nations, rather than a small group of wines. 

When making my boxplot, I chose to exclude the whiskers from the diagram. This was done to ensure I was able to communicate with my audience. As Stephen Few mentions, relatively few members of the public have been taught to read box plots, and thus an effort should be made to ensure their clarity and simplicity[^2]. To address this further, I have chosen to include a boxplot guide with my visualization. This helps ensure readers understand what I have presented, as may serve to teach readers who are unfamiliar with boxplots. Other design decisions, such as removing horizontal gridlines and unnecessary axis titles, have been done in an effort to minimize non-data ink.


``` {R}
liq <- read.csv("/Users/.../BCLiquorStoreProductPriceListDec2015.csv")

# Preparing the Dataset
# *********************

wine <- liq %>%
  rename(Country = PRODUCT_COUNTRY_ORIGIN_NAME) %>%
  filter(ITEM_CATEGORY_NAME == "Wine") %>%
  mutate(L_per_sale = PRD_CONTAINER_PER_SELL_UNIT*
                      PRODUCT_LITRES_PER_CONTAINER) %>%
  mutate(Price_per_Litre = PRODUCT_PRICE/L_per_sale) %>%
  select(Country, Price_per_Litre) %>%
  mutate(Country = paste(substr(Country,1,1),
                         tolower(substr(Country,2,15)),sep="")) %>%
  mutate(Country = ifelse(Country == "United states o", 
                          "USA", Country)) %>%
  mutate(Country = ifelse(Country == "New zealand", 
                          "New Zealand", Country)) %>%
  mutate(Country = ifelse(Country == "South africa", 
                          "South Africa", Country)) %>%
  group_by(Country) %>%
  mutate(total = n()) %>%
  filter(total >= 15)


# Plotting the Visualization
# **************************

ggplot(wine, aes(x=reorder(Country,Price_per_Litre, FUN=median),
                 y=Price_per_Litre)) + 
  labs(title="Wine Prices by Orgin Country (CAD/L)") + 
  geom_boxplot(fill = "#744357", 
               colour = "white",
               outlier.shape = NA, 
               coef = 0) + 
  annotate("rect", ymin= -5, ymax=310, 
           xmin=13.8, xmax=15.35, fill = "white") + 
  theme_minimal() + 
  coord_flip(ylim=c(0,300)) + 
  annotate("rect", ymin= 90, ymax=210, 
           xmin=14.65, xmax=15.35, fill = "grey30") + 
  annotate("segment", x=14.65, xend=15.35, y=150, yend=150, color="white", size=1) + 
  annotate("text", label="Median", x=14.3,y=150,size=3) + 
  annotate("text", label="25th", x=14.3,y=90,size=3) +
  annotate("text", label="75th", x=14.3,y=210,size=3) + 
  scale_y_continuous(breaks = c(0,50,100,150,200,250), 
                     labels = c("0", "50", "100", 
                                "150", "200", "$250")) + 
  theme(axis.title.x=element_blank(), 
        axis.title.y=element_blank(),
        axis.text.x =element_text(size=10),
        axis.text.y =element_text(size=10),        
        panel.grid.major.y=element_blank())

```

##### Footnotes:
[^1]: Open Data BC. (u.d.). BC Liquor Store Price List [Data set]. Retrieved March 26, 2016 from: https://www.opendatabc.ca/dataset/bc-liquor-store-product-price-list-current-prices
[^2]: Few, S. (2012). Show Me the Numbers: Designing Tables and Graphs to Enlighten (2nd ed.). USA: Analytics Press.
