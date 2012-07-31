---
comments: true
date: 2011-02-07 19:44:24
layout: post
slug: unit-testing-django-with-a-nosql-backend
title: Unit Testing Django with a NoSQL Backend
wordpress_id: 27
categories:
- django
- NoSQL
- python
- Technology
tags:
- django
- mongodb
- nosql
- python
---

In my previous post about [unit testing for django](http://www.belchak.com/2011/02/05/unit-testing-your-django-application/), I laid the groundwork for how to unit test any django application. One nice feature that django includes with its test framework is the test database syncing. Even better is if you are using [South](http://www.belchak.com/2011/01/27/django-database-migrations-with-south/) to do database migrations - it will run the migrations in  your test environment for you.

However, what if you are using a NoSQL database backend like MongoDB, Cassandra, CouchDB or something similar and you aren't using the Django ORM? How do you handle setting up and tearing down the database environments?

The good news is that Python's unittest framework makes this easy. You can override the `setUp()` and `tearDown()` on each TestCase that you build. Here is a snippet to get you started:



``` python
import pymongo
from django.test import TestCase

# It would be best to define this in a utility class somewhere
def get_db(db_name=None):
    """ GetDB - simple function to wrap getting a database
    connection from the connection pool.
    """
    return pymongo.Connection(
            host=settings.MONGO_HOST,
            port=settings.MONGO_PORT)[getattr(
                settings, "MONGO_DBNAME_PREFIX", "") +
                (db_name or settings.MONGO_DBNAME)]

class MyTestCase(TestCase):
    fixtures = ['test_data.json']
    def setUp(self):
        self.db = get_db('test')
        self.db.create_collection('mytestcollection')

    def test_doc_published(self):
        # Set up a document to save
        doc = dict(text="test",
                      user_id=1)
        self.db.mytestcollection.save(doc)
        self.assertEqual(self.db.mytestcollection.find_one(
            {'user_id':1})['text'], 'test')

    def tearDown(self):
        self.db.drop_collection('mytestcollection')
```




What this does is `setUp()` a collection in the `mytestcollection` collection, runs the `my_doc_published` test and then tears down the test database environment by dropping the `mytestcollection` collection.

Things to remember for `setUp()` and `tearDown()`:




  * `setUp()` is called before every test method in your TestCase class.


  * `tearDown()` is called after every test method in your TestCase class.


  * `tearDown()` is called even if your test methods fail or error out.



And there you have it! Django makes testing even non-ORM datasources a snap, if you know how to wire it up.
**UPDATE:** Some would say that database fixtures and setting up/tearing down database environments as part of your unit tests is not "unit testing". This is not entirely accurate, because in order to do unit tests that rely on backend data, you **must** instantiate and tear down pristine database environments.

