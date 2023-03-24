# 抽象

## 封装与副作用

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

## 重构

实际上，我们上面的这个过程也演示了什么叫做“**重构（reconstruction）**”。很多人会对老板说“我之前写的代码已经太乱了，很难维护，我想我需要大约两个星期的时间进行**重构**，在这段时间里，代码是无法运行的”。事实上，这不叫做“**重构**”，而叫做“**重写**”。“重构”是一步一步将混乱的代码变得清晰可维护的过程，这是一个小步渐进式的过程，在这个过程中的任何一步，代码都是可以运行的。

这种**小步渐进式的重构**有很大的好处。每一步完成后，你都可以运行程序，看看程序是否按照期望运行，或者更规范一点——运行一遍测试，看看有没有引入新的 BUG。这样就可以尽可能小地降低修改代码引入新 BUG 地可能性，并且即使出现了新 BUG，你也可以知道是在哪一个小步引入的 BUG，这样就很容易排查 BUG 的位置。而重写不是这样，你无法知道新程序是否会具有比老程序更多的 BUG，并且即使出现了 BUG，也很难排查。因此，我们说**重构优于重写**。

## 那么，到底什么是抽象？

封装是一种典型的抽象模式。除了上面提到的函数封装，面向对象中的“类”也是一种封装形式。这里不详谈面向对象，也就不给出例子了。

那么，具体什么是“**抽象（abstraction）**”？

抽象是人类构建复杂事物的基本方式。这不仅局限于编程，甚至也不仅局限在计算机科学上。当我们使用自然数计数时，实际上就已经是一种抽象。当我们使用计算机编程时，其实也有很多抽象层级在起作用，从硬件层的电子元件到电信号抽象，到操作系统层的硬件到软件的抽象，再到编程语言从机器语言到易于人类理解的高级语言的抽象，这都是抽象。抽象不是什么复杂与深奥的概念，恰恰相反，它是一个非常基本与简单的概念，是抽象组成了我们现代化的世界。

在具体的编码过程中，我们首先将重复的代码段抽象为“**函数（Function）**”（或者换一种颇有历史意味的说法，过程），然后对于某些执行特定功能的、具有“**状态（State）**”的、难以抽象为函数的部分，抽象为**对象**（事实上，对象最重要的地方就在于其具有**状态**）。

再往上，我们发现许多对象、函数之间的组织形式都是相似的。因此，我们将这些组织对象、函数的**惯例**归纳为“**设计模式（Design Patterns）**”。

1994 年，出版了第一本具有广泛影响力的，有关于设计模式的书《设计模式：可复用面向对象软件的基础》，这本书由四位共同作者 Gamma, Erich; Helm, Richard; Johnson, Ralph; Vlissides, John 编写，因此后来人们将这四位作者称为“四人帮（Gang of Four, GoF）”，并将这本书中提到的 23 种设计模式称为“**GoF 的 23 种设计模式**”。

但是，设计模式当然不只是“23 种”。事实上，很多人都将设计模式等同于 GoF 设计模式。然而，作为一种组织代码的“惯例”，设计模式显然是无限的，而不只有这 23 种。因此，我们要清楚 GoF 的 23 种设计模式是经典且应该了解的，但也要知道 GoF 当初提出 23 种设计模式只是为了“抛砖引玉”，引导人们总结出更多“设计模式”，而绝非将设计模式限制在“23 种”。

再往上一些，人们将通用的、不同软件具有的相似的形式称为“**软件架构（Software Architecture）**”，或者将这个英文单词翻译为另一种形式，即“软件体系结构”，它们指的实际上是一种东西，只不过工业界常常使用前一种翻译，而学术界采用后一种。

很难说在未来，当软件变得更加复杂，是否会出现比架构更高一级的抽象形式，或许它会叫“元架构”？没人知道。但是我们应当明白，抽象是我们构建软件的基本方式，我们应当理解并记住在程序设计的过程中使用抽象。
