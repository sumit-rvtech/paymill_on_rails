# PaymillOnRails:  [**http://lgs.github.io/paymill_on_rails**][18]

[**<img src="https://gemnasium.com/lgs/paymill_on_rails.png" alt="Dependency Status" />**][1] [**<img src="https://codeclimate.com/github/lgs/paymill_on_rails.png" />**][2]

Paymill subscriptions on Rails 4.0.1 and ruby-2.0.0-p247, [**deployed on Heroku**][3]. This code is adapted from [**Railscast #288**][4] and based on ruby wrapper for the Paymill API gem [**paymill-ruby**][5]. See also Paymill API reference:  [**Paymill API/v2 documentation**][6]

## Security Standards

Important information for the credit card data in your HTML form:

With the payment form, it is essential that you do not put a name attribute with the <input> tags for credit card data! In this way, the credit card data will not be returned to your server, and you will stay outside the legal provisions concerning the storage of such data ([**PCI compliance**][7]). The only data which Paymill on Rails stores locally infact, are Plans and Subscriptions, which are not sensible and absolutely [**PCI compliant**][7].

See current Paymill on Rails data model 

```
  ActiveRecord::Schema.define(version: 20121207103300) do

    create_table "plans", force: true do |t|
      t.string "paymill_id"
      t.string "name"
      t.float "price"
      t.datetime "created_at"
      t.datetime "updated_at"
    end

    create_table "subscriptions", force: true do |t|
      t.integer "plan_id"
      t.string "email"
      t.string "name"
      t.string "paymill_id"
      t.datetime "created_at"
      t.datetime "updated_at"
    end

  end
```

For further reading see also [**payment form**][8] and [**security standards**][9]

## Usage

Since the release of [**paymill_on_rails gem 0.0.5**][10], PaymillOnRails becomes a [**Mountable Rails Engines**][11] for Rails 4. Set up a subscription payment integrating it with your existing Rails application service is now very simple just following nex steps:

- register for a test account at [**paymill.com**][12] and create the subscription plans, also called Offers ([**read more**][13], which are how Subscription Plans are called in Paymill. 

- build a new Rails app from scratch (or use your existing one) :

```
$ rails new myapp && myapp
$ echo "gem 'paymill_on_rails' >> Gemfile
$ bundle update
$ rake paymill_on_rails:install:migrations
$ rake db:migrate
```

- now mount the engine adding the following to config/routes.rb :

```
  Prova::Application.routes.draw do
     root 'paymill_on_rails/plans#index' #(or use another path)
     mount PaymillOnRails::Engine => "/paymill_on_rails"
  end
```

- Login in with your Paymill account and follow [**Dasboard --> My Account --> Settings --> APY Keys**][14], to see your keys and set PAYMILL_PUBLIC_KEY/API_KEY accordingly. 
    
- set OS env variables, with your own paymill test keys :

```
   $ env | grep _KEY
   PAYMILL_PUBLIC_KEY=11111111111111111111111
   API_KEY=a1b2c3d4e5f6g7h8i9l1i2m3
```

- get the plans and their IDs from paymill :

```
    $ rake paymill_on_rails:import_plans
```

That's it! Run 

```
    $ rails server
```
and it should work.

## Deploy on Heroku

In your rails app  run 

```
   $ heroku config:add API_KEY='your-own-api-key'
   $ heroku config:add PAYMILL_PUBLIC_KEY='your-own-public-key'
   $ heroku config:add BUNDLE_WITHOUT="development:test"
   $ heroku rake db:migrate
   $ heroku rake paymill_on_rails:import_plans
```

## Current Bundle

PATH
```
  remote: .
  specs:
    paymill_on_rails (0.0.6)
      paymill (~> 0.4.0)
      rails (~> 4.0.0)
```

GEM
```
  remote: https://rubygems.org/
  specs:
    actionmailer (4.0.1)
      actionpack (= 4.0.1)
      mail (~> 2.5.4)
    actionpack (4.0.1)
      activesupport (= 4.0.1)
      builder (~> 3.1.0)
      erubis (~> 2.7.0)
      rack (~> 1.5.2)
      rack-test (~> 0.6.2)
    activemodel (4.0.1)
      activesupport (= 4.0.1)
      builder (~> 3.1.0)
    activerecord (4.0.1)
      activemodel (= 4.0.1)
      activerecord-deprecated_finders (~> 1.0.2)
      activesupport (= 4.0.1)
      arel (~> 4.0.0)
    activerecord-deprecated_finders (1.0.3)
    activesupport (4.0.1)
      i18n (~> 0.6, >= 0.6.4)
      minitest (~> 4.2)
      multi_json (~> 1.3)
      thread_safe (~> 0.1)
      tzinfo (~> 0.3.37)
    arel (4.0.1)
    atomic (1.1.14)
    builder (3.1.4)
    erubis (2.7.0)
    hike (1.2.3)
    i18n (0.6.5)
    json (1.8.1)
    mail (2.5.4)
      mime-types (~> 1.16)
      treetop (~> 1.4.8)
    mime-types (1.25)
    minitest (4.7.5)
    multi_json (1.8.2)
    paymill (0.4.0)
      json
    polyglot (0.3.3)
    rack (1.5.2)
    rack-test (0.6.2)
      rack (>= 1.0)
    rails (4.0.1)
      actionmailer (= 4.0.1)
      actionpack (= 4.0.1)
      activerecord (= 4.0.1)
      activesupport (= 4.0.1)
      bundler (>= 1.3.0, < 2.0)
      railties (= 4.0.1)
      sprockets-rails (~> 2.0.0)
    railties (4.0.1)
      actionpack (= 4.0.1)
      activesupport (= 4.0.1)
      rake (>= 0.8.7)
      thor (>= 0.18.1, < 2.0)
    rake (10.1.0)
    sprockets (2.10.0)
      hike (~> 1.2)
      multi_json (~> 1.0)
      rack (~> 1.0)
      tilt (~> 1.1, != 1.3.0)
    sprockets-rails (2.0.1)
      actionpack (>= 3.0)
      activesupport (>= 3.0)
      sprockets (~> 2.8)
    sqlite3 (1.3.8)
    thor (0.18.1)
    thread_safe (0.1.3)
      atomic
    tilt (1.4.1)
    treetop (1.4.15)
      polyglot
      polyglot (>= 0.3.1)
    tzinfo (0.3.38)
```

PLATFORMS
```
  ruby 2.0.0
```

DEPENDENCIES
```
  paymill_on_rails!
  sqlite3
```

## Related works

See [**apalancat**][15] gem [**paymill-rails**][16]. More in general see the [**Search repository results on github**][17].

## Copyright

(The MIT License)

Copyright (c) 2013 Luca G. Soave

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the 'Software'), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[1]: https://gemnasium.com/lgs/paymill_on_rails
[2]: https://codeclimate.com/github/lgs/paymill-on-rails
[3]: https://paymill-on-rails.herokuapp.com
[4]: http://railscasts.com/episodes/288-billing-with-stripe
[5]: https://github.com/dkd/paymill-ruby
[6]: https://www.paymill.com/en-gb/documentation-3/reference/api-reference/index.html
[7]: http://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard
[8]: https://paymill.com/en-gb/documentation-3/introduction/payment-form
[9]: https://paymill.com/en-gb/support-3/security/security-standards
[10]: https://rubygems.org/gems/paymill_on_rails
[11]: http://edgeguides.rubyonrails.org/engines.html
[12]: https://paymill.com
[13]: https://app.paymill.com/en-gb#!/offers
[14]: https://app.paymill.com/en-gb#!/index
[15]: https://github.com/apalancat
[16]: https://github.com/apalancat/paymill-rails
[17]: https://github.com/search?l=Ruby&q=paymill&ref=cmdform&type=Repositories



