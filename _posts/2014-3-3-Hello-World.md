---
layout: post
title: Write Unit Test For Your RecyclerView Adapter
published: true
---

When customizing a RecyclerView adapter, it is useful to create unit test so you yourself or any other future developers
will not break existing functionalities while modifying your adapter.

There are many literature on writing unit tests, and most will make reference to MVP (Model-View-Presenter) or clean 
architecture designs. These are good. Writing testable code is always good. 

But there are times when you need to take over a piece of legacy code, and it dates way back MVP became a buzzword-of-the-day. 
One may be tempted to go through the uphill task of refactoring the legacy code to MVP first, then apply unit test. This approach is definitely good, but introduces a HUGE overhead if you just wants to dive into learning how to write unit tests. 

It is still possible to write unit tests without MVP structure.

Here, I will document what I did to write unit test for a change request in a RecyclerView adapter.

To keep things simple, we have a recycler view that have some title or text. The change request is to insert new content every three items in the recyler view - probably advertisement. To keep things simple we will not focus on the content of the item-to-insert. It will just be another text view. 

Project Structure
-----------------
Project consists of just a MainActivity.java file to set up our recycler view with test data.

{% highlight java %}
  public class MainActivity extends AppCompatActivity
  {
  }
{% highlight java %}

To build a RecyclerView adapter. Insert special view contents within your adapter.

![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
