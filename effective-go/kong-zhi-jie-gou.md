# 控制结构

Go 的控制结构类似 C，但是有几个重要的不同点。Go 语言没有 do 和 while 循环，只有 for；switch 更灵活；if 和 switch 可以像 for 那样接收一个可选的初始化语句；break 和 continue 语句可以接收一个可选的 label，指明谁要被 break 或 continue；新的控制结构包括 type switch 和多路选择器 select。语法也有些微的区别：不用小括号、控制体一定要用大括号包裹。

## if

在 Go 中，一个简单的 if 语句如下：

```golang
if x > 0 {
    return y
}
```

强制使用大括号鼓励把简单的 if 语句写成多行。这是一种好的风格，尤其当控制体中有控制语句时，例如 return 或 break。

因为 if 和 switch 可以接收一个初始化语句，所以经常会看到初始化一个本地变量这种操作。

```golang
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

在 Go 的库中，你会发现，如果 if 中的语句不会执行到下一句，即 if 的最后一句是 break，continue，goto 或者 return，那么不必要的 else 需要省略。

```golang
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```

这个例子展现了一个很常见的场景：代码需要防卫一连串的错误条件。成功的流程顺序执行，如果遇到错误就抛出。这种代码的可读性就很好。因为错误情况总是以 return 语句结束，剩下的代码就不需要 else

```golang
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)
```

## 重复声明、重复赋值

上一节的例子中同样展示了`:=`短声明形式的使用方法。调用 os.Open 的那句是：
`f, err := os.Open(name)`
这句声明了两个变量：f, err。隔几行调用 f.Stat：
`d, err := f.Stat()`
看起来他好像声明了 d 和 err，而且注意到 err 出现了两次。这种重复是合法的：err 被第一句声明，在第二句里只是重复赋值。这意味着调用 f.Stat 这句时，使用的是上面那句声明的 err，只是给他赋了新值。

满足以下条件，变量 v 即使已经被声明也可以出现在:=中：

* 这个声明和已经存在的声明在同一个作用域内（如果 v 是在外面的作用域中声明，则本次声明会创建一个新变量）[1]
* 初始化表达式中的变量值可以赋值给 v，声明中至少还要有一个新声明的变量

这种特性纯粹是为了实用，在一连串的 if-else 中，可以简单的使用一个 err 变量。你会经常遇到这种写法。

[1] 需要说明的是，在 Go 中，函数的入参列表和返回值和函数体是相同的作用域，尽管他们看起来是在大括号外面。

## For

Go 的 for 循环类似 C，但又有不同。它统一了 for 和 while，因此没有 do-while 循环。有以下三种形式，但是只有一种形式有分号。

```golang
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```

短声明语法使得在循环中声明 index 变量很简单：

```golang
sum := 0
for i:=0; i < 10; i++{
    sum += i
}
```

如果你遍历一个 array、slice、String、map 或者从 channel 里读数据，range 分句可以用在循环里：

```golang
for key, value := range oldMap {
    newMap[key] = value
}
```

如果你只需要 range 中的第一个值（key 或者 index）简单的省略第二个值就好了：

```golang
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

如果你只需要第二个值（value）使用空白标识符，一个下划线：

```golang
sum := 0
for _, value := range array {
    sum += value
}
```

空白标识符有很多用处，后面会提到。

对于字符串，range 帮你做了更多事情。通过解析 utf-8,分拆出每个 Unicode 码点。错误的编码占用一个 byte，值为 rune（U+FFFD）（rune 是 Go 的术语，指代单个 Unicode 码点）

```golang
for pos, char := range "日本\x80語" { // \x80 is an illegal UTF-8 encoding
    fmt.Printf("character %#U starts at byte position %d\n", char, pos)
}
```

运行结果为：

```plain
character U+65E5 '日' starts at byte position 0
character U+672C '本' starts at byte position 3
character U+FFFD '�' starts at byte position 6
character U+8A9E '語' starts at byte position 7
```

最后要说明的是，Go 没有逗号操作符，++和--是语句不是表达式。所以如果你想在 for 循环中使用多个变量，只能使用多值赋值。

```golang
//Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

## Switch

Go 的 switch 比 C 的更通用。表达式不需要是常数或整形，case 分句会自顶向下的判断，直到遇到一个匹配的，如果 switch 没有表达式则默认表达式为 true。因此可以把 if-else-if-else 链改写成 switch，这样更符合 Go 的惯用法。

```golang
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

Go 的 switch 没有自动向下执行的机制，但是 case 语句可以提供一个逗号隔开的列表：

```golang
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

break 语句可以用来尽早的结束 switch 执行，尽管在 Go 语言中并不像其他类 C 语言那样常用。有时，需要 break 的是外部的循环而不是 switch，在 Go 语言中可以通过给循环设置一个标签，然后“break”到这个标签上。以下例子展示了这种用法：

```golang
Loop:
    for n := 0; n < len(src); n += size {
        switch {
        case src[n] < sizeOne:
            if validateOnly {
                break
            }
            size = 1
            update(src[n])

        case src[n] < sizeTwo:
            if n+1 >= len(src) {
                err = errShortInput
                break Loop
            }
            if validateOnly {
                break
            }
            size = 2
            update(src[n] + src[n+1]<<shift)
        }
    }
```

当然，continue 语句也可以接受一个可选的标签，但只能在循环里用。

用一个比较 byte 切片的 routine 结束本节：

```golang
// Compare returns an integer comparing the two byte slices,
// lexicographically.
// The result will be 0 if a == b, -1 if a < b, and +1 if a > b
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

## Type switch

switch 同样可以用来发现接口变量的动态类型。这种 type switch 使用一种类型断言语法，小括号加一个 type 关键词。如果 switch 在表达式中定义了一个变量，那么在每个分句中，这个变量都是对应的类型。在每个 case 中重用这个名字是符合语言习惯的。事实上，在每个 case 分句中会声明一个新的同名的变量，只是类型不同。

```golang
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}
```