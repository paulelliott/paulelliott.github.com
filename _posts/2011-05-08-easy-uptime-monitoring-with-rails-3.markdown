---
layout: posts
title: Easy Uptime Monitoring with Rails 3
---
A number of services are available to monitor your website's uptime. The ones I have seen are all very easy to set up on their end. All you need to do is tell it what URL to hit in your system.

Your first reaction might be to just point to the root url of your app. If you have database resources or other services that you access on the server side along with that page request, then you will be hitting those every time the uptime monitor checks your site. You are also cluttering up your logs with noise. I typically configure it for once per minute, so you are looking at 1440 extra requests per day.

It is really easy to make a lightweight endpoint for services like this in Rails 3 though. You can just setup a custom route to a proc right in your routes file.

    match 'heartbeat', to: proc { [200, {}, ''.chars] }

That will just return a successful status and empty body, which is all those services need. Now you can point your monitoring service to `http://yourdomain.com/heartbeat` and save yourself a little bit of processing!
