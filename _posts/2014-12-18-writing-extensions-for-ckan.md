---
layout: post_page
title: Writing extensions for CKAN
date: 2014-12-18
permalink: writing-extensions-for-ckan
---

Although the CKAN [extension documentation](http://docs.ckan.org/en/latest/extensions/index.html)
is fairly complete I thought it might be useful to add some examples for each of the
[plugin interfaces](http://docs.ckan.org/en/latest/extensions/plugin-interfaces.html) that are
available to you as a CKAN extension author. They're not going to be fully complete plugin examples,
they [already exist](https://github.com/ckan/ckan/tree/master/ckanext), but rather just more guidance
on what you can do with each plugin and how.


{% highlight python %}

class Obj(object):

    def __init__(self):
        pass

{% endhighlight %}


Post