===================
Intermediate Python
===================

.. class:: right big

  | *Matt Harrison*
  | matthewharrison@gmail.com
  | http://panela.blog-city.com/

.. class:: small

   ©2009, licensed under a `Creative Commons
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


``*args`` and ``**kw`` 
--------------------------

    >>> def param_func(a, b='b', *args, **kw):
    ...     print [x for x in [a, b, args, kw]] 
    >>> param_func(2, 'c', 'd', 'e,')
    [2, 'c', ('d', 'e,'), {}]
    >>> args = ('f', 'g')
    >>> param_func(3, args)
    [3, ('f', 'g'), (), {}]
    >>> param_func(4, *args) # tricksey!
    [4, 'f', ('g',), {}]
    >>> param_func(*args) # tricksey!
    ['f', 'g', (), {}]
    >>> param_func(5, 'x', *args)
    [5, 'x', ('f', 'g'), {}]
    >>> param_func(6, **{'foo':'bar'})
    [6, 'b', (), {'foo': 'bar'}]


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

    >>> echo('123456')
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

    >>> results = [ 2*x for x in seq \
    ...            if x >= 0 ]

Shorthand for accumulation:

    >>> results = []
    >>> for x in seq:
    ...     if x >= 0:
    ...         results.append(2*x)Can be nested

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

