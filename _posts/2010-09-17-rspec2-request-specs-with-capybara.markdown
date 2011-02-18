---
layout: posts
title: RSpec 2 Request Specs With Capybara
---
With RSpec 2 has come lots of great new features. One of the most important of which is the request example group that now ships with rspec-rails. It provides a simple built-in framework for writing integration specs in pure RSpec. As of beta 20, it now gives you the choice of using either webrat or Capybara and and setting it up is really easy.

First, include `capybara` and `rspec-rails` in your `Gemfile`. Since `rspec-rails` depends on rspec, you don't need to explicitly require that as well.

`Gemfile`

    group [:development, :test] do
      gem "capybara", "0.3.9"
      gem "rspec-rails", "2.0.0.beta.22"
    end

After running a `bundle install`, you need to install the rspec support files.

    rails generate rspec:install

Now, the reason we have to include rspec-rails in both the development and test groups is because the rake tasks are no longer under `lib/tasks` in your app. They are included in the gem itself, which Bundler needs to require in dev for them to be accessible. Alternatively, you could specify the RAILS_ENV every time you run the command, but who wants to do that?

Getting running in your new request spec platform is really easy. All you need to do is make the folder for the specs:

    spec/requests

As far as the setup goes, you are all done. Now you can write integration specs against your full stack.
