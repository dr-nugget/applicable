# applicable.py

applicable.py is a very basic package with a single function: `applicable()`. It is simply used to test if a callable can be called with a set of arguments without raising an error.

It is recommended to use this package with Python 3.8 or higher, because best functionality is acheived with the walrus operator (`:=`), but it is not required. The package is Python 2.6+ compatible.

## Signature and Arguments

The signature of `applicable()` is:

    applicable(callable: Callable,
               *args: Any,
               **kwargs: Any,
               ret_result: bool = True,
               ret_exc: bool = True) -> Any

\* **Note:** This is not the actual signature, as you can probably tell. The actual signature (and the one type checkers and other tools will give you) is `applicable(callable: Callable, *args: Any, **kwargs: Any)`. As noted in the docstring (`applicable.__doc__`), `ret_result` and `ret_exc` are special keyword arguments, and it would be preferred that they be used after the callable arguments.

### Arguments:

- `callable`: The callable to be tested
- `ret_result`: Whether to return `callable(*args, **kwargs)` upon no error. `True` by default. If false, `applicable()` will return `True` upon no error.
- `ret_exc`: Whether to return an `applicable._FalseException` instance using the exception raised upon error. `_FalseException` is a subclass of Exception, with a couple of differences: It has a boolean value of `False`, a _FalseException instance will always equal another _FalseException instance, and has the `inst` and `cls` attribute, which are the exception instance and class, respectively, of the offending error so that you can still trace the exception that occured. This argument is `True` by default. If false, `False` will be returned upon error.
- `args` and `kwargs`: The arguments that are passed to `callable()`.

## Use

This function will usually be used in an `if` statement like the following:
```
    from applicable import applicable

    # Python 3.8+
    if (val := applicable(SomeCallable, 'arg!', a_kwarg='kwarg!')):
        # Do something with val
    else:
        # Do something with the exception; use val.cls for the exception class

    # Python 2.6+
    if applicable(SomeCallable, 'arg!', a_kwarg='kwarg!'):
        val = applicable(SomeCallable, 'arg!', a_kwarg='kwarg!')
        # Do somthing with val
    else:
        exc = applicable(SomeCallable, 'arg!', a_kwarg='kwarg!')
        # Do something with the exception; use val.cls for the exception class
```
\* **Note:** In this example, for both versions, the following would be more accurate (i.e, it wouldn't catch empty sequences, `0` (zero), and other objects that evaluate to boolean `False`) and, for the 2.6+ version, faster and more convenient:
```
from applicable import applicable

val = applicable(SomeCallable, 'arg!', a_kwarg='kwarg!')
if val == False or isinstance(val, _FalseException):
    # Do somthing with val
else:
    # Do something with the exception; use val.cls for the exception class
```

More examples, to show the full functionality of `applicable()` (and `_FalseException`):
```
>>> from applicable import applicable
>>> a = applicable(int, '4')
>>> a
4
>>>
>>> a = applicable(int, 'whoops')
>>> a
applicable._FalseException(exc_inst=ValueError(...))
>>> a.cls
<class 'ValueError'>
>>> a.inst
ValueError("invalid literal for int() with base 10: 'whoops'")
>>> bool(a)
False
>>>
>>> a = applicable(complex, 4, imag=3)
>>> a
(4+3j)
>>>
>>> a = applicable(complex, 4, imag=3, ret_result=False)
>>> a
True
>>> a = applicable(int, 'whoops', ret_exc=False)
False
```
