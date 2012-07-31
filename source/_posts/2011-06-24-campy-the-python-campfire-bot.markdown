---
comments: true
date: 2011-06-24 11:06:13
layout: post
slug: campy-the-python-campfire-bot
title: 'Campy: the Python Campfire Bot'
wordpress_id: 34
categories:
- python
---

We use a [Campfire](http://www.campfirenow.com) at [Needle](http://www.needle.com) for our day-to-day communication between our tech team members and other business members. We really like Campfire's ability to store files, conference call and keep transcripts even when you are not logged in. We get [GitHub](http://github.com) commit messages, [Pivotal Tracker](http://pivotaltracker.com) story updates and more scattered in our conversations.

These one-way updates are very nice, but I thought it would be nice to be able to have a bot that sat in the channel and could take commands from anybody and do more complex tasks for us without us ever having to leave Campfire. To solve this problem, I wrote [Campy](http://www.github.com/bbelchak/campy), a pure Python Campfire bot with an extensible plugin system.

The first plugin I wrote as a proof of concept is a Google image search plugin. You tell the bot some search parameters that you would like it to search for, and you get back an image inline in your chat transcript. Don't worry, safe search is on by default so you don't get any NSFW images back.

The second plugin I wrote was the all-important Pivotal Tracker plugin. This plugin lets you do the following:



	
  * pt story create "Title" "Description" bug|feature|chore|release -- Create a story

	
  * pt getmine started|finished|delivered|accepted|rejected|unstarted|unscheduled -- Get a list of all stories that belong to you

	
  * pt start #story_id -- Start a particular story.

	
  * pt start|tell next bug|feature|chore [mine] -- Start or tell the next story of a given type. Optionally supply 'mine' at the end of the message to only work on your own stories.


The plugins system is very extensible, so you can create a plugin to do pretty much anything!

You can get Campy here:  [https://github.com/bbelchak/campy](https://github.com/bbelchak/campy)
