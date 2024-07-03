# Dictionary and Set operators in Python 3.9+

## Set operators: `|` and `&`

The `|` and `&` operators, which are bitwise `OR` and `AND` when applied to numbers, are the union and intersection operator on sets:

```py
>>> {1, 2, 3} | {3, 4, 5}
{1, 2, 3, 4, 5}
>>> {1, 2, 3} & {3, 4, 5}
{3}
```

## Dict operator: `|`

Dictionaries support `|` but not `&`:

```py
>>> {'a': 1, 'b': 2, 'c': 3} | {'z': 26}
{'a': 1, 'b': 2, 'c': 3, 'z': 26}
>>> {'a': 1, 'b': 2, 'c': 3} & {'c': 333}
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for &: 'dict' and 'dict'
```

If there are multiple values, the rightmost value wins:

```py
>>> {'a': 1, 'b': 2, 'c': 3} | {'c': 333}
{'a': 1, 'b': 2, 'c': 333}
```

This makes a nice way to make an updated dict without mutating the original.

## Assignment operators

Recall that Python has shorthand for updating a variable with an assigning operator:

```py
>>> x = 0
>>> x = x + 1  # these two lines
>>> x += 1     # do the same thing
>>> x
2
```

You can do the same with the `|` operator:

```py
>>> x = 1
>>> x = x | 4
>>> x |= 4
>>> x
5
```

Remember, that's just doing a bitwise OR:

```
    DEC    BIN
      1 = 0001
OR    4 = 0100
---------------
      5 = 0101
```

So of course `|=` and `&=` works with Sets:

```py
set1 |= set2
# is equivalent to
set1 = set1 | set2
# is equivalent to
set1 = set1.union(set2)

set1 &= set2
# is equivalent to
set1 = set1 & set2
# is equivalent to
set1 = set1.intersection(set2)
```

And `|=` works with Dicts:

```py
dict1 |= dict2
# is equivalent to
dict1 = dict1 | dict2
# is equivalent to
dict1.update(dict2)
```

For dict update, since this feature was only added in Python 3.9 you may need to stick to the `.update()` method for backward compatibility.
