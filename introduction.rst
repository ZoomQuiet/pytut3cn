.. _tut-informal:

**********************************
An Informal Introduction to Python Python 简介
**********************************

In the following examples, input and output are distinguished by the presence or
absence of prompts (``>>>`` and ``...``): to repeat the example, you must type
everything after the prompt, when the prompt appears; lines that do not begin
with a prompt are output from the interpreter. Note that a secondary prompt on a
line by itself in an example means you must type a blank line; this is used to
end a multi-line command.

以下的例子中，通过有没有以提示符（``>>>`` 和 ``...``）起始来区分输入和输出。
要录入示例的话，我们必须输入以提示符开头的行中，提示符后的所有东西；没有以
提示符开头的行是解释器输出的。注意示例中出现从属提示符意味着你一定要在最后加上一个空行，这表示多行命令执行结束。

Many of the examples in this manual, even those entered at the interactive
prompt, include comments.  Comments in Python start with the hash character,
``'#'``, and extend to the end of the physical line.  A comment may appear at
the start of a line or following whitespace or code, but not within a string
literal.  A hash  character within a string literal is just a hash character.

手册中的很多例子，甚至是在提示符下输入的，都包含了注释。Python中的注释以井号``'#'``起始，直至行尾。注释可以从行首开始，也可以跟在空白或代码后面，但不能包含在字符串文本中。字符串文本中的 # 符号只是 # 符号。

Some examples::
例如：

   # this is the first comment 这是第一个注释
   SPAM = 1                 # and this is the second comment 这是第二个
                            # ... and now a third! 这是第三个！
   STRING = "# This is not a comment. 这不是注释"


.. _tut-calculator:

Using Python as a Calculator 把 Python 当做计算器使用
============================

Let's try some simple Python commands.  Start the interpreter and wait for the
primary prompt, ``>>>``.  (It shouldn't take long.)

让我们尝试一些简单的命令。启动解释器，等待主提示符``>>>``出现。（这花不了多少时间。）

.. _tut-numbers:

Numbers 数值
-------

The interpreter acts as a simple calculator: you can type an expression at it
and it will write the value.  Expression syntax is straightforward: the
operators ``+``, ``-``, ``*`` and ``/`` work just like in most other languages
(for example, Pascal or C); parentheses can be used for grouping.  For example::

解释器可作为一个简单的计算器：你可以输入一个表达式，它返回其值。表达式语法
很直白，加减乘除（``+``, ``-``, ``*`` 和 ``/``）运算符的用法就和其它语言一样（例如，Pascal 和 C）；括号可以用来分组。例如：

   >>> 2+2
   4
   >>> # This is a comment
   ... 2+2
   4
   >>> 2+2  # and a comment on the same line as code
   4
   >>> (50-5*6)/4
   5.0
   >>> 8/5 # Fractions aren't lost when dividing integers
   1.6000000000000001

Note: You might not see exactly the same result; floating point results can 
differ from one machine to another.  We will say more later about controlling
the appearance of floating point output; what we see here is the most
informative display but not as easy to read as we would get with::
注意，你可能看到的是不一样的结果；浮点数在其它机器上可能会得到不同的结果。我们希望在未来的版本能够更好的控制浮点数输出，最好像下面这样得到一个可读性更强的结果：

   >>> print(8/5)
   1.6

For clarity in this tutorial we will show the simpler floating point output
unless we are specifically discussing output formatting, and explain later
why these two ways of displaying floating point data come to be different.
See :ref:`tut-fp-issues` for a full discussion.

在本书中，为了表达清楚，除非要特别讨论输出格式，我们尽量使用简短的浮点数表示。后面的章节我们再解释为什么这两种浮点数显示方式有所不同。在 :ref: `tut-fp-issues`中有完整的讨论。

To do integer division and get an integer result, 
discarding any fractional result, there is another operator, ``//``::

要对整数做除法，求整数结果，抛弃小数部分的话，可以使用另一个运算符，``//``：
   
   >>> # Integer division returns the floor:
   ... 7//3
   2
   >>> 7//-3
   -3

The equal sign (``'='``) is used to assign a value to a variable. Afterwards, no
result is displayed before the next interactive prompt::

等号（``=``）用于给一个变量赋值。其后不会输出任何结果，而是跟随下一个交互提示符：

   >>> width = 20
   >>> height = 5*9
   >>> width * height
   900

A value can be assigned to several variables simultaneously::

一个值可以同时赋给几个变量：

   >>> x = y = z = 0  # Zero x, y and z
   >>> x
   0
   >>> y
   0
   >>> z
   0

There is full support for floating point; operators with mixed type operands
convert the integer operand to floating point::

Python 完全支持浮点数：整数和浮点数的混合计算中，整数会被转换为浮点数：

   >>> 3 * 3.75 / 1.5
   7.5
   >>> 7.0 / 2
   3.5

Complex numbers are also supported; imaginary numbers are written with a suffix
of ``j`` or ``J``.  Complex numbers with a nonzero real component are written as
``(real+imagj)``, or can be created with the ``complex(real, imag)`` function.
::

复数也有支持：虚部以``i`` 或``j``结尾。带有非零实部的复数写作：``(real+imagj)``，也可以通过 ``complex(real, imag)`` 函数创建：

   >>> 1j * 1J
   (-1+0j)
   >>> 1j * complex(0, 1)
   (-1+0j)
   >>> 3+1j*3
   (3+3j)
   >>> (3+1j)*3
   (9+3j)
   >>> (1+2j)/(1+1j)
   (1.5+0.5j)

Complex numbers are always represented as two floating point numbers, the real
and imaginary part.  To extract these parts from a complex number *z*, use
``z.real`` and ``z.imag``.   ::

复数总是被表达为两个浮点数，实部和虚部。要从复数 *z* 中获得这两部分，使用 ``z.real`` 和 ``z.imag`` ：

   >>> a=1.5+0.5j
   >>> a.real
   1.5
   >>> a.imag
   0.5

The conversion functions to floating point and integer (:func:`float`,
:func:`int`) don't work for complex numbers --- there is not one correct way to
convert a complex number to a real number.  Use ``abs(z)`` to get its magnitude
(as a float) or ``z.real`` to get its real part::

整数和浮点数的转换函数（:func: `float`， ::func:`int`）不能用于复数－－没有一个正确的方式可以把一个复数变成一个实数。使用 ``abs(z)`` 得到的是 z 的模（以浮点数形式），而 ``z.real`` 取得的是它是实部：

   >>> a=3.0+4.0j
   >>> float(a)
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: can't convert complex to float; use abs(z)
   >>> a.real
   3.0
   >>> a.imag
   4.0
   >>> abs(a)  # sqrt(a.real**2 + a.imag**2)
   5.0
   >>>

In interactive mode, the last printed expression is assigned to the variable
``_``.  This means that when you are using Python as a desk calculator, it is
somewhat easier to continue calculations, for example::

交互模式下，最近一次表达式输出被赋给变量 ``_``。这意味着把 Python 当做桌面计算器使用的时候，可以方便的进行连续计算，例如：

   >>> tax = 12.5 / 100
   >>> price = 100.50
   >>> price * tax
   12.5625
   >>> price + _
   113.0625
   >>> round(_, 2)
   113.06
   >>>

This variable should be treated as read-only by the user.  Don't explicitly
assign a value to it --- you would create an independent local variable with the
same name masking the built-in variable with its magic behavior.

用户应该视这个变量为只读。不要试图去给它赋值－－这样做你只会创建出一个同名的局部变量，屏蔽了原本内置变量的魔术效果。

.. _tut-strings:

Strings 字符串
-------

Besides numbers, Python can also manipulate strings, which can be expressed in
several ways.  They can be enclosed in single quotes or double quotes::

除了数值， Python 还可以通过几种不同的方法操作字符串。字符串用单引号或双引号标识：

   >>> 'spam eggs'
   'spam eggs'
   >>> 'doesn\'t'
   "doesn't"
   >>> "doesn't"
   "doesn't"
   >>> '"Yes," he said.'
   '"Yes," he said.'
   >>> "\"Yes,\" he said."
   '"Yes," he said.'
   >>> '"Isn\'t," she said.'
   '"Isn\'t," she said.'

The interpreter prints the result of string operations in the same way as they
are typed for input: inside quotes, and with quotes and other funny characters
escaped by backslashes, to show the precise value.  The string is enclosed in
double quotes if the string contains a single quote and no double quotes, else
it's enclosed in single quotes.  Once again, the :func:`print` function
produces the more readable output.

解释器打印字符串结果的时候与它们输入的方式相同：为了显示严谨，字符串包含在引号中，引号和其它奇异字符用反斜杠标识（即通常我们说的转义符－－译注）。如果字符串中只有单引号没有双引号，就用双引号标识；否则用单引号。再强调一次， :func: `print` 函数生成可读性更好的输出。

String literals can span multiple lines in several ways.  Continuation lines can
be used, with a backslash as the last character on the line indicating that the
next line is a logical continuation of the line::

有几种不同的方式可以将字符串文本分行。可以在行尾以反斜杠为继续符结束，表示下一行是它逻辑上的后续。

   hello = "This is a rather long string containing\n\
   several lines of text just as you would do in C.\n\
       Note that whitespace at the beginning of the line is\
    significant."

   print(hello)

Note that newlines still need to be embedded in the string using ``\n``; the
newline following the trailing backslash is discarded.  This example would print
the following::

注意字符串中的换行还是要用用 ``\n`` 来表示；反斜杠后面的换行会被忽略。这个例子会输出：

   This is a rather long string containing
   several lines of text just as you would do in C.
       Note that whitespace at the beginning of the line is significant.

If we make the string literal a "raw" string, however, the ``\n`` sequences are
not converted to newlines, but the backslash at the end of the line, and the
newline character in the source, are both included in the string as data.  Thus,
the example::

然而，如果我们构造了一个“行”（``raw``）字符串，``\n`` 序列不会转为换行，行尾的反斜框和代码中的换行，都会作为数据包含在字符串中。因此，以下的示例：

   hello = r"This is a rather long string containing\n\
   several lines of text much as you would do in C."

   print(hello)

would print::

会打印：

   This is a rather long string containing\n\
   several lines of text much as you would do in C.

Or, strings can be surrounded in a pair of matching triple-quotes: ``"""`` or
``'''``.  End of lines do not need to be escaped when using triple-quotes, but
they will be included in the string. ::

另外，字符串可以用一对三重引号 ``"""`` 或 ``'''`` 来标识。三重引号中的字符串在行尾不需要换行标记，所有的格式都会包括在字符串中：

   print("""
   Usage: thingy [OPTIONS] 
        -h                        Display this usage message
        -H hostname               Hostname to connect to
   """)

produces the following output::

生成以下输出：


   Usage: thingy [OPTIONS] 
        -h                        Display this usage message
        -H hostname               Hostname to connect to


Strings can be concatenated (glued together) with the ``+`` operator, and
repeated with ``*``::

字符串可以用加号 ``+`` 联接，也可以用乘号 ``*`` 循环：


   >>> word = 'Help' + 'A'
   >>> word
   'HelpA'
   >>> '<' + word*5 + '>'
   '<HelpAHelpAHelpAHelpAHelpA>'

Two string literals next to each other are automatically concatenated; the first
line above could also have been written ``word = 'Help' 'A'``; this only works
with two literals, not with arbitrary string expressions::

两个相邻的字符串会自动连接；前一行也可以写成： ``word = 'Help' 'A'``；这只能用在两个字符串文本值，而不能用于两个字符串表达式：


   >>> 'str' 'ing'                   #  <-  This is ok
   'string'
   >>> 'str'.strip() + 'ing'   #  <-  This is ok
   'string'
   >>> 'str'.strip() 'ing'     #  <-  This is invalid
     File "<stdin>", line 1, in ?
       'str'.strip() 'ing'
                         ^
   SyntaxError: invalid syntax

Strings can be subscripted (indexed); like in C, the first character of a string
has subscript (index) 0.  There is no separate character type; a character is
simply a string of size one.  As in the Icon programming language, substrings
can be specified with the *slice notation*: two indices separated by a colon.
::

字符串可以用下标（索引）查询；就像C一样，字符串的第一个下标（索引）是0。 Python 没有字符类型，


   >>> word[4]
   'A'
   >>> word[0:2]
   'He'
   >>> word[2:4]
   'lp'

Slice indices have useful defaults; an omitted first index defaults to zero, an
omitted second index defaults to the size of the string being sliced. ::

切割检索有简略用法；第一个索引默认为零，第二个默认是字符串的长度：


   >>> word[:2]    # The first two characters
   'He'
   >>> word[2:]    # Everything except the first two characters
   'lpA'

Unlike a C string, Python strings cannot be changed.  Assigning to an  indexed
position in the string results in an error::

不像 C 字符串， Python 字符串不可改变。给字符串中的索引位置赋值会引发错误：


   >>> word[0] = 'x'
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: 'str' object doesn't support item assignment
   >>> word[:1] = 'Splat'
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   TypeError: 'str' object doesn't support slice assignment

However, creating a new string with the combined content is easy and efficient::

然而，组合创建新字符串很方便快捷：

   >>> 'x' + word[1:]
   'xelpA'
   >>> 'Splat' + word[4]
   'SplatA'

Here's a useful invariant of slice operations: ``s[:i] + s[i:]`` equals ``s``.
::

切片有一个很有用的不变性操作：`` s[:i] + s[i:]`` 等于 ```s` ：

   >>> word[:2] + word[2:]
   'HelpA'
   >>> word[:3] + word[3:]
   'HelpA'

Degenerate slice indices are handled gracefully: an index that is too large is
replaced by the string size, an upper bound smaller than the lower bound returns
an empty string. ::

退化的索引操作很优美：过大的索引值代替为字符串大小，下界比上界大的返回空字符串：

   >>> word[1:100]
   'elpA'
   >>> word[10:]
   ''
   >>> word[2:1]
   ''

Indices may be negative numbers, to start counting from the right. For example::

索引可以是负数，计数从右边开始。例如：

   >>> word[-1]     # The last character
   'A'
   >>> word[-2]     # The last-but-one character
   'p'
   >>> word[-2:]    # The last two characters
   'pA'
   >>> word[:-2]    # Everything except the last two characters
   'Hel'

But note that -0 is really the same as 0, so it does not count from the right!
::

不过需要请注意的是 -0 仍然等于0，它没有从右边计数！

   >>> word[-0]     # (since -0 equals 0)
   'H'

Out-of-range negative slice indices are truncated, but don't try this for
single-element (non-slice) indices::



   >>> word[-100:]
   'HelpA'
   >>> word[-10]    # error
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   IndexError: string index out of range

One way to remember how slices work is to think of the indices as pointing
*between* characters, with the left edge of the first character numbered 0.
Then the right edge of the last character of a string of *n* characters has
index *n*, for example::

    +---+---+---+---+---+ 
    | H | e | l | p | A |
    +---+---+---+---+---+ 
    0   1   2   3   4   5 
   -5  -4  -3  -2  -1

The first row of numbers gives the position of the indices 0...5 in the string;
the second row gives the corresponding negative indices. The slice from *i* to
*j* consists of all characters between the edges labeled *i* and *j*,
respectively.

For non-negative indices, the length of a slice is the difference of the
indices, if both are within bounds.  For example, the length of ``word[1:3]`` is
2.

The built-in function :func:`len` returns the length of a string::

   >>> s = 'supercalifragilisticexpialidocious'
   >>> len(s)
   34


.. seealso::

   :ref:`typesseq`
      Strings are examples of *sequence types*, and support the common 
      operations supported by such types.

   :ref:`string-methods`
      Strings support a large number of methods for
      basic transformations and searching.

   :ref:`string-formatting`
      The formatting operations invoked by the :meth:`format` string method are
      described in more detail here.


.. _tut-unicodestrings:

About Unicode
-------------

.. sectionauthor:: Marc-Andre Lemburg <mal@lemburg.com>


Starting with Python 3.0 all strings support Unicode. 
(See http://www.unicode.org/) 

Unicode has the advantage of providing one ordinal for every character in every
script used in modern and ancient texts. Previously, there were only 256
possible ordinals for script characters. Texts were typically bound to a code
page which mapped the ordinals to script characters. This lead to very much
confusion especially with respect to internationalization (usually written as
``i18n`` --- ``'i'`` + 18 characters + ``'n'``) of software.  Unicode solves
these problems by defining one code page for all scripts.

If you want to include special characters in a string,
you can do so by using the Python *Unicode-Escape* encoding. The following
example shows how::

   >>> 'Hello\u0020World !'
   'Hello World !'

The escape sequence ``\u0020`` indicates to insert the Unicode character with
the ordinal value 0x0020 (the space character) at the given position.

Other characters are interpreted by using their respective ordinal values
directly as Unicode ordinals.  If you have literal strings in the standard
Latin-1 encoding that is used in many Western countries, you will find it
convenient that the lower 256 characters of Unicode are the same as the 256
characters of Latin-1.

Apart from these standard encodings, Python provides a whole set of other ways
of creating Unicode strings on the basis of a known encoding.

To convert a string into a sequence of bytes using a specific encoding,
string objects provide an :func:`encode` method that takes one argument, the
name of the encoding.  Lowercase names for encodings are preferred. ::

   >>> "����pfel".encode('utf-8')
   b'\xc3\x84pfel'

.. _tut-lists:

Lists
-----

Python knows a number of *compound* data types, used to group together other
values.  The most versatile is the *list*, which can be written as a list of
comma-separated values (items) between square brackets.  List items need not all
have the same type. ::

   >>> a = ['spam', 'eggs', 100, 1234]
   >>> a
   ['spam', 'eggs', 100, 1234]

Like string indices, list indices start at 0, and lists can be sliced,
concatenated and so on::

   >>> a[0]
   'spam'
   >>> a[3]
   1234
   >>> a[-2]
   100
   >>> a[1:-1]
   ['eggs', 100]
   >>> a[:2] + ['bacon', 2*2]
   ['spam', 'eggs', 'bacon', 4]
   >>> 3*a[:3] + ['Boo!']
   ['spam', 'eggs', 100, 'spam', 'eggs', 100, 'spam', 'eggs', 100, 'Boo!']

Unlike strings, which are *immutable*, it is possible to change individual
elements of a list::

   >>> a
   ['spam', 'eggs', 100, 1234]
   >>> a[2] = a[2] + 23
   >>> a
   ['spam', 'eggs', 123, 1234]

Assignment to slices is also possible, and this can even change the size of the
list or clear it entirely::

   >>> # Replace some items:
   ... a[0:2] = [1, 12]
   >>> a
   [1, 12, 123, 1234]
   >>> # Remove some:
   ... a[0:2] = []
   >>> a
   [123, 1234]
   >>> # Insert some:
   ... a[1:1] = ['bletch', 'xyzzy']
   >>> a
   [123, 'bletch', 'xyzzy', 1234]
   >>> # Insert (a copy of) itself at the beginning
   >>> a[:0] = a
   >>> a
   [123, 'bletch', 'xyzzy', 1234, 123, 'bletch', 'xyzzy', 1234]
   >>> # Clear the list: replace all items with an empty list
   >>> a[:] = []
   >>> a
   []

The built-in function :func:`len` also applies to lists::

   >>> a = ['a', 'b', 'c', 'd']
   >>> len(a)
   4

It is possible to nest lists (create lists containing other lists), for
example::

   >>> q = [2, 3]
   >>> p = [1, q, 4]
   >>> len(p)
   3
   >>> p[1]
   [2, 3]
   >>> p[1][0]
   2

You can add something to the end of the list::

   >>> p[1].append('xtra')
   >>> p
   [1, [2, 3, 'xtra'], 4]
   >>> q
   [2, 3, 'xtra']

Note that in the last example, ``p[1]`` and ``q`` really refer to the same
object!  We'll come back to *object semantics* later.


.. _tut-firststeps:

First Steps Towards Programming
===============================

Of course, we can use Python for more complicated tasks than adding two and two
together.  For instance, we can write an initial sub-sequence of the *Fibonacci*
series as follows::

   >>> # Fibonacci series:
   ... # the sum of two elements defines the next
   ... a, b = 0, 1
   >>> while b < 10:
   ...       print(b)
   ...       a, b = b, a+b
   ... 
   1
   1
   2
   3
   5
   8

This example introduces several new features.

* The first line contains a *multiple assignment*: the variables ``a`` and ``b``
  simultaneously get the new values 0 and 1.  On the last line this is used again,
  demonstrating that the expressions on the right-hand side are all evaluated
  first before any of the assignments take place.  The right-hand side expressions
  are evaluated  from the left to the right.

* The :keyword:`while` loop executes as long as the condition (here: ``b < 10``)
  remains true.  In Python, like in C, any non-zero integer value is true; zero is
  false.  The condition may also be a string or list value, in fact any sequence;
  anything with a non-zero length is true, empty sequences are false.  The test
  used in the example is a simple comparison.  The standard comparison operators
  are written the same as in C: ``<`` (less than), ``>`` (greater than), ``==``
  (equal to), ``<=`` (less than or equal to), ``>=`` (greater than or equal to)
  and ``!=`` (not equal to).

* The *body* of the loop is *indented*: indentation is Python's way of grouping
  statements.  Python does not (yet!) provide an intelligent input line editing
  facility, so you have to type a tab or space(s) for each indented line.  In
  practice you will prepare more complicated input for Python with a text editor;
  most text editors have an auto-indent facility.  When a compound statement is
  entered interactively, it must be followed by a blank line to indicate
  completion (since the parser cannot guess when you have typed the last line).
  Note that each line within a basic block must be indented by the same amount.

* The :func:`print` function writes the value of the expression(s) it is
  given.  It differs from just writing the expression you want to write (as we did
  earlier in the calculator examples) in the way it handles multiple 
  expressions, floating point quantities, 
  and strings.  Strings are printed without quotes, and a space is inserted
  between items, so you can format things nicely, like this::

     >>> i = 256*256
     >>> print('The value of i is', i)
     The value of i is 65536

   The keyword end can be used to avoid the newline after the output::

     >>> a, b = 0, 1
     >>> while b < 1000:
     ...     print(b, ' ', end='')
     ...     a, b = b, a+b
     ... 
     >>> print()
     1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987

 Note that nothing appeared after the loop ended, until we printed
 a newline.



