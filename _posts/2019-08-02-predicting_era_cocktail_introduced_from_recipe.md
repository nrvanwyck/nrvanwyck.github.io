---
layout: post
title: Modeling with Mr. Boston
subtitle: Guessing Cocktail Eras from Recipes
comments: false
---

The Mr. Boston distillery has [digitized](https://mrbostondrinks.com/discover) eleven editions of its Official Bartender's Guide published between 1935 and 2012. Each edition has its own webpage on which each cocktail recipe featured in that edition is listed; here, for instance, is the webpage for the [1935 edition](https://mrbostondrinks.com/books/old-mr-boston-deluxe-official-bartenders-guide-1935):

![alt text](/img/Mr_Boston_1935_Edition.png "Mr. Boston 1935 Edition")

 Further, each cocktail recipe in each edition has its own webpage, so there are distinct webpages for the [1935 Abbey Cocktail recipe](https://mrbostondrinks.com/recipes/abbey-cocktail-1935) and for the [2012 Abbey Cocktail recipe](https://mrbostondrinks.com/recipes/abbey-cocktail).

![alt text](/img/Abbey_Cocktail_1935_Edition.png "Abbey Cocktail 1935 Edition")

In a future blog post I will explain how to scrape this cocktail recipe data using [Selenium](https://pypi.org/project/selenium/). The Jupyter Notebook I used to scrape each cocktail recipe from each edition of Mr. Boston's Official Bartender's Guide is available [here](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Web-Scraping%20Mr.%20Boston.ipynb), and the Jupyter Notebook I used to create pandas dataframes from the scraped JSON data is available [here](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Creating%20Cocktail%20DataFrames%20from%20JSON.ipynb).

Using this data, we can create a model that predicts the era in which cocktails were introduced from their recipes.

## Data Cleaning and Feature Engineering

Cocktails that have appeared in multiple editions of Mr. Boston's Official Bartender's Guide are assigned a 'master_recipe_id' on the Mr. Boston site; while the 2012 recipe for the Abbey Cocktail is distinct from the 1935 recipe for the Abbey Cocktail, the two recipes share a 'master_recipe_id'. By finding the edition in which a particular 'master_recipe_id' first appeared, we can determine the year in which a cocktail first appeared in Mr. Boston's Official Bartender's Guide. We can use that year to group cocktails into four different eras: cocktails introduced before 1957, cocktails introduced between 1957 and 1974, cocktails introduced between 1974 and 2000, and cocktails introduced after 2000.

While the 'master_recipe_id' is an extremely useful feature, the data on the Mr. Boston website is imperfect, and some cocktails that should share a master_recipe_id do not. [This Jupyter Notebook](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Cleaning%20Mr.%20Boston%20Cocktail%20Data.ipynb) contains my attempt to fix the 'master_recipe_id' for cocktails that should be grouped together but are not. Cleaning this data is a work-in-progress; I am sure that I have not caught all of the 'master_recipe_id's that can be fixed.

On the Mr. Boston website, each possible cocktail ingredient is assigned an 'ingredient_id.' For each ingredient used in each recipe, the site gives the 'ingredient_id', a 'recipe_ingredient_type', a 'measurement_id', and an 'amount'. The site makes it easy to find the name of the ingredient associated with each 'ingredient_id', though it does not make it easy to find the unit of measurement associated with each 'measurement_id.' Since each possible ingredient is usually (though not always) given in the same units of measurement across all recipes, a great deal of comparitive information can be gleaned from the numeric 'amount' independently of the 'measurement_id'.

At the beginning of [this Jupyter Notebook](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Predictive%20Model%20for%20Mr.%20Boston%20Cocktail%20Dataset.ipynb), I use the 'ingredient_id' and 'amount' features from the Mr. Boston website to engineer new ingredient features. The resulting dataframe has a column for each possible ingredient, with the name of the ingredient as the column header and the amount used as the column value.

When I came across places where the site assigns two different ids to the same ingredient (e.g., assinging one id to 'fresh lime juice' and another to 'juice from lime'), I consolidated the ids and engineered a single ingredient feature for them. With more than 1500 ingredient_ids on the website, there are doubtless more ingredient_ids that can be consolidated.

## Predictive Model

In the second half of [this Jupyter Notebook](https://github.com/nrvanwyck/DS-Unit-2-Sprint-4-Predictive-Modeling-Project/blob/master/Predictive%20Model%20for%20Mr.%20Boston%20Cocktail%20Dataset.ipynb), I used XGBClassifier with recursive feature elimination and early stopping to create a model that predicts the era in which cocktails were introduced from the ingredients used in their recipes. The model has a 42.7% accuracy, which beats the 27.6% accuracy of a simple majority classifier. Precision is highest for cocktails introduced after 1975; precision is particularly low for cocktails introduced between 1957 and 1974:

![alt text](/img/Cocktail_Confusion_Matrix.png "Cocktail Confusion Matrix")

Only about fifty ingredients were predictive, with vanilla ice cream, elderflower liqueur, whiskey, and vodka being the most predictive:

![alt text](/img/Cocktail_Feature_Importances.png "Cocktail Feature Importances")

Looking more closely at those features in the dataset, it seems that vanilla ice cream played a much bigger role in cocktails introduced between 1975 and 2000 than in cocktails introduced at other times. Elderflower liqueur seems to have been absent from cocktails introduced before 2000. Whiskey has been playing a less prominent part in cocktails with each successive era. While vodka was mostly absent from cocktails before 1957, it became much more prominent in cocktails introduced between 1957 and 2000, though it seems to have become less prominent since 2000:

![alt text](/img/Average_Amount_of_Ingredient_Used_Per_Cocktail_by_Era.png "Average Amount of Ingredient Used per Cocktail by Era")

There's a lot more cleaning that can be done with this dataset, but it does seem that cocktail recipes are at least somewhat predictive of the year in which cocktails were introduced.
