---
comments: true
date: 2011-01-27 17:04:04
layout: post
slug: django-database-migrations-with-south
title: Django database migrations with South
wordpress_id: 19
categories:
- django
- python
- Technology
tags:
- django
- python
- south
- technology
---

I have been using [django](http://djangoproject.com) for web development for almost a year now, and I just recently started using [South](http://south.aeracode.org/) to do database migrations. To be fair, most of the work that I have been doing with databases has centered around [MongoDB](http://www.mongodb.org) and schema-less document stores instead of a traditional RDBMS. Since Django does not come with any database migration tools, my standard approach was to make sure that my models are completely thought out before running the manage.py syncdb command. The lack of a good database migration tool was one of the things that originally had turned me off to django.

Enter South. South lets you manage your database in a way very similar to how [Ruby on Rails](http://rubyonrails.org) works.



Converting a project to a South-managed project is very easy:



	
  1. Ensure that your database and models are completely synced up. (i.e. your models are not ahead of your database or vice-versa)

	
  2. Install South by running `[sudo] pip install south`

	
  3. Add South to your `INSTALLED_APPS` list in the `settings.py` for your django project.

	
  4. Run `./manage.py syncdb` in your project root directory to add the South database tables to your database.

	
  5. If you have an existing application that you would like to conver to a South-managed application, run the following command: `./manage.py convert_to_south YOUR_APP_NAME` If not, go to the next step!

	
  6. Now you are ready to go! You can change one of your models and then proceed to the next step.

	
  7. Run the following command to get South to create an automatic migration for you: `./manage.py schemamigration YOUR_APP_NAME --auto`

	
  8. Now you can apply your newly created migration to your database:`./manage.py migrate YOUR_APP_NAME`

	
  9. Congratulations, you have performed your first database migration using South!


South lets you apply up to or back to any migration point by running a command like: `./manage.py migrate YOUR_APP_NAME 0001` (that command would take you back to your initial migration point. You can get a list of all your migrations and a description about each one by running `./manage.py migrate YOUR_APP_NAME --list`. This lists all of the migrations you have available and denotes with a (*) which ones have been applied.

South is great for working in a team. All migrations are stored in YOUR_APP_NAME/migrations, so you can simply add these to your VCS and all of your team  members will get all of your migrations. If there is a conflict in some of the migrations that you and a team member have been working on, South will detect it and let you [merge the conflicts](http://south.aeracode.org/docs/tutorial/part5.html#team-workflow).

All in all, I am really loving South. It makes working with an RDBMS and Django much more pleasant!
