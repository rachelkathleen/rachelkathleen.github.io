---
layout: post
title:      "Sinatra Portfolio Project Roundup"
date:       2019-04-05 00:08:37 +0000
permalink:  sinatra_portfolio_project_roundup
---


One of my more sentimental possessions is an old, knobby hiking stick covered with medallions from parks I’ve visited and trails I’ve hiked. 

For my Sinatra project, I wanted to create an application that could modernize the idea behind the hiking stick. I wanted to create an app where I (and other users) could keep track of National Parks we’d been to, what parks are on our personal bucket list’s, and be able to write and share notes about our experiences.

Anytime I’m about to start on something complex - writing a term paper, planning a multi-leg trip for a group of friend - or building an application, I start by thinking about what the goals are, work backwards from there. 

The task at hand was to build a CRUD (create, read, update, destroy) application using the MVC (model, view, controller) framework. Below is an ‘outline’ of sorts breaking down the different elements in the program, in order, with the corresponding languages. I also had a README file with a dynamic to-do list ebbing and waning as the project unfolded.


> (DB   <------>   ORM   <------>  MODEL)   <----->  CONTROLLER   <----------->   VIEW   <----------- User
> 
> (SQL)<--->(SQL/RUBY)<--->(RUBY) <------> (RUBY) <------->(ERB/HTML/CSS)<-------->(HTML, CSS, JS)


**Model**

The concept has three models (users, parks, notes) and needs another model to represent the join table between users and parks. The first step was creating the database - which meant really understanding the relationships between the models. 

```
class Note < ActiveRecord::Base
  belongs_to :user
  belongs_to :park
end
```

```
class Park < ActiveRecord::Base
  has_many :notes
  has_many :user_parks
  has_many :users, through: :user_parks
end```

```
class User < ActiveRecord::Base
   has_many :notes
  has_many :user_parks
  has_many :parks, through: :user_parks
end```

```
class UserPark < ActiveRecord::Base
  belongs_to :user
  belongs_to :park
end```

In hindsight, that looks like a fairly straightforward and ‘easy’ bit of coding - but setting up the right relationships is the backbone of the ‘model’ part of the MVC application, and there’s several overlapping relationships. 

The next step was creating migrations and seeding the database. I scraped the National Park Service website with Nokogiri to get a list of the 61 national parks, alone with websites, an image, description and headline to use for my application. 

I made two errors in the first run - one was naming the column “password” in my users table. I used bcrypt for password encryption, which requires the column name be “password_digest”. 

Second, when I was thinking about the relationships, I had UserParks as the model that bridged users and parks, and had initially set that up as the only join table. However - notes belonged to users and parks, and needed foreign keys for each as well.

**Controller**

I set up four controllers (on top of my main application controller) to maintain separation of concerns. I started by stubbing out the seven RESTful routes for each model, adding erb files for each view as I went.  Then deleted routes that wouldn’t be used - i.e. parks wouldn’t be added, edited or deleted. 

Building out the controllers was the core of the project - and having a clear map of the relationships made it much easier to tackle multiple controllers.

**View**

I started building views in tandem with controllers - I needed forms to test the routes for ‘new’, ‘edit’ and ‘delete’. Using HTML & CSS to build and style the pages in a way that way functional, aesthetically pleasing and user friendly was almost a second project unto itself. I used [Materialize](https://materializecss.com/) and [Skeleton ](http://getskeleton.com/) so I wouldn’t have to build everything from scratch.  Looking back, one thing I would do differently is take more time to break down the different parts, and plan better - like I had done for the overall application, specifically for the ‘view’ component.

View a short demo [here](https://drive.google.com/open?id=1AFNIKso2AJRAZatkG4qXT43FCqvKTZP6) - or use the app for yourself [here](parkfinder-sinatra-project.herokuapp.com).
