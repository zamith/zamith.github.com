---
layout: post
title: "Observer pattern"
date: 2013-10-06 20:48
comments: true
categories: [ruby, design patterns, refactoring]
---

I like to think of the observer pattern as [dependency injection](/blog/2013/10/06/dependency-injection/) on steroids, the difference being that with
the latter (taking off from the example on the linked article) you need to call
the `store` method on the database, and therefore you must know there is one.
As with observers you don't care if there is a database or not.

The parser example would be something like:

``` ruby
require 'observer'

class DB
  def initialize(consumer)
    consumer.add_observer(self)
  end

  def update(data)
    self.store(data)
  end

  ...
end

class Consumer
  include Observable
  attr_accessor :parser

  def initialize(parser: XMLParser)
    @parser = parser
  end

  def consume(data)
    parsed_data = parser.parse(data)
    changed
    notify_observers(parsed_data)
  end
end
```

Here the database is observing the consumer, and when it has any new parsed
data, the DB gets notified and acts accordingly. If there is no database, the
consumer goes on with it's business and nothing else happens, but there can be
one, two or more databases, loggers, etc, listening and performing actions when the
data is parsed.

Observer is a library that comes bundled with ruby, but there are others you can
use, such as:

* [wisper-async](https://github.com/krisleech/wisper-async)
* [publisher](https://github.com/atomicobject/publisher)

Rails had it's own observers you could use, they were removed from the core in
version 4.0, but you can still use them as a gem.

* [rails-observers](https://github.com/rails/rails-observers)

The observer pattern is to a certain extent similar to javascript's events and as
them, it can make your code really hard to understand and even debug, but used at
the correct time and place is a very powerful tool. Use it wisely.
