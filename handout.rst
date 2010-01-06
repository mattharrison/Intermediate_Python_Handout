===================
Intermediate Python
===================

.. class:: right big

  | *Matt Harrison*
  | matthewharrison@gmail.com
  | http://panela.blog-city.com/

.. class:: small

   ©2009-2010, licensed under a `Creative Commons
   Attribution/Share-Alike (BY-SA) license
   <http://creativecommons.org/licenses/by-sa/3.0/>`__.


Functional Programming
======================

``lambda``
----------

Create simple functions


  >>> def mul(a, b):
  ...     return a * b
  >>> mul_2 = lambda a, b: a*b
  >>> mul_2(4, 5) == mul(4,5)
  True

``map``
-------

Apply a function to items of a sequence
  
    >>> map(str, range(3))
    ['0', '1', '2']
  
``reduce``
----------

Apply a function to pairs of the sequence

    >>> import operator
    >>> reduce(operator.mul, [1,2,3,4])
    24 # ((1 * 2) * 3) * 4
  
``filter``
----------

Return a sequence items for which ``function(item)`` is ``True``
  
    >>> filter(lambda x:x >= 0, [0, -1, 3, 4, -2])
    [0, 3, 4]

Notes about "functional" programming in *Python*
------------------------------------------------

  * ``sum`` or ``for`` loop can replace ``reduce``
  * List comprehensions replace ``map`` and ``filter``

Functions
=========

Functions are Objects
----------------------

    >>> def adder(a, b):
    ...     return a + b

It's just an object

    >>> adder
    <function adder at 0x...>

Now invoke it (with ``(``)

    >>> adder(2, 5)
    7

Parameters: normal, named, ``*args`` and ``**kw`` 
---------------------------------------------------

    >>> def param_func(a, b='b', *args, **kw):
    ...     print [x for x in [a, b, args, kw]] 

    >>> args = ('f', 'g')

    >>> param_func(2, 'c', 'd', 'e,')
    [2, 'c', ('d', 'e,'), {}]
    >>> param_func(3, args)
    [3, ('f', 'g'), (), {}]
    >>> param_func(4, *args) # tricksey!
    [4, 'f', ('g',), {}]

All you need is ``*args`` for normal, named and variable args.

    >>> param_func(*args) # tricksey!
    ['f', 'g', (), {}]
    >>> param_func(5, 'x', *args)
    [5, 'x', ('f', 'g'), {}]
    >>> param_func(6, **{'foo':'bar'})
    [6, 'b', (), {'foo': 'bar'}]

Closures
========

Closures are inner functions that have (readonly in 2.6) access to the state they were
defined in.  (Use ``nonlocal`` keywork in 3.x for write access).

One common use is for generating functions

    >>> def add_x(x):
    ...     def add(num):
    ...         # note x isn't defined in add
    ...         return x + num
    ...     return add

    >>> add_2 = add_x(2)
    >>> add_2(5)
    7

Decorators
==========

Use closures to "wrap" functions in order to execute code before or
after the function proper executes.


Decorator Template
------------------

    >>> def decorator(func_to_decorate):
    ...     # update wrapper.__doc__ and .func_name
    ...     # or @functools.wraps(wrapper)
    ...     def wrapper(*args, **kw):
    ...         # do something before invocation
    ...         result = func_to_decorate(*args, **kw)
    ...         # do something after
    ...         return result
    ...     return wrapper



Complete Simple Example
-----------------------

Define the decorator

    >>> def limit4(function):
    ...     #@functool.wraps(wrapper)
    ...     def wrapper(*args, **kw):
    ...         result = function(*args, **kw)
    ...         return result[:4]
    ...     wrapper.__doc__ = function.__doc__
    ...     wrapper.func_name = function.func_name
    ...     return wrapper

Reassigning ``__doc__`` and ``func_name`` can also be done by
uncommenting ``@functool.wraps(wrapper)``.  Without this, uses can be
confused by decorated functions (and some tools like ``pickle`` won't work).

Wrap a function

    >>> @limit4
    ... def echo(foo):
    ...     '''echo contents back'''
    ...     return foo

``@limit4`` is syntactic sugar for placing ``echo = limit4(echo)``
after the function definition.

    >>> echo('123456') # should only have 4
    '1234'

    >>> echo.func_name
    'echo'
    >>> help(echo)
    <BLANKLINE>
    echo(*args, **kw)
        echo contents back
    <BLANKLINE>


Parameterized decorators (need 2 closures)
---------------------------------------------

    >>> def limit(length):
    ...     def decorator(function):
    ...         def wrapper(*args, **kw):
    ...             result = function(*args, **kw)
    ...             result = result[:length]
    ...             return result
    ...         return wrapper
    ...     return decorator

    >>> @limit(5) # notice parens
    ... def echo(foo): return foo

``@limit(5)`` is syntactic sugar for ``echo = limit(5)(echo)``
    

    >>> echo('123456') # should only have 5
    '12345'

Class instances as decorators
-----------------------------


    >>> class Decorator(object):
    ...     # in __init__ set up state
    ...     def __call__(self, function):
    ...         def wrapper(*args, **kw):
    ...             # do something before invocation
    ...             result = self.function(*args, **kw)
    ...             # do something after
    ...             return result
    ...         return wrapper

    >>> decorator = Decorator()
    >>> @decorator
    ... def nothing(): pass

List Comprehension
===================

    >>> seq = range(10)
    >>> results = [ 2*x for x in seq \
    ...            if x >= 0 ]

Shorthand for accumulation:

    >>> results = []
    >>> for x in seq:
    ...     if x >= 0:
    ...         results.append(2*x) #Can be nested

Nested List Comprehensions
--------------------------

     >>> nested = [ (x, y) for x in xrange(3) \
     ...           for y in xrange(4) ]
     >>> nested
     [(0, 0), (0, 1), (0, 2), (0, 3), (1, 0), (1, 1), (1, 2), (1, 3), (2, 0), (2, 1), (2, 2), (2, 3)]

Same as:


    >>> nested = []
    >>> for x in xrange(3):
    ...     for y in xrange(4):
    ...         nested.append((x,y))

Iteration Protocol
==================

    >>> sequence = [ 'foo', 'bar']
    >>> seq_iter = iter(sequence)  
    >>> seq_iter.next()
    'foo'
    >>> seq_iter.next()
    'bar'
    >>> seq_iter.next()
    Traceback (most recent call last):
      ...
    StopIteration

Making instances iterable
--------------------------

    >>> class Iter(object):
    ...     def __iter__(self):
    ...         return self
    ...     def next(self):
    ...         # return next item
    ...         return item

Making instances iterable(2)
----------------------------

    >>> class Iter(object):
    ...     def __iter__(self):
    ...         yield item

Generators
===========

Functions with ``yield`` remember state and return to it when
iterating over them


    >>> def gen_range(end):
    ...     cur = 0
    ...     while cur < end:
    ...         yield cur
    ...         # returns here next time
    ...         cur += 1 

    >>> print [x for x in gen_range(2)]
    [0, 1]

Making instances generate
--------------------------

     >>> class Generate(object):
     ...     def __iter__(self):
     ...         # returns a generator
     ...         return self.next() 
     ...     def next(self):
     ...         # logic
     ...         yield result

Generator expressions
------------------------

Like list comprehensions.  Except results are generated on the fly.
Use ``(`` and ``)`` instead of ``[`` and ``]`` (or omit if expecting a
sequence)

  
    >>> [x*x for x in xrange(5)]
    [0, 1, 4, 9, 16]

    >>> (x*x for x in xrange(5)) # doctest: +ELLIPSIS,
    <generator object <genexpr> at ...>
    >>> list(x*x for x in xrange(5))
    [0, 1, 4, 9, 16]

