---
layout: posts
title: Integration Testing Setup with RSpec 2 and Capybara
---
Be advised, this is an update to a previous post that contains information that is out of date for the final release of rspec 2 and beyond. If you want to read that post for some reason, you can find it [here](http://codingfrontier.posterous.com/integration-testing-setup-with-rspec-2-and-ca).

RSpec 2 has introduced a ton of great new features to make our spec suite better. Writing integration specs in RSpec is easier now, too. They are called request specs and they are all nice and built in to the framework as a first class citizen.

If you already have RSpec integration specs in your project and are upgrading from RSpec 1, then you will want to start by removing any requires or includes from your spec_helper.rb or support/capybara.rb files. Those inclusions are no longer necessary. You then need to move your `spec/integration` folder to `spec/requests`

Next up, put the necessary requires in your Gemfile if you haven't already.

    group :test do
      gem "capybara"
      gem "rspec-rails"
    end

Run a quick `bundle` and you should be good to go from a gem perspective. 

If you are upgrading rspec, be sure to run `rails generate rspec:install` to generate the basic rspec setup for the new version. Note that the spec_helper.rb may have changed. I always advise against modifying generated files. Any customizations should go in separate files in your `spec/support` folder.

Now on to Capybara! By default it uses the rack-test driver, which is similar to webrat in that it is headless and does not run javascript. Unless you have a very javascript intensive app, the rack-test driver should handle the majority of your test cases. The really cool thing about Capybara is that you can use another driver like selenium when you need javascript and rack-test when you don’t. 

Now let’s test a simple sign in process. Create a file called spec/integration/guest_signs_in_spec.rb and fill it in like so. Be sure to change the field, button, and link labels to match your actual screens. 

    require 'spec_helper'

    context 'as a guest on the sign in page' do

      #Make sure your fabricator generates a valid user for your authentication system
      let(:user) { Fabricate(:user) }

      #Browse to the homepage and click the Sign In link
      before do
        visit root_path
        click 'Sign In'
      end

      context 'with valid credentials' do

        #Fill in the form with the user’s credentials and submit it.
        before do
          fill_in 'Email', :with => user.email
          fill_in 'Password', :with => 'password'
          click 'Submit'
        end

        it 'has a sign out link' do
          page.should have_xpath('//a', :text => 'Sign Out')
        end

        it 'knows who I am' do
          page.should have_content("Welcome, #{user.email}!")
        end

      end

      context 'with invalid credentials' do

        #No form entry should produce an error
        before do
          click 'Submit'
        end

        it 'has errors' do
          page.should have_xpath("//div[@id='errorExplanation']")
        end

      end

    end

That is a trivial example and you would typically want to make more numerous and comprehensive asserts than that, but it should get you going with your first test. 

Now that you have a modern rspec integration test setup, I expect to see the quality of your app improve dramatically! Happy TDD’ing!
