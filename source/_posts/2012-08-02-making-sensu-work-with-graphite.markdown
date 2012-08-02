---
layout: post
title: "Making Sensu work with Graphite"
date: 2012-08-02 10:48
comments: true
categories:
- sensu
- graphite
---

Like many companies, I'm sure, we're investigating using [Sensu](http://www.sonian.com/cloud-monitoring-sensu/) for monitoring
and [Graphite](http://graphite.wikidot.com/) for metrics collecting. The docs for making these two commuicate together over AMQP
are pretty good except for one thing.

Using the docs that I've read, when you start up Sensu first with its default options for AMQP, and then start up Graphite
to read from that queue, Graphite will crash every time it tries to create the exchange. The reason for this, after lots of
hair-pulling and debugging, is because Graphite is hard-coded to set up a durable exchange for its metrics, while Sensu's default
is to set up a non-durable exchange. [RabbitMQ](http://rabbitmq.com/) will not allow you to create an exchange with the same name
and different options, so whichever piece of software created the exchange will work, but the other will blow up all over your
face.

The solution to this is pretty simple, but I haven't seen it documented anywhere. Simply add `"durable": true` to your exchange
configuration in your Sensu config.json like so:

``` json
"handlers": {
    "graphite": {
      "exchange": {
        "name": "graphite",
        "type": "topic",
        "key": "metrics",
        "durable": true
      },
      "type": "amqp",
      "send_only_check_output": true
    },
}
```

Hope that helps!
