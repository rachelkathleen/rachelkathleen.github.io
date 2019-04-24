---
layout: post
title:      "Rails - Nested Forms in Three Steps"
date:       2019-04-24 00:43:00 -0400
permalink:  rails_-_nested_forms_in_three_steps
---


Rails is rife with magic. Form helpers, in particular the ability to build forms directly linked to Ruby objects, are one piece of Rails magic that I had a hard time wrapping my head around. 

This is a quick guide to setting up nested forms in rails. 
 
 First - what is a nested form? It is, as it's name suggests, a form within a form. More specifically, the nested form is creating or manipulating a model that different from the model the parent form is for.
 
 Let's pretend we have two models - owners, and dogs. An owner can have many dogs, and a dog belongs to one owner.  
 
 
```
class Owner < ActiveRecord::Base
 has_many :dogs
end
```

and

```
class Dog < ActiveRecord::Base
 belongs_to :owner
end
```

The form to create a new dog would look something like this:

```
<h1>Create a new Dog:</h1>

<%= form_for(@dog) do |f|%>

  <div>
    <%= f.label :name%>
    <%= f.text_field :name%>
  </div><br>
	
  <div>
    <%= f.label :breed%>
    <%= f.text_field :breed%>
  </div><br>
	
  <div>
    <%= f.label :age%>
    <%= f.text_field :age%>
  </div><br>
	
  <div>
    <label for="dog_owner_id">Select an owner:</label>
    <%= f.collection_select :owner_id, owner.all, :id, :name, prompt: true%>
  </div><br>
	
  <%= f.submit %>
	
<% end %>
```

The above form gives a the user the ability to select and owner from owners already saved to the database - but what if they need to create a new owner? It would be a terrible user experience to have the user realize this, then go to a different page to create an owner, then back to this page to create the dog. Nested forms to the rescue!

**Step 1 - Model**

Add the `accepts_nested_attributes_for` method to the model that our main form is for, in our example, Dogs. This is a class method that defines an attribute writer for the specified association (owners). You can read up on it [here](https://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html). 

Our Owner class now looks like this:

```
class Dog < ActiveRecord::Base
 belongs_to :owner
 accepts_nested_attributes_for :owner
end
```

**Step 2 - View**

Next, we need to update our view by adding the nested form. To do this, we'll use[ `fields_for`](https://api.rubyonrails.org/v5.2.3/classes/ActionView/Helpers/FormHelper.html#method-i-fields_for), which, like `form_for` yields a FormBuilder object.

```
<div>
  <h3>Or create a new owner:</h3>
  <%= f.fields_for :owner, Owner.new do |owner_attributes|%>
      <%= owner_attributes.label :name, "Owner Name:" %>
      <%= owner_attributes.text_field :name %>
  <% end %>
</div>
```

Our updated form looks like this:

```
<h1>Create a new Dog:</h1>

<%= form_for(@dog) do |f|%>

  <div>
    <%= f.label :name%>
    <%= f.text_field :name%>
  </div><br>
	
  <div>
    <%= f.label :breed%>
    <%= f.text_field :breed%>
  </div><br>
	
  <div>
    <%= f.label :age%>
    <%= f.text_field :age%>
  </div><br>
	
  <div>
    <label for="dog_owner_id">Select an owner:</label>
    <%= f.collection_select :owner_id, owner.all, :id, :name, prompt: true%>
  </div><br>

  <div>
    <h3>Or create a new owner:</h3>
    <%= f.fields_for :owner, Owner.new do |owner_attributes|%>
        <%= owner_attributes.label :name, "Owner Name:" %>
        <%= owner_attributes.text_field :name %>
    <% end %>
  </div>
	
  <%= f.submit %>
	
<% end %>
```

**Step 3a - Controller**

When a user submits the original form, the controller action is to create a new instance of a dog. With the nested form, we also need to create a new instance of an owner, and associate that owner with the dog. This is accomplished with the `build` method. 

```
class DogsController < ApplicationController

  def new
   @dog = Dog.new
   @dog.build_owner 
  end

 end
 ```
 
**Step 3b - Controller**
 
 We'll also need to update our strong parameters in the controller.  Using `accepts_nested_attributes_for` in the Owner model added a new attribute to the class.
 
 ```
class DogsController < ApplicationController

  def  dog_params
    params.require(:dog.permit(:name, :breed, :age owner_attributes: [:name])
  end
	
end
```



