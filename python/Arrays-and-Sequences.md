# An Array of Sequences

| Container Sequences  | Flat Sequences |
| ------------- | ------------- |
| list, tuple...  | str, bytes, array.array...  |
| hold references to objects  | stores the value of each item  |
|   | More compact, but limited to holding primitive values  |

| Mutable Sequences  | Immutable Sequences |
| ------------- | ------------- |
| list, bytearray... | tuple, str, bytes |
| inherits methods from immutable sequences |  |


## List Comprehensions
- listcomp is meant to do one thing only: to build a new list.
``` python
new_thing = [letter for letter in thing]
```
- you can put in some conditionals, if you want. This is like `map` or `filter`
``` python
new_thang = [letter for letter in thing if letter > 'm']
```

### Cartesian Products
You can use list comp for matrix multiplication, if you're okay with the individual elements being tuples

## Generators
Iterators that you can only iterate over once
- They generate values on the fly rather than storing them in memory
- same syntax as list comprehensions, but uses (perens) rather than [brackets]

## Tuples
can function as immutable lists **AND** records without field names. When tuples function as records, then the position of each element is important. The position helps you determine what the element stands for.

### Tuple Unpacking
```
# useful for parallel assignment
name, age = ('Ngozi', 28)

# You can use the % operator to assign each item in a tuple to a slot in a format string
person = (name, age)
print('%s, %s' % person)

# you can use parallel assignment to swap variable values
name, age = age, name

# you can prefix a tuple with a star (*) to unpack a function call
t = (20, 8)
q, r = divmod(*t)

# You can also use it to grab excess items
a, b, *the_rest = range(5)
```

### Named Tuples
name tuples take up less memory than objects
```
>>> from collections import namedtuple
>>> Person = namedtuple('Person', 'name age hometown')
>>> ngozi = Person('Ngozi', 28, 'St. Louis Park')
>>> ngozi
Person(name='Ngozi', age=28, hometown='St. Louis Park')
>>> ngozi.age
28
```

the `-fields` attribute is especially helpful because it lists out a tuple containing all of the fields of your named tuple
```
>>> ngozi._fields
('name', 'age', 'hometown')
>>> Person._fields
('name', 'age', 'hometown')
```

the `_make()` function lets you instantiate a named tuple from an iterable 
```
>>> darla = ['Darla', 53, 'Becker']
>>> nne = Person._make(darla)
>>> nne
Person(name='Darla', age=53, hometown='Becker')
```

the `_asdict()` method returns an ordered dictionary. Good for displaying data
```
>>> ngozi._asdict()
OrderedDict([('name', 'Ngozi'), ('age', 28), ('hometown', 'St. Louis Park')])
```


# General Notes
- ABC: [Abstract Base Class](https://docs.python.org/3/library/abc.html)