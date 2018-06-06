# 注释

Go 提供 C 风格的 `/* */` 块注释和 C++风格的 `//` 行注释。行注释更常用；块注释几乎只作为包注释在用，但是在表达式内部使用以及注释大量代码时也比较常用。

godoc 程序会处理 Go 的源文件，导出包内容的文档。顶层声明前的注释（两者间不能有空行）会被导出成该声明的解释性文档。这些注释的样式和内容决定了 godoc 导出的文档的质量。

每个包都应该有包注释。包注释是写在 package 语句前的块注释。多文件的包，包注释只需要出现在一个文件中就可以。包注释应该介绍一下这个包，从总体上给出和这个包相关的信息。它会出现在 godoc 页的最前面。

```go
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp
```

如果这个包比较简单，那么包注释也可以比较简短：

```golang
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```

注释不需要额外的格式化，例如在左边放一排星号这种。生成的输出甚至有可能不是定宽字体，所以不要使用空格来排版，godoc 类似 gofmt，会帮你处理这些。注释会被视为纯文本，不会被解析，所以像 HTML 或其他标签都会原封不动的输出，例如`_this_`这种，输出依然是`_this_`。godoc 会做的一个调整是，它会把缩进的文本用定宽字体来显示，这样更适合显示代码片段。

godoc 的格式化完全由注释内容决定，甚至有的注释 godoc 根本不会 reformat。所以，在写注释的时候请确保它的整洁：使用正确的拼写、标点和句子结构，长的行要分行等等。

在包内，紧邻顶级声明前的注释会自动作为这个声明的文档注释。程序中每个导出的变量都应该有文档注释。

文档注释最好是完整的句子，可以适应多样的自动化展示。注释的第一句话应该以被定义的名字开头，作为一句话的总结，下面是个例子：

```golang
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```

如果每个文档注释都是以它描述的对象开头，godoc 产生的输出就会更适合用 grep 搜索。设想一下你忘记了函数"Compile"的名字，但是想找解析正则表达式的函数，你就可以用以下命令来找：

```bash
godoc regexp | grep -i parse
```

如果这个包里的所有函数的开头都是"This function"，grep 命令就没什么帮助了。但正是因为这个包的文档注释都是以变量名开始，你就能看到以下结果，这会帮你回忆起你想找的那个词。

```bash
$ godoc regexp | grep parse
    Compile parses a regular expression and returns, if successful, a Regexp
    parsed. It simplifies safe initialization of global variables holding
    cannot be parsed. It simplifies safe initialization of global variables
$
```

Go 语言的声明语法允许对声明分组。一行文档注释可以给一组相关的常数或变量注释，但是因为要给一组声明注释，所以这种注释都比较笼统。

```golang
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)
```

分组也可以暗示组员之间的关系，比如一组受信号量保护的变量：

```golang
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```
