---
layout: post
title:      "Rails Project Roundup - Scope Methods & Refactoring"
date:       2019-06-05 03:44:41 +0000
permalink:  rails_project_roundup_-_scope_methods_and_refactoring
---


[WINELOG](http://wine-log.herokuapp.com)

Building scope methods was one of the more challenging (and more rewarding) aspects of building my rails project. 

The first one was straightforward - in the form to create a new wine, users could mark a wine as 'favorite'. The goal was the query the database using Active Record query interface and return an array of favorite wines. The `where` method was the best fit, since I wanted to return multiple objects.

  `scope :is_favorite, -> {where(favorite: true)}`
	
I also wanted to be able to return wines that were highly rated - even if the user hadn't flagged that wine as a favorite. `where` worked perfectly here as well.

  `scope :highly_rated, -> {where("rating > ?", 7)}`
	
The next set of methods were more complex - I wanted to sort wines by type and average rating, to give insight on what types of wine a user preferred, on average.

Building an `average rating` method is straightforward:

```
  def self.average_rating
    average(:rating)
  end
```

However, querying by wine type was not. Wine types (red, white, rose) are finite - so I opted not to create a model for it. 

Still, I wanted to be able to sort wines by type to provide insights to the user - so I created a set of scope methods so I could query based on wine type:

```
  scope :red, -> {where(wine_type: "Red")}
  scope :white, -> {where(wine_type: "White")}	  
  scope :rose, -> {where(wine_type: "Rose")}	
  scope :sweet, -> {where(wine_type: "Sweet")}	  
  scope :sparkling, -> {where(wine_type: "Spakling")}	 
  scope :other, -> {where(wine_type: "other")}	  
```

Now, I could call `.red` and return all red wines!

The first version of the `top_rated` class method was:

```
  def self.top_rated
    averages = {"Red" => Wine.red.average_rating.to_f,
                "White" => Wine.white.average_rating.to_f,
                "Rose"=> Wine.rose.average_rating.to_f,
                "Sparkling"=> Wine.sparkling.average_rating.to_f,
                "sweet"=> Wine.sweet.average_rating.to_f}
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
  end
```

It worked - but it was long, and, because I was writing more than one scope method based on wine type, repetitious. 

I made the code more DRY in two steps. First, I eliminated the hash in favor of iterating through the values in the `:wine_type` of the wines table using `pluck`.

```
def self.top_rated
    averages = {}
    types = Wine.pluck(:wine_type).uniq
    types.each do |type|
      averages[type] = Wine.where(wine_type: type).average_rating.to_f
    end
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
end
```

This is cleaner, but still imperfect. Why query the database for an attribute that isn't even an object, and I know the possible options for?
 
Creating a class contstant eliminated a line of code, and a query to the database.

`WINE_TYPES = ["Red", "White", "Rose", "Sparkling", "Sweet", "Other"]`

The final method is below:

```
  def self.top_rated
    averages = {}
    WINE_TYPES.each do |type|
      averages[type] = Wine.where(wine_type: type).average_rating.to_f
    end
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
  end
```

I was also able to declutter the wines form with the class constant:

```
<%= f.label :wine_type, "Type:" %>
<%= f.select :wine_type, options_for_select(Wine::WINE_TYPES, selected: @wine.wine_type) %>
```

Instead of:

```
<%= f.label :wine_type, "Type:" %>
<%= f.select :wine_type, options_for_select(["Red", "White", "Rose", "Sparking", "Sweet", "Other"], selected: @wine.wine_type) %>
```

Refactoring was a multistep process that made my code easier to understand, less complex, and made it easier for me to add on additional methods.

