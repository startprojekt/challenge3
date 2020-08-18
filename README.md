Python Stack Trace Interpretation
=================================


Traceback Problem 1
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 45, in <lambda>
    run_trace(1, lambda: perform_calculation(add, '1', 3))
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 12, in add
    return x + y
TypeError: can only concatenate str (not "int") to str
```

The problem lies in different argument types given to the function
perform_calculation, which are a string '1' and an integer 3 respectively.
Then the function calc tries to add (or exactly concatenate since the first
value is a string) two values which are of different types so TypeError
is raised.

If we could modify the input I would correct the lambda
function to: perform_calculation(add, 1, 3) so we jump to line 45.
Otherwise we would move to line 12 and rewrite it like: return int(x) + int(y)
to ensure we always add two integers (a ValueError may occur however).


Traceback Problem 2
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 46, in <lambda>
    run_trace(2, lambda: perform_calculation(add, 7, '3'))
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 12, in add
    return x + y
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

This is a situation similar to the Problem 1. This time however we have
the second argument of type string. We get a different error than in the first
case because Python can't add a string to an integer (which is the first
variable).

I would again jump to line 46 in "stack_traces.py" and make
arguments of same type or if input should stay the same I would rewrite
the line 12 to e.g. return int(x) + int(y)


Traceback Problem 3
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 47, in <lambda>
    run_trace(3, lambda: perform_calculation(mult, '3', '3'))
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 15, in mult
    return x * y
TypeError: can't multiply sequence by non-int of type 'str'
```

Since we have two string arguments on line 47, we can't multiply them
directly using * operator on line 15. We need to either rewrite them by removing
quotes (line 47) or convert x and y to integer before multiplying them.
In such a case the line 15 would be the first place to visit.


Traceback Problem 4
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 48, in <lambda>
    run_trace(4, lambda: perform_calculation(mult, [4], [3]))
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 15, in mult
    return x * y
TypeError: can't multiply sequence by non-int of type 'list'
```

Again we provide two list arguments on line 48 which are to be multiplied
on line 15 which obviously isn't possible in Python and raises an exception.
We would need to either modify the input arguments of the function themselves
or go to line 15 and figure out how to handle lists (multiply all values
from both arguments or maybe raise an error?).


Traceback Problem 5
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 49, in <lambda>
    run_trace(5, lambda: perform_calculation(innoc, '1', 3))
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 22, in innoc
    spelunk()
  File "stack_traces.py", line 21, in spelunk
    raise ValueError('Invalid')
ValueError: Invalid
```

Here our initial arguments walk through two functions, namely "innoc"
and "spelunk". Since we have only information about some "Invalid" value
at the end, we would need to go to line 21 first to check what caused
this ValueError (maybe some if-statement before) to be explicitly raised.


Traceback Problem 6
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 50, in <lambda>
    run_trace(6, lambda: comp_calc([1, 2, 3], 1, add))
  File "stack_traces.py", line 30, in comp_calc
    return [perform_calculation(calc, x_i, y_i) for x_i, y_i in zip(x, y)]
TypeError: zip argument #2 must support iteration
```

In this situation we would need to either convert the second variable on line 50
to be an iterable: comp_calc([1, 2, 3], [1], add) or make it an iterable
on line 30 in zip function call.


Traceback Problem 7
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 51, in <lambda>
    run_trace(7, lambda: comp_calc([1, 2, [3]], [4, 5, 6], add))
  File "stack_traces.py", line 30, in comp_calc
    return [perform_calculation(calc, x_i, y_i) for x_i, y_i in zip(x, y)]
  File "stack_traces.py", line 30, in <listcomp>
    return [perform_calculation(calc, x_i, y_i) for x_i, y_i in zip(x, y)]
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 12, in add
    return x + y
TypeError: can only concatenate list (not "int") to list
```

Third element of the first argument in comp_calc on line 51 is a one-element
list which causes an error on line 12. We should either correct the input
on line 51 or handle such a situation by adding some code on line 12.


Traceback Problem 8
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 52, in <lambda>
    run_trace(8, lambda: calc_dict({'one': 1, 'two': '2'}, 'one', 'two', add))
  File "stack_traces.py", line 26, in calc_dict
    return perform_calculation(calc, d[k1], d[k2])
  File "stack_traces.py", line 8, in perform_calculation
    calc(x, y)
  File "stack_traces.py", line 12, in add
    return x + y
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

The value under the key 'two' is again a string so we can't add it to
value of 'one' which is an integer. Again we need to either correct it
on line 52 or handle such a case on line 12 in add function.


Traceback Problem 9
-------------------

```
Traceback (most recent call last):
  File "stack_traces.py", line 36, in run_trace
    f()
  File "stack_traces.py", line 53, in <lambda>
    run_trace(9, lambda: calc_dict({}, 'one', 'two', add))
  File "stack_traces.py", line 26, in calc_dict
    return perform_calculation(calc, d[k1], d[k2])
KeyError: 'one'
```

We get a key error because we pass an empty dictionary on line 53. We should
either provide 'one' and 'two' key-value pairs (dictionary on line 53) 
or extra check if requested keys exist before calling perform_calculation function on line 26.

