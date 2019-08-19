---
layout: page
title: Swift Style Guide 中文版
---
<!-- {% comment %}
The width of <pre> elements on this page is carefully regulated, so we
can afford to drop the scrollbar boxes.
{% endcomment %} -->
<style>
article pre {
    overflow: visible;
}
</style>

This style guide is based on Apple's excellent Swift standard library style and
also incorporates feedback from usage across multiple Swift projects within
Google. It is a living document and the basis upon which the formatter is
implemented.

这份代码风格指南基于Apple 优秀的 Swift 标准库代码风格，吸取了多个 Google 内部 Swift 项目的使用反馈而成。本文档会保持更新，并且已经基于本文档实现了格式化工具。

## 目录/Table of Contents
{:.no_toc}

* TOC
{:toc}
## 源文件的基础要求/Source File Basics

### 文件名/File Names

All Swift source files end with the extension `.swift`.

所有 Swift 源文件以扩展名 `.swift` 结尾。

In general, the name of a source file best describes the primary entity that it
contains. A file that primarily contains a single type has the name of that
type. A file that extends an existing type with protocol conformance is named
with a combination of the type name and the protocol name, joined with a plus
(`+`) sign. For more complex situations, exercise your best judgment.

通常来说，源文件的名字最好描述包含的主要内容。如果文件主要包含单个类型，则用类型名命名文件。如果文件是为已存在类型添加新的协议遵循，则命名为类名和协议名的组合，通过加号（+）连接。对于更复杂的情况，最好由你自己判断。

For example,

例如，

* A file containing a single type `MyType` is named `MyType.swift`.
* 文件中包含单个类型 `MyType`，命名为 `MyType.swift`。
* A file containing a type `MyType` and some top-level helper functions is also
  named `MyType.swift`. (The top-level helpers are not the primary entity.)
* 文件中包含类型 `MyType` 和一些顶层的工具函数，也命名为 `MyType.swift`。（顶层的工具函数不是主要的内容。）
* A file containing a single extension to a type `MyType` that adds conformance
  to a protocol `MyProtocol` is named `MyType+MyProtocol.swift`.
* 文件中包含单个扩展，为类型 `MyType` 添加 `MyProtocol` 协议遵循，命名为 `MyType+MyProtocol.swift`。
* A file containing multiple extensions to a type `MyType` that add
  conformances, nested types, or other functionality to a type can be named more
  generally, as long as it is prefixed with `MyType+`; for example,
  `MyType+Additions.swift`.
* 文件中包含多个扩展，为类型 `MyType` 添加协议遵循、嵌套类型或者其他功能的拓展，可以使用更通用的命名，只要它的前缀是 `MyType+`；例如，`MyType+Additions.swift`。
* A file containing related declarations that are not otherwise scoped under a
  common type or namespace (such as a collection of global mathematical
  functions) can be named descriptively; for example, `Math.swift`.
* 文件中包含多个在公共类型或命名空间下没有作用域限制的相关声明（比如一系列全局的数学函数），可以命名得更有描述性。例如：`Math.swift`。

### 文件编码/File Encoding

Source files are encoded in UTF-8.

源文件以 UTF-8 方式编码。

### 空白符/Whitespace Characters

Aside from the line terminator, the Unicode horizontal space character
(`U+0020`) is the only whitespace character that appears anywhere in a source
file. The implications are:

除了行终止符之外，Unicode 水平空格符（`U+0020`）是唯一可以出现在源文件里的空白符。这意味着：

* All other whitespace characters in string and character literals are
  represented by their corresponding escape sequence.
* 字符串或者字符字面量里的所有其他空白符，要用对应的转义字符表示。
* Tab characters are not used for indentation.
* 制表符不用于缩进。

### 特殊转义字符/Special Escape Sequences

For any character that has a special escape sequence (`\t`, `\n`, `\r`, `\"`,
`\'`, `\\`, and `\0`), that sequence is used rather than the equivalent Unicode
(e.g., `\u{000a}`) escape sequence.

任何字符中如果包含了特殊转义字符（`\t`、`\n`、`\r`、`\"`、`\'`、`\\` 和 `\0`），直接使用该转义字符，不用其等价的 Unicode 转义字符（例如：`\u{000a}`）。

### 不可见字符和修饰符/Invisible Characters and Modifiers

Invisible characters, such as the zero width space and other control characters
that do not affect the graphical representation of a string, are always written
as Unicode escape sequences.

不可见字符，例如零宽空格和其他在字符串里不影响可视化表达的控制字符，都要用 Unicode 转义字符表示。

Control characters, combining characters, and variation selectors that _do_
affect the graphical representation of a string are not escaped when they are
attached to a character or characters that they modify. If such a Unicode scalar
is present in isolation or is otherwise not modifying another character in the
same string, it is written as a Unicode escape sequence.

控制字符、组合字符以及字符串里*会*影响可视化表达的异体字选择器，如果跟在其修改的字符后面则不转义。如果该 Unicode 标量单独使用或者没有修改同字符串中其他的字符，则用 Unicode 转义字符表示。

The strings below are well-formed because the umlauts and variation selectors
associate with neighboring characters in the string. The second example is in
fact composed of _five_ Unicode scalars, but they are unescaped because the
specific combination is rendered as a single character.

下面的字符串是符合要求的，因为元音和异体字选择器都和临近的字符关联。第二个例子实际上由 *5* 个 Unicode 标量组成，不过它们没有被转义，因为在特定组合后作为单一字符进行渲染。

~~~ swift
let size = "Übergröße"
let shrug = "🤷🏿‍️"
~~~
{:.good}

In the example below, the umlaut and variation selector are in strings by
themselves, so they are escaped.

下面的例子中，字符串里元音和异体字选择器是单独出现，所以要被转义。

~~~ swift
let diaeresis = "\u{0308}"
let skinToneType6 = "\u{1F3FF}"
~~~
{:.good}

If the umlaut were included in the string literally, it would combine with
the preceding quotation mark, impairing readability. Likewise, while most
systems may render a standalone skin tone modifier as a block graphic, the
example below is still forbidden because it is a modifier that is not modifying
a character in the same string.

如果元音以字面量方式出现在字符串里，它会和前面的引号组合起来，影响可读性。同样的，尽管大部分系统会将单独的皮肤着色修饰符作为单独图形块渲染，但下面例子依旧是不允许的，因为该修饰符并没有修改同一字符串里任何字符。

~~~ swift
let diaeresis = "̈"
let skinToneType6 = "🏿"
~~~
{:.bad}

### 字符串字面量/String Literals

Unicode escape sequences (`\u{????}`) and literal code points (for example, `Ü`)
outside the 7-bit ASCII range are never mixed in the same string.

7 位 ASCII 码范围以外的 Unicode 转义字符（`\u{????}`）和代码点字面量（例如：`Ü`）永远不要在同一字符串里混合使用。

More specifically, string literals are either:

更具体来说，字符串字面量只能是下面两者之一:

* composed of a combination of Unicode code points written literally and/or
  single character escape sequences (such as `\t`, but _not_ `\u{????}`), or
* 由字面量方式的 Unicode 代码点组合和/或单一转义字符组合，或者
* composed of 7-bit ASCII with any number of Unicode escape sequences and/or
  other escape sequences.
* 由任意数量的 Unicode 转义字符的 7 位 ASCII 码和或其他转义字符组成。

The following example is correct because `\n` is allowed to be present among
other Unicode code points.

下面的例子是正确的，因为 `\n` 允许在其他 Unicode 代码点中存在。

~~~ swift
let size = "Übergröße\n"
~~~
{:.good}

The following example is allowed because it follows the rules above, but it is
_not preferred_ because the text is harder to read and understand compared to
the string above.

下面的例子也是被允许的，因为它遵守了上面的规则，但`并不推荐`，因为和上面的字符串相比，它更难以阅读和理解。

~~~ swift
let size = "\u{00DC}bergr\u{00F6}\u{00DF}e\n"
~~~
{:.good}

The example below is forbidden because it mixes code points outside the 7-bit
ASCII range in both literal form and in escaped form.

下面的例子是被禁止的，因为它混合了 7 位 ASCII 码范围以外的字面量形式和转义形式的代码点。

~~~ swift
let size = "Übergr\u{00F6}\u{00DF}e\n"
~~~
{:.bad}

> **Aside:** Never make your code less readable simply out of fear
> that some programs might not handle non-ASCII characters properly. If that
> should happen, those programs are broken and must be fixed.
>
> **题外话**：不要因为某些程序可能无法正确处理非 ASCII 码字符，就降低代码的可读性。如果遇到这种情况，应该被修复的是那个程序，不是你的代码。

## 源文件结构/Source File Structure

### 文件注释/File Comments

Comments describing the contents of a source file are optional. They are
discouraged for files that contain only a single abstraction (such as a class
declaration)&mdash;in those cases, the documentation comment on the abstraction
itself is sufficient and a file comment is only present if it provides
additional useful information. File comments are allowed for files that contain
multiple abstractions in order to document that grouping as a whole.

描述源文件内容的注释是可选的。对只包含了单一抽象（例如一个类的声明）的文件并不建议用这种注释——这种情况下，抽象本身的文档注释就足够了，文件注释只有当提供了额外的有用信息时才需要。如果文件中包含多个抽象，可以添加文件注释，对整体内容进行解释。

### 导入语句/Import Statements

A source file imports exactly the top-level modules that it needs; nothing more
and nothing less. If a source file uses definitions from both `UIKit` and
`Foundation`, it imports both explicitly; it does not rely on the fact that some
Apple frameworks transitively import others as an implementation detail.

源文件中应该显式导入需要的顶层模块；不要多也不要少。如果源文件中同时使用了 `UIKit` 中的定义和 `Foundation` 中的定义，那么都进行显式导入；即使有些 Apple 框架已经在实现细节中导入其他框架。

Imports of whole modules are preferred to imports of individual declarations or
submodules.

优先考虑导入整个模块，而非导入单个声明或者子模块。

> There are a number of reasons to avoid importing individual members:
>
> 避免导入单个成员的原因如下：
>
> * There is no automated tooling to resolve/organize imports.
> * 没有自动化工具来解决/组织那些导入。
> * Existing automated tooling (such as Xcode's migrator) are less likely to
> work well on code that imports individual members because they are
> considered corner cases.
> * 现存地自动化工具（例如 Xcode 迁移器）很可能无法处理导入单个成员的代码，因为这不是常见用法。
> * The prevailing style in Swift (based on official examples and community
> code) is to import entire modules.
> * 目前流行的 Swift 代码风格（基于官方例子和社区代码）都是导入整个模块。

Imports of individual declarations are permitted when importing the whole module
would otherwise pollute the global namespace with top-level definitions (such as
C interfaces). Use your best judgment in these situations.

如果导入完整模块的顶层定义（例如 C 接口）会污染全局命名空间，那导入单个声明是允许的。在这些情况下，由你自己判断应该如何导入。

Imports of submodules are permitted if the submodule exports functionality that
is not available when importing the top-level module. For example,
`UIKit.UIGestureRecognizerSubclass` must be imported explicitly to expose the
methods that allow client code to subclass `UIGestureRecognizer`&mdash;those are
not visible by importing `UIKit` alone.

如果子模块的导出功能在只导入顶层模块时不可用，那么允许导入子模块。例如：`UIKit.UIGestureRecognizerSubclass` 必须要显式导入，以暴露继承 `UIGestureRecognizer` 时代码允许重写的方法——这在只导入 `UIKit` 时并不可见。

Import statements are not line-wrapped.

导入语句不可换行。

Import statements are the first non-comment tokens in a source file. They are
grouped in the following fashion, with the imports in each group ordered
lexicographically and with exactly one blank line between each group:

在源文件中，导入语句放在除了注释以外的最前面。按以下方式分组，每组中的导入按照字母顺序排序，每组之间只有一个空行：

1. Module/submodule imports not under test

   无测试模块/子模块的导入

1. Individual declaration imports (`class`, `enum`, `func`, `struct`, `var`)

   单个声明的导入 (`class`、`enum`、`func`、`struct`、`var`)

1. Modules imported with `@testable` (only present in test sources)

   `@testable` 模块的导入（只存在测试源码中）

~~~ swift
import CoreLocation
import MyThirdPartyModule
import SpriteKit
import UIKit

import func Darwin.C.isatty

@testable import MyModuleUnderTest
~~~
{:.good}

### 类型，变量和函数声明/Type, Variable, and Function Declarations

In general, most source files contain only one top-level type, especially when
the type declaration is large. Exceptions are allowed when it makes sense to
include multiple related types in a single file. For example,

通常情况下，大部分源文件只包含一个顶层类型，特别是类型声明很庞大时。除非在同一文件里包含多个相关类型是有意义的。例如，

* A class and its delegate protocol may be defined in the same file.

* 类和它的代理协议可以定义在同一文件中。

* A type and its small related helper types may be defined in the same file.
  This can be useful when using `fileprivate` to restrict certain functionality
  of the type and/or its helpers to only that file and not the rest of the
  module.
  
* 类型和它相关的轻量帮助类型可以定义在同一文件中。这种时候 `fileprivate` 很有用，可以将类型和/或它帮助类的某些功能限制在那个文件中而非暴露给模块的其他地方。

The order of types, variables, and functions in a source file, and the order of
the members of those types, can have a great effect on readability. However,
there is no single correct recipe for how to do it; different files and
different types may order their contents in different ways.

在源文件中类型、变量和函数之间的顺序，和该类型成员的顺序，都会大大影响可读性。然而，如何组织它们并没有单一正确的法则；不同的文件和不同的类型可以用不同的方式组织它们内容的排序。

What is important is that each file and type uses _**some** logical order,_
which its maintainer could explain if asked. For example, new methods are not
just habitually added to the end of the type, as that would yield "chronological
by date added" ordering, which is not a logical ordering.

重要的是，每一个文件和类型使用_**同一**排序逻辑_，并且维护者应该可以解释清楚这个逻辑。例如，新的方法不能习惯性地加在类型的最后面，因为这只是顺从“日期递增地时间排序”，而不是有逻辑性的排序。

When deciding on the logical order of members, it can be helpful for readers and
future writers (including yourself) to use `// MARK:` comments to provide
descriptions for that grouping. These comments are also interpreted by Xcode and
provide bookmarks in the source window's navigation bar. (Likewise,
`// MARK: - `, written with a hyphen before the description, causes Xcode to
insert a divider before the menu item.) For example,

当决定成员的排序逻辑后，使用 `// MARK:` 注释对该分组提供描述，对阅读者和将来的编码者（包括你自己）是很有帮助的。这种注释也会被 Xcode 理解并在源码窗口的导航栏中提供书签。（类似的还有 `// MARK: -`，在描述之前使用一个连字符的话， Xcode 会在菜单元素前插入一条分隔线。）例如，

~~~ swift
class MovieRatingViewController: UITableViewController {

  // MARK: - View controller lifecycle methods

  override func viewDidLoad() {
    // ...
  }

  override func viewWillAppear(_ animated: Bool) {
    // ...
  }

  // MARK: - Movie rating manipulation methods

  @objc private func ratingStarWasTapped(_ sender: UIButton?) {
    // ...
  }

  @objc private func criticReviewWasTapped(_ sender: UIButton?) {
    // ...
  }
}
~~~
{:.good}

### 声明重载/Overloaded Declarations

When a type has multiple initializers or subscripts, or a file/type has multiple
functions with the same base name (though perhaps with different argument
labels), _and_ when these overloads appear in the same type or extension scope,
they appear sequentially with no other code in between.

当一个类型有多个构造器或者下标，或者一个文件/类型内有多个相同名字的函数（尽管可能有不同的实参标签），*并且*当这些重载在同一类型或者扩展作用域内时，它们应该按顺序排列，不应该在中间插入其他代码。

### 扩展/Extensions

Extensions can be used to organize functionality of a type across multiple
"units." As with member order, the organizational structure/grouping you choose
can have a great effect on readability; you must use _**some** logical
organizational structure_ that you could explain to a reviewer if asked.

扩展可以将一个类型的功能组织到多个“单元”中。配合成员排序和所选择的组织结构/分组，会对代码可读性有很大的帮助；你必须使用_**某种**_能给审查者解释的_逻辑结构_进行组织。

## 常规格式/General Formatting

### 单行字符限制/Column Limit

Swift code has a column limit of 100 characters. Except as noted below, any line
that would exceed this limit must be line-wrapped as described in
[Line-Wrapping](#line-wrapping).

Swift 代码有 100 字符单行限制。除了下面的说明之外，任何超过该限制的行都需要换行，详情见 [换行](#line-wrapping)。

**Exceptions:**

**例外：**

1. Lines where obeying the column limit is not possible without breaking a
   meaningful unit of text that should not be broken (for example, a long URL in
   a comment).
   
   即便是遵循单行字符限制的行，也不应该破坏文本中有意义的部分（例如，注释里的长 URL ）。
   
1. `import` statements.

   `import` 语句。

1. Code generated by another tool.

   其他工具生成的代码。

### 花括号/Braces

In general, braces follow Kernighan and Ritchie (K&R) style for non-empty
blocks with exceptions for Swift-specific constructs and rules:

通常来说，内容非空的花括号遵循 Kernighan 和 Ritchie（K&R）代码风格，除了 Swift 特殊结构和规则以外：

* There **is no** line break before the opening brace (`{`), **unless** required
  by application of the rules in [Line-Wrapping](#line-wrapping).
  
* 左花括号（`{`）之前**不需要**换行，**除非**是为了满足 [换行](#line-wrapping) 规则。

* There **is a** line break after the opening brace (`{`), except

* 左花括号（`{`）之后**需要**换行，除非满足下面的条件
  
  * in closures, where the signature of the closure is placed on the same line
    as the curly brace, if it fits, and a line break follows the `in` keyword.
    
  * 在闭包中，如果长度足够，将闭包的签名和花括号在同一行，在 `in` 关键字后面换行。
    
  * where it may be omitted as described in
    [One Statement Per Line](#one-statement-per-line).
    
  * 可以省略成 [单行语句](#one-statement-per-line)。
    
  * empty blocks may be written as `{}`.
  
  * 空白块应该写作 `{}`。
  
* There **is a** line break before the closing brace (`}`), except where it may
  be omitted as described in [One Statement Per Line](#one-statement-per-line),
  or it completes an empty block.
  
* 右花括号（`}`）之前**需要**换行，除非可以省略成 [单行语句](#one-statement-per-line) 或是空白块。
  
* There **is a** line break after the closing brace (`}`), **if and only if**
  that brace terminates a statement or the body of a declaration. For example,
  an `else` block is written `} else {` with both braces on the same line.
  
* 右花括号（`}`）之后**需要**换行的情况，**有且仅当**该花括号用作终止语句或者作为声明体。例如，`else` 块写成 `} else {` 时两个花括号在同一行。

### 分号/Semicolons

Semicolons (`;`) are **not used**, either to terminate or separate statements.

分号（`;`）**禁止使用**，无论是用于终止或者分割语句。

In other words, the only location where a semicolon may appear is inside a
string literal or a comment.

换而言之，分号只可能出现在字符串字面量或者注释中。

~~~ swift
func printSum(_ a: Int, _ b: Int) {
  let sum = a + b
  print(sum)
}
~~~
{:.good}

~~~ swift
func printSum(_ a: Int, _ b: Int) {
  let sum = a + b;
  print(sum);
}
~~~
{:.bad}

### 每行一个语句/One Statement Per Line

There is **at most** one statement per line, and each statement is followed by a
line break, except when the line ends with a block that also contains zero
or one statements.

每行**最多**一个语句，每个语句后换行，除非该行结尾的块中只有 0 或者 1 条语句。

~~~ swift
guard let value = value else { return 0 }

defer { file.close() }

switch someEnum {
case .first: return 5
case .second: return 10
case .third: return 20
}

let squares = numbers.map { $0 * $0 }

var someProperty: Int {
  get { return otherObject.property }
  set { otherObject.property = newValue }
}

var someProperty: Int { return otherObject.somethingElse() }

required init?(coder aDecoder: NSCoder) { fatalError("no coder") }
~~~
{:.good}

Wrapping the body of a single-statement block onto its own line is always
allowed. Exercise best judgment when deciding whether to place a conditional
statement and its body on the same line. For example, single line conditionals
work well for early-return and basic cleanup tasks, but less so when the body
contains a function call with significant logic. When in doubt, write it as a
multi-line statement.

将块里包含的单个语句和块放在同一行总是允许的。由你自己判断是否将条件语句和它的执行体放在同一行中。例如，单行条件适合跟提前返回并进行简单收尾的代码放在一行，但是当执行体里包含了函数调用或者重要的逻辑就不太合适。如果不确定哪种更好，使用多行语句。

### 换行/Line-Wrapping

> Terminology note: **Line-wrapping** is the activity of dividing code into
> multiple lines that might otherwise legally occupy a single line.
>
> 术语说明：**换行**是将代码分割到多个行的行为，否则它们都会堆积到同一行。

For the purposes of Google Swift style, many declarations (such as type
declarations and function declarations) and other expressions (like function
calls) can be partitioned into **breakable** units that are separated by
**unbreakable** delimiting token sequences.

根据 Google Swift 代码风格的思想，大多声明（例如类型声明和函数声明）和其他表达式（例如函数调用）可以被划分成**可破坏**单元，被定义的**不可破坏**标记符进行分割。

As an example, consider the following complex function declaration, which needs
to be line-wrapped:

举个例子，考虑下面这个复杂的函数声明该如何进行换行：

~~~ swift
public func index<Elements: Collection, Element>(of element: Element, in collection: Elements) -> Elements.Index? where Elements.Element == Element, Element: Equatable {
  // ...
}
~~~
{:.bad}

This declaration is split as follows (scroll horizontally if necessary to see
the full example). Unbreakable token sequences are indicated in orange;
breakable sequences are indicated in blue.

这个声明可以像下面这样进行分割（要看完整例子可能需要水平滑动）。不可破坏标记符标记为橙色；可破坏符号标记为蓝色。

<pre class="lw-container lw-container-numbered">
<span class="lw-ub">public func index&lt;</span><span class="lw-br">Elements: Collection, Element</span><span class="lw-ub">&gt;(</span><span class="lw-br">of element: Element, in collection: Elements</span><span class="lw-ub">) -&gt;</span><span class="lw-br">Elements.Index?</span><span class="lw-ub">where</span><span class="lw-br">Elements.Element == Element, Element: Equatable</span>{
  // ...
}
</pre>

1. The **unbreakable** token sequence up through the open angle bracket (`<`)
   that begins the generic argument list.
   
   **不可破坏**标记符从开始直到标志范型实参列表开始的左尖括号（`<`）。
   
1. The **breakable** list of generic arguments.

   范型实参的**可破坏**列表。

1. The **unbreakable** token sequence (`>(`) that separates the generic
   arguments from the formal arguments.
   
   **不可破坏**标记符（`>（`）将范型实参和正式实参进行分割。
   
1. The **breakable** comma-delimited list of formal arguments.

   正式实参的**可破坏**逗号分隔列表。

1. The **unbreakable** token-sequence from the closing parenthesis (`)`) up
   through the arrow (`->`) that precedes the return type.
   
   **不可破坏**标记符从右括号（`)`）到返回类型之前的箭头（`->`）。
   
1. The **breakable** return type.

   **可破坏**返回类型。

1. The **unbreakable** `where` keyword that begins the generic constraints list.

   在范型约束列表开始的**不可破坏** `where` 关键字。

1. The **breakable** comma-delimited list of generic constraints.

   范型约束的**可破坏**逗号分隔列表。

Using these concepts, the cardinal rules of Google Swift style for line-wrapping
are:

用上这些概念，Google Swift 代码风格的基本换行规则如下：

1. If the entire declaration, statement, or expression fits on one line, then do
   that.
   
   如果整个声明，语句或者表达式适合使用一行，就使用一行。
   
1. Comma-delimited lists are only laid out in one direction: horizontally or
   vertically. In other words, all elements must fit on the same line, or each
   element must be on its own line. A horizontally-oriented list does not
   contain any line breaks, even before the first element or after the last
   element. Except in control flow statements, a vertically-oriented list
   contains a line break before the first element and after each element.
   
   逗号分隔列表只能一个方向展示：水平或者垂直。换句话说，所有元素必须在同一行上，或者每个元素必须在单独的行上。水平向的列表不包含任何换行，即使在第一个元素之前或者最后一个元素之后。除控制流语句外，垂直向的列表在第一个元素之前和每个元素之后需要换行。
   
1. A continuation line starting with an unbreakable token sequence is indented
   at the same level as the original line.
   
   以不可破坏标记符开始的后续行和原始行缩进保持一致。
   
1. A continuation line that is part of a vertically-oriented comma-delimited
   list is indented exactly +2 from the original line.
   
   作为垂直向逗号分隔列表一部分的后续行在原始行缩进的基础上+2。
   
1. When an open curly brace (`{`) follows a line-wrapped declaration or
   expression, it is on the same line as the final continuation line unless that
   line is indented at +2 from the original line. In that case, the brace is
   placed on its own line, to avoid the continuation lines from blending
   visually with the body of the subsequent block.

   在换行的声明或者表达式后的左花括号（`{`），除非该行的缩进是在原始行的基础上+2，都和最后的后续行在同一行。那种情况下，花括号另起一行，避免该行和随后块里的内容在视觉上有混淆。
   
   ~~~ swift
   public func index<Elements: Collection, Element>(
     of element: Element,
     in collection: Elements
   ) -> Elements.Index?
   where
     Elements.Element == Element,
     Element: Equatable
   {  // GOOD.
     for current in elements {
       // ...
     }
   }
   ~~~
   {:.good}
   
   ~~~ swift
   public func index<Elements: Collection, Element>(
     of element: Element,
     in collection: Elements
   ) -> Elements.Index?
   where
     Elements.Element == Element,
     Element: Equatable {  // AVOID.
     for current in elements {
       // ...
     }
   }
   ~~~
   {:.bad}

For declarations that contain a `where` clause followed by generic constraints,
additional rules apply:

当声明里包含了用于范型约束的 `where` 关键字时，需要遵循的额外规则：

1. If the generic constraint list exceeds the column limit when placed on the
   same line as the return type, then a line break is first inserted **before**
   the `where` keyword and the `where` keyword is indented at the same level as
   the original line.
   
   如果范型约束列表和返回类型在同一行时超过了单行字符限制，在 `where` 关键字**之前**插入换行，并且和原始行缩进保持一致。
   
1. If the generic constraint list still exceeds the column limit after inserting
   the line break above, then the constraint list is oriented vertically with a
   line break after the `where` keyword and a line break after the final
   constraint.
   
   如果范型约束列表在换行之后依旧超过单行字符限制，那么在 `where` 关键字后换行，约束列表用垂直方向展示，并在最后一个约束后面换行。

Concrete examples of this are shown in the relevant subsections below.

具体例子见下面相关段落的内容。

This line-wrapping style ensures that the different parts of a declaration are
_quickly and easily identifiable to the reader_ by using indentation and line
breaks, while also preserving the same indentation level for those parts
throughout the file. Specifically, it prevents the zig-zag effect that would be
present if the arguments are indented based on opening parentheses, as is common
in other languages:

这个换行风格能确保通过缩进和换行让_读者_可以_快速容易地识别_声明的不同部分，并且在文件中的这些部分缩进风格应该保持一致。具体来说，这能避免实参基于左括号缩进而出现的锯齿效应，这在其他语言里很常见：

~~~ swift
public func index<Elements: Collection, Element>(of element: Element,  // AVOID.
                                                 in collection: Elements) -> Elements.Index?
    where Elements.Element == Element, Element: Equatable {
  doSomething()
}
~~~
{:.bad}

#### 函数声明/Function Declarations

<pre class="lw-container">
<span class="lw-ub"><em>modifiers</em> func <em>name</em>(</span><span class="lw-br"><em>formal arguments</em></span><span class="lw-ub">)</span>{
<span class="lw-ub"><em>modifiers</em> func <em>name</em>(</span><span class="lw-br"><em>formal arguments</em></span><span class="lw-ub">) -&gt;</span><span class="lw-br"><em>result</em></span>{

<span class="lw-ub"><em>modifiers</em> func <em>name</em>&lt;</span><span class="lw-br"><em>generic arguments</em></span><span class="lw-ub">&gt;(</span><span class="lw-br"><em>formal arguments</em></span><span class="lw-ub">) throws -&gt;</span><span class="lw-br"><em>result</em></span>{

<span class="lw-ub"><em>modifiers</em> func <em>name</em>&lt;</span><span class="lw-br"><em>generic arguments</em></span><span class="lw-ub">&gt;(</span><span class="lw-br"><em>formal arguments</em></span><span class="lw-ub">) throws -&gt;</span><span class="lw-br"><em>result</em></span><span class="lw-ub">where</span><span class="lw-br"><em>generic constraints</em></span>{
</pre>

Applying the rules above from left to right gives us the following
line-wrapping:

将上面的规则从左到右应用得到下面的换行：

~~~ swift
public func index<Elements: Collection, Element>(
  of element: Element,
  in collection: Elements
) -> Elements.Index? where Elements.Element == Element, Element: Equatable {
  for current in elements {
    // ...
  }
}
~~~
{:.good}

Function declarations in protocols that are terminated with a closing
parenthesis (`)`) may place the parenthesis on the same line as the final
argument **or** on its own line.

协议里以右括号（`)`）结束的函数声明可以将括号和最后的实参放在同一行**或者**另起一行。

~~~ swift
public protocol ContrivedExampleDelegate {
  func contrivedExample(
    _ contrivedExample: ContrivedExample,
    willDoSomethingTo someValue: SomeValue)
}

public protocol ContrivedExampleDelegate {
  func contrivedExample(
    _ contrivedExample: ContrivedExample,
    willDoSomethingTo someValue: SomeValue
  )
}
~~~
{:.good}

If types are complex and/or deeply nested, individual elements in the
arguments/constraints lists and/or the return type may also need to be wrapped.
In these rare cases, the same line-wrapping rules apply to those parts as apply
to the declaration itself.

如果类型很复杂和/或有深层嵌套，在作为实参/约束列表和/或返回类型的单个元素时也可能需要换行。在这种罕见情况下，使用和声明一致的换行规则。

~~~ swift
public func performanceTrackingIndex<Elements: Collection, Element>(
  of element: Element,
  in collection: Elements
) -> (
  Element.Index?,
  PerformanceTrackingIndexStatistics.Timings,
  PerformanceTrackingIndexStatistics.SpaceUsed
) {
  // ...
}
~~~
{:.good}

However, `typealias`es or some other means are often a better way to simplify
complex declarations whenever possible.

然而，如果可以用 `typealias` 或其他手段简化复杂声明通常是更好的解决方法。

#### 类型和拓展声明/Type and Extension Declarations

The examples below apply equally to `class`, `struct`, `enum`, `extension`, and
`protocol` (with the obvious exception that all but the first do not have
superclasses in their inheritance list, but they are otherwise structurally
similar).

下面的例子同样适用于 `class`，`struct`，`enum`，`extension` 和 `protocol`（除了第一个的继承列表里有父类外，其余结构都是类似的）。

<pre class="lw-container">
<span class="lw-ub"><em>modifiers</em> class <em>Name</em></span>{

<span class="lw-ub"><em>modifiers</em> class <em>Name</em>:</span><span class="lw-br"><em>superclass and protocols</em></span>{

<span class="lw-ub"><em>modifiers</em> class <em>Name</em>&lt;</span><span class="lw-br"><em>generic arguments</em></span><span class="lw-ub">&gt;:</span><span class="lw-br"><em>superclass and protocols</em></span>{

<span class="lw-ub"><em>modifiers</em> class <em>Name</em>&lt;</span><span class="lw-br"><em>generic arguments</em></span><span class="lw-ub">&gt;:</span><span class="lw-br"><em>superclass and protocols</em></span><span class="lw-ub">where</span><span class="lw-br"><em>generic constraints</em></span>{
</pre>

~~~ swift
class MyClass:
  MySuperclass,
  MyProtocol,
  SomeoneElsesProtocol,
  SomeFrameworkProtocol
{
  // ...
}

class MyContainer<Element>:
  MyContainerSuperclass,
  MyContainerProtocol,
  SomeoneElsesContainerProtocol,
  SomeFrameworkContainerProtocol
{
  // ...
}

class MyContainer<BaseCollection>:
  MyContainerSuperclass,
  MyContainerProtocol,
  SomeoneElsesContainerProtocol,
  SomeFrameworkContainerProtocol
where BaseCollection: Collection {
  // ...
}

class MyContainer<BaseCollection>:
  MyContainerSuperclass,
  MyContainerProtocol,
  SomeoneElsesContainerProtocol,
  SomeFrameworkContainerProtocol
where
  BaseCollection: Collection,
  BaseCollection.Element: Equatable,
  BaseCollection.Element: SomeOtherProtocolOnlyUsedToForceLineWrapping
{
  // ...
}
~~~
{:.good}

#### 函数调用/Function Calls

When a function call is line-wrapped, each argument is written on its own line,
indented +2 from the original line.

当函数调用需要换行时，每一个实参单独一行，并在原始行缩进基础上 +2。

As with function declarations, if the function call terminates its enclosing
statement and ends with a closing parenthesis (`)`) (that is, it has no trailing
closure), then the parenthesis may be placed **either** on the same line as the
final argument **or** on its own line.

和函数声明一样，如果函数调用的语句以右括号（`)`）结束（意味着没有尾随闭包），括号**既可以**和最后一个实参在同一行**也可以**另起一行。

~~~ swift
let index = index(
  of: veryLongElementVariableName,
  in: aCollectionOfElementsThatAlsoHappensToHaveALongName)

let index = index(
  of: veryLongElementVariableName,
  in: aCollectionOfElementsThatAlsoHappensToHaveALongName
)
~~~
{:.good}

If the function call ends with a trailing closure and the closure's signature
must be wrapped, then place it on its own line and wrap the argument list in
parentheses to distinguish it from the body of the closure below it.

如果函数调用以尾随闭包结束，并且闭包签名需要换行的话，另起一行并将实参列表包在括号中以便和下面的闭包体区分。

~~~ swift
someAsynchronousAction.execute(withDelay: howManySeconds, context: actionContext) {
  (context, completion) in
  doSomething(withContext: context)
  completion()
}
~~~
{:.good}

#### 控制流语句/Control Flow Statements

When a control flow statement (such as `if`, `guard`, `while`, or `for`) is
wrapped, the first continuation line is indented to the same position as the
token following the control flow keyword. Additional continuation lines are
indented at that same position if they are syntactically parallel elements, or
in +2 increments from that position if they are syntactically nested.

当控制流语句（例如 `if`,`gurad`,`while` 或 `for`）需要换行时，首个后续行的缩进和紧接着控制流关键字的元素保持一致。其余的后续行如果是语法上平级的元素，那么缩进也保持一致，如果语法上有嵌套层级，则在原来缩进基础上+2。

The open brace (`{`) preceding the body of the control flow statement can either
be placed on the same line as the last continuation line or on the next line,
at the same indentation level as the beginning of the statement. For `guard`
statements, the `else {` must be kept together, either on the same line or on
the next line.

控制流语句执行体前面左花括号（`{`）既可以和最后的条件同一行也可以另起一行，并和该语句缩进保持一致。对于 `guard` 语句， `else {` 必须连在一起，不管是在同一行中还是另起一行。

~~~ swift
if aBooleanValueReturnedByAVeryLongOptionalThing() &&
   aDifferentBooleanValueReturnedByAVeryLongOptionalThing() &&
   yetAnotherBooleanValueThatContributesToTheWrapping() {
  doSomething()
}

if aBooleanValueReturnedByAVeryLongOptionalThing() &&
   aDifferentBooleanValueReturnedByAVeryLongOptionalThing() &&
   yetAnotherBooleanValueThatContributesToTheWrapping()
{
  doSomething()
}

if let value = aValueReturnedByAVeryLongOptionalThing(),
   let value2 = aDifferentValueReturnedByAVeryLongOptionalThing() {
  doSomething()
}

if let value = aValueReturnedByAVeryLongOptionalThing(),
   let value2 = aDifferentValueReturnedByAVeryLongOptionalThingThatForcesTheBraceToBeWrapped()
{
  doSomething()
}

guard let value = aValueReturnedByAVeryLongOptionalThing(),
      let value2 = aDifferentValueReturnedByAVeryLongOptionalThing() else {
  doSomething()
}

guard let value = aValueReturnedByAVeryLongOptionalThing(),
      let value2 = aDifferentValueReturnedByAVeryLongOptionalThing()
else {
  doSomething()
}

for element in collection
    where element.happensToHaveAVeryLongPropertyNameThatYouNeedToCheck {
  doSomething()
}
~~~
{:.good}

#### 其他表达式/Other Expressions

When line-wrapping other expressions that are not function calls (as described
above), the second line (the one immediately following the first break) is
indented exactly +2 from the original line.

非函数调用（上面提到的）的其他表达式换行时，第二行（在第一个换行后的行）的缩进在原始行的基础上+2。

When there are multiple continuation lines, indentation may be varied in
increments of +2 as needed. In general, two continuation lines use the same
indentation level if and only if they begin with syntactically parallel
elements. However, if there are many continuation lines caused by long wrapped
expressions, consider splitting them into multiple statements using temporary
variables when possible.

当有多个后续行时，缩进会根据需要在原来的基础上 +2 递增变化。通常来说，有且仅当两个后续行以语法上平级的元素开始时才使用相同的缩进。然而，如果因为很长的表达式产生了很多个后续行，考虑将它分隔成多个语句的可能性，并使用临时变量。

~~~ swift
let result = anExpression + thatIsMadeUpOf * aLargeNumber +
  ofTerms / andTherefore % mustBeWrapped + (
    andWeWill - keepMakingItLonger * soThatWeHave / aContrivedExample)
~~~
{:.good}

~~~ swift
let result = anExpression + thatIsMadeUpOf * aLargeNumber +
    ofTerms / andTherefore % mustBeWrapped + (
        andWeWill - keepMakingItLonger * soThatWeHave / aContrivedExample)
~~~
{:.bad}

### 水平空格/Horizontal Whitespace

> **Terminology note:** In this section, _horizontal whitespace_ refers to
> _interior_ space. These rules are never interpreted as requiring or forbidding
> additional space at the start of a line.
>
> **术语说明：**在这个章节，_水平空格_指的是_内部_空格。这些规则不适用于行开始时需要或禁止的额外空格。

Beyond where required by the language or other style rules, and apart from
literals and comments, a single Unicode space also appears in the following
places **only**:

根据语言要求或其他代码风格的规则，除了字面量和注释外的单个 Unicode 空格**只能**在下面这些情况出现：

1. Separating any reserved word starting a conditional or switch statement (such
   as `if`, `guard`, `while`, or `switch`) from the expression that follows it
   if that expression starts with an open parenthesis (`(`).

   条件或 switch 语句（例如 `if`，`guard`，`while` 或者 `switch`）开始的任何保留关键字要和它之后的表达式分隔开，如果该表达式是以左括号（`(`）开始的。
   
   ~~~ swift
   if (x == 0 && y == 0) || z == 0 {
     // ...
   }
   ~~~
   {:.good}

   ~~~ swift
   if(x == 0 && y == 0) || z == 0 {
     // ...
   }
   ~~~
   {:.bad}

1. Before any closing curly brace (`}`) that follows code on the same line,
   before any open curly brace (`{`), and after any open curly brace (`{`) that
   is followed by code on the same line.
   
   在同一行代码后面的右花括号（`}`）之前，任何左话括号（`{`）之前，后续代码在同一行的左花括号（`{`）之后。

   ~~~ swift
   let nonNegativeCubes = numbers.map { $0 * $0 * $0 }.filter { $0 >= 0 }
   ~~~
   {:.good}

   ~~~ swift
   let nonNegativeCubes = numbers.map { $0 * $0 * $0 } .filter { $0 >= 0 }
   let nonNegativeCubes = numbers.map{$0 * $0 * $0}.filter{$0 >= 0}
   ~~~
   {:.bad}

1. _On both sides_ of any binary or ternary operator, including the
   "operator-like" symbols described below, with exceptions noted at the end:

   在二元或者三元运算符的每一侧，包括下面描述的“类运算符”，除了最后的例外说明：

   1. The `=` sign used in assignment, initialization of variables/properties,
      and default arguments in functions.

      `=` 运算符用在赋值，变量/属性的构造过程以及函数里的默认实参时。

      ~~~ swift
      var x = 5
      
      func sum(_ numbers: [Int], initialValue: Int = 0) {
       // ...
      }
      ~~~
      {:.good}

      ~~~ swift
      var x=5
      
      func sum(_ numbers: [Int], initialValue: Int=0) {
       // ...
      }
      ~~~
      {:.bad}

   1. The ampersand (`&`) in a protocol composition type.
   
      And 符号（`&`）用在协议组合类型时。

      ~~~ swift
      func sayHappyBirthday(to person: NameProviding & AgeProviding) {
        // ...
      }
      ~~~
      {:.good}

      ~~~ swift
      func sayHappyBirthday(to person: NameProviding&AgeProviding) {
       // ...
      }
      ~~~
      {:.bad}

   1. The operator symbol in a function declaring/implementing that operator.

      运算符用在函数声明/实现时。

      ~~~ swift
      static func == (lhs: MyType, rhs: MyType) -> Bool {
        // ...
      }
      ~~~
       {:.good}

      ~~~ swift
      static func ==(lhs: MyType, rhs: MyType) -> Bool {
        // ...
      }
      ~~~
      {:.bad}

   1. The arrow (`->`) preceding the return type of a function.
   
      箭头（`->`）用在函数的返回类型之前时。
   
      ~~~ swift
      func sum(_ numbers: [Int]) -> Int {
        // ...
      }
      ~~~
      {:.good}
      
      ~~~ swift
      func sum(_ numbers: [Int])->Int {
        // ...
      }
      ~~~
      {:.bad}

   1. **Exception:** There is no space on either side of the dot (`.`) used to
      reference value and type members.

      **例外：**点（`.`）用在引用值和类型成员时两侧都没有空格。
      
      ~~~ swift
      let width = view.bounds.width
      ~~~
      {:.good}

      ~~~ swift
      let width = view . bounds . width
      ~~~
      {:.bad}

   1. **Exception:** There is no space on either side of the `..<` or `...`
      operators used in range expressions.
   
      **例外：**`..<` 或者 `…` 运算符用在范围表达式时两侧都没空格。
   
      ~~~ swift
      for number in 1...5 {
       // ...
      }
      
      let substring = string[index..<string.endIndex]
      ~~~
      {:.good}
      
      ~~~ swift
      for number in 1 ... 5 {
       // ...
      }
      
      let substring = string[index ..< string.endIndex]
      ~~~
      {:.bad}
   
1. After, but not before, the comma (`,`) in parameter lists and in
   tuple/array/dictionary literals.

   逗号（`,`）用在形参列表和元组/数组/字典字面量时，在逗号后面而不是前面。
   
   ~~~ swift
   let numbers = [1, 2, 3]
   ~~~
   {:.good}
   
   ~~~ swift
   let numbers = [1,2,3]
   let numbers = [1 ,2 ,3]
   let numbers = [1 , 2 , 3]
   ~~~
   {:.bad}

1. After, but not before, the colon (`:`) in

   冒号（`:`）的后面而不是前面用在

   1. Superclass/protocol conformance lists and generic constraints.

      父类/协议遵循列表和范型约束时。

      ~~~ swift
      struct HashTable: Collection {
        // ...
      }
      
      struct AnyEquatable<Wrapped: Equatable>: Equatable {
        // ...
      }
      ~~~
      {:.good}

      ~~~ swift
      struct HashTable : Collection {
        // ...
      }
      
      struct AnyEquatable<Wrapped : Equatable> : Equatable {
        // ...
      }
      ~~~
      {:.bad}

   1. Function argument labels and tuple element labels.

      函数实参标签和元组元素标签时。

      ~~~ swift
      let tuple: (x: Int, y: Int)
      
      func sum(_ numbers: [Int]) {
        // ...
      }
      ~~~
      {:.good}

      ~~~ swift
      let tuple: (x:Int, y:Int)
      let tuple: (x : Int, y : Int)
      
      func sum(_ numbers:[Int]) {
        // ...
      }
      
      func sum(_ numbers : [Int]) {
        // ...
      }
      ~~~
      {:.bad}

   1. Variable/property declarations with explicit types.

      变量/属性的类型显式声明时。

      ~~~ swift
      let number: Int = 5
      ~~~
      {:.good}

      ~~~ swift
      let number:Int = 5
      let number : Int = 5
      ~~~
      {:.bad}

   1. Shorthand dictionary type names.

      字典类型缩写时。

      ~~~ swift
      var nameAgeMap: [String: Int] = []
      ~~~
      {:.good}

      ~~~ swift
      var nameAgeMap: [String:Int] = []
      var nameAgeMap: [String : Int] = []
      ~~~
      {:.bad}

   1. Dictionary literals.

      字典字面量时。
      
      ~~~ swift
      let nameAgeMap = ["Ed": 40, "Timmy": 9]
      ~~~
      {:.good}
      
      ~~~ swift
      let nameAgeMap = ["Ed":40, "Timmy":9]
      let nameAgeMap = ["Ed" : 40, "Timmy" : 9]
      ~~~
      {:.bad}

1. At least two spaces before and exactly one space after the double slash
   (`//`) that begins an end-of-line comment.

   双斜杠（`//`）用于开始行结束的注释时，双斜杠之前最少两个空格，之后正好一个空格。
   
   ~~~ swift
   let initialFactor = 2  // Warm up the modulator.
   ~~~
   {:.good}
   
   ~~~ swift
   let initialFactor = 2 //    Warm up the modulator.
   ~~~
   {:.bad}

1. Outside, but not inside, the brackets of an array or dictionary literals and
   the parentheses of a tuple literal.

   括号用于数组、字典或元组字面量时，括号外面而不是里面。
   
   ~~~ swift
   let numbers = [1, 2, 3]
   ~~~
   {:.good}
   
   ~~~ swift
   let numbers = [ 1, 2, 3 ]
   ~~~
   {:.bad}

### 水平对齐/Horizontal Alignment

> **Terminology note:** _Horizontal alignment_ is the practice of adding a
> variable number of additional spaces in your code with the goal of making
> certain tokens appear directly below certain other tokens on previous lines.
>
> **术语说明：**_水平对齐_是一种约定，通过在代码中添加不同数量的空格让某些元素直接显示在前面行中该类型的其他元素下面。

Horizontal alignment is forbidden except when writing obviously tabular data
where omitting the alignment would be harmful to readability. In other cases
(for example, lining up the types of stored property declarations in a `struct`
or `class`), horizontal alignment is an invitation for maintenance problems if a
new member is introduced that requires every other member to be realigned.

水平对齐是禁止的，除了在分明的表格数据里，省略会不利于可读性之外。其他情况下（例如，对 `struct` 或  `class` 里的存储属性声明的类型进行对齐）水平对齐会引起维护问题，因为在新的成员引入时其余所有的成员都需要重新对齐。

~~~ swift
struct DataPoint {
  var value: Int
  var primaryColor: UIColor
}
~~~
{:.good}

~~~ swift
struct DataPoint {
  var value:        Int
  var primaryColor: UIColor
}
~~~
{:.bad}

### 垂直空行/Vertical Whitespace

A single blank line appears in the following locations:

单独的空白行在以下这些情况下出现：

1. Between consecutive members of a type: properties, initializers, methods,
   enum cases, and nested types, **except that**:

   在类型中这些连续成员之间：属性，构造器，方法，枚举情况，嵌套类型，**除了**：
   
   1. A blank line is optional between two consecutive stored properties or two
      enum cases whose declarations fit entirely on a single line. Such blank
      lines can be used to create _logical groupings_ of these declarations.
      
      两个连续的存储属性或者枚举里两个也完全适合声明在一行里的 case，之间空白行是可选的。这时候空白行可以用来将这些声明进行_逻辑分组_。
      
   1. A blank line is optional between two extremely closely related properties
   that do not otherwise meet the criterion above; for example, a private
      stored property and a related public computed property.
   
     不适用于前面规则，但两个属性非常相关，之间的空白行也是可选的。例如，一个私有的存储属性和它相关的公开计算属性。
   
1. _Only as needed_ between statements to organize code into logical
   subsections.
   
   **只有需要**用于组织代码进行逻辑分段的语句之间。
   
1. _Optionally_ before the first member or after the last member of a type
   (neither is encouraged nor discouraged).
   
   类型的第一个成员之前或者最后一个成员之后的空白行是_可选的_（不赞成也不反对）。
   
1. Anywhere explicitly required by other sections of this document.

   本文档中其他章节中明确要求的地方。

_Multiple_ blank lines are permitted, but never required (nor encouraged). If
you do use multiple consecutive blank lines, do so consistently throughout your
code base.

_多个_空白行是允许的，但不是必须的（不赞成）。如果你使用多个连续的空白行，那么在你的代码里应该贯彻到底。

### 括号/Parentheses

Parentheses are **not** used around the top-most expression that follows an
`if`, `guard`, `while`, or `switch` keyword.

`if`，`guard`，`while` 或 `switch` 关键字后面的顶层表达式**不需要**使用括号。

~~~ swift
if x == 0 {
  print("x is zero")
}

if (x == 0 || y == 1) && z == 2 {
  print("...")
}
~~~
{:.good}

~~~ swift
if (x == 0) {
  print("x is zero")
}

if ((x == 0 || y == 1) && z == 2) {
  print("...")
}
~~~
{:.bad}

Optional grouping parentheses are omitted only when the author and the reviewer
agree that there is no reasonable chance that the code will be misinterpreted
without them, nor that they would have made the code easier to read. It is _not_
reasonable to assume that every reader has the entire Swift operator precedence
table memorized.

分组括号是可选的，只有当作者和审查者觉得没有也不会令代码容易误解，或者会让代码更容易阅读时才可以被省略。**不**要认为每个阅读者都能记得完整的 Swift 操作符优先级表格。

## 特定结构格式化/Formatting Specific Constructs

### 非文档注释/Non-Documentation Comments

Non-documentation comments always use the double-slash format (`//`), never the
C-style block format (`/* ... */`).

非文档注释总是用双斜杠进行格式化（`//`），而不要使用 C 风格的块格式化（`/* ... */`）。

### 属性/Properties

Local variables are declared close to the point at which they are first used
(within reason) to minimize their scope.

局部变量尽量声明在接近首次使用的地方，（在合理的情况下）最小化作用域。

With the exception of tuple destructuring, every `let` or `var` statement
(whether a property or a local variable) declares exactly one variable.

除了元组的解构时，每个 `let` 或者 `var` 语句（无论是属性或者局部变量）只声明一个变量。

~~~ swift
var a = 5
var b = 10

let (quotient, remainder) = divide(100, 9)
~~~
{:.good}

~~~ swift
var a = 5, b = 10
~~~
{:.bad}

### Switch 语句/Switch Statements

Case statements are indented at the _same_ level as the switch statement to
which they belong; the statements inside the case blocks are then indented +2
spaces from that level.

Case 语句的缩进和它们的 switch 语句保持_一致_;case 块里的语句在该缩进基础上+2空格。

~~~ swift
switch order {
case .ascending:
  print("Ascending")
case .descending:
  print("Descending")
case .same:
  print("Same")
}
~~~
{:.good}

~~~ swift
switch order {
  case .ascending:
    print("Ascending")
  case .descending:
    print("Descending")
  case .same:
    print("Same")
}
~~~
{:.bad}

~~~ swift
switch order {
case .ascending:
print("Ascending")
case .descending:
print("Descending")
case .same:
print("Same")
}
~~~
{:.bad}

### 枚举 Case/Enum Cases

In general, there is only one `case` per line in an `enum`. The comma-delimited
form may be used only when none of the cases have associated values or raw
values, all cases fit on a single line, and the cases do not need further
documentation because their meanings are obvious from their names.

通常来说，在一个 `enum` 里每行只有一个 `case`。逗号分隔形式只能在 case 都没有关联值或者原始值时使用，所有 case 都能从名字明确其含义而不需要额外的注释，就可以写在同一行。

~~~ swift
public enum Token {
  case comma
  case semicolon
  case identifier
}

public enum Token {
  case comma, semicolon, identifier
}

public enum Token {
  case comma
  case semicolon
  case identifier(String)
}
~~~
{:.good}

~~~ swift
public enum Token {
  case comma, semicolon, identifier(String)
}
~~~
{:.bad}

When all cases of an `enum` must be `indirect`, the `enum` itself is declared
`indirect` and the keyword is omitted on the individual cases.

当 `enum` 里所有 case 都需要被声明为 `indirect` 时，该 `enum` 就声明为 `indirect`，单独 case 前面的关键字就可以省略。

~~~ swift
public indirect enum DependencyGraphNode {
  case userDefined(dependencies: [DependencyGraphNode])
  case synthesized(dependencies: [DependencyGraphNode])
}
~~~
{:.good}

~~~ swift
public enum DependencyGraphNode {
  indirect case userDefined(dependencies: [DependencyGraphNode])
  indirect case synthesized(dependencies: [DependencyGraphNode])
}
~~~
{:.bad}

When an `enum` case does not have associated values, empty parentheses are never
present.

当 `enum` 的 case 没有关联值时，不应该出现空的括号。

~~~ swift
public enum BinaryTree<Element> {
  indirect case node(element: Element, left: BinaryTree, right: BinaryTree)
  case empty  // GOOD.
}
~~~
{:.good}

~~~ swift
public enum BinaryTree<Element> {
  indirect case node(element: Element, left: BinaryTree, right: BinaryTree)
  case empty()  // AVOID.
}
~~~
{:.bad}

The cases of an enum must follow a logical ordering that the author could
explain if asked. If there is no obviously logical ordering, use a
lexicographical ordering based on the cases' names.

枚举的 case 必须遵循一定的可解释排序逻辑。如果没有明显的排序逻辑，按照 case 名字的首字母排序。

In the following example, the cases are arranged in numerical order based on the
underlying HTTP status code and blank lines are used to separate groups.

在下面的例子中，case 根据其表示的 HTTP 状态码数字进行排序，并通过空行进行分组。

~~~ swift
public enum HTTPStatus: Int {
  case ok = 200

  case badRequest = 400
  case notAuthorized = 401
  case paymentRequired = 402
  case forbidden = 403
  case notFound = 404

  case internalServerError = 500
}
~~~
{:.good}

The following version of the same enum is less readable. Although the cases are
ordered lexicographically, the meaningful groupings of related values has been
lost.

同样的枚举，下面这个版本的写法可读性就差一些。尽管 case 根据字母排序，但是却失去了对关联值含义的表达。

~~~ swift
public enum HTTPStatus: Int {
  case badRequest = 400
  case forbidden = 403
  case internalServerError = 500
  case notAuthorized = 401
  case notFound = 404
  case ok = 200
  case paymentRequired = 402
}
~~~
{:.bad}

### 尾随闭包/Trailing Closures

Functions should not be overloaded such that two overloads differ _only_ by the
name of their trailing closure argument. Doing so prevents using trailing
closure syntax&mdash;when the label is not present, a call to the function with
a trailing closure is ambiguous.

函数重载时，不能出现两个重载_只有_尾随闭包的实参名字有区别的情况。

Consider the following example, which prohibits using trailing closure syntax to
call `greet`:

考虑下面的例子，这样会不允许用尾随闭包语法来调用 `greet`：

~~~ swift
func greet(enthusiastically nameProvider: () -> String) {
  print("Hello, \(nameProvider())! It's a pleasure to see you!")
}

func greet(apathetically nameProvider: () -> String) {
  print("Oh, look. It's \(nameProvider()).")
}

greet { "John" }  // error: ambiguous use of 'greet'
~~~
{:.bad}

This example is fixed by differentiating some part of the function name other
than the closure argument&mdash;in this case, the base name:

这个例子可以用除闭包实参外函数名的一部分差异来区分——具体这种情况下，可以用函数的基础名字：

~~~ swift
func greetEnthusiastically(_ nameProvider: () -> String) {
  print("Hello, \(nameProvider())! It's a pleasure to see you!")
}

func greetApathetically(_ nameProvider: () -> String) {
  print("Oh, look. It's \(nameProvider()).")
}

greetEnthusiastically { "John" }
greetApathetically { "not John" }
~~~
{:.good}

If a function call has multiple closure arguments, then _none_ are called using
trailing closure syntax; _all_ are labeled and nested inside the argument
list's parentheses.

当一个函数调用有多个闭包实参，那么_都不_使用尾随闭包语法调用；_都_需要写出标签并放在在实参列表的括号里。

~~~ swift
UIView.animate(
  withDuration: 0.5,
  animations: {
    // ...
  },
  completion: { finished in
    // ...
  })
~~~
{:.good}

~~~ swift
UIView.animate(
  withDuration: 0.5,
  animations: {
    // ...
  }) { finished in
    // ...
  }
~~~
{:.bad}

If a function has a single closure argument and it is the final argument, then
it is _always_ called using trailing closure syntax, except in the following
cases to resolve ambiguity or parsing errors:

如果函数只有一个闭包实参并且是最后的实参，那么_永远_使用尾随闭包语法调用它，除了下面这些解决歧义或者分析错误的情况：

1. As described above, labeled closure arguments must be used to disambiguate
   between two overloads with otherwise identical arguments lists.
   
   如上面所描述，必须使用带标签的闭包参数来消除两个其他实参列表都相同的重载之间的歧义。
   
1. Labeled closure arguments must be used in control flow statements where the
   body of the trailing closure would be parsed as the body of the control flow
   statement.
   
   在控制流语句里必须使用带标签的闭包实参，因为尾随闭包会被解析成控制流语句的执行体。

~~~ swift
Timer.scheduledTimer(timeInterval: 30, repeats: false) { timer in
  print("Timer done!")
}

if let firstActive = list.first(where: { $0.isActive }) {
  process(firstActive)
}
~~~
{:.good}

~~~ swift
Timer.scheduledTimer(timeInterval: 30, repeats: false, block: { timer in
  print("Timer done!")
})

// This example fails to compile.
if let firstActive = list.first { $0.isActive } {
  process(firstActive)
}
~~~
{:.bad}

When a function called with trailing closure syntax takes no other arguments,
empty parentheses (`()`) after the function name are _never_ present.

如果函数调用使用的是尾随闭包语法且没有其他实参，函数名后面的空括号（`()`）_永远不_要出现。

~~~ swift
let squares = [1, 2, 3].map { $0 * $0 }
~~~
{:.good}

~~~ swift
let squares = [1, 2, 3].map({ $0 * $0 })
let squares = [1, 2, 3].map() { $0 * $0 }
~~~
{:.bad}

### 末尾逗号/Trailing Commas

Trailing commas in array and dictionary literals are _required_ when each
element is placed on its own line. Doing so produces cleaner diffs when items
are added to those literals later.

当数组和字典里字面量里每个元素独占一行时_需要_加上末尾逗号。这样做在这些字面量后续加入新的元素时会有更明显的区分。

~~~ swift
let configurationKeys = [
  "bufferSize",
  "compression",
  "encoding",                                    // GOOD.
]
~~~
{:.good}

~~~ swift
let configurationKeys = [
  "bufferSize",
  "compression",
  "encoding"                                     // AVOID.
]
~~~
{:.bad}

### 数字字面量/Numeric Literals

It is recommended but not required that long numeric literals (decimal,
hexadecimal, octal, and binary) use the underscore (`_`) separator to group
digits for readability when the literal has numeric value or when there exists a
domain-specific grouping.

当长数字字面量（十进制，十六进制，八进制和二进制）有数值或存在特定领域分组时建议使用下划线（`_`）对数字进行分组，但不强制。

Recommended groupings are three digits for decimal (thousands separators), four
digits for hexadecimal, four or eight digits for binary literals, or
value-specific field boundaries when they exist (such as three digits for octal
file permissions).

十进制建议每三个数字分组（按千数量级分隔），十六进制建议每四个数字分组，二进制建议每四或八个数字进行分组，或者根据存在的特定值的字段边界进行分组（例如八进制文件权限的三个数字）。

Do not group digits if the literal is an opaque identifier that does not have a
meaningful numeric value.

如果字面量是透明标识符且没有数值含义，则不要分组。

### 注解/Attributes

Parameterized attributes (such as `@availability(...)` or `@objc(...)`) are each
written on their own line immediately before the declaration to which they
apply, are lexicographically ordered, and are indented at the same level as the
declaration.

每个带参数的注解（例如 `@availability(…)` 或 `@objc(…)`）写在其适用声明的前面单独一行，并且按照首字母排序，缩进和声明保持一致。

~~~ swift
@available(iOS 9.0, *)
public func coolNewFeature() {
  // ...
}
~~~
{:.good}

~~~ swift
@available(iOS 9.0, *) public func coolNewFeature() {
  // ...
}
~~~
{:.bad}

Attributes without parameters (for example, `@objc` without arguments,
`@IBOutlet`, or `@NSManaged`) are lexicographically ordered and _may_ be placed
on the same line as the declaration if and only if they would fit on that line
without requiring the line to be rewrapped. If placing an attribute on the same
line as the declaration would require a declaration to be wrapped that
previously did not need to be wrapped, then the attribute is placed on its own
line.

不带参数的注解（例如不带参数的 `@objc` ，`@IBOutlet` 或者 `@NSManaged`）当且仅当不导致换行时_可以_按首字母排序与声明写在同一行。如果在声明的行增加该注解后导致需要换行的话，则将注解另起一行。

~~~ swift
public class MyViewController: UIViewController {
  @IBOutlet private var tableView: UITableView!
}
~~~
{:.good}


## 命名/Naming

### Apple API 代码风格指南/Apple's API Style Guidelines

Apple's
[official Swift naming and API design guidelines](https://swift.org/documentation/api-design-guidelines/)
hosted on swift.org are considered part of this style guide and are followed as
if they were repeated here in their entirety.

这部分代码风格指南是参考 Apple 官方的 Swift 命名和 API 代码风格指南而成的，并且应该遵循那些在这里重复的部分。

### 命名约定不涉及访问控制/Naming Conventions Are Not Access Control

Restricted access control (`internal`, `fileprivate`, or `private`) is preferred
for the purposes of hiding information from clients, rather than naming
conventions.

使用约定俗成的访问控制（`internal`，`fileprivate` 或 `private`）来达到隐藏信息的目的，而不要使用命名约定。

Naming conventions (such as prefixing a leading underscore) are only used in
rare situations when a declaration must be given higher visibility than is
otherwise desired in order to work around language limitations&mdash;for
example, a type that has a method that is only intended to be called by other
parts of a library implementation that crosses module boundaries and must
therefore be declared `public`.

命名约定（例如下划线前缀）只有在声明必须用到更高的可见性来解决语言限制的罕见情况下使用——例如，类型有一个方法，只打算被另一个库的实现跨模块调用，导致必须被声明为 `public` 的情况下。

### 标识符/Identifiers

In general, identifiers contain only 7-bit ASCII characters. Unicode identifiers
are allowed if they have a clear and legitimate meaning in the problem domain
of the code base (for example, Greek letters that represent mathematical
concepts) and are well understood by the team who owns the code.

通常来说，标识符只能包含 7 位 ASCII 码字符。Unicode 标识符只有在代码所需要解决的问题领域有明确且合理的含义（例如：希腊字母用于表达数学上的概念），并且能被团队成员理解的情况下才可以使用。

~~~ swift
let smile = "😊"
let deltaX = newX - previousX
let Δx = newX - previousX
~~~
{:.good}

~~~ swift
let 😊 = "😊"
~~~
{:.bad}

### 构造器/Initializers

For clarity, initializer arguments that correspond directly to a stored property
have the same name as the property. Explicit `self.` is used during assignment
to disambiguate them.

为了更明确地表达，构造器实参和其直接对应的存储属性同名。在赋值的时候使用显式 `self.` 来消除歧义。

~~~ swift
public struct Person {
  public let name: String
  public let phoneNumber: String

  // GOOD.
  public init(name: String, phoneNumber: String) {
    self.name = name
    self.phoneNumber = phoneNumber
  }
}
~~~
{:.good}

~~~ swift
public struct Person {
  public let name: String
  public let phoneNumber: String

  // AVOID.
  public init(name otherName: String, phoneNumber otherPhoneNumber: String) {
    name = otherName
    phoneNumber = otherPhoneNumber
  }
}
~~~
{:.bad}

### 静态属性和类属性/Static and Class Properties

Static and class properties that return instances of the declaring type are
_not_ suffixed with the name of the type.

静态属性和类属性返回声明类型的实例时**不需要**加上该类型名字作后缀。

~~~ swift
public class UIColor {
  public class var red: UIColor {                // GOOD.
    // ...
  }
}

public class URLSession {
  public class var shared: URLSession {          // GOOD.
    // ...
  }
}
~~~
{:.good}

~~~ swift
public class UIColor {
  public class var redColor: UIColor {           // AVOID.
    // ...
  }
}

public class URLSession {
  public class var sharedSession: URLSession {   // AVOID.
    // ...
  }
}
~~~
{:.bad}

When a static or class property evaluates to a singleton instance of the
declaring type, the names `shared` and `default` are commonly used. This style
guide does not require specific names for these; the author should choose a name
that makes sense for the type.

当静态属性或者类属性用于描述该声明类型的单例实例时，通常使用 `shared` 和 `default` 作为名字。这个代码风格指南不强制要求使用这些命名，作者可以自行选择对该类型有意义的名字。

### 全局常量/Global Constants

Like other variables, global constants are `lowerCamelCase`. Hungarian notation,
such as a leading `g` or `k`, is not used.

和其他变量类似，全局常量也使用 `lowerCamelCase(驼峰命名法)`。而不使用匈牙利命名法，例如以 `g` 或者 `k` 开头。

~~~ swift
let secondsPerMinute = 60
~~~
{:.good}

~~~ swift
let SecondsPerMinute = 60
let kSecondsPerMinute = 60
let gSecondsPerMinute = 60
let SECONDS_PER_MINUTE = 60
~~~
{:.bad}

### 代理方法/Delegate Methods

Methods on delegate protocols and delegate-like protocols (such as data sources)
are named using the linguistic syntax described below, which is inspired by
Cocoa's protocols.

代理协议和类似代理的协议（例如数据源协议）里的方法命名使用下面描述的口语化语法，这是受 Cocoa 框架里协议的命名启发而来。

> The term "delegate's source object" refers to the object that invokes methods
> on the delegate. For example, a `UITableView` is the source object that
> invokes methods on the `UITableViewDelegate` that is set as the view's
> `delegate` property.
>
> 术语“代理源对象”指的是响应代理方法的对象。例如：`UITableView` 是响应视图 `delegate` 属性设置的 `UITableViewDeleagte` 里方法的源对象。

All methods take the delegate's source object as the first argument.

所有方法将代理源对象作为第一个实参。

For methods that take the delegate's source object as their **only** argument:

对于**只**有代理源对象实参的方法：

* If the method returns `Void` (such as those used to notify the delegate that
  an event has occurred), then the method's base name is the **delegate's
  source type** followed by an **indicative verb phrase** describing the
  event. The argument is **unlabeled.**

* 如果方法返回 `void`（例如用于提醒代理事件发生），那么方法名为**代理源类型**后面加上描述事件的**指示性动词**。实参**无标签**。
  
  ~~~ swift
  func scrollViewDidBeginScrolling(_ scrollView: UIScrollView)
  ~~~
  {:.good}
  
* If the method returns `Bool` (such as those that make an assertion about the
  delegate's source object itself), then the method's name is the **delegate's
  source type** followed by an **indicative or conditional verb phrase**
  describing the assertion. The argument is **unlabeled.**

* 如果方法返回 `Bool`（例如对代理源对象本身做断言），那么方法名为**代理源类型**后面加上描述断言的**指示性或条件性动词**。实参**无标签**。
  
  ~~~ swift
  func scrollViewShouldScrollToTop(_ scrollView: UIScrollView) -> Bool
  ~~~
  {:.good}

* If the method returns some other value (such as those querying for
  information about a property of the delegate's source object), then the
  method's base name is a **noun phrase** describing the property being
  queried. The argument is **labeled with a preposition or phrase with a
  trailing preposition** that appropriately combines the noun phrase and the
  delegate's source object.

* 如果方法返回其他值（例如查询代理源对象上的属性信息），那么方法名是描述查询属性的**名词**。实参**标签是介词或后置介词**，用于将名词和代理源对象合适地连接起来。
  
  ~~~ swift
  func numberOfSections(in scrollView: UIScrollView) -> Int
  ~~~
  {:.good}

For methods that take **additional** arguments after the delegate's source
object, the method's base name is the delegate's source type **by itself** and
the first argument is **unlabeled.** Then:

对于在代理源对象后有**额外**实参的方法，方法名是代理源类型**自身**并且第一个实参**无标签**。然后：

* If the method returns `Void`, the second argument is **labeled with an
  indicative verb phrase** describing the event that has the argument as its
  **direct object or prepositional object,** and any other arguments (if
  present) provide further context.

* 如果方法返回 `void`，第二个实参**标签是指示性动词**，用于描述实参是**直接宾语或者间接宾语**的事件，并给其它实参（如果有的话）提供更多上下文。
  
  ~~~ swift
  func tableView(
  _ tableView: UITableView,
    willDisplayCell cell: UITableViewCell,
    forRowAt indexPath: IndexPath)
  ~~~
  {:.good}
  
* If the method returns `Bool`, the second argument is **labeled with an
  indicative or conditional verb phrase** that describes the return value in
  terms of the argument, and any other arguments (if present) provide further
  context.

* 如果方法返回 `Bool`，第二个实参**标签是指示性或者条件性动词**，用于描述对于实参的返回值，并给其他实参（如果有的话）提供更多上下文。
  
  ~~~ swift
  func tableView(
  _ tableView: UITableView,
    shouldSpringLoadRowAt indexPath: IndexPath,
    with context: UISpringLoadedInteractionContext
  ) -> Bool
  ~~~
  {:.good}
  
* If the method returns some other value, the second argument is **labeled
  with a noun phrase and trailing preposition** that describes the return
  value in terms of the argument, and any other arguments (if present) provide
  further context.

* 如果方法返回其他值，第二个实参**标签是名词和后置介词**，用于描述对于实参的返回值，并给其他实参（如果有的话）提供更多上下文。
  
  ~~~ swift
  func tableView(
  _ tableView: UITableView,
    heightForRowAt indexPath: IndexPath
  ) -> CGFloat
  ~~~
  {:.good}

Apple's documentation on
[delegates and data sources](https://developer.apple.com/library/content/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html)
also contains some good general guidance about such names.

Apple 的 [代理和数据源](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html) 文档也提供了一些在这种情况下的通用命名指引。

## 编程实践/Programming Practices

Common themes among the rules in this section are: avoid redundancy, avoid
ambiguity, and prefer implicitness over explicitness unless being explicit
improves readability and/or reduces ambiguity.

本章节中规则的通用主旨是：避免冗余，避免歧义，除了能明显提高可读性和/或减少歧义外尽量使用隐式而不是显式。

### 编译器警告/Compiler Warnings

Code should compile without warnings when feasible. Any warnings that are able
to be removed easily by the author must be removed.

代码在编译时尽可能保持没有警告。任何可以简单去除的警告作者都应该去除。

A reasonable exception is deprecation warnings, where it may not be possible to
immediately migrate to the replacement API, or where an API may be deprecated
for external users but must still be supported inside a library during a
deprecation period.

在不可能马上迁移到替代 API 时或者在 API 对外部用户废弃但还需要继续对库内部支持的废弃时期，有理由的废弃警告可以例外。

### 构造器/Initializers

For `struct`s, Swift synthesizes a non-public memberwise `init` that takes
arguments for `var` properties and for any `let` properties that lack default
values. When that initializer is suitable (that is, a `public` one is not
needed), it is used and no explicit initializer is written.

对于 `Struct`，Swift 会合成实参为 `var` 属性和缺少默认值的 `let` 属性的非公开逐一成员 `init`。如果该构造器已经足够（也就是说不需要 `public` 的话），直接使用而不需要书写显式的构造器。

The initializers declared by the special `ExpressibleBy*Literal` compiler
protocols are never called directly.

遵循特殊的 `ExpressibleBy*Literal` 编译器协议而声明的构造器永远不应该直接调用。

~~~ swift
struct Kilometers: ExpressibleByIntegerLiteral {
  init(integerLiteral value: Int) {
    // ...
  }
}

let k1: Kilometers = 10                          // GOOD.
let k2 = 10 as Kilometers                        // ALSO GOOD.
~~~
{:.good}

~~~ swift
struct Kilometers: ExpressibleByIntegerLiteral {
  init(integerLiteral value: Int) {
    // ...
  }
}

let k = Kilometers(integerLiteral: 10)           // AVOID.
~~~
{:.bad}

Explicitly calling `.init(...)` is allowed only when the receiver of the call is
a metatype variable. In direct calls to the initializer using the literal type
name, `.init` is omitted. (**Referring** to the initializer directly by using
`MyType.init` syntax to convert it to a closure is permitted.)

只有当调用者是元类型变量时才允许明确调用  `.init(...)`  。使用字面量类型名字直接调用构造器时，省略 `.init`。（构造器使用 `MyType.init` 语法作为闭包进行**引用**是允许的。）

~~~ swift
let x = MyType(arguments)

let type = lookupType(context)
let x = type.init(arguments)

let x = makeValue(factory: MyType.init)
~~~
{:.good}

~~~ swift
let x = MyType.init(arguments)
~~~
{:.bad}

### 属性/Properties

The `get` block for a read-only computed property is omitted and its body is
directly nested inside the property declaration.

只读计算属性的 `get` 块可以省略，并将执行体直接嵌套在属性声明里。

~~~ swift
var totalCost: Int {
  return items.sum { $0.cost }
}
~~~
{:.good}

~~~ swift
var totalCost: Int {
  get {
    return items.sum { $0.cost }
  }
}
~~~
{:.bad}

### 类型简称/Types with Shorthand Names

Arrays, dictionaries, and optional types are written in their shorthand form
whenever possible; that is, `[Element]`, `[Key: Value]`, and `Wrapped?`. The
long forms `Array<Element>`, `Dictionary<Key, Value>`, and `Optional<Wrapped>`
are only written when required by the compiler; for example, the Swift parser
requires `Array<Element>.Index` and does not accept `[Element].Index`.

数组，字典和可选类型尽可能使用简写形式，也就是 `[Element]`，`[Key: Value]` 和 `Wrapped?`。完整形式 `Array<Element>`，`Dictionary<Key, Value>` 和 `Optional<Wrapped>` 只有在编译器需要时才使用，例如 Swift 语法分析程序不接受 `[Element].Index` 而需要用 `Array<Element>.Index`。

~~~ swift
func enumeratedDictionary<Element>(
  from values: [Element],
  start: Array<Element>.Index? = nil
) -> [Int: Element] {
  // ...
}
~~~
{:.good}

~~~ swift
func enumeratedDictionary<Element>(
  from values: Array<Element>,
  start: Optional<Array<Element>.Index> = nil
) -> Dictionary<Int, Element> {
  // ...
}
~~~
{:.bad}

`Void` is a `typealias` for the empty tuple `()`, so from an implementation
point of view they are equivalent. In function type declarations (such as
closures, or variables holding a function reference), the return type is always
written as `Void`, never as `()`. In functions declared with the `func` keyword,
the `Void` return type is omitted entirely.

`Void` 是空元组 `()` 的 `typealias`，所以从实现来说它们是等价的。在函数类型声明（例如闭包或者持有函数引用变量）的返回类型永远写作 `void`，而不用 `()`。在用 `func` 关键字声明的函数中，全都省略 `void` 返回类型。

Empty argument lists are always written as `()`, never as `Void`. (In fact,
the function signature `Void -> Result` is an error in Swift because function
arguments must be surrounded by parentheses, and `(Void)` has a different
meaning: an argument list with a single empty-tuple argument.)

空的实参列表永远写作 `()`，而不是 `Void`。（事实上， `Void -> Result` 的函数签名在 Swift 里会报错，因为函数实参必须用括号包围，而 `(void)` 有着其他含义：单个空元组实参的实参列表。

~~~ swift
func doSomething() {
  // ...
}

let callback: () -> Void
~~~
{:.good}

~~~ swift
func doSomething() -> Void {
  // ...
}

func doSomething2() -> () {
  // ...
}

let callback: () -> ()
~~~
{:.bad}

### 可选类型/Optional Types

Sentinel values are avoided when designing algorithms (for example, an "index"
of &minus;1 when an element was not found in a collection). Sentinel values can
easily and accidentally propagate through other layers of logic because the type
system cannot distinguish between them and valid outcomes.

在设计算法时避免哨兵值（例如使用 -1 的 “索引” 表示集合里找不到某个元素）。哨兵值容易被偶然传递到其它逻辑层，因为类型系统没办法将它们和合法结果进行区分。

`Optional` is used to convey a non-error result that is either a value or the
absence of a value. For example, when searching a collection for a value, not
finding the value is still a **valid and expected** outcome, not an error.

`Optional` 用于值和缺省值其中之一的表达，是非错误结果。例如：在集合中查询一个值时，值没有找到是一个**合法并可预期**的结果，而不是一个错误。

~~~ swift
func index(of thing: Thing, in things: [Thing]) -> Int? {
  // ...
}

if let index = index(of: thing, in: lotsOfThings) {
  // Found it.
} else {
  // Didn't find it.
}
~~~
{:.good}

~~~ swift
func index(of thing: Thing, in things: [Thing]) -> Int {
  // ...
}

let index = index(of: thing, in: lotsOfThings)
if index != -1 {
  // Found it.
} else {
  // Didn't find it.
}
~~~
{:.bad}

`Optional` is also used for error scenarios when there is a single, obvious
failure state; that is, when an operation may fail for a single domain-specific
reason that is clear to the client. (The domain-specific restriction is meant to
exclude severe errors that are typically out of the user's control to properly
handle, such as out-of-memory errors.)

`Optional` 也用于表示单一而明确失败的错误哨兵，也就是当操作是因为使用者明确的单个特定领域原因而失败时。（限制在特定领域是为了排除那些用户明显无法正确处理的严重错误，例如内存不足错误。）

For example, converting a string to an integer would fail if the
string does not represent a valid integer that fits into the type's bit width:

例如，如果字符串不能用适合类型位宽的合法整数表达，将字符串转换为整型可能会失败：

~~~ swift
struct Int17 {
  init?(_ string: String) {
    // ...
  }
}
~~~
{:.good}

Conditional statements that test that an `Optional` is non-`nil` but do not
access the wrapped value are written as comparisons to `nil`. The following
example is clear about the programmer's intent:

判断一个 `Optional` 是否非 `nil` 但不需要访问解包值时，条件语句用和 `nil` 比较的形式。下面的例子能清晰地表达程序意图：

~~~ swift
if value != nil {
  print("value was not nil")
}
~~~
{:.good}

This example, while taking advantage of Swift's pattern matching and binding
syntax, obfuscates the intent by appearing to unwrap the value and then
immediately throw it away.

这个例子里，如果利用 Swift 模式匹配和绑定语法，将值解包后马上丢弃，会混淆程序意图。

~~~ swift
if let _ = value {
  print("value was not nil")
}
~~~
{:.bad}

### 错误类型/Error Types

Error types are used when there are multiple possible error states.

错误类型在错误有多种可能得状态时使用。

Throwing errors instead of merging them with the return type cleanly separates
concerns in the API. Valid inputs and valid state produce valid outputs in the
result domain and are handled with standard sequential control flow. Invalid
inputs and invalid state are treated as errors and are handled using the
relevant syntactic constructs (`do`-`catch` and `try`). For example:

将错误抛出而不是随着返回值返回可以更清晰地将问题从 API 里分离。合法输入和合法状态在结果域里产生合法输出，并通过标准的控制流进行处理。非法输入和非法状态应视作错误，并使用相关语法结构进行处理（`do`-`catch` 和 `try`）。例如：

~~~ swift
struct Document {
  enum ReadError: Error {
    case notFound
    case permissionDenied
    case malformedHeader
  }

  init(path: String) throws {
    // ...
  }
}

do {
  let document = try Document(path: "important.data")
} catch Document.ReadError.notFound {
  // ...
} catch Document.ReadError.permissionDenied {
  // ...
} catch {
  // ...
}
~~~
{:.good}

Such a design forces the caller to consciously acknowledge the failure case by:

下面这样的设计能迫使调用者有意识地面对错误：

* wrapping the calling code in a `do`-`catch` block and handling error cases to
  whichever degree is appropriate,
* 将代码包在 `do`-`catch` 块里调用，并根据错误严重程度进行处理，
* declaring the function in which the call is made as `throws` and letting the
  error propagate out, or
* 将函数声明为在调用时 `throws` 并将错误传递给上层，或者
* using `try?` when the specific reason for failure is unimportant and only the
  information about whether the call failed is needed.
* 在某些不重要失败原因并且只需要调用是否失败的信息时使用 `try?` 。

In general, with exceptions noted below, force-`try!` is forbidden; it is
equivalent to `try` followed by `fatalError` but without a meaningful message.
If an error outcome would mean that the program is in such an unrecoverable
state that immediate termination is the only reasonable action, it is better to
use `do`-`catch` or `try?` and provide more context in the error message to
assist debugging if the operation does fail.

通常来说，除了下面的说明以外，强制-`try!` 是禁止的；它等同于对 `fatalError` 使用 `try` 但却没有有意义的信息。如果某个错误的发生意味着程序处在无法恢复的状态，那么立即终止是唯一合理的动作，这时使用 `do`-`catch` 或者 `try?` 并提供错误的更多上下文信息，可以更好地帮助调试。

> **Exception:** Force-`try!` is allowed in unit tests and test-only code. It is
> also allowed in non-test code when it is unmistakably clear that an error
> would only be thrown because of **programmer** error; we specifically define
> this to mean a single expression that could be evaluated without context in
> the Swift REPL. For example, consider initializing a regular expression from a
> a string literal:
>
> **例外：**强制-`try!` 在单元测试和仅用于测试的代码是允许使用的。也可以在非测试代码里使用，在错误抛出非常明确只可能是由**编程人员**导致时；我们特别定义这种情况，是因为在 Swift REPL 里有些单个表达式没有上下文就无法被推断。例如，考虑通过字符串字面量来构造正则表达式的情况：
>
> ~~~ swift
> let regex = try! NSRegularExpression(pattern: "a*b+c?")
> ~~~
> {:.good}
>
> The `NSRegularExpression` initializer throws an error if the regular
> expression is malformed, but when it is a string literal, the error would only
> occur if the programmer mistyped it. There is no benefit to writing extra
> error handling logic here.
>
> `NSRegularExpression` 构造器会在正则表达式不合法时抛出错误，但当它是字符串字面量时，错误只可能由于编程人员的编写错误导致。这时候编写额外的错误处理逻辑并没有什么益处。
>
> If the pattern above were not a literal but instead were dynamic or derived
> from user input, `try!` should **not** be used and errors should be handled
> gracefully.
>
> 如果上面 pattern 不是字面量，而是动态生成的或者是使用者传入的，则**不**应该使用 `try!` ，而应该更优雅地处理出现的错误。

### 强制解包和强制擦除/Force Unwrapping and Force Casts

Force-unwrapping and force-casting are often code smells and are strongly
discouraged. Unless it is extremely clear from surrounding code why such an
operation is safe, a comment should be present that describes the invariant that
ensures that the operation is safe. For example,

强制解包和强制擦除通常意味着有代码异味和被强迫进行妥协。除非它能通过周围代码解释清楚该操作的安全性，并需要附加注释来描述这个操作是永远安全的。例如，

~~~ swift
let value = getSomeInteger()

// ...intervening code...

// This force-unwrap is safe because `value` is guaranteed to fall within the
// valid enum cases because it came from some data source that only permits
// those raw values.
return SomeEnum(rawValue: value)!
~~~
{:.good}

> **Exception:** Force-unwraps are allowed in unit tests and test-only code
> without additional documentation. This keeps such code free of unnecessary
> control flow. In the event that `nil` is unwrapped or a cast operation is to
> an incompatible type, the test will fail which is the desired result.
>
> **例外：**在单元测试和仅用于测试的代码里允许没有附加注释的强制解包。这可以减少代码不必要的控制流。在 `nil` 被解包或者不合适的类型擦除发生时，测试也会按照预期而失败。

### 可选值隐式解包/Implicitly Unwrapped Optionals

Implicitly unwrapped optionals are inherently unsafe and should be avoided
whenever possible in favor of non-optional declarations or regular `Optional`
types. Exceptions are described below.

可选值隐式解包有潜在的不安全之处，当可以用非可选值声明或者习惯的 `Optional` 类型时就应该避免。除了下面描述的情况外。

User-interface objects whose lifetimes are based on the UI lifecycle instead of
being strictly based on the lifetime of the owning object are allowed to use
implicitly unwrapped optionals. Examples of these include `@IBOutlet`
properties connected to objects in a XIB file or storyboard, properties that are
initialized externally like in the `prepareForSegue` implementation of a calling
view controller, and properties that are initialized elsewhere during a class's
life cycle, like views in a view controller's `viewDidLoad` method. Making such
properties regular optionals can put too much burden on the user to unwrap them
because they are guaranteed to be non-nil and remain that way once the objects
are ready for use.

存活时间基于 UI 生命周期而不是严格基于持有关系的用户界面元素可以使用可选值显式解包。这种情况的例子包括连接 XIB 文件或 storyboard 中元素的 `@IBOutlet` 属性，显式初始化的属性，例如在 `prepareForSegue` 实现里调用的 view controller，还有在类生命周期以外时间点会被初始化的属性，例如在 view controller `viewDidLoad` 方法里的视图。这些属性如果用习惯的可选值会加重使用者解包的负担，因为它们能确保非空并且一旦已经准备好被使用就会一直保持在这种状态。

~~~ swift
class SomeViewController: UIViewController {
  @IBOutlet var button: UIButton!

  override func viewDidLoad() {
    populateLabel(for: button)
  }

  private func populateLabel(for button: UIButton) {
    // ...
  }
}
~~~
{:.good}

Implicitly unwrapped optionals can also surface in Swift code when using
Objective-C APIs that lack the appropriate nullability attributes. If possible,
coordinate with the owners of that code to add those annotations so that the
APIs are imported cleanly into Swift. If this is not possible, try to keep the
footprint of those implicitly unwrapped optionals as small as possible in your
Swift code; that is, do not propagate them through multiple layers of your own
abstractions.

可选值隐式解包也会在 Swift 代码使用缺少恰当判空特性的 Objective-C API 时出现。如果可能，和该代码的拥有者商量添加上那些注解，该 API 在 Swift 就可以更清晰的引入。如果没有可能，尽可能尝试将这些可选值隐式解包在 Swift 代码中的影响缩小 ；也就是说，不要将它们扩散到多个你自己的抽象层。

Implicitly unwrapped optionals are also allowed in unit tests. This is for
reasons similar to the UI object scenario above&mdash;the lifetime of test
fixtures often begins not in the test's initializer but in the `setUp()` method
of a test so that they can be reset before the execution of each test.

可选值隐式解包在单元测试也被允许。这和上面的 UI 元素情况理由差不多——测试里对象的生命周期通常不从测试构造器开始，而是从测试的 `setUp()` 方法开始，以便在每次测试执行前重置。

### 访问等级/Access Levels

Omitting an explicit access level is permitted on declarations. For top-level
declarations, the default access level is `internal`. For nested declarations,
the default access level is the lesser of `internal` and the access level of the
enclosing declaration.

在声明里省略显式的访问等级是允许的。顶层声明的默认访问等级是 `internal`。嵌套的声明默认访问等级和其外层声明访问等级相同但不能高于 `internal` 。

Specifying an explicit access level at the file level on an extension is
forbidden. Each member of the extension has its access level specified if it is
different than the default.

给文件级别的扩展指定显式访问等级是不允许的。拓展里的每一个成员如果不采用默认的访问等级则应该单独进行指定。

~~~ swift
extension String {
  public var isUppercase: Bool {
    // ...
  }

  public var isLowercase: Bool {
    // ...
  }
}
~~~
{:.good}

~~~ swift
public extension String {
  var isUppercase: Bool {
    // ...
  }

  var isLowercase: Bool {
    // ...
  }
}
~~~
{:.bad}

### 嵌套和命名空间/Nesting and Namespacing

Swift allows `enum`s, `struct`s, and `class`es to be nested, so nesting is
preferred (instead of naming conventions) to express scoped and hierarchical
relationships among types when possible. For example, flag `enum`s or error
types that are associated with a specific type are nested in that type.

Swift 里允许嵌套 `enum`，`struct` 和 `class`，所以在可能时，嵌套更适合（比起命名约定）表示作用域和类型之间的分级关系。例如，在类型里嵌套特定类型的 `enum` 作标志或者错误类型。

~~~ swift
class Parser {
  enum Error: Swift.Error {
    case invalidToken(String)
    case unexpectedEOF
  }

  func parse(text: String) throws {
    // ...
  }
}
~~~
{:.good}

~~~ swift
class Parser {
  func parse(text: String) throws {
    // ...
  }
}

enum ParseError: Error {
  case invalidToken(String)
  case unexpectedEOF
}
~~~
{:.bad}

Swift does not currently allow protocols to be nested in other types or vice
versa, so this rule does not apply to situations such as the relationship
between a controller class and its delegate protocol.

Swift 目前还不支持嵌套协议在其它类型中，反之亦然，所以该规则不适用于解决例如控制器类型和它代理协议之间的关系。

Declaring an `enum` without cases is the canonical way to define a "namespace"
to group a set of related declarations, such as constants or helper functions.
This `enum` automatically has no instances and does not require that extra
boilerplate code be written to prevent instantiation.

声明一个没有 case 的 `enum` 是定义用于相关声明分组的“命名空间”的公认方案，例如常量或者帮助方法。该 `enum` 会自然而然不存在实例并且不需要额外样板代码来避免可被实例化。

~~~ swift
enum Dimensions {
  static let tileMargin: CGFloat = 8
  static let tilePadding: CGFloat = 4
  static let tileContentSize: CGSize(width: 80, height: 64)
}
~~~
{:.good}

~~~ swift
struct Dimensions {
  private init() {}

  static let tileMargin: CGFloat = 8
  static let tilePadding: CGFloat = 4
  static let tileContentSize: CGSize(width: 80, height: 64)
}
~~~
{:.bad}

### 提前退出的 `guard`/`guard`s for Early Exits

A `guard` statement, compared to an `if` statement with an inverted condition,
provides visual emphasis that the condition being tested is a special case that
causes early exit from the enclosing scope.

`guard` 语句，比起条件相反的 `if` 语句，会更好地从视觉上强调该检查条件是导致从外层作用域提前退出的特例。

Furthermore, `guard` statements improve readability by eliminating extra levels
of nesting (the "pyramid of doom"); failure conditions are closely coupled to
the conditions that trigger them and the main logic remains flush left within
its scope.

更远了说，`guard` 语句通过减少额外嵌套层级（“鞭尸金字塔”）来提高可读性；令错误情况和触发条件靠近，而主逻辑在作用域里保持向左对齐。

This can be seen in the following examples; in the first, there is a clear
progression that checks for invalid states and exits, then executes the main
logic in the successful case. In the second example without `guard`, the main
logic is buried at an arbitrary nesting level and the thrown errors are
separated from their conditions by a great distance.

下面的例子中会体现这些理论；第一种例子里，有清晰的流程，检查不合法的状态并退出，然后在成功的情况下执行主逻辑。在没有 `guard` 的第二个例子里，主逻辑混杂在某个任意嵌套层级里，抛出的错误和它们的触发条件被分隔得很开。

~~~ swift
func discombobulate(_ values: [Int]) throws -> Int {
  guard let first = values.first else {
    throw DiscombobulationError.arrayWasEmpty
  }
  guard first >= 0 else {
    throw DiscombobulationError.negativeEnergy
  }

  var result = 0
  for value in values {
    result += invertedCombobulatoryFactory(of: value)
  }
  return result
}
~~~
{:.good}

~~~ swift
func discombobulate(_ values: [Int]) throws -> Int {
  if let first = values.first {
    if first >= 0 {
      var result = 0
      for value in values {
        result += invertedCombobulatoryFactor(of: value)
      }
      return result
    } else {
      throw DiscombobulationError.negativeEnergy
    }
  } else {
    throw DiscombobulationError.arrayWasEmpty
  }
}
~~~
{:.bad}

A `guard`-`continue` statement can also be useful in a loop to avoid increased
indentation when the entire body of the loop should only be executed in some
cases (but see also the `for`-`where` discussion below.)

`guard`-`continue` 语句也可以避免循环的整个循环体只在某些情况下执行时缩进增加（但也可以看看下面 `for`-`where` 的讨论）。

### `for`-`where` 循环/`for`-`where` Loops

When the entirety of a `for` loop's body would be a single `if` block testing a
condition of the element, the test is placed in the `where` clause of the `for`
statement instead.

当整个 `for` 循环体只包含了对元素的条件检查 `if` 块时，可以将该检查可以放在 `for` 语句里 `where` 分句中。

~~~ swift
for item in collection where item.hasProperty {
  // ...
}
~~~
{:.good}

~~~ swift
for item in collection {
  if item.hasProperty {
    // ...
  }
}
~~~
{:.bad}

### 在 `switch` 语句里的 `fallthrough`/ `fallthrough` in `switch` Statements

When multiple `case`s of a `switch` would execute the same statements, the
`case` patterns are combined into ranges or comma-delimited lists. Multiple
`case` statements that do nothing but `fallthrough` to a `case` below are not
allowed.

当 `switch` 里的多个 `case	` 执行同样的语句时，这些 `case` 可以合并成一个范围或者逗号分隔的列表。声明多个 `case` 却不做任何事只 `fallthrough` 到下面 `case` 是不允许的。

~~~ swift
switch value {
case 1: print("one")
case 2...4: print("two to four")
case 5, 7: print("five or seven")
default: break
}
~~~
{:.good}

~~~ swift
switch value {
case 1: print("one")
case 2: fallthrough
case 3: fallthrough
case 4: print("two to four")
case 5: fallthrough
case 7: print("five or seven")
default: break
}
~~~
{:.bad}

In other words, there is never a `case` whose body contains _only_ the
`fallthrough` statement. Cases containing _additional_ statements which then
fallthrough to the next case are permitted.

也就是说，不能有_只_执行 `fallthrough` 语句的 `case` 。包含_其余_语句再贯穿到下一个的 case 是允许的。

### 模式匹配/Pattern Matching

The `let` and `var` keywords are placed individually in front of _each_ element
in a pattern that is being matched. The shorthand version of `let`/`var` that
precedes and distributes across the entire pattern is forbidden because it can
introduce unexpected behavior if a value being matched in a pattern is itself a
variable.

_每个_模式匹配元素前面都有单独的 `let` 和 `var` 关键字。适用于整个匹配模式的前置简写 `let`/`var` 是禁止的，因为当匹配模式的值本身是个变量时，会引入非预期行为。

~~~ swift
enum DataPoint {
  case unlabeled(Int)
  case labeled(String, Int)
}

let label = "goodbye"

// `label` is treated as a value here because it is not preceded by `let`, so
// the pattern below matches only data points that have the label "goodbye".
switch DataPoint.labeled("hello", 100) {
case .labeled(label, let value):
  // ...
}

// Writing `let` before each individual binding clarifies that the intent is to
// introduce a new binding (shadowing the local variable within the case) rather
// than to match against the value of the local variable. Thus, this pattern
// matches data points with any string label.
switch DataPoint.labeled("hello", 100) {
case .labeled(let label, let value):
  // ...
}
~~~
{:.good}

In the example below, if the author's intention was to match using the value of
the `label` variable above, that has been lost because `let` distributes across
the entire pattern and thus shadows the variable with a binding that applies to
any string value:

在下面的例子中，如果作者意图是使用上面的 `label` 变量进行匹配，那么就会因为 `let` 适用于整个模式匹配，从而该值会被任何绑定的字符串所覆盖。

~~~ swift
switch DataPoint.labeled("hello", 100) {
case let .labeled(label, value):
  // ...
}
~~~
{:.bad}

Labels of tuple arguments and `enum` associated values are omitted when binding
a value to a variable with the same name as the label.

元组的实参标签和 `enum` 的关联值在用相同标签名字的变量来绑定值时可以被省略。

~~~ swift
enum BinaryTree<Element> {
  indirect case subtree(left: BinaryTree<Element>, right: BinaryTree<Element>)
  case leaf(element: Element)
}

switch treeNode {
case .subtree(let left, let right):
  // ...
case .leaf(let element):
  // ...
}
~~~
{:.good}

Including the labels adds noise that is redundant and lacking useful
information:

包含多余并缺乏有用信息的标签只会造成混淆：

~~~ swift
switch treeNode {
case .subtree(left: let left, right: let right):
  // ...
case .leaf(element: let element):
  // ...
}
~~~
{:.bad}

### 元组模式/Tuple Patterns

Assigning variables through a tuple pattern (sometimes referred to as a _tuple
shuffle_) is only permitted if the left-hand side of the assignment is
unlabeled.

只有赋值表达式左侧没有标签的元组模式（有时候用_乱序元组_）变量赋值才被允许。

~~~ swift
let (a, b) = (y: 4, x: 5.0)
~~~
{:.good}

~~~ swift
let (x: a, y: b) = (y: 4, x: 5.0)
~~~
{:.bad}

Labels on the left-hand side closely resemble type annotations, and can lead to
confusing code.

左侧的标签与类型注解很类似，会导致代码难以理解。

~~~ swift
// This declares two variables, `Int`, which is a `Double` with value 5.0, and
// `Double`, which is an `Int` with value 4.
// `x` and `y` are not variables.
let (x: Int, y: Double) = (y: 4, x: 5.0)
~~~
{:.bad}

### 数字和字符串字面量/Numeric and String Literals

Integer and string literals in Swift do not have an intrinsic type. For example,
`5` by itself is not an `Int`; it is a special literal value that can express
any type that conforms to `ExpressibleByIntegerLiteral` and only becomes an
`Int` if type inference does not map it to a more specific type. Likewise, the
literal `"x"` is neither `String` nor `Character` nor `UnicodeScalar`, but it
can become any of those types depending on its context, falling back to `String`
as a default.

Swift 里的整型和字符串字面量没有固定类型。例如，`5` 本身不是一个 `Int`；它是能被 `ExpressibleByIntegerLiteral` 解释成任意类型的特殊字面量值，并且如果类型推断没有把它转换为更具体的类型，就会变成 `Int` 值。类似的，字面量`"x"`并不是 `String`， `Character` 或  `UnicodeScalar`，不过它可以根据上下文变成这些类型，默认情况是变成 `String`。

Thus, when a literal is used to initialize a value of a type other than its
default, and when that type cannot be inferred otherwise by context, specify the
type explicitly in the declaration or use an `as` expression to coerce it.

因此类型在使用默认以外的字面量方式构造值，并且该类型不能通过上下文推断更多信息时，需要在声明里用显式类型或者用 `as` 表达式来进行强制转换。

~~~ swift
// Without a more explicit type, x1 will be inferred as type Int.
let x1 = 50

// These are explicitly type Int32.
let x2: Int32 = 50
let x3 = 50 as Int32

// Without a more explicit type, y1 will be inferred as type String.
let y1 = "a"

// These are explicitly type Character.
let y2: Character = "a"
let y3 = "a" as Character

// These are explicitly type UnicodeScalar.
let y4: UnicodeScalar = "a"
let y5 = "a" as UnicodeScalar

func writeByte(_ byte: UInt8) {
  // ...
}
// Inference also occurs for function arguments, so 50 is a UInt8 without
// explicitly coercion.
writeByte(50)
~~~
{:.good}

The compiler will emit errors appropriately for invalid literal coercions if,
for example, a number does not fit into the integer type or a multi-character
string is coerced to a character. So while the following examples emit errors,
they are "good" because the errors are caught at compile-time and for the right
reasons.

如果字面量的强制转换不合理，编译器会抛出合适的错误，例如，数字不属于整型类型或者强制转换为字符的多字符的字符串时。所以下面例子抛出错误是“好”事，因为这些错误在编译期就找到了正确的错因。

~~~ swift
// error: integer literal '9223372036854775808' overflows when stored into 'Int64'
let a = 0x8000_0000_0000_0000 as Int64

// error: cannot convert value of type 'String' to type 'Character' in coercion
let b = "ab" as Character
~~~
{:.good}

Using initializer syntax for these types of coercions can lead to misleading
compiler errors, or worse, hard-to-debug runtime errors.

这些类型使用构造器语法进行强制转换的话会产生易误导的编译器错误，或者可能更糟糕，产生难调试的运行时错误。

~~~ swift
// This first tries to create an `Int` (signed) from the literal and then
// convert it to a `UInt64`. Even though this literal fits into a `UInt64`, it
// doesn't fit into an `Int` first, so it doesn't compile.
let a1 = UInt64(0x8000_0000_0000_0000)

// This invokes `Character.init(_: String)`, thus creating a `String` "a" at
// runtime (which involves a slow heap allocation), extracting the character
// from it, and then releasing it. This is significantly slower than a proper
// coercion.
let b = Character("a")

// As above, this creates a `String` and then `Character.init(_: String)`
// attempts to extract the single character from it. This fails a precondition
// check and traps at runtime.
let c = Character("ab")
~~~
{:.bad}

### Playground 字面量/Playground Literals

The graphically-rendered playground literals `#colorLiteral(...)`,
`#imageLiteral(...)`, and `#fileLiteral(...)` are forbidden in non-playground
production code. They are permitted in playground sources.

会进行图形渲染的 playground 字面量 `#colorLiteral(...)`，`#imageLiteral(...)` 和 `#fileLiteral(...)` 在非 playground 的代码里是禁止的。它们只允许在 playground 源码里。

~~~ swift
let color = UIColor(red: 1.0, green: 1.0, blue: 1.0, alpha: 1.0)
~~~
{:.good}

~~~ swift
let color = #colorLiteral(red: 1.0, green: 1.0, blue: 1.0, alpha: 1.0)
~~~
{:.bad}

### 捕获溢出运算/Trapping vs. Overflowing Arithmetic

The standard (trapping-on-overflow) arithmetic and bitwise operators (`+`, `-`,
`*`, `<<`, and `>>`) are used for most normal operations, rather than the
masking operations (preceded by `&`). Trapping on overflow is safer because it
prevents bad data from propagating through other layers of the system.

标准（捕获溢出）运算和二元运算符（`+`，`-`，`*`，`<<` 和 `>>`）大部分用于普通操作，而非掩码操作（前置 `&`）。捕获溢出会更加安全，因为它防止错误数据被传递到系统的其他层级。

~~~ swift
// GOOD. Overflow will not cause the balance to go negative.
let newBankBalance = oldBankBalance + recentHugeProfit
~~~
{:.good}

~~~ swift
// AVOID. Overflow will cause the balance to go negative if the summands are
// large.
let newBankBalance = oldBankBalance &+ recentHugeProfit
~~~
{:.bad}

Masking operations are comparatively rare but are permitted (and in fact
necessary for correctness) in problem domains that use modular arithmetic, such
as cryptography, big-integer implementations, hash functions, and so forth.

掩码操作比较少见，但在模数运算的问题领域是允许的（事实上为了正确性是必要的），例如加密，大整型实现，哈希函数等等。

~~~ swift
var hashValue: Int {
  // GOOD. What matters here is the distribution of the bit pattern rather than
  // the actual numeric value.
  return foo.hashValue &+ 31 * (bar.hashValue &+ 31 &* baz.hashValue)
}
~~~
{:.good}

~~~ swift
var hashValue: Int {
  // INCORRECT. This will trap arbitrarily and unpredictably depending on the
  // hash values of the individual terms.
  return foo.hashValue + 31 * (bar.hashValue + 31 * baz.hashValue)
}
~~~
{:.bad}

Masking operations are also permitted in performance-sensitive code where the
values are already known to not cause overflow (or where overflow is not a
concern). In this case, comments should be used to indicate why the use of
masking operations is important. Additionally, consider adding debug
preconditions to check these assumptions without affecting performance of
optimized builds.

掩码操作在确保值不会导致溢出（或者不需要担心溢出）的性能敏感代码里也是允许的。在这种情况下，需要使用注释来注明使用掩码操作的重要性。更进一步，在不影响性能优化的情况下，考虑增加先决调试条件来检查这些假设。

### 定义新运算符/Defining New Operators

When used unwisely, custom-defined operators can significantly reduce the
readability of code because such operators often lack the historical context of
the more common ones built into the standard library.

不理智地使用自定义运算符会显著影响代码可读性，因为这样的运算符比起标准库中更常用的运算符，通常缺乏历史背景。

In general, defining custom operators should be avoided. However, it is allowed
when an operator has a clear and well-defined meaning in the problem domain
and when using an operator significantly improves the readability of the code
when compared to function calls. For example, since `*` is the only
multiplication operator defined by Swift (not including the masking version), a
numeric matrix library may define additional operators to support other
operations like cross product and dot product.

通常来说，应该避免自定义运算符。然而，当一个运算符在问题领域中有清晰和含义良好的定义，并且使用它会比函数调用显著提高代码的可读性时，是允许的。例如，`*` 在 Swift 里只定义为乘法运算符（不包含掩码版本）， 数学矩阵库可能会定义额外的运算符来支持其他运算比如叉乘和点乘。

An example of a prohibited use case is defining custom `<~~` and `~~>` operators
to decode and encode JSON data. Such operators are not native to the problem
domain of processing JSON and even an experienced Swift engineer would have
difficulty understanding the purpose of the code without seeking out
documentation of those operators.

禁止用法其中一种例子是定义自定义 `<~~` 和 `~~>` 运算符来解码和编码 JSON 数据。这样的运算符不是 JSON 领域问题的原生处理方式，甚至连有经验的 Swift 工程师在没有这些运算符文档的情况下也可能会对这种处理代码有着不同的理解。

If you must use third-party code of unquestionable value that provides an API
only available through custom operators, you are **strongly encouraged** to
consider writing a wrapper that defines more readable methods that delegate to
the custom operators. This will significantly reduce the learning curve required
to understand how such code works for new teammates and other code reviewers.

如果你一定要让没问题的值使用第三方代码里只提供了自定义运算符形式的 API，**强烈建议**你编写一个包装器，定义可读性更好的方法作为该自定义运算符的代理。对团队新成员或者其他代码审查者来说，这会显著降低理解这样的代码是如何工作的学习曲线。

### 重载已存在运算符/Overloading Existing Operators

Overloading operators is permitted when your use of the operator is semantically
equivalent to the existing uses in the standard library. Examples of permitted
use cases are implementing the operator requirements for `Equatable` and
`Hashable`, or defining a new `Matrix` type that supports arithmetic operations.

用语义上和标准库中已存在等同的重载运算符是允许的。允许的例子是为 `Equatable` 和 `Hashable` 实现运算符要求，或者定义新的 `Matrix` 类型来支持算数运算。

If you wish to overload an existing operator with a meaning other than its
natural meaning, follow the guidance in
[Defining New Operators](#defining-new-operators) to determine whether this is
permitted. In other words, if the new meaning is well-established in the problem
domain and the use of the operator is a readability improvement over other
syntactic constructs, then it is permitted.

如果你希望用和原本不同的含义重载已存在的运算符，参考 [定义新运算符](#defining-new-operators) 指引来确定是否允许。也就是说，如果新的含义在问题领域是确定已久的，并且使用该运算符会比其他语法结构提高可读性，那么就是允许的。

An example of a prohibited case of operator repurposing would be to overload `*`
and `+` to build an ad hoc regular expression API. Such an API would not provide
strong enough readability benefits compared to simply representing the entire
regular expression as a string.

禁止更改运算符含义的一种例子是重载 `*` 和 `+` 来构建特定正则表达式的 API。这样的 API 没有比简单用字符串表示整个正则表达式的方式可读性强很多。

## 文档注释/Documentation Comments

### 通常格式/General Format

Documentation comments are written using the format where each line is preceded
by a triple slash (`///`). Javadoc-style block comments (`/** ... */`) are not
permitted.

文档注释使用每行前面三个斜杠（`///`）的格式。Java 文档风格的块状注释（`/** ...*/`）是不允许的。

~~~ swift
/// Returns the numeric value of the given digit represented as a Unicode scalar.
///
/// - Parameters:
///   - digit: The Unicode scalar whose numeric value should be returned.
///   - radix: The radix, between 2 and 36, used to compute the numeric value.
/// - Returns: The numeric value of the scalar.
func numericValue(of digit: UnicodeScalar, radix: Int = 10) -> Int {
  // ...
}
~~~
{:.good}

~~~ swift
/**
 * Returns the numeric value of the given digit represented as a Unicode scalar.
 *
 * - Parameters:
 *   - digit: The Unicode scalar whose numeric value should be returned.
 *   - radix: The radix, between 2 and 36, used to compute the numeric value.
 * - Returns: The numeric value of the scalar.
 */
func numericValue(of digit: UnicodeScalar, radix: Int = 10) -> Int {
  // ...
}

/**
Returns the numeric value of the given digit represented as a Unicode scalar.

- Parameters:
  - digit: The Unicode scalar whose numeric value should be returned.
  - radix: The radix, between 2 and 36, used to compute the numeric value.
- Returns: The numeric value of the scalar.
*/
func numericValue(of digit: UnicodeScalar, radix: Int = 10) -> Int {
  // ...
}
~~~
{:.bad}

### 一句话概括/Single-Sentence Summary

Documentation comments begin with a brief **single-sentence** summary that
describes the declaration. (This sentence may span multiple lines, but if it
spans too many lines, the author should consider whether the summary can be
simplified and details moved to a new paragraph.)

文档注释的开始使用简短的**一句话**概括来描述声明。（这句话可以跨行，但如果跨了很多行，作者应该考虑是否可以将概况简化并将细节移到新的段落中。）

If more detail is needed than can be stated in the summary, additional
paragraphs (each separated by a blank line) are added after it.

如果概括需要陈述更多细节，在后面添加额外的段落（每个段落用空行分隔）。

The single-sentence summary is not necessarily a complete sentence; for example,
method summaries are generally written as verb phrases **without** "this method
[...]" because it is already implied as the subject and writing it out would be
redundant. Likewise, properties are often written as noun phrases **without**
"this property is [...]". In any case, however, they are still terminated with a
period.

一句话概括不需要是完整的句子；例如，方法的概括通常写作动词短语，**不需要**加上“这个方法 [...]”，因为这就是要表达的，写出来是多余的。类似，属性通常写作名词短语，**不需要**加上“这个属性是 [...]”。然而无论如何，它们还是要以句号结尾。

~~~ swift
/// The background color of the view.
var backgroundColor: UIColor

/// Returns the sum of the numbers in the given array.
///
/// - Parameter numbers: The numbers to sum.
/// - Returns: The sum of the numbers.
func sum(_ numbers: [Int]) -> Int {
  // ...
}
~~~
{:.good}

~~~ swift
/// This property is the background color of the view.
var backgroundColor: UIColor

/// This method returns the sum of the numbers in the given array.
///
/// - Parameter numbers: The numbers to sum.
/// - Returns: The sum of the numbers.
func sum(_ numbers: [Int]) -> Int {
  // ...
}
~~~
{:.bad}

### 形参，返回值和抛出标签/Parameter, Returns, and Throws Tags

Clearly document the parameters, return value, and thrown errors of functions
using the `Parameter(s)`, `Returns`, and `Throws` tags, in that order. None ever
appears with an empty description. When a description does not fit on a single
line, continuation lines are indented 2 spaces in from the position of the
hyphen starting the tag.

对形参，返回值和函数抛出的错误按照 `Parameter(s)`，`Returns` 和 `Throws` 标签的顺序清晰地写下文档。不要出现空白的描述。当一个描述需要换行时，续行的缩进在开始的标记连字符基础上加上 2 个空格。

The recommended way to write documentation comments in Xcode is to place the
text cursor on the declaration and press **Command + Option + /**. This will
automatically generate the correct format with placeholders to be filled in.

Xcode 里编写文档注释的推荐方式是将文字光标放在声明上并且按下 **Command + Option + /**。这会自动创建有待填充占位符的正确格式注释。

`Parameter(s)` and `Returns` tags may be omitted only if the single-sentence
brief summary fully describes the meaning of those items and including the tags
would only repeat what has already been said.

`Parameter(s)` 和 `Returns` 标签只有当一句话简短概括中已经有完整描述时可以省略，如果还包括它们就只是重复已经说过的内容。

The content following the `Parameter(s)`, `Returns`, and `Throws` tags should be
terminated with a period, even when they are phrases instead of complete
sentences.

`Parameter(s)`，`Returns` 和 `Throws` 标签后面跟着的内容需要以句号结尾，即使它们只是短语而不是完整的句子。

When a method takes a single argument, the singular inline form of the
`Parameter` tag is used. When a method takes multiple arguments, the grouped
plural form `Parameters` is used and each argument is written as an item in a
nested list with only its name as the tag.

当方法只有单一的实参时，使用内联单数形式的 `Parameter` 标签。当方法有多个实参时，使用分组复数形式 `Parameters`，嵌套列表里用每个实参的名字作标签。

~~~ swift
/// Returns the output generated by executing a command.
///
/// - Parameter command: The command to execute in the shell environment.
/// - Returns: A string containing the contents of the invoked process's
///   standard output.
func execute(command: String) -> String {
  // ...
}

/// Returns the output generated by executing a command with the given string
/// used as standard input.
///
/// - Parameters:
///   - command: The command to execute in the shell environment.
///   - stdin: The string to use as standard input.
/// - Returns: A string containing the contents of the invoked process's
///   standard output.
func execute(command: String, stdin: String) -> String {
  // ...
}
~~~
{:.good}

The following examples are incorrect, because they use the plural form of
`Parameters` for a single parameter or the singular form `Parameter` for
multiple parameters.

下面的例子是错误的，因为它们对单个形参使用复数形式 `Parameters` 或者对多个形参使用单数形式 `Parameter`。

~~~ swift
/// Returns the output generated by executing a command.
///
/// - Parameters:
///   - command: The command to execute in the shell environment.
/// - Returns: A string containing the contents of the invoked process's
///   standard output.
func execute(command: String) -> String {
  // ...
}

/// Returns the output generated by executing a command with the given string
/// used as standard input.
///
/// - Parameter command: The command to execute in the shell environment.
/// - Parameter stdin: The string to use as standard input.
/// - Returns: A string containing the contents of the invoked process's
///   standard output.
func execute(command: String, stdin: String) -> String {
  // ...
}
~~~
{:.bad}

### Apple 标记格式/Apple's Markup Format

Use of
[Apple's markup format](https://developer.apple.com/library/content/documentation/Xcode/Reference/xcode_markup_formatting_ref/)
is strongly encouraged to add rich formatting to documentation. Such markup
helps to differentiate symbolic references (like parameter names) from
descriptive text in comments and is rendered by Xcode and other documentation
generation tools. Some examples of frequently used directives are listed below.

强烈建议使用 [Apple 标记格式](https://developer.apple.com/library/content/documentation/Xcode/Reference/xcode_markup_formatting_ref/) 来添加富文本到文档中。这种标记有助于区分注释里的特征引用（例如形参名字）和描述性文本，并且可以被 Xcode 和其他文档生成工具渲染。下面列出一些常用指令的示例。

* Paragraphs are separated using a single line that starts with `///` and is
  otherwise blank.
* 段落以 `///` 开始的单一空白行分隔。
* *\*Single asterisks\** and _\_single underscores\__ surround text that should
  be rendered in italic/oblique type.
* 以*\*单星号\**和_\_单下划线\__包围的文本会被渲染成斜体/斜型。
* **\*\*Double asterisks\*\*** and __\_\_double underscores\_\___ surround text
  that should be rendered in boldface.
* 以**\*\*双星号\*\***和__\_\_双下划线\_\___包围的文本会被渲染成粗体。
* Names of symbols or inline code are surrounded in `` `backticks` ``.
* 符号名或者内联代码以 `` `反引号` ``包围。
* Multi-line code (such as example usage) is denoted by placing three backticks
  (` ``` `) on the lines before and after the code block.
* 多行代码（例如作为用例）以三个反引号（` ``` `）的行开头和结束。

### 注释的位置/Where to Document

At a minimum, documentation comments are present for every open or public
declaration, and every open or public member of such a declaration, with
specific exceptions noted below:

最起码，每个 open 或 public 声明和里面的每个 open 或 public 成员都应该有文档注释，除了下面的说明以外：

* Individual cases of an `enum` often are not documented if their meaning is
  self-explanatory from their name. Cases with associated values, however,
  should document what those values mean if it is not obvious.

* `enum` 里单个的 case 的名字如果已经可以自解释则通常不需要注释。有关联值的 Case 如果值的含义不明确，那么不管怎样都应该注释。
  
* A documentation comment is not always present on a declaration that overrides
  a supertype declaration or implements a protocol requirement, or on a
  declaration that provides the default implementation of a protocol requirement
  in an extension.

  It is acceptable to document an overridden declaration to describe new
  behavior from the declaration that it overrides. In no case should the
  documentation for the override be a mere copy of the base declaration's
  documentation.

* 继承父类的声明，协议要求的实现或者提供协议要求的默认实现的扩展声明可以不需要文档注释。

  继承声明里用来描述继承后的新表现的注释是可以接受的。任何情况下都不应该出现单纯拷贝父声明文档的注释。

* A documentation comment is not always present on test classes and test
  methods. However, they can be useful for functional test classes and for
  helper classes/methods shared by multiple tests.

* 测试类和测试方法可以不需要文档注释。然而，注释对于在多个测试里复用的函数式测试类和帮助类/方法时是有帮助的。
  
* A documentation comment is not always present on an extension declaration
  (that is, the `extension` itself). You may choose to add one if it help
  clarify the purpose of the extension, but avoid meaningless or misleading
  comments.

  In the following example, the comment is just repetition of what is already
  obvious from the source code:

* 扩展声明（也就是自身的 `extension`）可以不需要文档注释。如果能帮助明确拓展的用途，你可以选择添加，但避免无意义或者误导的注释。
  
  在下面的例子中，注释仅仅重复了源码显而易见的事：
  
  ~~~ swift
  /// Add `Equatable` conformance.
  extension MyType: Equatable {
    // ...
  }
  ~~~
  {:.bad}
  
  The next example is more subtle, but it is an example of documentation that is
not scalable because the extension or the conformance could be updated in the
  future. Consider that the type may be made `Comparable` at the time of that
  writing in order to sort the values, but that is not the only possible use of
  that conformance and client code could use it for other purposes in the
  future.
  
  下面的例子则更微妙一些，但这是一个注释无法拓展的例子，因为这个拓展或者类型一致性在以后可能会变化。这个 `Comparable` 可能是在编写对该类型值的排序代码时加上的，但这不是一致性的唯一可能用途，并且使用者可能在以后其他用途代码里会依赖它。
  
  ~~~ swift
  /// Make `Candidate` comparable so that they can be sorted.
  extension Candidate: Comparable {
    // ...
}
  ~~~
  
  {:.bad}

In general, if you find yourself writing documentation that simply repeats
information that is obvious from the source and sugaring it with words like
"a representation of," then leave the comment out entirely.

通常来说，如果你发现你写的注释只是简单地重复源码中显而易见的事，并用类似"用于表示"的词语进行美化，那么将这些注释完全去掉。

However, it is _not_ appropriate to cite this exception to justify omitting
relevant information that a typical reader might need to know. For example, for
a property named `canonicalName`, don't omit its documentation (with the
rationale that it would only say `/// The canonical name.`) if a typical reader
may have no idea what the term "canonical name" means in that context. Use the
documentation as an opportunity to define the term. 

但是，*不*要用这个例外来证明可以省略某些正常读者可能需要的相关信息。例如，对于 `canonicalName` 名字的属性，不要省略注释（只有合理的时候才可以只写 `/// The canonical name.`），因为正常读者可能不知道术语“规范名字”在上下文中的含义。 使用注释是定义该术语的好机会。
