<div id="comments"></div>

# 注释

块注释放在 `(*` 和 `*)` 之间。行注释从 `//` 开始，一直持续到行尾。

```fsharp
(* 这是块注释 *)

// 而这是一个行注释
```

XML 文档注释在 `///` 之后，允许我们使用 XML 标签生成文档。

```fsharp
/// 将一个数字翻倍并加1
let myFunction n = n * 2 + 1
```

<div id="strings"></div>

# 字符串

F# 的 `string` 类型是 `System.String` 类型的别名。

```fsharp
// 使用字符串连接创建字符串
let hello = "Hello" + " World"
```

使用带有 `@` 符号前缀的*逐字字符串*可以避免转义控制字符（除了使用 `""` 转义 `"` 之外）。

```fsharp
let verbatimXml = @"<book title=""Paradise Lost"">"
```

使用*三引号字符串*时，我们甚至不需要转义 `"`。

```fsharp
let tripleXml = """<book title="Paradise Lost">"""
```

*反斜杠字符串*通过去除前导空格来缩进字符串内容。

```fsharp
let poem =
    "The lesser world was daubed\n\
        By a colorist of modest skill\n\
        A master limned you in the finest inks\n\
        And with a fresh-cut quill."
```

*字符串切片*通过使用 `[start..end]` 语法来支持。

```fsharp
let str = "Hello World"
let firstWord = str[0..4] // "Hello"
let lastWord = str[6..] // "World"
```

*字符串插值*通过在字符串前添加 `$` 符号来支持。以下所有示例都会输出 `"Hello" \ World!`：

```fsharp
let expr = "Hello"
printfn " \"%s\" \\ World!" expr
printfn $" \"{expr}\" \\ World!"
printfn $" \"%s{expr}\" \\ World!" // 使用格式说明符
printfn $@" ""{expr}"" \ World!"
printfn $@" ""%s{expr}"" \ World!"
printf  $@" ""%s{expr}"" \ World!"  // 无换行
```

有关转义字符、字节数组和格式说明符的更多信息，请参阅 
[Strings (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/strings)。

<div id="basic-types-and-literals"></div>

# 基本类型和字面量

使用 `let` 关键字定义值。默认情况下，值是不可变的，但如果使用 `mutable` 关键字指定，则可以修改。

```fsharp
let myStringValue = "my string"
let myIntValue = 10
let myExplicitlyTypedIntValue: int = 10
let mutable myMutableInt = 10
myMutableInt <- 11  // 使用 <- 箭头赋新值
```

*整数前缀*用于十六进制、八进制或二进制

```fsharp
let numbers = (0x9F, 0o77, 0b1010)  // (159, 63, 10)
```

*字面量类型后缀*用于整数、浮点数、小数和 ascii 数组

```fsharp
let ( sbyte, byte   )  = ( 55y, 55uy )  // 8位整数

let ( short, ushort )  = ( 50s, 50us )  // 16位整数

let ( int,   uint   )  = ( 50,  50u  )  // 32位整数

let ( long,  ulong  )  = ( 50L, 50uL )  // 64位整数

let bigInt             = 9999999999999I // System.Numerics.BigInteger

let float              = 50.0f          // 有符号32位浮点数

let double             = 50.0           // 有符号64位浮点数

let scientific         = 2.3E+32        // 有符号64位浮点数

let decimal            = 50.0m          // 有符号128位小数

let byte               = 'a'B           // ascii 字符; 97uy

let byteArray          = "text"B        // ascii 字符串; [|116uy; 101uy; 120uy; 116uy|]
```

*撇号*（或标签名称末尾的单引号 `'`）是函数式语言的惯用写法，在 F# 中也有包含。
它们是标识符名称的一部分，仅向开发者表明是现有值或函数的变体。例如：

```fsharp
let x = 5
let x' = x + 1
let x'' = x' + 1
```

完整参考请参见 [Literals (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/literals)。

<div id="functions"></div>

# 函数

## `let` 绑定

使用 `let` 关键字定义命名函数。管道和组合运算符

```fsharp
let add n1 n2 = n1 + n2
let subtract n1 n2 = n1 - n2
let negate num = -1 * num
let print num = printfn $"The number is: {num}"
```
## 管道和组合运算符

管道运算符 `|>` 用于将函数和参数链接在一起。

```fsharp
let addTwoSubtractTwoNegateAndPrint num =
    num |> add 2 |> subtract 2 |> negate |> print
```

组合运算符 `>>` 用于组合函数：

```fsharp
let addTwoSubtractTwoNegateAndPrint' =
    add 2 >> subtract 2 >> negate >> print
```

注意：输出是下一个函数的 _最后一个_ 参数。

```fsharp
// `addTwoSubtractTwoNegateAndPrint 10` 变为：
10
|> add 2       //  2 + 10  = 12
|> subtract 2  //  2 - 12  = -10
|> negate      // -1 * -10 = 10
|> print       // "The number is 10"
```

## 匿名函数

匿名函数（也称为“lambda”函数）由 `fun` 关键字和箭头运算符 `->` 表示。

```fsharp
let isDescending xs =
    xs
    |> List.pairwise
    |> List.forAll (fun (x, y) -> x > y)

let suspiciousRecords =
    records
    |> Seq.filter (fun x -> x.Age >= 150)
```
### _.Property 简写

如果 lambda 函数只有一个参数，且该参数用于原子表达式中，自 F# 8 起可以使用以下简写形式：

```fsharp
let names =
    people
    |> List.map (fun person -> person.Name)   // 常规 lambda 表达式

let names' =
    people
    |> List.map _.Name    // _.Property 简写
```

只要表达式中没有“空格”，你可以链式调用属性和方法。例如：

```fsharp
let uppercaseNames =
    people |> List.map _.Name.ToUpperInvariant()
```

<div id="functions-unit-type"></div>

## `unit` 类型

`unit` 类型是一种表示不存在特定值的类型，由 `()` 表示。
最常见的用法是当你有一个不接收参数的函数，但需要它在每次调用时都重新求值：

```fsharp
// 没有 unit 时，DateTime.Now 只被求值一次。返回值永远不会改变。
let getCurrentDateTime = DateTime.Now

// 这个版本在每次用 `unit` 参数调用时都会求值 DateTime.Now。
let getCurrentDateTime2 () = DateTime.Now

// 如何调用该函数：
let startTime = getCurrentDateTime2 ()
```

<div id="functions-signatures"></div>

## 函数签名和显式类型

函数签名对于快速了解函数的输入和输出非常有用。最后一个类型是返回类型，所有前面的类型是输入类型。

```fsharp
int -> string                       // 定义了一个接收整数并返回字符串的函数
int -> int -> string                // 两个整数输入，返回字符串
unit -> string                      // unit 输入，返回字符串
string -> unit                      // 接受字符串，无返回值
(int * string) -> string -> string  // 一个包含 int 和 string 的元组，以及一个 string 输入，返回字符串
```

大多数情况下，编译器可以确定参数的类型，但有些情况下你可能希望显式指定类型，或者编译器需要帮助。
下面是一个签名为 `string -> char -> int` 的函数，其输入和返回类型都已显式指定：

```fsharp
let countWordsStartingWithLetter (theString: string) (theLetter: char) : int =
    theString.Split ' '
    |> Seq.where (fun (word: string) -> word.StartsWith theLetter)  // lambda 中的显式类型
    |> Seq.length
```

接受 [`unit`](#functions-unit-type) 作为参数并返回不同 [集合](#collections) 类型的函数示例：

```fsharp
let getList (): int list = ...  // unit -> int list
let getArray (): int[] = ...
let getSeq (): seq<int> = ...
```

带有 [匿名记录](#data-types-anonymous-records) 的复杂声明：

```fsharp
let anonRecordFunc (record: {| Count: int; LeftAndRight: bigint * bigint |}) =
    ...
```

<div id="functions-recursive"></div>

## 递归函数

`rec` 关键字与 `let` 关键字一起使用来定义递归函数：

```fsharp
let rec fact x =
    if x < 1 then 1
    else x * fact (x - 1)
```

### TailCallAttribute

在 _尾递归_ 函数中，递归调用是函数中的最终操作，其结果直接返回，没有嵌套的函数调用
（以及由此带来的栈使用）。这种模式允许编译器通过重用当前栈帧而不是为每次调用分配
新栈帧，来生成嵌套调用的等效循环。

作为一种保障措施，你可以使用“TailCall”属性（自 F# 8 起）。

默认情况下，如果将此属性用于非正确尾递归的函数，编译器会发出警告。通常，将此警告
提升为错误是个好主意，可以在项目文件中设置，或者通过使用 
[compiler option](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/compiler-messages/) 来实现。

如果我们将此属性添加到前面的示例中：

```fsharp
[<TailCall>]
let rec fact x =
    if x < 1 then 1
    else x * fact (x - 1)
```
...编译器会给出以下警告：
```
Warning FS3569 : The member or function 'fact' has the 'TailCallAttribute' attribute, but is not being used in a tail recursive way.
```
然而，当通过使用累加器参数重构为正确的尾递归函数时，警告会消失：
```fsharp
[<TailCall>]
let rec factTail acc x =
    if x < 1 then acc
    else factTail (acc * x) (x - 1)
```

### 相互递归函数

相互调用的函数对或函数组通过 `rec` 和 `and` 关键字来表示：

```fsharp
let rec even x =
    if x = 0 then true
    else odd (x - 1)

and odd x =
    if x = 0 then false
    else even (x - 1)
```



<div id="statically-resolved-type-parameters"></div>

## 静态解析类型参数

*静态解析类型参数* 是一种在编译时而非运行时替换为实际类型的类型参数。它们的主要用途是与成员约束配合使用。

```fsharp
let inline add x y = x + y
let integerAdd = add 1 2
let floatAdd = add 1.0f 2.0f // 如果 `add` 函数没有 `inline`，这会导致类型错误
```

```fsharp
type RequestA = { Id: string; StringValue: string }
type RequestB = { Id: string; IntValue: int }

let requestA: RequestA = { Id = "A"; StringValue = "Value" }
let requestB: RequestB = { Id = "B"; IntValue = 42 }

let inline getId<'T when 'T : (member Id: string)> (x: 'T) = x.Id

let idA = getId requestA  // "A"
let idB = getId requestB  // "B"
```

有关更多示例，请参阅 [静态解析类型参数（MS Learn）](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/generics/statically-resolved-type-parameters)和 [约束（MS Learn）](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/generics/constraints)。

<div id="collections"></div>

# 集合

<div id="collections-lists"></div>

## 列表

*列表*是相同类型元素的不可变集合；在内部实现为链表。

```fsharp
// Create
let list1 = [ "a"; "b" ]
let list2 =
    [ 1
        2 ]
let list3 = "c" :: list1   // 前置添加；[ "c"; "a"; "b" ]
let list4 = list1 @ list3  // 连接；[ "a"; "b"; "c"; "a"; "b" ]
let list5 = [ 1..2..9 ]    // 开始..增量..结束；[ 1; 3; 5; 7; 9 ]

// 切片是包含性的
let firstTwo = list5[0..1]  // [ 1; 3 ]

// 模式匹配
match myList with
| [] -> ...            // 空列表
| [ 3 ] -> ...         // 单个元素，值为 '3'
| [ _; 4 ] -> ...      // 两个元素，第二个元素为 '4'
| head :: tail -> ...  // cons 模式；匹配非空列表。`head` 是第一个元素，`tail` 是剩余部分

// 使用 cons 模式的列表尾递归
[<TailCall>]
let rec sumEachItem' (acc:int) (myList:int list) =
    match myList with
    | [] -> acc
    | head :: tail -> sumEachItem' (acc + head) tail
let sumEachItem (myList:int list) = sumEachItem' 0 myList
```

有关更多信息，请参阅 [Lists (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/lists)。有关内置函数，请参阅 [List Module](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-collections-listmodule.html)。

<div id="collections-arrays"></div>

## 数组

*数组*是固定大小、从零开始的连续数据元素集合，作为一个内存块维护。它们是*可变的*；可以更改单个元素。

```fsharp
// 创建
let array1 = [| "a"; "b"; "c" |]
let array2 =
    [| 1
        2 |]
let array3 = [| 1..2..9 |]  // 开始..增量..结束；[| 1; 3; 5; 7; 9 |]

// 索引访问
let first = array1[0]  // "a"

// 切片是包含性的；[| "a"; "b" |]
let firstTwo = array1[0..1]

// 使用 `<-` 赋值
array1[1] <- "d"  // [| "a"; "d"; "c" |]

// 模式匹配
match myArray with
| [||] -> ...        // 匹配空数组
| [| 3 |] -> ...     // 匹配包含单个 3 元素的数组
| [| _; 4 |] -> ...  // 匹配包含 2 个元素的数组，第二个元素 = 4
```

有关更多信息，请参阅 [Arrays (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/arrays)。有关内置函数，请参阅 [Array Module](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-collections-arraymodule.html)。

<div id="collections-sequences"></div>

## 序列

*序列*是相同类型元素的逻辑系列。序列是延迟计算的，仅在需要时才计算单个元素。
`seq<'t>` 是 [`System.Collections.Generic.IEnumerable<'t>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1) 的别名。

```fsharp
let emptySeq : int seq = seq { } // 空序列在 F# 9 中可用
let seq1 = { 1; 2 }
let seq2 = seq {
        1
        2 }
let seq3 = seq { 1..2..9 }  // 开始..增量..结束； 1,3,5,7,9
```

有关更多信息，请参阅 [Sequences (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/sequences)。
有关内置函数，请参阅 [Seq Module](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-collections-seqmodule.html)。

## 集合推导式

- 使用 `->` 的计算表达式。结果为 _1, 3, 5, 7, 9_
    ```fsharp
    let listComp  = [ for i in 0..4 -> 2 * i + 1 ]
    let arrayComp = [| for i in 0..4 -> 2 * i + 1 |]
    let seqComp   = seq { for i in 0..4 -> 2 * i + 1 }
    ```

- 用带有 `yield` 和 `yield!` 的计算表达式。（在 `do` 中 `yield` 是可选的，但此处显式使用）：
    ```fsharp
    let comprehendedList = [  // [ 1;3;5;7;9 ]
        for i in 0..4 do
            yield 2 * i + 1
        ]
    let comprehendedArray = [|  // [| 1;3;5;7;9;1;3;5;7;9 |]
        for i in 0..4 do
            yield 2 * i + 1
        yield! comprehendedList
        |]
    let comprehendedSequence = seq {  // seq { 1;3;5;7;9;1;3;5;7;9;.... }
        while true do
            yield! listWithYield
        }
    ```

<div id="data-types"></div>

# 数据类型

<div id="data-types-tuples"></div>

## 元组

*元组*是一组未命名但有序的值，可能具有不同类型：

```fsharp
// 构造
let numberAndWord = (1, "Hello")
let numberAndWordAndNow = (1, "Hello", System.DateTime.Now)

// 解构
let (number, word) = numberAndWord
let (_, _, now) = numberAndWordAndNow

// 用于两个元素元组的 fst 和 snd 函数：
let number = fst numberAndWord
let word = snd numberAndWord

// 模式匹配
let printNumberAndWord numberAndWord =
    match numberAndWord with
    | (1, word) -> printfn $"One: %s{word}"
    | (2, word) -> printfn $"Two: %s{word}"
    | (_, word) -> printfn $"Number: %s{word}"

// 函数参数解构
let printNumberAndWord' (number, word) = printfn $"%d{number}: %s{word}"
```

在 C# 中，如果方法有 `out` 参数（例如 [`DateTime.TryParse`](https://learn.microsoft.com/en-us/dotnet/api/system.datetime.tryparse)），`out` 结果将成为元组的一部分。

```fsharp
let (success, outParsedDateTime) = System.DateTime.TryParse("2001/02/06")
```

有关更多信息，请参阅 [Tuples (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/tuples)。

<div id="data-types-records"></div>

## 记录类型

*记录类型*表示命名值的聚合。它们是带有额外特性的密封类：默认不可变性、结构相等性和模式匹配支持。

```fsharp
// 声明
type Person = { Name: string; Age: int }
type Car =
    { Make: string
        Model: string
        Year: int }

// 创建
let paul = { Name = "Paul"; Age = 28 }

// 复制和更新
let paulsTwin = { paul with Name = "Jim" }

// 内置相等性
let evilPaul = { Name = "Paul"; Age = 28 }
paul = evilPaul  // true

// 模式匹配
let isPaul person =
    match person with
    | { Name = "Paul" } -> true
    | _ -> false
```

有关更多信息，包括基于 `struct` 的记录，请参阅 [Records (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/records)。

<div id="data-types-anonymous-records"></div>

## 匿名记录

*匿名记录*表示命名值的聚合，但不需要在使用前声明。

```fsharp
// 创建
let anonRecord1 = {| Name = "Don Syme"; Language = "F#"; Age = 999 |}

// 复制和更新
let anonRecord2 = {| anonRecord1 with Name = "Mads Torgersen"; Language = "C#" |}

let getCircleStats (radius: float) =
    {| Radius = radius
        Diameter = radius * 2.0
        Area = System.Math.PI * (radius ** 2.0)
        Circumference = 2.0 * System.Math.PI * radius |}

// 签名
let printCircleStats (circle: {| Radius: float; Area: float; Circumference: float; Diameter: float |}) =
    printfn $"Circle with R=%f{circle.Radius}; D=%f{circle.Diameter}; A=%f{circle.Area}; C=%f{circle.Circumference}"

let cc = getCircleStats 2.0
printCircleStats cc
```

有关更多信息，包括基于 `struct` 的匿名记录，请参阅 [Anonymous Records (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/anonymous-records)。

<div id="data-types-discriminated-unions"></div>

## 可区分联合

*可区分联合* (DU) 提供对值的支持，这些值可以是多个命名情况之一，每个情况可能具有不同的值和类型。

```fsharp
// 声明
type Interaction =
    | Keyboard of char
    | KeyboardWithModifier of char * modifier: System.ConsoleModifiers
    | MouseClick of countOfClicks: int

// 创建
let interaction1 = MouseClick 1
let interaction2 = MouseClick (countOfClicks = 2)
let interaction3 = KeyboardWithModifier ('c', System.ConsoleModifiers.Control)

// 模式匹配
match interaction3 with
| Keyboard chr -> $"Character: {chr}"
| KeyboardWithModifier (chr, modifier) -> $"Character: {modifier}+{chr}"
| MouseClick (countOfClicks = 1) -> "Click"
| MouseClick (countOfClicks = x) -> $"Clicked: {x}"
```

泛型

```fsharp
type Tree<'T> =
    | Node of Tree<'T> * 'T * Tree<'T>
    | Leaf

let rec depth =
    match depth with
    | Node (l, _, r) -> 1 + max (depth l) (depth r)
    | Leaf -> 0
```

F# Core 为错误处理提供了内置的可区分联合，例如 [`option`](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/options) 和 [`Result`](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/results)。

```fsharp
let optionPatternMatch input =
    match input with
    | Some value -> printfn $"input is %d{value}"
    | None -> printfn "input is missing"

let resultPatternMatch input =
    match input with
    | Ok value -> $"Input: %d{value}"
    | Error value -> $"Error: %d{value}"
```

单例可区分联合通常用于创建具有模式匹配支持的类型安全抽象：

```fsharp
type OrderId = Order of string

// 创建 DU 值
let orderId = Order "12"

// 使用模式匹配解构单例 DU
let (Order id) = orderId  // id = "12"
```

有关更多信息，请参阅 [Discriminated Unions](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions)。

<div id="pattern-matching"></div>

# 模式匹配

模式是一个核心概念，使 F# 语言和其他 ML 语言非常强大。
它们出现在 `let` 绑定、`match` 表达式、 lambda 表达式和[异常](#exceptions)中。

匹配按从上到下、从左到右的顺序评估；选择第一个匹配的模式。

[集合](#collections)和[数据类型](#data-types)中的模式匹配示例可以在相应部分找到。
以下是一些其他模式：

```fsharp
match intValue with
| 0 -> "Zero"                  // 常量模式
| 1 | 2 -> "One or Two"        // 带常量的 OR 模式
| x -> $"Something else: {x}"  // 变量模式；将值分配给 x

match tupleValue with
| (_ ,3) & (x, y) -> $"{x}, 3"  // 带常量和变量的 AND 模式；匹配 3 并将 3 分配给 x
| _ -> "Wildcard"               // 下划线匹配任何内容
```

## `when` 守卫子句

为了匹配复杂的输入，可以使用 `when` 为模式创建过滤器或守卫：

```fsharp
match num with
| 0 -> 0
| x when x < 0 -> -1
| x -> 1
```

## 模式匹配 `function`

可以使用 `function` 语句简化 `let..match..with` 语句：

```fsharp
let filterNumbers num =
    match num with
        | 1 | 2 | 3 -> printfn "Found 1, 2, or 3!"
        | a -> printfn "%d" a

let filterNumbers' =  // 参数和 `match num with` 被组合
    function | 1 | 2 | 3 -> printfn "Found 1, 2, or 3!"
             | a -> printfn "%d" a
```

有关更多信息，请参阅 [Pattern Matching (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/pattern-matching) 。

<div id="exceptions"></div>

# Exceptions

## Try..With

An illustrative example with: custom F# exception creation, all exception aliases, `raise` usage, and an exhaustive demonstration of the exception handler patterns:

```fsharp
open System
exception MyException of int * string // (1)
let guard = true

try
    failwith   "Message"                // throws a System.Exception (aka exn)
    nullArg    "ArgumentName"           // throws a System.ArgumentNullException
    invalidArg "ArgumentName" "Message" // throws a System.ArgumentException
    invalidOp  "Message"                // throws a System.InvalidOperation

    raise (NotImplementedException("Message")) // throws a .NET exception (2)
    raise (MyException(0, "Message"))          // throws an F# exception (2)

    true // (3)
with
| :? ArgumentNullException                      -> printfn "NullException"; false // (3)
| :? ArgumentException as ex                    -> printfn $"{ex.Message}"; false // (4)
| :? InvalidOperationException as ex when guard -> printfn $"{ex.Message}"; reraise () // (5,6)
| MyException(num, str) when guard              -> printfn $"{num}, {str}"; false // (5)
| MyException(num, str)                         -> printfn $"{num}, {str}"; reraise () // (6)
| ex when guard                                 -> printfn $"{ex.Message}"; false
| ex                                            -> printfn $"{ex.Message}"; false
```

1. define your own F# exception types with `exception`, a new type that will inherit from `System.Exception`;
2. use `raise` to throw (an F# or .NET) exception;
3. the entire `try..with` expression must evaluate to the same type, in this example: bool;
4. `ArgumentNullException` inherits from `ArgumentException`, so `ArgumentException` must follow after;
5. support for `when` guards;
6. use `reraise ()` to re-throw the exception being handled (retaining the original throw site in the stack trace); works with both .NET and F# exceptions

The difference between F# and .NET exceptions is how they are created and how they can be handled.

## Try..Finally

The `try..finally` expression enables you to execute clean-up code even if a block of code throws an exception. Here's an example that also defines custom exceptions.

```fsharp
exception InnerError of string
exception OuterError of string

let handleErrors x y =
    try
        try
            if x = y then raise (InnerError("inner"))
            else raise (OuterError("outer"))
        with
        | InnerError str -> printfn "Error1 %s" str
    finally
        printfn "Always print this."
```

Note that `finally` does not follow `with`. `try..with` and `try..finally` are separate expressions.

<div id="classes-and-inheritance"></div>

# Classes and Inheritance

This example is a basic class with (1) local let bindings, (2) properties, (3) methods, and (4) static members.

```fsharp
type Vector(x: float, y: float) =
    let mag = sqrt(x * x + y * y) // (1)
    member _.X = x // (2)
    member _.Y = y
    member _.Mag = mag
    member _.Scale(s) = // (3)
        Vector(x * s, y * s)
    static member (+) (a : Vector, b : Vector) = // (4)
        Vector(a.X + b.X, a.Y + b.Y)
```

Call a base class from a derived one.

```fsharp
type Animal() =
    member _.Rest() = ()

type Dog() =
    inherit Animal()
    member _.Run() =
        base.Rest()
```

*Upcasting* is denoted by `:>` operator.

```fsharp
let dog = Dog()
let animal = dog :> Animal
```

*Dynamic downcasting* (`:?>`) might throw an `InvalidCastException` if the cast doesn't succeed at runtime.

```fsharp
let shouldBeADog = animal :?> Dog
```

<div id="interfaces-and-object-expressions"></div>

# Interfaces and Object Expressions

Declare `IVector` interface and implement it in `Vector`.

```fsharp
type IVector =
    abstract Scale : float -> IVector

type Vector(x, y) =
    interface IVector with
        member _.Scale(s) =
            Vector(x * s, y * s) :> IVector
    member _.X = x
    member _.Y = y
```

Another way of implementing interfaces is to use *object expressions*.

```fsharp
type ICustomer =
    abstract Name : string
    abstract Age : int

let createCustomer name age =
    { new ICustomer with
        member _.Name = name
        member _.Age = age }
```

<div id="active-patterns"></div>

# Active Patterns

## Single-case active patterns

*Single-case active patterns* can be thought of as a simple way to convert data to a new form.

```fsharp
// Basic
let (|EmailDomain|) email =
    let match' = Regex.Match(email, "@(.*)$")
    if match'.Success
    then match'.Groups[1].ToString()
    else ""
let (EmailDomain emailDomain) = "yennefer@aretuza.org"  // emailDomain = 'aretuza.org'

// As Parameters
open System.Numerics
let (|Real|) (x: Complex) =
    (x.Real, x.Imaginary)
let addReal (Real (real1, _)) (Real (real2, _)) =  // conversion done in the parameters
    real1 + real2
let addRealOut = addReal Complex.ImaginaryOne Complex.ImaginaryOne

// Parameterized
let (|Default|) onNone value =
    match value with
    | None -> onNone
    | Some e -> e
let (Default "random citizen" name) = None // name = "random citizen"
let (Default "random citizen" name) = Some "Steve" // name = "Steve"
```

## Complete active patterns

```fsharp
let (|Even|Odd|) i =
    if i % 2 = 0 then Even else Odd

let testNumber i =
    match i with
    | Even -> printfn "%d is even" i
    | Odd -> printfn "%d is odd" i

let (|Phone|Email|) (s:string) =
    if s.Contains '@' then Email $"Email: {s}" else Phone $"Phone: {s}"

match "yennefer@aretuza.org" with // output: "Email: yennefer@aretuza.org"
| Email email -> printfn $"{email}"
| Phone phone -> printfn $"{phone}"
```

## Partial active patterns

*Partial active patterns* share the syntax of parameterized patterns, but their active recognizers accept only one argument.

A partial active pattern typically returns an `Option<'T>`. However, as of [F# 9](https://learn.microsoft.com/en-us/dotnet/fsharp/whats-new/fsharp-9#partial-active-patterns-can-return-bool-instead-of-unit-option), where no value is being returned, and there is only one success case, you may return a `bool` instead.

- `Option<T>`

    ```fsharp
    let (|DivisibleBy|_|) by n =
        if n % by = 0
        then Some DivisibleBy
        else None

    let fizzBuzz = function
        | DivisibleBy 3 & DivisibleBy 5 -> "FizzBuzz"
        | DivisibleBy 3 -> "Fizz"
        | DivisibleBy 5 -> "Buzz"
        | i -> string i
    ```

- `bool`

    ```fsharp
    let (|DivisibleBy|_|) by n = n % by = 0
    ```

    ```fsharp
    let (|EqualsIgnoreCase|_|) (pattern: string) (value: string) =
        String.Equals(value, pattern, StringComparison.OrdinalIgnoreCase)
    ```

See [Active Patterns (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/active-patterns) to learn more.

<div id="asynchronous-programming"></div>

# Asynchronous Programming

F# asynchronous programming support consists of two complementary mechanisms::
- .NET's Tasks (via `task { }` expressions). This provides semantics very close to that of C#'s `async`/`await` mechanism, requiring explicit direct management of `CancellationToken`s.
- F# native `Async` computations (via `async { }` expressions). Predates `Task`. Provides intrinsic `CancellationToken` propagation.

## .NET Tasks

In F#, .NET Tasks can be constructed using the `task { }` computational expression.
.NET Tasks are "hot" - they immediately start running. At the first `let!` or `do!`, the `Task<'T>` is returned and execution continues on the ThreadPool.

```fsharp
open System
open System.Threading
open System.Threading.Tasks
open System.IO

let readFile filename ct = task {
    printfn "Started Reading Task"
    do! Task.Delay((TimeSpan.FromSeconds 5), cancellationToken = ct)  // use do! when awaiting a Task
    let! text = File.ReadAllTextAsync(filename, ct)  // use let! when awaiting a Task<'T>, and unwrap 'T from Task<'T>.
    return text
}

let readFileTask: Task<string> = readFile "myfile.txt" CancellationToken.None  // (before return) Output: Started Reading Task

// (readFileTask continues execution on the ThreadPool)

let fileContent = readFileTask.Result  // Blocks thread and waits for content. (1)
let fileContent' = readFileTask.Result  // Task is already completed, returns same value immediately; no output
```

(1) `.Result` used for demonstration only. Read about [async/await Best Practices](https://learn.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way)

## Async Computations

Async computations were invented before .NET Tasks existed, which is why F# has two core methods for asynchronous programming. However, async computations did not become obsolete. They offer another, but different, approach: dataflow.
Async computations are constructed using `async { }` expressions, and the [`Async` module](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-control-fsharpasync.html#section3) is used to compose and execute them.
In contrast to .NET Tasks, async expressions are "cold" (need to be explicitly started) and every execution [propagates a CancellationToken implicitly](#asynchronous-programming-cancellation-async).

```fsharp
open System
open System.Threading
open System.IO

let readFile filename = async {
    do! Async.Sleep(TimeSpan.FromSeconds 5)  // use do! when awaiting an Async
    let! text = File.ReadAllTextAsync(filename) |> Async.AwaitTask  // (1)
    printfn "Finished Reading File"
    return text
}

// compose a new async computation from exising async computations
let readFiles = [ readFile "A"; readFile "B" ] |> Async.Parallel

// execute async computation
let textOfFiles: string[] = readFiles |> Async.RunSynchronously
// Out: Finished Reading File
// Out: Finished Reading File

// re-execute async computation again
let textOfFiles': string[] = readFiles |> Async.RunSynchronously
// Out: Finished Reading File
// Out: Finished Reading File
```

(1) As .NET Tasks became the central component of task-based asynchronous programming after F# Async were introduced, F#'s Async has `Async.AwaitTask` to map from `Task<'T>` to `Async<'T>`. Note that cancellation and exception handling require [special considerations](https://github.com/fsprojects/FSharp.Control.TaskSeq/issues/141).

### Creation / Composition

The `Async` module has a number of functions to compose and start computations. The full list with explanations can be found in the [Async Type Reference](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-control-fsharpasync.html#section0).

| Function                | Description                                                                                                                                                                                                                     |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Async.Ignore            | Creates an `Async<unit>` computation from an `Async<'T>`                                                                                                                                                                        |
| Async.Parallel          | Composes a new computation from multiple computations, `Async<'T> seq`, and runs them in parallel; it returns all the results in an array `Async<'T[]>`                                                                         |
| Async.Sequential        | Composes a new computation from multiple computations, `Async<'T> seq`, and runs them in series; it returns all the results in an array `Async<'T[]>`                                                                           |
| Async.Choice            | Composes a new computation from multiple computations, `Async<'T option> seq`, and returns the first where `'T'` is `Some value` (all others running are canceled). If all computations return `None` then the result is `None` |

For all functions that compose a new computation from children, if any child computations raise an exception, then the overall computation will trigger an exception. The `CancellationToken` passed to the child computations will be triggered, and execution continues when all running children have cancelled execution.

### Executing

| Function                     | Description                                                                                                                  |
|------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Async.RunSynchronously       | Runs an async computation and awaits its result.                                                                             |
| Async.StartAsTask            | Runs an async computation on the ThreadPool and wraps the result in a `Task<'T>`.                                            |
| Async.StartImmediateAsTask   | Runs an async computation, starting immediately on the current operating system thread, and wraps the result in a `Task<'T>` |
| Async.Start                  | Runs an `Async<unit>` computation on the ThreadPool (without observing any exceptions).                                      |
| Async.StartImmediate         | Runs a computation, starting immediately on the current thread and continuations completing in the ThreadPool.               |

## Cancellation

### .NET Tasks

.NET Tasks do not have any intrinsic handling of `CancellationToken`s; you are responsible for passing `CancellationToken`s down the call hierarchy to all sub-Tasks.

```fsharp
open System
open System.Threading
open System.Threading.Tasks

let loop (token: CancellationToken) = task {
    for cnt in [ 0 .. 9 ] do
        printf $"{cnt}: And..."
        do! Task.Delay((TimeSpan.FromSeconds 2), token)  // token is required for Task.Delay to be interruptible
        printfn "Done"
}

let cts = new CancellationTokenSource (TimeSpan.FromSeconds 5)
let runningLoop = loop cts.Token
try
    runningLoop.GetAwaiter().GetResult()  // (1)
with :? OperationCanceledException -> printfn "Canceled"
```

Output:

    0: And...Done
    1: And...Done
    2: And...Canceled

(1) `.GetAwaiter().GetResult()` used for demonstration only. Read about [async/await Best Practices](https://learn.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way)

<div id="asynchronous-programming-cancellation-async"></div>

### Async

Asynchronous computations have the benefit of implicit `CancellationToken` passing and checking.

```fsharp
open System
open System.Threading
open System.Threading.Tasks

let loop = async {
    for cnt in [ 0 .. 9 ] do
        printf $"{cnt}: And..."
        do! Async.Sleep(TimeSpan.FromSeconds 1)  // Async.Sleep implicitly receives and checks `cts.Token`

        let! ct = Async.CancellationToken // when interoperating with Tasks, cancellationTokens need to be passed explicitly
        do! Task.Delay((TimeSpan.FromSeconds 1), cancellationToken = ct) |> Async.AwaitTask

        printfn "Done"
}

let cts = new CancellationTokenSource(TimeSpan.FromSeconds 5)
try
    Async.RunSynchronously (loop, Timeout.Infinite, cts.Token)
with :? OperationCanceledException -> printfn "Canceled"
```

Output:

    0: And...Done
    1: And...Done
    2: And...Canceled

All methods for cancellation can be found in the [Core Library Documentation](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-control-fsharpasync.html#section3)

## More to Explore

Asynchronous programming is a vast topic. Here are some other resources worth exploring:

- [Asynchronous Programming in F#](https://learn.microsoft.com/en-us/dotnet/fsharp/tutorials/async) - Microsoft's tutorial guide. Recommended as it is up-to-date and expands on some of the topics here.
- [Iced Tasks](https://github.com/TheAngryByrd/IcedTasks?tab=readme-ov-file#icedtasks) - .NET Tasks start immediately. The IcedTasks library provide additional [computational expressions](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions) such as `cancellableTask`, which combines the benefits of .NET Tasks (natural interoperation with Task APIs and the performance benefits of the `task`'s State-Machine based implementation) with asynchronous expressions (composability, implicit `CancellationToken` passing, and the fact that you can invoke (or retry) a given computation multiple times).
- [Asynchronous Programming Best Practices](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#table-of-contents) by David Fowler - offers a fantastic list of good practices for .NET Task usage.

<div id="code-organization"></div>

# Code Organization

<div id="modules"></div>

## Modules
Modules are key building blocks for grouping related code; they can contain `types`, `let` bindings, or (nested) sub `module`s.
Identifiers within modules can be referenced using dot notation, or you can bring them into scope via the [`open`](#open-and-autoopen) keyword.
Illustrative-only example:

```fsharp
module Money =
    type CardInfo =
        { number: string
            expiration: int * int }

    type Payment =
        | Card of CardInfo
        | Cash of int

    module Functions =
        let validCard (cardNumber: string) =
            cardNumber.Length = 16 && (cardNumber[0], ['3';'4';'5';'6']) ||> List.contains
```

If there is only one module in a file, the `module` name can be declared at the top, and all code constructs
within the file will be included in the `module`s definition (no indentation required).

```fsharp
module Functions  // notice there is no '=' when at the top of a file

let sumOfSquares n = seq {1..n} |> Seq.sumBy (fun x -> x * x)  // Functions.sumOfSquares
```

## Namespaces
Namespaces are simply dotted names that prefix `type` and `module` declarations to allow for hierarchical scoping.
The first `namespace` directives must be placed at the top of the file. Subsequent `namespace` directives either: (a) create a sub-namespace; or (b) create a new namespace.

```fsharp
namespace MyNamespace

module MyModule =  // MyNamspace.MyModule
    let myLet = ...  // MyNamspace.MyModule.myLet

namespace MyNamespace.SubNamespace

namespace MyNewNamespace  // a new namespace
```

A top-level [`module`](#modules)'s namespace can be specified via a dotted prefix:

```fsharp
module MyNamespace.SubNamespace.Functions
```

<div id="open-and-autoopen"></div>

## Open
The `open` keyword can be used on `module`, `namespace`, and `type`.

```fsharp
module Groceries =
    type Fruit =
        | Apple
        | Banana

let fruit1 = Groceries.Banana
open Groceries  // module
let fruit2 = Apple
```
```fsharp
open System.Diagnostics  // namespace
let stopwatch = Stopwatch.StartNew()  // Stopwatch is accessible
```
```fsharp
open type System.Text.RegularExpressions.Regex  // type
let isHttp url = IsMatch("^https?:", url)  // Regex.IsMatch directly accessible
```

### RequireQualifiedAccess Attribute

This attribute can be used on modules, records, and discriminated unions to avoid namespace collisions and unexpected "shadowing". It can also make your code more explicit and readable.

```fsharp
[<RequireQualifiedAccess>]
module Calcs =
    let add x y = x + y
    let subtract x y = x - y

open Calcs   // Error! Although you may open the containing namespace, if allowed.

// As you can't open the module, you must use qualified names for module members.
let sum = Calcs.add 5 3       // Works
let diff = subtract 5 3      // Error!
```

### AutoOpen Attribute

Applicable to `module` declarations only, the `AutoOpen` attribute alleviates the need for an `open`.

```fsharp
[<AutoOpen>]
module Groceries =
    type Fruit =
        | Apple
        | Banana

let fruit = Banana
```

#### Use `AutoOpen` and/or ignore `RequireQualifiedAccess` best practice with caution!

Functions with identical names from different modules will silently "shadow" each other, causing the most recently imported definition to be used instead of the one you might expect. **The compiler will _not_ warn you that this has happened.** Even if your code works, you may encounter compiler errors if the `open` order changes. A [coding convention (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/style-guide/conventions#sort-open-statements-topologically) exists for `open` statements to avoid pitfalls.


## Accessibility Modifiers

F# supports `public`, `private` (limiting access to its containing `type` or `module`) and `internal` (limiting access to its containing assembly).
They can be applied to `module`, `let`, `member`, `type`, [`new` (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/classes#constructors), and [`val` (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/members/explicit-fields-the-val-keyword).

With the exception of `let` bindings in a class `type`, everything defaults to `public`.

| Element                                         | Example with Modifier                      |
|-------------------------------------------------|--------------------------------------------|
| Module                                          | `module internal MyModule =`               |
| Module .. `let`                                 | `let private value =`                      |
| Record                                          | `type internal MyRecord = { id: int }`     |
| Record [ctor](#smart-constructors)              | `type MyRecord = private { id: int }`      |
| Discriminated Union                             | `type internal MyDiscUni = A \| B`         |
| Discriminated Union [ctor](#smart-constructors) | `type MyDiscUni = private A \| B `         |
| Class                                           | `type internal MyClass() =`                |
| Class [ctor](#smart-constructors)               | `type MyClass private () =`                |
| Class Additional [ctor](#smart-constructors)    | `internal new() = MyClass("defaultValue")` |
| Class .. `let`                                  | *Always private. Cannot be overridden*     |
| `type` .. `member`                              | `member private _.TypeMember =`            |
| `type` .. `val`                                 | `val internal explicitInt : int`           |

<div id="smart-constructors"></div>

## Smart Constructors

Making a primary constructor (ctor) `private` or `internal` is a common convention for ensuring value integrity;
otherwise known as ["making illegal states unrepresentable" (YouTube:Effective ML)](https://youtu.be/-J8YyfrSwTk?si=ml3AWro6jG77F0YW&t=1080).

Example of Single-case Discriminated Union with a `private` constructor that constrains a quantity between 0 and 100:

```fsharp
type UnitQuantity =
    private UnitQuantity of int

module UnitQuantity =  // common idiom: type companion module
    let tryCreate qty =
        if qty < 1 || qty > 100
        then None
        else Some (UnitQuantity qty)
    let value (UnitQuantity uQty) = uQty
    let zero = UnitQuantity 0
...
let unitQtyOpt = UnitQuantity.tryCreate 5

let validQty =
    unitQtyOpt
    |> Option.defaultValue UnitQuantity.zero
```

## Recursive Reference

F#'s type inference and name resolution runs in file and line order. By default, any forward references are considered errors.
This default provides a single benefit, which can be hard to appreciate initially: you never need to look beyond the current file for a dependency.
In general this also nudges toward more careful design and organisation of codebases,
which results in cleaner, maintainable code. However, in rare cases forward referencing might be needed.
To do this we have `rec` for `module` and `namespace`; and `and` for `type` and [`let` (Recursive Functions)](#functions-recursive) functions.

```fsharp
module rec CarModule

exception OutOfGasException of Car  // Car not defined yet; would be an error

type Car =
    { make: string; model: string; hasGas: bool }
    member self.Drive destination =
        if not self.hasGas
        then raise (OutOfGasException self)
        else ...
```
```fsharp
type Person =
    { Name: string; Address: Address }
and Address =
    { Line1: string; Line2: string; Occupant: Person }
```

See [Namespaces (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/namespaces) and [Modules (MS Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/modules) to learn more.

<div id="compiler-directives"></div>

# Compiler Directives

## time

The `dotnet fsi` directive, `#time` switches on basic metrics covering real time, CPU time, and garbage collection information.

```fsharp
#time
System.Threading.Thread.Sleep (System.TimeSpan.FromSeconds 1)
#time
```

Output:

    --> Timing now on
    Real: 00:00:01.001, CPU: 00:00:00.000, GC gen0: 0, gen1: 0, gen2: 0
    val it: unit = ()
    --> Timing now off

## load

Load another F# source file into FSI.

```fsharp
#load "../lib/StringParsing.fs"
```

## Referencing packages or assemblies in a script

Reference a .NET assembly (`/` symbol is recommended for Mono compatibility).
Reference a .NET assembly:

```fsharp
#r "../lib/FSharp.Markdown.dll"
```

Reference a nuget package

```fsharp
#r "nuget:Serilog.Sinks.Console" // latest production release
#r "nuget:FSharp.Data, 6.3.0"    // specific version
#r "nuget:Equinox, *-*"          // latest version, including `-alpha`, `-rc` version etc
```

Include a directory in assembly search paths.

```fsharp
#I "../lib"
#r "FSharp.Markdown.dll"
```

## Other important directives

Other important directives are conditional execution in FSI (`INTERACTIVE`) and querying current directory (`__SOURCE_DIRECTORY__`).

```fsharp
#if INTERACTIVE
let path = __SOURCE_DIRECTORY__ + "../lib"
#else
let path = "../../../lib"
#endif
```
