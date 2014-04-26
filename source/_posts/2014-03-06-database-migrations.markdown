---
layout: post
title: "Database Migrations, which is what exactly?"
date: 2014-03-06 15:03:28 -0400
comments: false
categories: "ActiveRecord Database&nbspMigrations Legacy&nbspMigrations"
---
I’m into my third week of Flatiron School’s Ruby Class, and I’ve hit a snag with databases.  No, not the deceptively complex nature and syntax of relational databases, which I’m 70% sure I kind of understand.  What I’m confused about is what exactly is a database migration.  What is being migrated? Schema? Data? Schema and data?

The vague answer is that it depends.  This week we have learned only about database migrations involving the schema.  The schema is the way the layout that data is stored in; it consists of tables, columns, and rows as well as the ways that those tables relate to one another.  If we want to change the schema we use a database migration.  This can happen in development, testing, or production when either a developer isn’t sure what the schema should be or if he or she wants to change the schema later on to add or drop a feature.  This is what we’ve been doing in class so far.

This isn’t to say that a migration, meant to update the schema, can’t also move the data to the new format. It certainly can.  If a migration occurs while there is data in the database the ORM, such as ActiveRecord or Sequel, will update the data within in the database to match the new schema.  Thanks ORMs!

That brings us to the last case, moving just data.  This is known as a legacy migration. It isn’t related so much to a change in schema as much as it is to move the information of database into a new database written in a different language or an updated version of the language it was currently written in.  It sounds similar but is a more drastic change than what we were experimenting in class with.

We’ve been using migrations to update smaller changes in our schema, reversible changes at that. Our changes to the schema were along the lines of adding new features to a website or dropping old ones.  We use an ORM to do this.  ActiveRecord has a way of time stamping these migrations so that we can decide to revert back to changes we’ve previously made kind of like we do when we use Git.

This lead to one more question, why the hell aren’t we using Git!?  It’s complicated sure, but at least I already learned that! Right? Well no…  I’m very wrong for wanting to use Git instead of ActiveRecord.  Git is version control where as AR acts like more of a backup tool.  Where I get confused is that AR’s ability to roll back migrations feels like version control.  The reason it feels this way is that I have so far only used an ORM to change schema for empty databases.  If I never had to add data to my database I could use Git, which would be perfect if I never wanted to use my database!

AR and other ORMs do a much better job at changes to the data than Git can.  Git will get easily confused by changes made to the data within the database and won’t know how to merge changes in the data within those files.  This could happen even if the schema isn’t changed, just the data within it.  Git will then give you merge conflicts that will be practically impossible to solve, and frankly conflicts we never wanted in the first place; we expect our data to change.  That’s an important feature of ORMs like AR, and why we use them to manage the changes to our schema and moving our data into the new schema.  That and ORMs make working with databases a breeze since they allow us to handle data with a language as awesome as Ruby.

[ActiveRecord and Rails]("http://guides.rubyonrails.org/migrations.html")
[Git vs. ActiveRecord]("http://stackoverflow.com/a/5801140/3023931")