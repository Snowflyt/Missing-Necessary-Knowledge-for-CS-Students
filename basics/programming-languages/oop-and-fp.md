# 面向对象与函数式编程

有两种基本的“**编程范式（Programming Paradigms）**”：“**命令式编程（Imperative Programming）**”与“**声明式编程（Declarative Programming）**”.

然而实际中，我们常常讨论的是“**面向对象编程（Object-Oriented Programming）**”与“**函数式编程（Functional Programming）**”，它们分别代表了命令式编程与声明式编程。实际上，命令式编程除了面向对象编程，还包含传统的过程式编程，如 C 语言的通常编程方式；而声明式编程，也包含了逻辑式编程、数学编程及响应式编程等方式。

在这里，简单展示一下面向对象编程与函数式编程的差异。为了易于理解，这里都使用了 Java：

```java
public class Words {
    private Set<String> NON_WORDS = new HashSet<>() {{
        add("the"); add("and"); add("of"); add("to"); add("a");
        add("i"); add("it"); add("in"); add("or"); add("is");
        add("d"); add("s"); add("as"); add("so"); add("but");
        add("be");
    }};

    public Map<String, Integer> wordFreq(String words) {
        TreeMap<String, Integer> wordMap = new TreeMap<>();
        Matcher m = Pattern.compile("\\w+").matcher(words);
        while (m.find()) {
            String word = m.group().toLowerCase();
            if (!NON_WORDS.contains(word)) {
                if (wordMap.containsKey(word)) {
                    wordMap.put(word, wordMap.get(word) + 1);
                } else {
                    wordMap.put(word, 1);
                }
            }
        }
        return wordMap;
    }
 }
```

```java
private List<String> regexToList(String words, String regex) {
    List<String> wordList = new ArrayList<>();
    Matcher m = Pattern.compile(regex).matcher(words);
    while (m.find()) {
        wordList.add(m.group());
    }
    return wordList;
}

public Map<String, Integer> wordFreq(String words) {
    TreeMap<String, Integer> wordMap = new TreeMap<>();
    regexToList(words, "\\w+").stream()
            .map(String::toLowerCase)
            .filter(word -> !NON_WORDS.contains(word))
            .forEach(word -> wordMap.put(
                    word,
                    wordMap.getOrDefault(word, 0) + 1
            ));
    return wordMap;
}
```

可以看到，后者相比于前者的嵌套层级更少。并且它们具有本质上的区别：前者描述了程序工作的具体细节，描述“该怎么做”，而后者描述了程序应该输出什么，通过一个个变换描述“想要得到什么”。

实际上，真正函数式风格的代码要比这里的 Java 代码简洁得多。比如这是一段用 Clojure 编写的，处理一个员工姓名列表的代码：

```clojure
(defn process [list-of-emps]
  (->> list-of-emps
    (filter #(< 1 (count %)))
    (map s/capitalize)
    (interpose ", ")
    (reduce str)))
```

通常认为函数式编程提供了一种比面向对象编程更高阶的抽象方式。在面向对象编程中，我们传递对象，使用对象上的方法来完成某一功能；而在函数式编程中，我们通过组合一系列函数实现某一功能。实际上，这两种方式应该并无明确的好坏之分，而仅仅代表了两种思维方式的差异。

目前普遍的观点认为，对于业务中数据的处理，使用偏函数式编程的模式能够更清晰地表达逻辑。而对于大型程序各部分之间的组织，使用面向对象的思路会更加合适。

不过，许多观点也认为，使用函数式编程同样能够清晰地组织大规模的程序，例如使用“**领域驱动设计（Design Driven Design, DDD）**”，这是一种通用的设计程序的形式，而不仅仅限于某种具体的编程范式，只是人们已经习惯于使用面向对象的思想来组织程序了。

不过，关键是要清楚这两种编程范式并非对立的，而是可以结合使用的。在程序中恰当地使用函数式编程思想，往往能使我们的代码变得更加清晰且易于维护。
