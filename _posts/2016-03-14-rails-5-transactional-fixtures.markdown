---
layout: post
title: "Rails 5 features. Commit callbacks in transactional fixtures"
date: 2016-03-13 10:30:00
tags: rails sql rspec
---

This blog posts series are about small and interesting "hidden" features from the newest rails 5.

Support `after_commit` callbacks in transactional fixtures.

Now you don't need to include [database_cleaner][database_cleaner] or other same stuff for database cleaning. Commit callbacks are included in your transactions stack automatically. I had to use gem [test_after_commit][test_after_commit] for these purposes before.

Note from [github][github]:

>   Tests now run after_commit callbacks. You no longer have to declare
>    `uses_transaction ‘test name’` to test the results of an after_commit.
>
>    after_commit callbacks run after committing a transaction whose parent
>    is not `joinable?`: un-nested transactions, transactions within test cases,
>    and transactions in `console --sandbox`.
>
>    *arthurnn*, *Ravil Bayramgalin*, *Matthew Draper*

Before:

{% highlight ruby %}
config.use_transactional_fixtures = false

it 'adds mailer task to queue' do
  DatabaseCleaner.cleaning do
    User.create! user_params
    expect(MailerQueue.tasks_count).to eq 1
  end
end
{% endhighlight %}

Before with test_after_commit:
{% highlight ruby %}
require 'test_after_commit'
config.use_transactional_fixtures = true

it 'adds mailer task to queue' do
  User.create! user_params
  expect(MailerQueue.tasks_count).to eq 1
end
{% endhighlight %}

After:

{% highlight ruby %}
config.use_transactional_fixtures = true

it 'adds mailer task to queue' do
  User.create! user_params
  expect(MailerQueue.tasks_count).to eq 1
end
{% endhighlight %}

<div style="text-align:center;">
<iframe src="//giphy.com/embed/opmIBtljGbwZi" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="http://giphy.com/gifs/queue-family-guy-stewie-griffin-opmIBtljGbwZi">via GIPHY</a></p>
</div>

[github]: https://github.com/rails/rails/pull/18458/
[test_after_commit]: https://github.com/grosser/test_after_commit/
[database_cleaner]: https://github.com/DatabaseCleaner/database_cleaner/
