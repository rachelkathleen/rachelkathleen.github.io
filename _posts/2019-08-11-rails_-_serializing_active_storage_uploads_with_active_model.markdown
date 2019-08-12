---
layout: post
title:      "Rails - Serializing Active Storage uploads with Active Model"
date:       2019-08-12 01:19:50 +0000
permalink:  rails_-_serializing_active_storage_uploads_with_active_model
---



The fourth project for the [Flatiron School's ](https://flatironschool.com/)software engineering program was to update our previous rails project with javascript featyres. 

My rails project enables users to upload an image. I used [Active Storage](https://edgeguides.rubyonrails.org/active_storage_overview.html) to handle the file upload, and Google Cloud Storage to store images. I won't detail that process - there's already a recent and well written[ blog](https://medium.com/@pjbelo/setting-up-rails-5-2-active-storage-using-google-cloud-storage-and-heroku-23df91e830f8) on the topic that also outlines how to deploy to Heroku.

Here, I'll tackle how to serialize files uploaded with Active Storage to JSON, and how to upload the files via an AJAX request.

The first piece is relatively straightforward. I used [Active Model](https://guides.rubyonrails.org/active_model_basics.html) to serialize data. `rails g wine serializer` generates a directory for serializers, and creates a serializer for the wine class.

```
class WineSerializer < ActiveModel::Serializer

end
```

From there, I added in the attributes and relationships I wanted to serialize.

```
class WineSerializer < ActiveModel::Serializer

  attributes :id, :producer, :wine_name, :wine_type, :price_range, :vintage,
             :rating, :notes, :favorite
						 
  belongs_to :user
  belongs_to :varietal
  belongs_to :country

end
```

The last step is adding the Rails URL helpers, and creating a method to make the image an attribute.

```
class WineSerializer < ActiveModel::Serializer
  include Rails.application.routes.url_helpers

  attributes :id, :producer, :wine_name, :wine_type, :price_range, :vintage,
             :rating, :notes, :favorite, :picture
						 
  belongs_to :user
  belongs_to :varietal
  belongs_to :country


  def picture
    rails_blob_path(object.picture, disposition: "attachment", only_path: true) if object.picture.attached?
  end
end
```

There are different ways to create the picture method depending on how your app is set up, and what type of file is uploaded. An alternal if that doesn't work is:

```
def picture
  object.picture.service_url if object.picture.attached?
end
```

You can read up on `service_url` and `rails_blob_path` in the [rails docs](https://api.rubyonrails.org/classes/ActiveStorage/Blob.htm) to figure out which is better suited for your use case.


