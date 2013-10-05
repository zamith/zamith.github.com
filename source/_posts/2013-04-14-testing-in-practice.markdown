---
published: false
layout: post
title: "Testing in practice"
date: 2013-04-14 14:39
comments: true
categories: [rails, testing]
---

## Getting to the point

We're finally through with theory. Now to the good part. Remember, unless stated
otherwise, we'll be talking about mockist TDD with an outside-in process.

### The tools

There are far too many gems you can use too aid you in testing for me to
enumerate, I will name the few I consider the most important, in no particular
order.

* [RSpec](https://github.com/rspec/rspec-rails) - The testing gem per se
* [Cucumber](https://github.com/cucumber/cucumber-rails) - If you wish to to
  write readable scenarios (you can do integration
testing with RSpec, they won't be as readable though)
* [Capybara](https://github.com/jnicklas/capybara) - So you can simulate the
  user's interactions
* [Capybara Webkit](https://github.com/thoughtbot/capybara-webkit) or
  [Poltergeist](https://github.com/jonleighton/poltergeist) - A headless webkit
  to speed up your tests
* [Factory Girl](https://github.com/thoughtbot/factory_girl) - To help you build
  you objects, as real ones or stubs (you can also use rails fixtures, their
  just not my cup of tea)
* [Spork](https://github.com/sporkrb/spork) - Loads your environment to memory
  and speeds your TDD cycle (you can also use
  [Spring](https://github.com/jonleighton/spring) or
  [Zeus](https://github.com/burke/zeus))
* [Guard](https://github.com/guard/guard) and [Guard
  Spork](https://github.com/guard/guard-spork) - Tells spork when to run the
  tests
* [Timecop](https://github.com/travisjeffery/timecop) - So you can bend time
* [VCR](https://github.com/vcr/vcr) - Record HTTP requests and replays them on
future tests

### The configs

There are far too many possible configuration you may want or need to do, I'll
just paste my `Guardfile` and `spec_helper` files and hope they might help.

``` ruby Guardfile
guard 'spork', :rspec_env => { 'RAILS_ENV' => 'test' }, :cucumber => false, :test_unit => false do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch('config/environments/test.rb')
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile')
  watch('Gemfile.lock')
  watch('spec/spec_helper.rb') { :rspec }
  watch(%r{^app/decorators/.+\.rb$}) { :rspec }
end


guard 'rspec', :cli => "--drb", :all_on_start => false, :all_after_pass => false do
  watch(%r{spec/spec_helper.rb$})                       { "spec" }
  watch(%r{config/routes.rb$})                          { "spec" }
  watch(%r{app/controllers/application_controller.rb$'}) { "spec/controllers" }
  watch(%r{^spec/.+_spec\.rb$})
  watch(%r{^app/(.+)\.rb$})                           { |m| "spec/#{m[1]}_spec.rb" }
  watch(%r{^lib/(.+)\.rb$})                           { "spec" }
  watch(%r{^app/controllers/(.+)_(controller)\.rb$})  { |m| ["spec/#{m[2]}s/#{m[1]}_#{m[2]}_spec.rb", "spec/requests/#{m[1]}_spec.rb"] }
  watch(%r{^app/views/(.+)/})                        { |m| "spec/requests/#{m[1]}_spec.rb" }
  watch(%r{^app/mailers/(.+)/})                      { |m| "spec/mailers/#{m[1]}_spec.rb" }
end
```

``` ruby spec_helper.rb
require 'rubygems'
require 'spork'

Spork.prefork do
  ENV["RAILS_ENV"] ||= 'test'
  require File.expand_path("../../config/environment", __FILE__)
  require 'rspec/rails'
  require 'rspec/autorun'
  require 'capybara-screenshot/rspec'
  require 'devise/test_helpers'
  require 'capybara/rails'
  require 'capybara/dsl'

  require "rails/application"

  Spork.trap_method(Rails::Application::RoutesReloader, :reload!)
  Rails.logger.level = 4
  Devise.stretches = 1
  include Warden::Test::Helpers

  # Requires supporting ruby files with custom matchers and macros, etc,
  # in spec/support/ and its subdirectories.
  Dir[Rails.root.join("spec/support/**/*.rb")].each {|f| require f}

  Capybara.javascript_driver = :webkit

  RSpec.configure do |config|
    config.mock_with :rspec
    config.include FactoryGirl::Syntax::Methods
    config.include EmailSpec::Helpers
    config.include EmailSpec::Matchers
    config.include Rails.application.routes.url_helpers
    config.include Rails.application.helpers

    require 'database_cleaner'
    config.before(:suite) do
      DatabaseCleaner.strategy = :truncation
      DatabaseCleaner.clean_with(:truncation)
    end

    config.before(:each) do
      DatabaseCleaner.start
    end

    config.after(:each) do
      DatabaseCleaner.clean
    end

    config.treat_symbols_as_metadata_keys_with_true_values = true
    config.filter_run :focus => true
    config.run_all_when_everything_filtered = true

    config.use_transactional_fixtures = false
    config.use_transactional_examples = false
  end

  VCR.configure do |c|
    c.cassette_library_dir = 'spec/vcr_cassettes'
    c.hook_into :webmock
    c.allow_http_connections_when_no_cassette = true
  end

  include ActionDispatch::TestProcess
end

Spork.each_run do
  I18n.backend.reload!

  FactoryGirl.reload
end
```
These are some pretty generic configurations to get everything up and running
with Guard and Spork. One thing you probably want to do, is have a `support`
directory where you can store code to be used across multiple test files. You
can define them in modules that in turn can easily be included in RSpec, for a
certain type of tests. Here's an example:

``` ruby spec/support/integration.rb
require_relative 'integration/session_helpers'

RSpec.configure do |config|
  config.include Integration::SessionHelpers, type: :request
  config.include Capybara::DSL, type: :request
end
```
There are many possible types, the best place to see them all is the actual
[commit](https://github.com/rspec/rspec-rails/commit/fc5cdbb603f0e66f9f3d19a0a60a775e124fb218)
to `rspec-rails`.

And with that you are ready to roll, i.e. write some tests.

### Writing a test

The first test we write should be an integration or acceptance test. In RSpec it
looks like this (this is a pretty dumb example, but bare with me):

``` ruby spec/integration/example_spec.rb
require 'spec_helper'

feature 'Links in the navigation bar' do
  scenario 'guest can see the static page links' do
    visit root_path

    page.should have_link "About"
    page.should have_link "Support"
  end

  scenario 'guest can see the blog link' do
    visit root_path

    page.should have_link "Blog"
  end
end
```
And with cucumber it might look like this (taken directly from
[citygate](https://github.com/zamith/citygate)):

``` cucumber features/users/login_omniauth.feature
@omniauth
Feature: Login
  In order to login easily
  As a user of other websites
  I want to be able to login with those credentials

  @facebook
  Scenario: Login with Facebook
    Given I am on the login page
    Then I can see Sign in with Facebook
    When I click Sign in with Facebook
    Then I should be signed in with Facebook

  @google
  Scenario: Login with Google
    Given I am on the login page
    Then I can see Sign in with Google
    When I click Sign in with Google
    Then I should be signed in with Google
```


## Getting to the point

We're finally through with theory. Now to the good part. Remember, unless stated
otherwise, we'll be talking about mockist TDD with an outside-in process.

### The tools

There are far too many gems you can use too aid you in testing for me to
enumerate, I will name the few I consider the most important, in no particular
order.

* [RSpec](https://github.com/rspec/rspec-rails) - The testing gem per se
* [Cucumber](https://github.com/cucumber/cucumber-rails) - If you wish to to
  write readable scenarios (you can do integration
testing with RSpec, they won't be as readable though)
* [Capybara](https://github.com/jnicklas/capybara) - So you can simulate the
  user's interactions
* [Capybara Webkit](https://github.com/thoughtbot/capybara-webkit) or
  [Poltergeist](https://github.com/jonleighton/poltergeist) - A headless webkit
  to speed up your tests
* [Factory Girl](https://github.com/thoughtbot/factory_girl) - To help you build
  you objects, as real ones or stubs (you can also use rails fixtures, their
  just not my cup of tea)
* [Spork](https://github.com/sporkrb/spork) - Loads your environment to memory
  and speeds your TDD cycle (you can also use
  [Spring](https://github.com/jonleighton/spring) or
  [Zeus](https://github.com/burke/zeus))
* [Guard](https://github.com/guard/guard) and [Guard
  Spork](https://github.com/guard/guard-spork) - Tells spork when to run the
  tests
* [Timecop](https://github.com/travisjeffery/timecop) - So you can bend time
* [VCR](https://github.com/vcr/vcr) - Record HTTP requests and replays them on
future tests

### The configs

There are far too many possible configuration you may want or need to do, I'll
just paste my `Guardfile` and `spec_helper` files and hope they might help.

``` ruby Guardfile
guard 'spork', :rspec_env => { 'RAILS_ENV' => 'test' }, :cucumber => false, :test_unit => false do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch('config/environments/test.rb')
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile')
  watch('Gemfile.lock')
  watch('spec/spec_helper.rb') { :rspec }
  watch(%r{^app/decorators/.+\.rb$}) { :rspec }
end


guard 'rspec', :cli => "--drb", :all_on_start => false, :all_after_pass => false do
  watch(%r{spec/spec_helper.rb$})                       { "spec" }
  watch(%r{config/routes.rb$})                          { "spec" }
  watch(%r{app/controllers/application_controller.rb$'}) { "spec/controllers" }
  watch(%r{^spec/.+_spec\.rb$})
  watch(%r{^app/(.+)\.rb$})                           { |m| "spec/#{m[1]}_spec.rb" }
  watch(%r{^lib/(.+)\.rb$})                           { "spec" }
  watch(%r{^app/controllers/(.+)_(controller)\.rb$})  { |m| ["spec/#{m[2]}s/#{m[1]}_#{m[2]}_spec.rb", "spec/requests/#{m[1]}_spec.rb"] }
  watch(%r{^app/views/(.+)/})                        { |m| "spec/requests/#{m[1]}_spec.rb" }
  watch(%r{^app/mailers/(.+)/})                      { |m| "spec/mailers/#{m[1]}_spec.rb" }
end
```

``` ruby spec_helper.rb
require 'rubygems'
require 'spork'

Spork.prefork do
  ENV["RAILS_ENV"] ||= 'test'
  require File.expand_path("../../config/environment", __FILE__)
  require 'rspec/rails'
  require 'rspec/autorun'
  require 'capybara-screenshot/rspec'
  require 'devise/test_helpers'
  require 'capybara/rails'
  require 'capybara/dsl'

  require "rails/application"

  Spork.trap_method(Rails::Application::RoutesReloader, :reload!)
  Rails.logger.level = 4
  Devise.stretches = 1
  include Warden::Test::Helpers

  # Requires supporting ruby files with custom matchers and macros, etc,
  # in spec/support/ and its subdirectories.
  Dir[Rails.root.join("spec/support/**/*.rb")].each {|f| require f}

  Capybara.javascript_driver = :webkit

  RSpec.configure do |config|
    config.mock_with :rspec
    config.include FactoryGirl::Syntax::Methods
    config.include EmailSpec::Helpers
    config.include EmailSpec::Matchers
    config.include Rails.application.routes.url_helpers
    config.include Rails.application.helpers

    require 'database_cleaner'
    config.before(:suite) do
      DatabaseCleaner.strategy = :truncation
      DatabaseCleaner.clean_with(:truncation)
    end

    config.before(:each) do
      DatabaseCleaner.start
    end

    config.after(:each) do
      DatabaseCleaner.clean
    end

    config.treat_symbols_as_metadata_keys_with_true_values = true
    config.filter_run :focus => true
    config.run_all_when_everything_filtered = true

    config.use_transactional_fixtures = false
    config.use_transactional_examples = false
  end

  VCR.configure do |c|
    c.cassette_library_dir = 'spec/vcr_cassettes'
    c.hook_into :webmock
    c.allow_http_connections_when_no_cassette = true
  end

  include ActionDispatch::TestProcess
end

Spork.each_run do
  I18n.backend.reload!

  FactoryGirl.reload
end
```
These are some pretty generic configurations to get everything up and running
with Guard and Spork. One thing you probably want to do, is have a `support`
directory where you can store code to be used across multiple test files. You
can define them in modules that in turn can easily be included in RSpec, for a
certain type of tests. Here's an example:

``` ruby spec/support/integration.rb
require_relative 'integration/session_helpers'

RSpec.configure do |config|
  config.include Integration::SessionHelpers, type: :request
  config.include Capybara::DSL, type: :request
end
```
There are many possible types, the best place to see them all is the actual
[commit](https://github.com/rspec/rspec-rails/commit/fc5cdbb603f0e66f9f3d19a0a60a775e124fb218)
to `rspec-rails`.

And with that you are ready to roll, i.e. write some tests.

### Writing a test

The first test we write should be an integration or acceptance test. In RSpec it
looks like this (this is a pretty dumb example, but bare with me):

``` ruby spec/integration/example_spec.rb
require 'spec_helper'

feature 'Links in the navigation bar' do
  scenario 'guest can see the static page links' do
    visit root_path

    page.should have_link "About"
    page.should have_link "Support"
  end

  scenario 'guest can see the blog link' do
    visit root_path

    page.should have_link "Blog"
  end
end
```
And with cucumber it might look like this (taken directly from
[citygate](https://github.com/zamith/citygate)):

``` cucumber features/users/login_omniauth.feature
@omniauth
Feature: Login
  In order to login easily
  As a user of other websites
  I want to be able to login with those credentials

  @facebook
  Scenario: Login with Facebook
    Given I am on the login page
    Then I can see Sign in with Facebook
    When I click Sign in with Facebook
    Then I should be signed in with Facebook

  @google
  Scenario: Login with Google
    Given I am on the login page
    Then I can see Sign in with Google
    When I click Sign in with Google
    Then I should be signed in with Google
```


