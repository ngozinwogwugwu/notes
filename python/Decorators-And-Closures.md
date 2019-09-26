# Function decorators and closures
we use **decorators** to _mark_ functions and enhance their behavior. **Closures** are also important to understand. We use them for async programming and callbacks. We need closures to write decorators

## Decorators
A decorator is a _callable_ that takes another function as argument. It usually replaces that function with a different one. Really, decorators are just syntactic sugar. They're also good for _metaprogramming_, or changing behavior at run-time

```python
def ngozis_decorator(original_function):
  def replacement_function():
    print('I\'m doing something wild!')
  return replacement_function

@ngozis_decorator
def normal_function():
  print('I\'m doing something totally normal')


```

Decorators run right after the decorated function is defined
- [here's an example](https://github.com/fluentpython/example-code/blob/master/07-closure-deco/registration.py)

_Import-time_ vs _run-time_ - there's a difference. Some code gets run when it's imported

### Variable Scope
If you define local variables in your code, those have to be your local variables. If you play with them out of order, you get yelled at. This is because
>  Python does not require you to declare variables, but assumes that a variable assigned in the body of a function is local.

The way around this is to declare a variable as `global`

## Closures
closures are functions. They have an _extended scope_ which encompasses _non-global variables_. These variables are referenced in the body of the function, but not defined there.

>  a closure is function that retains the bindings of the free variables that exist when the function is defined, so that they can be used later when the function is invoked and the defining scope is no longer available

- [here's an example](https://github.com/fluentpython/example-code/blob/master/07-closure-deco/average.py), but the point is this:
``` python
def make_averager():
    series = []
    def averager(new_value):
        series.append(new_value)
        total = sum(series)
        return total/len(series)
    return averager
```
`make_averager` here is a _higher order function_. The **closure** here is the function `averager` AND the variable `series`






