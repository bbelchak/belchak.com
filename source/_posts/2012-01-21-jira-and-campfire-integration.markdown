---
comments: true
date: 2012-01-21 12:25:22
layout: post
slug: jira-and-campfire-integration
title: JIRA and Campfire Integration
wordpress_id: 42
categories:
- JIRA
- Technology
tags:
- campfire
- groovy
- jira
---

I have been working on migrating my company's Pivotal Tracker to JIRA. We love most of the features of JIRA, but we were really missing the nice updates to our Campfire room to tell us what is going on with our issues.

Sure, we could use an RSS feed plugin for [Campy](http://www.belchak.com/2011/06/24/campy-the-python-campfire-bot/), but that is not real-time enough.

I didn't want to write a huge plugin just to send messages to Campfire (it seems like such a simple thing to do!). I was poking around for solutions, and came across [this plugin](https://studio.plugins.atlassian.com/wiki/display/GRV/Script+Runner). It's a plugin that runs scripts written in the [Groovy](http://groovy.codehaus.org/) dynamic language for Java. One nice thing about this plugin is that it lets you execute Python (Jython) or Ruby (Jruby). The only problem is that you can't write a listener using Python, so I ended up having to learn Groovy.

Here's what I came up with. It requires you to install the [HTTPBuilder](http://groovy.codehaus.org/modules/http-builder/doc/index.html) libraries for Groovy, and this script will require some customization to fit your environment. Right now it only supports sending messages when new issues are created and when a new comment is added to an existing issue, but other issue events would be easy enough to add - this is meant as an example.


``` groovy
    import com.atlassian.jira.event.issue.AbstractIssueEventListener
    import com.atlassian.jira.event.issue.IssueEvent
    import com.atlassian.jira.ComponentManager
    import org.apache.log4j.Category
    import groovyx.net.http.RESTClient
    import static groovyx.net.http.ContentType.JSON
    import static com.atlassian.jira.event.type.EventType.*

    class CampfireListener extends AbstractIssueEventListener {

        @Override
        void workflowEvent(IssueEvent event) {
            def Category log = Category.getInstance("com.onresolve.jira.groovy.PostFunction")
            def campfire = new RESTClient('https://CAMPFIREID.campfirenow.com/room/ROOMID/')
            def issueBaseUrl = "http://yourjirahost:8080/path-to-jira/browse/"
            campfire.auth.basic 'CAMPFIRE AUTH TOKEN', 'X'
            switch (event.getEventTypeId()) {
                case ISSUE_COMMENTED_ID:
                    def resp = campfire.post( path: 'speak.json',
                                          body: [ message: [ type: "TextMessage", body:
                                              String.format("%s added a comment to %s (%s%s):",
                                                  event.getUser().getDisplayName(),
                                                  event.issue.getKey(),
                                                  issueBaseUrl,
                                                  event.issue.getKey())] ],
                                          requestContentType: JSON)
                    resp = campfire.post( path: 'speak.json',
                                          body: [ message: [ type: "PasteMessage", body:
                                              String.format("%s", event.getComment().getBody()) ] ],
                                          requestContentType: JSON)
                    break
                case ISSUE_CREATED_ID:
                    def resp = campfire.post( path: 'speak.json',
                            body: [ message: [ type: "TextMessage", body:
                                String.format('%s created a new issue: "%s" (%s%s):',
                                    event.getUser().getDisplayName(),
                                    event.issue.getSummary(),
                                    issueBaseUrl,
                                    event.issue.getKey()) ] ],
                            requestContentType: JSON)
                    resp = campfire.post( path: 'speak.json',
                          body: [ message: [ type: "PasteMessage", body:
                              String.format("%s", event.getIssue().getDescription()) ] ],
                          requestContentType: JSON)
                    break
            }
        }
    }
```
