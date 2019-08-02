---
layout: post
title: Predicting the Era in Which Cocktails Were Introduced from Their Recipes
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [test]
comments: true
---

The Mr. Boston distillery has [digitized](https://mrbostondrinks.com/discover) eleven editions of its Official Bartender's Guide published between 1935 and 2012.  Each edition has its own webpage on which each cocktail recipe featured in that edition is listed; here, for instance, is the webpage for the [1935 edition](https://mrbostondrinks.com/books/old-mr-boston-deluxe-official-bartenders-guide-1935):

![alt text](/img/Mr_Boston_1935_Edition.png "Mr. Boston 1935 Edition")

 Further, each cocktail recipe in each edition has its own webpage, so there are distinct webpages for the (1935 Abbey Cocktail recipe)[https://mrbostondrinks.com/recipes/abbey-cocktail-1935] and for the (2012 Abbey Cocktail recipe)[https://mrbostondrinks.com/recipes/abbey-cocktail].

![alt text](/img/Abbey_Cocktail_1935_Edition.png "Abbey Cocktail 1935 Edition")

In a future blog post I will explain how to scrape this cocktail recipe data using [Selenium](https://pypi.org/project/selenium/). The Jupyter Notebook I used to scrape each cocktail recipe from each edition of Mr. Boston's Official Bartender's Guide is available [here](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Web-Scraping%20Mr.%20Boston.ipynb), and the Jupyter Notebook I used to create pandas dataframes from the scraped JSON data is available [here](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Creating%20Cocktail%20DataFrames%20from%20JSON.ipynb).

Using this data, we can create a model that predicts the era in which cocktails were introduced from their recipes.

## Cleaning Data and Engineering Features

We can use the year in which a cocktail first appeared in Mr. Boston's Official Bartender's Guide to group cocktails by the era in which they were introduced.  Cocktails that have appeared in multiple editions of Mr. Boston's Official Bartender's Guide are assigned a 'master_recipe_id' on the Mr. Boston site; while the 2012 recipe for the Abbey Cocktail is distinct from the 1935 recipe for the Abbey Cocktail, the two recipes share a 'master_recipe_id'.  By finding the edition in which a particular 'master_recipe_id' first appeared, we can determine the year in which a cocktail first appeared in Mr. Boston's Official Bartender's Guide.

While the 'master_recipe_id' is an extremely useful feature, the data on the Mr. Boston website is imperfect, and some cocktails that should share a master_recipe_id do not.  [This Jupyter Notebook](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Cleaning%20Mr.%20Boston%20Cocktail%20Data.ipynb) contains my attempt to fix the 'master_recipe_id' for cocktails that should be grouped together but are not.  Cleaning this data is a work-in-progress; at the time of writing this post I have not yet fixed all the 'master-recipe_id's that can be fixed.

On the site, each cocktail recipe is stored...

