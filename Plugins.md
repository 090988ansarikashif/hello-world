## Plugins

Plugins can extend/replace functionality of various components inside searx.

### example_plugin.py

```python
name = 'Example plugin'
description = 'This plugin extends the suggestions with the word "example"'
default_on = False  # disable by default


# attach callback to the post search hook
#  request: flask request object
#  ctx: the whole local context of the post search hook
def post_search(request, ctx):
    ctx['search'].suggestions.add('example')
    return True
```


### Currently implemented plugin entry points (a.k.a hooks)

 * Pre search hook (`pre_search`)
 * Post search hook (`post_search`)

Feel free to add more hooks to the code if it is required by a plugin.


### TODO

 * Client side dependency handling
 * Better documentation
 * More hooks
