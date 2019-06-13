---
layout: post
title:      "Google Authentication Strategy for Rails 5 Application"
date:       2019-06-12 22:37:45 -0400
permalink:  google_authentication_strategy_for_rails_5_application
---

When creating my rails final project, [WINELOG](https://wine-log.herokuapp.com/), I found it difficult to implement social login with Google. I also found there was a lack of recent documentation or blog posts on the subject. 

Here's a quick guide on how to set up Google log in.

1. **Create app in Google Developers Console.** Select credentials, then 'create credentials', and select 'Create OAuth client ID', then select 'web application'.
Here you'll be able to see the client ID and client secret - you'll need those soon!
Add in the authorized redirect URIs - http://localhost:3000/auth/google_oauth2/callback works for testing in the rails server, but remember to add the URI for the production well.

2.  **Add necessary gems to your gem file.**  You'll need to add three gems - `gem 'omniauth'`, `gem 'dotenv-rails'` and `gem 'omniauth-google-oauth2'`. Run `bundle` in the terminal.

3. **Create a .env file in the root directory of your app.** Add the following code to the file - and remeber to add this file to your gitignore. 
```
GOOGLE_CLIENT_ID=Your client id
GOOGLE_CLIENT_SECRET=Your client secret
```

4. **Add `omniauth.rb` to `/config/initializers` with this code:** 
```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, ENV["GOOGLE_CLIENT_ID"],ENV["GOOGLE_CLIENT_SECRET"], skip_jwt: true
end
```
5. **Add callback route to `routes.rb`.** 
`get '/auth/:provider/callback' => 'sessions#omniauth'`

6. **Add class method to user model.** You’ll use this method in the sessions controller. This is a good spot to put a `binding.pry` to inspect the auth hash - you can map the data to your User model however you would like; this is a very simple example.  
```
  def self.from_omniauth(auth)
    where(email: auth.info.email).first_or_initialize do |user|
      user.user_name = auth.info.name
      user.email = auth.info.email
      user.password = SecureRandom.hex
    end
  end
```

7. **Add methods to sessions controller.** You’ll need two methods in the sessions controller - one to create (or log in) a user via Google, and a private method to request the auth hash.
```
  def omniauth
    @user = User.from_omniauth(auth)
    @user.save
    session[:user_id] = @user.id
    redirect_to home_path
  end
	.
	.
	.
	private
	
  def auth
    request.env['omniauth.auth']
  end
```

 
8. **Add login/signup links.** You’ll need to add this link anyplace you want a user to be able to log in or sign up. It can be styled as a button or not, and does not need to be inside a form.
`<%= link_to "Log In with Google", '/auth/google_oauth2' %>` 

That should have everything up and running! You can view the full repository for WINELOG [here](https://github.com/rachelkathleen/wine-log). 
