---
layout: post
title: "How to typecast rails sql data everywhere"
date: 2015-09-08 17:20:00
tags: rails sql
---

Referencing to [rails-sqli.org][railssqli] information page there are a lot of ActiveRecord::Relation methods don't sanitize your data.

> Options:

> :conditions - Can be a string, an array, or a hash. If a string, it will be passed to the database unchanged. If an array, the first element is a string and the remaining elements are values to interpolate into the string. The values will be sanitized, but the string element will not. If a hash is given, the keys and values will be properly sanitized.

> :order - Unsanitized SQL string for ORDER BY clause.

> :group - Unsanitized SQL string for GROUP BY clause.

> :having - Unsanitized SQL string for HAVING clause.

> :limit - Will be converted to an integer.

> :offset - Will be converted to an integer.

> :joins - Accepts SQL string, which is not sanitized. Also accepts named associations or an array containing both strings and named associations.

> :include - Only accepts named associations.

> :select - Unsanitized SQL string SELECT clause.

> :from - Unsanitized SQL string.

> :readonly - Boolean value

> :lock - Unsanitized SQL string. May be ignored in databases that do not support locks. Also accepts a boolean.
The all, first, last, exists, where, calculate, update_all and other methods accept these options or a subset.

Ok, `where` is super cool, you can create things like this:

{% highlight ruby %}

Video.where(tags: %w(a b)).to_sql #=> "SELECT \"videos\".* FROM \"videos\" WHERE \"videos\".\"tags\" = '{a,b}'"

{% endhighlight %}
Rails detected array column and used `{elem0, elem1, ..}` postgres syntax.

{% highlight ruby %}

Video.where(name: %w(a b)).to_sql #=> "SELECT \"videos\".* FROM \"videos\" WHERE \"videos\".\"name\" IN ('a', 'b')"

{% endhighlight %}
For `name` column it made `IN` query for string collection.

When you want to use this sanitizing rule in `select` or `joins` query, you should cast it directly from your code. Example:

{% highlight ruby %}

scope :fixed_length, ->(data) { select("array_length(#{sanitized_array(data)})") }

def self.sanitized_array(data)
  # :tags is string array type column
  connection.quote column_for_attribute(:tags).type_cast_for_database data
end

{% endhighlight %}

`User.fixed_length params[:tags]` returns correct answer and protect you from SQL injection from user input data

[railssqli]: http://rails-sqli.org
