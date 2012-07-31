---
comments: true
date: 2011-02-05 21:33:40
layout: post
slug: unit-testing-your-django-application
title: Unit Testing Your Django Application
wordpress_id: 24
categories:
- django
- python
- Technology
tags:
- django
- python
- unit testing
---

Unit testing is a very important part of any software project. It helps you know that the new code you are deploying works, and isn't going to blow up in your face. It also helps you feel good about changing large chunks of code without destroying everything you've done for the last 3 years.

Unit testing with django is as simple as pie. [The documentation](http://docs.djangoproject.com/en/dev/topics/testing/) is very good, and you can learn a lot about more advanced testing methods from [the python documentation](http://docs.python.org/library/unittest.html). In this blog post, I aim to show a quick way to get up and running with testing your django application.

First, if you are just starting out, make sure you put a high emphasis on testing your application, otherwise you are going to end up with a bunch of code that has never been tested and you will find yourself writing code for weeks just to get partial coverage on the code you've already written. Starting off on the right foot is a much better approach, and you will find life much more enjoyable.

Let's get started...


First of all, you need to define your models:

``` python
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=50)
    body = models.TextField()
    author = models.ForeignKey(User, related_name="news_post")
    date = models.DateTimeField()
    users_read = models.ManyToManyField(User, related_name="users", blank=True)

    def __str__(self):
        return self.title
```


What we have done here is created a news post item. Let's test it!


``` python
class PostTestCase(TestCase):
    fixtures = ['test_data.json']
    def setUp(self):
        self.user = User.objects.create_user('newsposter',
                                             'newsposter@news.com', 'newspass')
        self.post = Post.objects.create(title="Test Post #1",
                body="Test Post #1 Body",
                author=self.user,
                date=datetime.datetime.now())
        self.c = Client()


    def test_post_creation(self):
        """
        Tests that we can create a Post
        """
        self.assertEqual(self.post.title, "Test Post #1")
        self.assertEqual(self.post.author, self.user)

    def test_user_can_read(self):
        """
        Tests that a user is allowed to read.
        """
        self.c.login(username='newsposter', password='newspass')
        response = self.c.get('/news/get_post/1/')
        self.assertEqual(response.status_code, 200)
        self.assertNotEqual(response.content, '{}')
```




One of the really cool thing about testing with django is that it comes with a testing client that allows you to make requests just like a real user would. As you can see in our `test_user_can_read()` method, we have used the client to make a GET request against a URL. You can make a POST request just as easily:


``` python
def test_i_read_this(self):
    """
    Tests a new user marking the story as read.
    """
    self.c.login(username='newsposter', password='newspass')
    response = self.c.post('/news/read/1/', {'add':True})
    self.assertEqual(response.status_code, 200)
    self.assertEquals(response.content, '{\n    "read": true\n}')
```



In the previous code sample, the client sends a POST request to `/news/read/1/` with the `{'add':True}` data. This gets converted to form data and submitted via the POST. The request returns back JSON, which we match up against what we expect it to return.

Here are some things to remember when you are writing your test cases:




  * `setUp()` gets called before **every** test method in your TestCase.


  * `tearDown()` gets called after **every** test method in your TestCase.


  * Test methods **must** start with "test" otherwise they will not be executed. It is safe to have other methods in your TestCase that do not begin with "test" if you want to abstract functionality for multiple test methods into a single function.


  * Django creates a test database for you, populates it, runs any south migrations (if you are using south), and then destroys it.


  * Do not expect that data that is available in one of your test methods will be available in another. Each test method starts with a blank data slate. If you need data instantiated before your tests are run, consider using the `setUp()` and `tearDown()` methods, or using fixtures. You can specify fixtures other than your initial_data fixtures by adding `fixtures = ['test_data.json']` to your TestCase class.



Here is in-depth documentation about what assert methods are available to you in different versions of Python in [the official Python unittest documentation](http://docs.python.org/library/unittest.html#test-cases).

As you can see, testing with django is really really simple, but very powerful. In my next post, I will discuss how to test django with a MongoDB backend that does not use the ORM.
