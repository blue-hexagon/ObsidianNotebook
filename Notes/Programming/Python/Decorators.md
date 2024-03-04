# Decorators
Resource: [Real Python Article on Decorators](https://realpython.com/primer-on-python-decorators/)
## Introduction
A simple example follows, it should be explanatory on its own.
```python
def do_twice(func):
    @functools.wraps(func)  # Needed to preserve information (f.e. __name__) about the original function
    def wrapper(*args, **kwargs):
        """ *args and **kwargs are needed to be able to  pass paramaters from the decorated function
            It's also needed if we want to return any value from our decorated function """
        func(*args, **kwargs)
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper

@do_twice
def say_hello(name):
    print("Hello " + name)
    return "Returned Value"

if __name__ == "__main__":
    return_val = say_hello(name="world!")
    print(return_val)
    print(say_hello.__name__)

``` 
## Nesting Decorators
Order matters -- the top-most decorator is called first and then it takes the next and so on ending with the decorator closest to the funciton signature.

## Decorators with Arguments
>when a decorator uses arguments, you need to add an extra outer function
To see how decorators can be used simultaneously __with and without__ arguments, refer to [this article](https://realpython.com/primer-on-python-decorators/#both-please-but-never-mind-the-bread)
```python
def repeat(num_times=13):
    def decorator_repeat(func):
        @functools.wraps(func)
        def wrapper_repeat(*args, **kwargs):
            value = ''
            for _ in range(num_times):
                value += func(*args, **kwargs)
            return value
        return wrapper_repeat
    return decorator_repeat

@repeat(num_times=3)
def say_hello(name="world!"):
    print("Hello " + name)
    return "GOODBYE!"

if __name__ == "__main__":
    return_val = say_hello(name="world!!!")
    print(return_val)
    print(say_hello.__name__)
```
    Hello world!!!
    Hello world!!!
    Hello world!!!
    GOODBYE!GOODBYE!GOODBYE!
    say_hello
## Boilerplate
### Do X before and Y after
```python
import functools

def decorator(func):
    @functools.wraps(func)
    def wrapper_decorator(*args, **kwargs):
        # Do something before
        value = func(*args, **kwargs)
        # Do something after
        return value
    return wrapper_decorator
```
### Timer
```python
import functools
import time

def timer(func):
    """Print the runtime of the decorated function"""
    @functools.wraps(func)
    def wrapper_timer(*args, **kwargs):
        start_time = time.perf_counter()    # 1
        value = func(*args, **kwargs)
        end_time = time.perf_counter()      # 2
        run_time = end_time - start_time    # 3
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper_timer

@timer
def waste_some_time(num_times):
    for _ in range(num_times):
        sum([i**2 for i in range(10000)])
```
### Debug
The following `@debug` decorator will print the arguments a function is called with as well as its return value every time the function is called:
```python
import functools

def debug(func):
    """Print the function signature and return value"""
    @functools.wraps(func)
    def wrapper_debug(*args, **kwargs):
        args_repr = [repr(a) for a in args]                      # 1
        kwargs_repr = [f"{k}={v!r}" for k, v in kwargs.items()]  # 2
        signature = ", ".join(args_repr + kwargs_repr)           # 3
        print(f"Calling {func.__name__}({signature})")
        value = func(*args, **kwargs)
        print(f"{func.__name__!r} returned {value!r}")           # 4
        return value
    return wrapper_debug

@debug
def make_greeting(name, age=None):
    if age is None:
        return f"Howdy {name}!"
    else:
        return f"Whoa {name}! {age} already, you are growing up!"

```
### Wrapping Standard Library Functions
Here we wrap a standard library function with our debug decorator from above.
```python
math.factorial = debug(math.factorial)
math.factorial(4)
```
### Rate-limiting / Slowing Down
```python
import functools
import time

def slow_down(func):
    """Sleep 1 second before calling the function"""
    @functools.wraps(func)
    def wrapper_slow_down(*args, **kwargs):
        time.sleep(1)
        return func(*args, **kwargs)
    return wrapper_slow_down

@slow_down
def countdown(from_number):
    if from_number < 1:
        print("Liftoff!")
    else:
        print(from_number)
        countdown(from_number - 1)

```
### Stateful Decorators
```python
def count_calls(func):
    @functools.wraps(func)
    def wrapper_count_calls(*args, **kwargs):
        wrapper_count_calls.num_calls += 1
        print(f"Call {wrapper_count_calls.num_calls} of {func.__name__!r}")
        return func(*args, **kwargs)
    wrapper_count_calls.num_calls = 0
    return wrapper_count_calls

@count_calls
def say_whee():
    print("Whee!")
```
### Registering Plugins
```python
import random
PLUGINS = dict()

def register(func):
    """Register a function as a plug-in"""
    PLUGINS[func.__name__] = func
    return func

@register
def say_hello(name):
    return f"Hello {name}"

@register
def be_awesome(name):
    return f"Yo {name}, together we are the awesomest!"

def randomly_greet(name):
    greeter, greeter_func = random.choice(list(PLUGINS.items()))
    print(f"Using {greeter!r}")
    return greeter_func(name)
```
#### Referencing/using the plugin system
```plaintext
>>> PLUGINS
{'say_hello': <function say_hello at 0x7f768eae6730>,
 'be_awesome': <function be_awesome at 0x7f768eae67b8>}

>>> randomly_greet("Alice")
Using 'say_hello'
'Hello Alice'
```
## Decorating Classes
> Writing a class decorator is very similar to writing a function decorator. The only difference is that the decorator will receive a class and not a function as an argument.

> Decorating a class does not decorate its methods. 
