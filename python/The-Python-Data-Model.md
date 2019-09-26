# Python Data Model
- [official docs](https://docs.python.org/3/reference/datamodel.html)

The python data model describes the API that you can use to construct classes that harness built in python tools
- special methods with the double-underscore, like `__getitem__` and `__len__` are part of this

This is also good for writing readable code. If you implement the __len__ function in your class, then a user can just type in `len(my_thing)` without having to investigate further. This goes for other built-in python functionality, like `my_thing[:3]`, `for thingy in my_thing`, and so on.

You can also specify how your class is sorted by adding a rating system. This would act as the `key` in the function `sorted()`

## Special Methods
These are shortcuts meant to be called by the python interpreter, not a human.
- they're implicitly called, and they're faster

## functionality to remember
Here are some functions that look like handy shortcuts

### collections.namedtuple
[from the docs](https://docs.python.org/3/library/collections.html#collections.namedtuple): Returns a new tuple subclass named _typename_
- has attributes without having methods
``` python
# collections.namedtuple
Card = collections.namedtuple('Card', ['rank', 'suit'])
```

### Special methods
- `__getitem__`: Called to implement evaluation of `self[key]`
- `__len__`: implements build-in python function `len()`
- `__init__`: treat this like a constructor
- `__repr__`: use this to specify how your object looks when you print it