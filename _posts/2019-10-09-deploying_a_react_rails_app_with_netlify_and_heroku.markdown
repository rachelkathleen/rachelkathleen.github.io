---
layout: post
title:      "Deploying a React/Rails app with Netlify and Heroku"
date:       2019-10-09 23:01:02 +0000
permalink:  deploying_a_react_rails_app_with_netlify_and_heroku
---


Before I dive in, let me give you a bit of background on how [Pet Roulette!](https://pet-roulette.com/) was set up going into deployment. First, I had two separate repositories. Secondly, I had created my Rails API with a Postgres database from the get-go, which is highly advisable if you do plan to deploy your app. Lastly, I used `create-react-app` to start my React application.

A few notes on why I chose to use Netlify over Heroku.  One - deploying a dual repo to Heroku was *significantly* more complicated. Two, Heroku is a great free hosting solution for a Rails app, but it does go to 'sleep' after 30 minutes of inactivity, and takes a full 30 seconds to reboot. A thirty second wait to load a web page can feel like decades. 

Netlify can only be used for static sites, and has no 'sleep' or downtime. Sites are loaded immediately - which is a much better user experience. If you are fetching data from your Rails API to display in your app, it's good to have the fetch request in the `componentDidMount` lifecycle method, and place it in `App.js`. This way, the API will be woken from the idle state as soon as the page is visited. You can also utilize the `loading` state to display a grey box or some sort of message for the brief delay, while the rest of your SPA is already rendered.

**Part I - Deploy React App to Netlify**
	
* Create a Netlify account, and select 'New site from'. Follow prompts to link your GitHub account and select the repo you wish to deploy. You'll have a chance to select which branch, specify the build command `npm run build` if you are using npm, and the publish directory, default is 'build'. 
* I also recommend installing the [Netlify CLI](https://www.netlify.com/docs/cli/). With the CLI, you can create new builds, `$ npm run build`,  then deploy and see a preview, '`$ netlify deploy`, before deploying to production, `$ netlify deploy --prod`.
* Netlify also makes it very easy to forward to a custom domain - it will prompt you for your domain, ask you to verify that you are the owner, then give you four nameservers to share with your domain provider, and that's it! 

If you are using React Router  for client side routing in an SPA - there are a few additional steps to prevent errors in your production app, since Netlify will show a 'Page Not Found' error if a user tries to load the page from anything but the root. To fix this:
* In the public folder or your React app, create a new file called `_redirects`, and add this code:
```
/*    /index.html   200
```


**Part I - Deploy Rails API to Heroku**

* Add `gem 'rails_12factor'` and `gem 'foreman'` to your gemfile and run `bundle`.
* Create a Procfile in the root directory of your application and include the following code:
``` 
web: bundle exec rails s
release: bundle exec bin/rake db:migrate```
* Update CORS settings to whitelist your Netlify URL. If you haven't already, add `gem 'rack-cors'` to your gemfile and run `bundle`. If you were already using CORS during development, you'll just need to add the netlify url and/or your custom domain to `cors.rb`. If you weren't, in `cors.rb`, uncomment out or add the following:
```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'YOUR-NETLIFY_URL', 'YOUR-CUSTOM-DOMAIN'

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```
This will allow your React front end to post and fetch data to your API.
* Commit all changes, and push to GitHub.
* Next, create a Heroku account, and to install the [Heroku toolbelt and CLI](https://devcenter.heroku.com/articles/heroku-cli). 
* In the command line, enter `$ heroku create YOUR-APP-NAME`. If you've never used the Heroku CLI before, it will prompt you to log in with your username and password.
* If you are using your master branch, enter `$ git push heroku`, if you are using any other branch, `$ git push heroku BRANCH-NAME`.

At that point, you should be good to go - and any updates can be pushed by repeating the last step. However - in my most recent two deployments, I've gotten the following error:

```remote:        Removing bundler (2.0.1)
remote:        Bundle completed (47.21s)
remote:        Cleaning up the bundler cache.
remote: -----> Installing node-v10.14.1-linux-x64
remote:        Detected manifest file, assuming assets were compiled locally
remote: -----> Detecting rails configuration
remote: -----> Detecting rake tasks
remote: 
remote:  !
remote:  !     Could not detect rake tasks
remote:  !     ensure you can run `$ bundle exec rake -P` against your app
remote:  !     and using the production group of your Gemfile.
remote:  !     Activating bundler (2.0.1) failed:
remote:  !     Could not find 'bundler' (2.0.1) required by your /tmp/build_94d6a4f5d4fbb862672998d5d06d2506/Gemfile.lock.
remote:  !     To update to the latest version installed on your system, run `bundle update --bundler`.
remote:  !     To install the missing version, run `gem install bundler:2.0.1`
remote:  !     Checked in 'GEM_PATH=/tmp/build_94d6a4f5d4fbb862672998d5d06d2506/vendor/bundle/ruby/2.7.0', execute `gem env` for more information
remote:  !     
remote:  !     To install the version of bundler this project requires, run `gem install bundler -v '2.0.1'`
remote:  !
remote: /app/tmp/buildpacks/b7af5642714be4eddaa5f35e2b4c36176b839b4abcd9bfe57ee71c358d71152b4fd2cf925c5b6e6816adee359c4f0f966b663a7f8649b0729509d510091abc07/lib/language_pack/helpers/rake_runner.rb:106:in `load_rake_tasks!': Could not detect rake tasks (LanguagePack::Helpers::RakeRunner::CannotLoadRakefileError)```

In reponse, I ran `$ bundle exec rake -P`, and it was fine. I updated bundler, removed bundler and then reinstalled with the specified version. I googled the error and read dozens of open tickets and Stackoverflow posts. In the end, what resolved the issue for me was removing the last two lines from `Gemfile.lock'.

```
BUNDLED WITH
   2.0.1
   ```

There is extensive documentation for hosting rails apps in the Heroku Dev center should you run into other errors!



	 



