# 封装

继承、封装、多态，这是我们学习”面向对象“时首先学习的，”面向对象“的”三大特性“。然而，这里我们不愿讨论这三大特性是什么，以及具有什么好处，有什么坏处，在此之上又有哪些误解。在这里，我们讨论的”封装“并非专属于面向对象中的封装，而是一个更高层的概念。

封装其实是一个相当简单的概念，即隐藏一段程序的实现细节，仅对外暴露用于调用的接口。一个函数就是一个最简单的封装。例如，考虑下面这段代码：

```python
PATH1 = ...
PATH1_OUTPUT = ...
PATH2 = ...
PATH2_OUTPUT = ...

start = time.time()
print(f'Processing "{PATH1}"...')
xls1_content = read_excel(PATH1)
processed_content = process3(process2(process1(xls1_content)))
save_excel(processed_content, PATH1_OUTPUT)
print(f'Processing "{PATH1}" fininshed in {time.time() - start}s.')

start = time.time()
print(f'Processing "{PATH2}"...')
xls2_content = read_excel(PATH2)
processed_content = process3(process2(process1(xls2_content)))
save_excel(processed_content, PATH2_OUTPUT)
print(f'Processing "{PATH2}" fininshed in {time.time() - start}s.')
```

上面这段代码其实还是相对比较干净的。然而，我们观察到读取 excel 文件、并对其进行处理的三行代码被重复了两次。这时我们就应该引起警觉了：这违背了”DRY（Don't Repeat Yourself）“原则。我们将同一段代码重复了两次，这也许还可以接受，但如果我们要重复多次呢？加入我们想要修改这里的处理逻辑，比如加上一个 process4 或是移除一个 process2，那就需要修改许多地方，而这对程序的可维护性将产生灾难性的影响。

因此，我们首先尝试将这一部分封装为一个函数。

```python
def process_excel(path: str, path_output: str) -> None:
    content = read_excel(path)
    processed_content = process3(process2(process1(content)))
    save_excel(processed_content, path_output)
```

这段代码很好，但它有一个问题，就是将”**副作用**“与”无副作用“的代码耦合到了一起：写入文件是”副作用“，即对函数的输出没有影响，而与外界交互的代码，比如文件输入、打开窗口、执行某一段命令行代码等，都是”副作用“；而这里处理文件内容的代码是”无副作用“的，它没有与外界交互，就像一个真正的数学函数——读取输入，产生输出，并且对于同样的输入，总是产生同样的输出。

考虑一下，如果我们之后不止需要处理 excel 文件，还需要处理 csv 等表格文件，这个函数就不方便复用了。因此，我们将该函数的逻辑进一步拆成两个函数：

```python
def process_content(content: str) -> str:
    processed_content = process3(process2(process1(content)))
    return processed_content

def process_excel(path: str, path_output: str) -> None:
    content = read_excel(path)
    processed_content = process_content(content)
    save_excel(processed_content, path_output)

def process_csv(path: str, path_output: str) -> None:
    content = read_csv(path)
    processed_content = process_content(content)
    save_csv(processed_content, path_output)
```

现在再来看看主要的程序代码：

```python
start = time.time()
print(f'Processing "{PATH1}"...')
process_excel(PATH1, PATH1_OUTPUT)
print(f'Processing "{PATH1}" fininshed in {time.time() - start}s.')

start = time.time()
print(f'Processing "{PATH2}"...')
process_excel(PATH2, PATH2_OUTPUT)
print(f'Processing "{PATH2}" fininshed in {time.time() - start}s.')
```

看起来好多了，但这里向终端打印提示信息的代码该如何封装呢？

我们可以尝试使用一个”高阶函数“进行封装，即接受一个函数，处理一个函数，然后输出一个函数的函数。

```python
def logged(func: Callable, path: str) -> Callable:
    def new_func(*args, **kwargs):
        start = time.time()
        print(f'Processing "{path}"...')
        func(*args, **kwargs)
        print(f'Processing "{path}" fininshed in {time.time() - start}s.')
    return new_func
```

现在代码变成了这样：

```python
logged(process_excel, PATH1)(PATH1, PATH1_OUTPUT)
logged(process_excel, PATH2)(PATH2, PATH2_OUTPUT)
```

这已经不错了。但是我们的`logged`函数还是太特化了：它只能向终端打印固定的信息。如果我们不希望向终端打印"Processing"，而是"Reading"呢？那么当前的`logged`函数就束手无策了。

所以，下面我们对`logged`函数做一些改进，允许用户自定义两个向终端输出的提示语句：

```python
def logged(func: Callable, log1: str, log2: str) -> Callable:
    def new_func(*args, **kwargs):
        start = time.time()
        print(f'{log1}...')
        result = func(*args, **kwargs)
        print(f'{log2} in {time.time() - start}s.')
        return result
    return new_func
```

现在可以写成这样：

```python
logged(process_excel, f'Processing "{PATH1}"', f'Processing "{PATH1}" finished')(PATH1, PATH1_OUTPUT)
```

不过，这似乎有点太不聪明了：我们将`PATH1`重复了三次，有没有更好的办法？

我们可以尝试将`log1`和`log2`变成两个函数：

```python
def logged(func: Callable, log1_func: Callable, log2_func: Callable) -> Callable:
    def new_func(*args, **kwargs):
        start = time.time()
        print(log1_func(*args, **kwargs))
        result = func(*args, **kwargs)
        print(f'{log2_func(*args, **kwargs)} in {time.time() - start}s.')
        return result
    return new_func
```

然后，我们就可以写成这样：

```python
log1_func = lambda path, _: f'Processing "{path}"'
log2_func = lambda path, _: f'Processing "{path}" finished'
logged(process_excel, log1_func, log2_func)(PATH1, PATH1_OUTPUT)
```

现在，再对比一下之前的完整代码与现在的完整代码：

```python
'''Before'''
PATH1 = ...
PATH1_OUTPUT = ...
PATH2 = ...
PATH2_OUTPUT = ...

start = time.time()
print(f'Processing "{PATH1}"...')
xls1_content = read_excel(PATH1)
processed_content = process3(process2(process1(xls1_content)))
save_excel(processed_content, PATH1_OUTPUT)
print(f'Processing "{PATH1}" fininshed in {time.time() - start}s.')

start = time.time()
print(f'Processing "{PATH2}"...')
xls2_content = read_excel(PATH2)
processed_content = process3(process2(process1(xls2_content)))
save_excel(processed_content, PATH2_OUTPUT)
print(f'Processing "{PATH2}" fininshed in {time.time() - start}s.')


'''After'''
PATH1 = ...
PATH1_OUTPUT = ...
PATH2 = ...
PATH2_OUTPUT = ...

LOG1_FUNC = lambda path, _: f'Processing "{path}"'
LOG2_FUNC = lambda path, _: f'Processing "{path}" finished'

logged_process_excel = logged(process_excel, LOG1_FUNC, LOG2_FUNC)

logged_process_excel(PATH1, PATH1_OUTPUT)
logged_process_excel(PATH2, PATH2_OUTPUT)

def logged(func: Callable, log1_func: Callable, log2_func: Callable) -> Callable:
    def new_func(*args, **kwargs):
        start = time.time()
        print(log1_func(*args, **kwargs))
        result = func(*args, **kwargs)
        print(f'{log2_func(*args, **kwargs)} in {time.time() - start}s.')
        return result
    return new_func

def process_excel(path: str, path_output: str) -> None:
    content = read_excel(path)
    processed_content = process_content(content)
    save_excel(processed_content, path_output)

def process_content(content: str) -> str:
    processed_content = process3(process2(process1(content)))
    return processed_content
```

在上面这个简单的例子中，这样封装似乎有些”过度“了，看起来没有太大必要。但是，想象一下，如果这个程序中包含数十个类似的处理 excel/csv 文件、并且需要向终端输出不同信息的相似代码，这样封装之后就能够很大程度上使代码变得更加清晰：

```python
logged_process_excel = logged(process_excel, ..., ...)
logged_process_csv = logged(process_csv, ..., ...)
logged_read_input_excel = logged(read_input, ..., ...)
logged_read_input_csv = logged(read_input, ..., ...)

path_input, path_output = logged_read_input_excel(...)
logged_process_excel(path_input, path_output)

path_input, path_output = logged_read_input_csv(...)
logged_process_csv(path_input, path_output)
```

同时，我们也可以看到，即使对于只出现了一次的代码，采用函数封装也有好处：它**为每一小段具有特定功能的代码起了一个清晰表意的名字**。当我看到`process_content`时，显然要比看到`process3(process2(process1(content)))`更容易理解这段代码的意思。同理，当我看到`logged_read_excel`时，也显然要比看到`read_excel`加上一些用于计时和向终端打印输出的语句更为清晰。
