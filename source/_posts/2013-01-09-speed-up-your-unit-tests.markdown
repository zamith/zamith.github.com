---
layout: post
title: "Speed up your unit tests"
date: 2013-01-09 00:19
comments: true
categories: [rails, testing]
---

Doing TDD is hard at first, but in the long run it surely pays off and with time (and practice) it gets easier to think test first. One vital part of developing this way is that your red-green-refactor cycle is as fast as possible, and you most definitely don't want to waste a whole bunch of time looking at your computer waiting for the test suite to run.

You can speed up running the whole suite with gems as [guard](https://github.com/guard/guard) and [spork](https://github.com/sporkrb/spork), and you can even limit the scope of the suite to the tests you are running with [rspec filters](http://blog.davidchelimsky.net/2010/06/14/filtering-examples-in-rspec-2/). I'll even talk about these in a later post, but no the case of model testing, or unit testing, you should only need to load ActiveRecord, so why are you loading a full fledged rails app?

### Active Record Spec Helper

The solution to this problem is having a separate spec helper which will only load ActiveRecord:

``` ruby Active Record Spec Helper
require 'active_record'

connection_info = YAML.load_file("config/database.yml")["test"]
ActiveRecord::Base.establish_connection(connection_info)

RSpec.configure do |config|
  config.around do |example|
    ActiveRecord::Base.transaction do
      example.run
      raise ActiveRecord::Rollback
    end
  end
end
```

We then include this helper instead of our regular spec helper in the model tests and also the model file, like so:

``` ruby
require 'active_record_spec_helper'
require_relative '../../app/models/team_role'
```

The rest of the test file remains exactly the same, as long as you use no other library apart from AR. If use something like FactoryGirl, you can load it in the active record spec helper, however this kind of defeats the whole purpose of this optimization.

You might also have to change your regular spec helper file by replacing `config.use_transactional_fixtures = true` with `require 'active_record_spec_helper'`.

### In Practice

Running a test suite of only two tests (in a rather big rails app) with both the regular spec helper and the active record spec helper I got from 20.604s to 1.683s of running time. This proves that 95% of time is wasted in the loading of unused libraries.

[via](https://gist.github.com/2068977)


