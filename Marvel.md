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
library(igraph)
library(foreign)

marv <- read.graph("/Users/.../marvel.graphml.txt", format=c("graphml"))

marv <- marv %>%
  remove.vertex.attribute("r")%>%
  remove.vertex.attribute("g")%>%
  remove.vertex.attribute("b")%>%
  remove.vertex.attribute("x")%>%
  remove.vertex.attribute("y")%>%
  remove.vertex.attribute("size")%>%
  remove.edge.attribute("Edge Label")%>%
  remove.edge.attribute("Edge Id")%>%
  remove.edge.attribute("weight")

ego <- make_ego_graph(marv, 1, nodes = 
                        which((V(marv)$label == "comic") &
                                (degree(marv)>80)))

e1 <- first(ego[1])
e2 <- first(ego[2])
e3 <- first(ego[3])
e4 <- first(ego[4])


V(e1)$name <- V(e1)$id
V(e2)$name <- V(e2)$id
V(e3)$name <- V(e3)$id
V(e4)$name <- V(e4)$id

g1 <- e1 %u% e2
V(g1)$label = ifelse((is.na(V(g1)$label_1)), 
                     V(g1)$label_2, 
                     V(g1)$label_1)
V(g1)$id = ifelse((is.na(V(g1)$id_1)),
                  V(g1)$id_2, 
                  V(g1)$id_1)
g1 <- g1 %>%
  remove.vertex.attribute("label_1") %>%
  remove.vertex.attribute("label_2") %>%
  remove.vertex.attribute("id_1") %>%
  remove.vertex.attribute("id_2") 

g2 <- e3 %u% e4
V(g2)$label = ifelse((is.na(V(g2)$label_1)), 
                     V(g2)$label_2, 
                     V(g2)$label_1)
V(g2)$id = ifelse((is.na(V(g2)$id_1)), 
                  V(g2)$id_2, 
                  V(g2)$id_1)
g2 <- g2 %>%
  remove.vertex.attribute("label_1") %>%
  remove.vertex.attribute("label_2") %>%
  remove.vertex.attribute("id_1") %>%
  remove.vertex.attribute("id_2") 

g <- g1 %u% g2
V(g)$label = ifelse((is.na(V(g)$label_1)), 
                    V(g)$label_2, 
                    V(g)$label_1)
V(g)$id = ifelse((is.na(V(g)$id_1)), 
                 V(g)$id_2,
                 V(g)$id_1)
g <- g %>%
  remove.vertex.attribute("label_1") %>%
  remove.vertex.attribute("label_2") %>%
  remove.vertex.attribute("id_1") %>%
  remove.vertex.attribute("id_2") 


V(g)$size  = ifelse(V(g)$label == "hero",
                    5, 17)
V(g)$color = ifelse(V(g)$label == "hero", 
                    "tomato", "steelblue")
V(g)$label = ifelse(V(g)$label == "hero",
                    NA, V(g)$id)


png(filename="Marvel.png", height=1600, width=2400)
par(mar=c(0,0,3,0) + 0.5)
par(oma=c(0,0,12,0))
plot(g, 
     layout = layout_with_kk(g),
     vertex.label.color = "#EEEEEE", 
     vertex.label.cex = 2)
title(main = list("Marvel Overlap", cex = 10), 
      outer = TRUE)
legend(x=-0.5, y=1.3,
       c("Hero","Comic"), 
       pch=21, cex=4.5,
      col="#777777", 
      pt.bg=c("tomato", "steelblue"), 
      pt.cex=10, 
      bty="n", ncol=2)
dev.off()

```