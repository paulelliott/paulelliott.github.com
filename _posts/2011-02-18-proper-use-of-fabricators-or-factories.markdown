---
layout: posts
title: Proper Use of Fabricators (or Factories)
---
Writing Fabrication has given me a lot of insight into the world of object generation. It has forced me to reflect a lot on how we are doing it and how we can do it better. The reason I wrote Fabrication to begin with was to try out the idea of lazy generation of associations, meaning that a defined association wouldn't actually be generated unless it was actually referenced. That would allow you to specify the entire object graph in a single set of fabricators but not worry about generating them all every time.
After implementing that and working extensively with the library, I have settled on what I consider to be a few best practices for defining fabricators. Whether you are using Factory Girl, Machinist, Object Daddy, Fabrication, or some other library, these principals all still apply.

In the spirit of the upcoming bike week, let's say we have the following class, representing a motorcycle model.

    class Model < ActiveRecord::Base
      belongs_to :manufacturer
      has_many :motorcycles

      validates :name, presence: true, uniqueness: true
    end

### The primary fabricator should only have the fields necessary to create a valid object.

In many cases, a table has quite a few fields on it. Most of them are not necessary for the object to be valid in the database and so don't need to be generated every time you create an object.

This particular class probably has a whole lot of fields on it specifying the engine displacement, drive type, tire sizes, etc. Our system may not care about any of those fields the majority of the time though. The only thing the model actually validates is the presence of the name.

### The primary fabricator should always create belongs_to associations, but never has_many.

A good object model will typically require objects going up the tree to exist, but never objects going down. Any time there is an id field on your model, as in the case of a belongs to, then that other object is very likely necessary for your object to exist. In the case of our Model, it has_many motorcycles but you don't actually need to make a production run for the model to exist. The manufacturer does need to exist for the model to make sense, so we should be creating that every time.

### Use a sequence when uniqueness is required.

Our Model has a uniqueness validation on name. There are a number of ways to ensure that, but the easiest one is to use a sequence.

### Let's put it all together.

Now that we have the basics down our fabricator should look like this.

    Fabricator(:model) do
      manufacturer!

      name { Fabricate.sequence(:model_name) { |i| "Model #{i}" } }
    end

We skipped all the fields we didn't absolutely need, didn't mention the has_many relationship, forced the manufacturer, and used a sequence for the name. It is lean and clean, but flexible enough for the vast majority of your specs.
