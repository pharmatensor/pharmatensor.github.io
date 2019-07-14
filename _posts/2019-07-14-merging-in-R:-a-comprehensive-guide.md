---
layout: post
title:  "Merging in R: A comprehensive guide"
permalink: /mergeR
author: omar
categories: [ R, SQL ]
tags: [R, Rstudio , wrangling , MariaDB, dplyr]
image: assets/images/mergeR.png
description: "Rstudio provides a secure and easy way to implement SQ: databases in R"
featured: true
hidden: false
#rating: 4.5
---

# Merging Dataframes with common columns
The idea here is commming from the powerful `join` idea in SQL. Just like SQL you can have left join, righ join, inner join and outer join. We will get data and see how can we join data like this. There are at least 3 ways to merge matched columns in R that I know however I will discuss the 2 most helpful ways.

<br/>
Let me first import some dataset. I will get a Mariadb SQL dataset.

```{r}
library(dbplyr)
library(dplyr)
library(DBI)


con <- dpl

 con <-  dplyr::src_mysql(con = RMariaDB::MariaDB() ,
              dbname = "Carcinogenesis"    ,
              host =  "relational.fit.cvut.cz" ,
              user = "guest",
              port =  3306,
              password = rstudioapi::askForPassword("Database password")
                )



atom<- as.data.frame(dplyr::tbl(con ,"atom" ))

sbond3<- as.data.frame(dplyr::tbl(con ,"sbond_3" ))

```

Before starting matching please take care that in real data, you must think about the nature of your column. In SQL you have a primary key and secondary key. While in R there is no inherent thing to control errors if you import from a flat file. So I is important to make sure everything is correct
```{r}
length(atom$atomid)
# 9064
length(unique(atom$atomid))
# 9064
```
Thus, atomid is definatly a unique key here

Now for the other dataset, the matching column doesn't have to be unique and matching can be repeated on each case.
```{r}
length(sbond3$atomid)
# 12
length(unique(sbond3$atomid))
# 12
```

Let's try merging now.

## 1. Merge()

Merge() function is base function that can do all types of matching.

```{r}
# Inner Join
#  Return all values, match what is found (Full outer join)
merge(x = atom , y=sbond3 , by.x = "atomid" , by.y = "atomid" )

#  Return all values, match what is found (Full Outer join)
merge(x = atom , y=sbond3 , by.x = "atomid" , by.y = "atomid"  , all = T)

#  Return all values of x, skip unmatched in y(Left join)
merge(x = atom , y=sbond3 , by.x = "atomid" , by.y = "atomid"  , all.x  = T)



```

That was neat however merge() function don't have full outer join. Also you will have to excute another where command

## 2. dplyr joins

I prefer using dplyr in merge as it supports easy way direct for full outerjoin, other joins, and can be used on data.frame or data.table which is great!

<br/>
We have 7 functions:
- inner_join()
- left_join()
- right_join()
- full_join()
- semi_join()
- nest_join()
- anti_join()

Let's make a full_join to demonestrate how it works.
```{r}
full_join(x = atom , y = sbond3 ,  by = c("atomid"))

```

If you have 2 different columns names, you can write the vector in by as `c("a"  = "b")`

where a is for the first table and b for the second table.

<br/>
There are more ways to merge like using sqldf for SQL syntax or using data.table for ultimate fast data.table approch.
