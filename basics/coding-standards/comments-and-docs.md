# 注释与文档

编写注释似乎是人人都会做的事情，但真要写好注释却是非常困难的事情。

```python
'''Bad: Use lowercase letter at the beginning of a comment'''
# get user input
n,*vals=map(int,input().split())

'''Good: Always begin a comment with an uppercase letter'''
# Get user input
n, *vals = map(int, input().split())
```

```python
'''Bad: Ambiguous variable name with explanation'''
s = num1 + num2 # Sum of the two numbers

'''Good: Just use explanatory variable names without comments as explanation'''
sum_of_nums = num1 + num2
```

```python
'''Bad: Worthless comments'''
# Add num1 with num2 and assign the result to sum_of_nums
sum_of_nums = num1 + num2

'''Good: Just remove those useless comments'''
sum_of_nums = num1 + num2
```

```python
'''Worse: Misleading / Outdated comments'''
def process_nums(nums):
    """Return odd nums in the sequence."""
    return filter(lambda num: num % 2 == 0, nums)

'''Good: Use correct / updated comments'''
def process_nums(nums):
    """Return even nums in the sequence."""
    return filter(lambda num: num % 2 == 0, nums)
```

而写好文档是更加困难的事情。这里以 Python 的一种文档格式（也是 PyCharm 默认的文档格式）Sphinx reStructuredText 举例。

```python
'''Bad'''
# Get the length of a string
def get_text_length(s, log_output=False):
    if not isinstance(s, str):
        raise TypeError("Argument `s` must be of type 'str'")
	result = len(s)
    if log_output:
        print(result)
    return result

'''Good'''
def get_text_length(/, string: str, *, log_output: bool = False) -> int:
    """Get the length of a string.

    :param string: The string to be calculated.
    :param log_output: Log output to console or not (default False).
    :returns: The length of the string.
    :raises TypeError: Raised when argument `string` is not a string.

    >>> get_text_length('foobar')
    6

    >>> get_text_length(42)
    Traceback (most recent call last):
      ...
    TypeError: Argument `string` must be of type 'str'
    """

    if not isinstance(s, str):
        raise TypeError("Argument `string` must be of type 'str'")

    result = len(s)

    if log_output:
        print(result)

    return result
```

一份好的函数文档至少应该包含以下部分：

- 一份对函数作用的简短概括
- 各参数的意义、类型及默认值（对于静态类型语言或已经标注了类型的动态类型语言代码，可以省略类型和默认值）
- 对返回值意义的简单解释。如果已经在概括中说明了这一点，可以省略
- 函数可能引起的异常，及可能引发异常的原因
- 一些关于函数如何使用的示例

除此之外，一个维护良好的项目也应该为大多数类定义及模块定义编写清晰准确的文档，并附以示例。在这里不再详述。
