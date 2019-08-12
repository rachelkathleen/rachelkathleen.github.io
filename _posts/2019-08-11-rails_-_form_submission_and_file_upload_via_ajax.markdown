---
layout: post
title:      "Rails - Form Submission and File Upload via AJAX"
date:       2019-08-11 21:59:27 -0400
permalink:  rails_-_form_submission_and_file_upload_via_ajax
---


A quick quide on how to render a form for creating a resource that is submitted dynamically.

This post assumes you've already serialized your resource with AMS, if you haven't gotten that far - read [this](https://rachelkathleen.github.io/rails_-_serializing_active_storage_uploads_with_active_model).

For my app, it was a longer form, so I didn't want to display the newly created resource on the same page - but wanted to either show error messages, or a success message with the name of the new resource, and a link to the full show page.

Below is the first iteration of code to make this work:


```
$(function () {
    $('form').submit(function(event) {
      //prevent form from submitting the default way
      event.preventDefault();
      // Create variable for div to display error messages, or JSON object data
      const formMessages = $('#form-messages');
			// Add class to div depending on if form submission is successful
      $(formMessages).removeClass('alert-success')
      $(formMessages).removeClass('alert-danger')
			// Add unordered list to inner html of div
      $(formMessages).html("<ul></ul>")
			// Serialize form values
      const values = $(this).serialize();
			// Post values
      const posting = $.post('/wines', values);
      posting.done(function(data) {
      const wine = data;
      if (!data.errors) {
			//If no errors, show formMessages div, add 'alert-success' as class
			// add JSON data to inner HTML 
        $(formMessages).addClass('alert-success');
        $(formMessages).show();
        document.querySelector('#form-messages').innerHTML+=`<p>${wine.producer} - ${wine.wine_name} has been added to your cellar - click <a class="alert-link" href="/wines/${wine.id}">here</a> to view. </p>`
        $("#producer").text(wine["producer"]);
        $("#wineName").text(wine["wine_name"]);
				//resets the form
        $('form')[0].reset();
      } else {
			// Show formMessages div, add 'alert-danger' as class and show full error message(s)
        $(formMessages).addClass('alert-danger');
        $(formMessages).show();
        data.errors.forEach(function(error){
          document.querySelector('#form-messages').innerHTML+=`<p>${error}</p>`
        })
      }
      });
    });
  })
```

This worked for all of the form fields - except the file upload.  The solution, after spending a not insignifcant amount of time googling and reading Stack Overflow posts, was to use the [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) object instead of serilaizing the values. Below is the final code, with comments on the changes.

  ```
$(function() {
      $('form').submit(function(event) {
          event.preventDefault();


          const formMessages = $('#form-messages');
          $(formMessages).removeClass('alert-success')
          $(formMessages).removeClass('alert-danger')
          $(formMessages).html("<ul></ul>")
					
					
    // New code - creates FormData object
          var formData = new FormData($(this)[0]);
		//Create variable for the action
          var action = $(this).attr('action');
		//create variable for post or patch, depending on the action
          const type = action === '/wines' ? 'POST' : 'PATCH'

    //jQuery ajax 
          $.ajax({
              url: action,
              type: type,
              data: formData,
              async: true,
              cache: false,
              contentType: false,
              enctype: 'multipart/form-data',
              processData: false,
          }).then(function(data) {
					//same code for handling error messages or displaying link to new resource
              const wine = data;
              if (!data.errors) {
                  $(formMessages).addClass(
                      'alert-success');
                  $(formMessages).show();
                  document.querySelector('#form-messages')
                      .innerHTML +=
                      `<p>${wine.producer} - ${wine.wine_name} has been added to your cellar - click <a class="alert-link" href="/wines/${wine.id}">here</a> to view. </p>`
                  $("#producer").text(wine["producer"]);
                  $("#wineName").text(wine["wine_name"]);
                  $('form')[0].reset();
              } else {
                  $(formMessages).addClass(
                      'alert-danger');
                  $(formMessages).show();

                  data.errors.forEach(function(error) {
                      document.querySelector(
                              '#form-messages')
                          .innerHTML +=
                          `<p>${error}</p>`
                  })
              }
          });
      });
  })
```


