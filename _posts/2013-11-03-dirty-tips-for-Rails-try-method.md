---
title: Using try method to check existence of key in hash  
layout: post
guid: GKjWXpcZgxVX1
tags:
  - Ruby
---

Parameter is a hash, frequently used to transfer data. Sometimes it nests as the following structure:

<pre><code>
:user => {:name => "Ryan", :sex => "male", :age => "28"}
</code></pre>

To access ':name' in your controller, you probably write code like this:

<pre><code>
def name
	params[:user][:name]
end
</code></pre>

Wait! Something is wrong. Rails will raise `undefined method[:name] for nil` error  when param[:user] doesn't exit

Sadly, you have to clutter your code to make it work.

<pre><code>
def name
	params[:user] ? params[:user][:name] : nil
end
</code></pre>

Actually, you have another choice -  try method. Code can also be writen as

<pre><code>
def name
	params[:user].try(:[], :name)
end
</code></pre>

Now it works perfectly and clean. 

> Look up [.try](http://api.rubyonrails.org/classes/Object.html#method-i-try) method of Rails.