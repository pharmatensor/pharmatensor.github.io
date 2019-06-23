---
layout: post
title:  "The Best Way To Use Postgresql with R: Rstudio 1.2 highlights"
permalink: /postgreqlimport
author: omar
categories: [ R, SQL ]
tags: [R, Rstudio , Postgresql, dplyr, tutorial]
image: assets/images/pgsqlimport.png
description: "Rstudio provides a secure and easy way to implement SQ: databases in R"
featured: true
hidden: false
#rating: 4.5
---

When I have started using PostgreSQL with R, I've found a big difficulty in importing the data I want is a productive and secure way. Along with tidyverse packages, Rstudio 1.2 provides a secure and productive way to import and manipulate SQL databases


#### Dependencies for this tutorial
Make sure you have downloaded and installed all of these before proceeding
1. Rstudio 1.2
  - You can check Rstudio version from `help` menu >> `About Rstudio`
2. dplyr package
3. dbplyr package
4. RPostgreSQL package
5. rstudioapi package

##### Step 1. Creat a new rmarkdown file
##### Step 2. Load the necessary R packages
Inside R chunck, write the following code.

````python

```{r}
if (!require("dplyr")) install.packages("dplyr"); library(dplyr)
if (!require("dbplyr")) install.packages("dbplyr") ; library(dbplyr)
if (!require("RPostgreSQL")) install.packages("RPostgreSQL"); library(RPostgreSQL)
if (!require("rstudioapi")) install.packages("rstudioapi"); library(rstudioapi)
```

````

Here we use `dbplyr` package to make a connection with PostgreSQL interface package `RPostgreSQL`. I found this is the best way to connect without problems
`rstudioapi` package is a new package that allows us to enter a password in encoded prompt so the database password is not visible when actually sharing the code.


##### Step 3: Connect To PostgreSQL databases
Make sure you have active connection to the database either actual host or localhost like the example here. In R chunck insert the following code


````python

```{r}
con <- src_postgres(  RPostgreSQL::PostgreSQL(),
                  host="localhost",
                  port = 5420,
                  dbname="demographics",
                  user= askForPassword("Database username"),
                  password= askForPassword("Database password"))

```

`````

Check the opened port and just enter the following parameters in `src_postgres()` function. Note that `dbname` parameter is case sensitive for the name of your database.

##### Step 4: Create a view for the data you want to subset from certain table
This step is not necessary, however I find this practice to be the best in order to avoid writing SQL wrapped in R functions as we will see next. If you will just import a table as it is, skip to the next step. <br/>

In SQL chuck write a query that will create a `view` for the data you want to import in R. In the chunck parameters, make sure to set `connection = con`.

````
```{sql connection=con}
create view "trial1" as (
  SELECT substring("Trade1", '^\S+') || upper(substring("Trade1" ,'^\S+\s*(\D+)')) as "Trade1", substring("Trade2", '^\S+') || upper(substring("Trade2" ,'^\S+\s*(\D+)')) as "Trade2" , "Generic"
  FROM "Table1"
)
```
````

##### Step 5: Import the data into R as dataframe
Finally we will import the data. We will do that using dplyr function `tbl()`. We will wrap it also with `build_sq()` from dbplyr that will allow us to select the data from a SQL query. <br/>

In R chunck import and save the data in new variable `dat` :


````python

```{r}
dat <- as.data.frame(tbl(con, build_sql('SELECT * FROM "trial1"')))
```

````

You can find a full the full snippet <a href="https://github.com/oelashkar/Postgresql-Import/blob/master/snippit.Rmd">here</a>
