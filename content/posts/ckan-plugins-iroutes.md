---
title: "CKAN plugins - IRoutes"
date: 2014-12-18T00:00:00+00:00
draft: false
---

> This blog post is almost certainly out of date. You should check the [CKAN docs](https://docs.ckan.org/) - they're pretty awesome.

Although the CKAN [extension documentation](http://docs.ckan.org/en/latest/extensions/index.html)
is fairly complete I thought it might be useful to document some examples for each of the
[plugin interfaces](http://docs.ckan.org/en/latest/extensions/plugin-interfaces.html) that are
available to you as a CKAN extension author. They're not going to be fully complete plugin examples,
they [already exist](https://github.com/ckan/ckan/tree/master/ckanext), but rather just more basic
guidance on what you can do with each plugin and how.</p>

### IRoutes

The IRoutes plugin is used to add new URLs to your CKAN instance.  Typically you&rsquo;ll use
it when you have created a new Controller, and you want to set up certain URLs to call
the actions on that Controller, but it can also be used to add fall-back URLs.

To implement IRoutes, your code will probably look _something_ like this:

```python
import ckan.plugins as p

class MyPlugin(p.SingletonPlugin):
    p.implements(p.IRoutes, inherit=True)

    def before_map(self, map):
        controller = "ckanext.myplugin.controllers:MyController"
        map.connect("something", "/something",
            controller=controller, action="index")
        map.connect("something_view", "/something/{name}",
            controller=controller, action="view")
        return map

    def after_map(self, map):
        return map
```

As the [documentation](http://docs.ckan.org/en/latest/extensions/plugin-interfaces.html?highlight=implements#ckan.plugins.implements)
explains, `p.implements(p.IRoutes, inherit=True)` is how you tell CKAN that your
plugin provides that functionality, in this case adding to the routing table. The part
of that call `inherit=True` simply means that if you do not implement one of the
methods of the interface then a default implementation will be provided for you.  So,
in our code above we could just remove `after_map(self, map)` if we're not going
to implement it, and it'll all still work.  It's probably easier to just always have
`inherit=True`.


### Using the map

In our before_map call above, we have a line that looks like
`controller = 'ckanext.myplugin.controllers:MyController'`, this is using a
[dotted-module-name](https://docs.python.org/2/tutorial/modules.html#packages)
to find the module (ckanext.myplugin.controllers in ckanext/myplugin/controller.py)
 and then the class to load is the name following `:`.  You had to use this
 format when [modifying your setup.py to point to your plugin](http://docs.ckan.org/en/latest/extensions/tutorial.html#adding-the-plugin-to-setup-py).

The following two lines are used to specify the new URLs we want to add, and which
action to call for each.

```python
map.connect(
    "something",
    "/something",
    controller=controller,
    action="index"
)
```

This line gives the URL a name ("something"), so that it can be looked up by name,
using `toolkit.url_for('something')`.

The next parameter ("/something") is the URL to be mapped. This example is pretty
straightforward, but you might want to support `/something/here` and
`/something/else`, in which case you specify the URL as `/something/{name}`.
The action parameter specifies the name of the method in your controller class. You
should remember though, that if you use a named parameter in the `map.connect()`
call that you need to add that parameter to the method signature.

```python
# /something
def index(self):
    pass

# /something/{name}
def view(self, name):
    pass
```

### More information

Under the hood, CKAN uses the [routes library](http://routes.readthedocs.org/en/latest/) v1.13
to handle the route generation, so that should be the next port of call if you need more
information on defining more complex routes. In particular:


* [Setting up routes](http://routes.readthedocs.org/en/latest/setting_up.html)
* [Using submappers](http://routes.readthedocs.org/en/latest/setting_up.html#submappers) for when you want lots of actions on a single controller all under a single root URL.

