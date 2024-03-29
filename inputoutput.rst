.. _tut-io:

****************
Input and Output 输出和输出
****************

There are several ways to present the output of a program; data can be printed
in a human-readable form, or written to a file for future use. This chapter will
discuss some of the possibilities.

有几种方法可以表现程序的输出结果；数据可以用可读的形式打印，也可以写入文件供以后使用。本章将会讨论几种可行的做法。

.. _tut-formatting:

Fancier Output Formatting 设计输出格式
=========================

So far we've encountered two ways of writing values: *expression statements* and
the :func:`print` function.  (A third way is using the :meth:`write` method
of file objects; the standard output file can be referenced as ``sys.stdout``.
See the Library Reference for more information on this.)

我们有两种大相径庭的输出值方法：表达式语句和 print 语句。（第三种方式是使用文件对象的 write() 方法，标准文件输出可以参考 ``sys.stdout``。详细内容参见库参考手册。）

.. index:: module: string

Often you'll want more control over the formatting of your output than simply
printing space-separated values.  There are two ways to format your output; the
first way is to do all the string handling yourself; using string slicing and
concatenation operations you can create any layout you can imagine.  The
standard module :mod:`string` contains some useful operations for padding
strings to a given column width; these will be discussed shortly.  The second
way is to use the ``%`` operator with a string as the left argument.  The ``%``
operator interprets the left argument much like a :cfunc:`sprintf`\ -style
format string to be applied to the right argument, and returns the string
resulting from this formatting operation.

可能你经常想要对输出格式做一些比简单的打印空格分隔符更为复杂的控制。有两种方法可以格式化输出。第一种是由你来控制整个字符串，使用字符切割和联接操作就可以创建出任何你想要的输出形式。标准模块 :mod:`string` 包括了一些操作，将字符串填充入给定列时，这些操作很有用。随后我们会讨论这部分内容。第二种方法是使用 % 操作符，以某个字符串做为其左参数。 ``%`` 操作符将左参数解释为类似于 :cfunc:`sprintf`\ 风格的格式字符串，并作用于右参数，从该操作中返回格式化的字符串。

One question remains, of course: how do you convert values to strings? Luckily,
Python has ways to convert any value to a string: pass it to the :func:`repr`
or :func:`str` functions.  Reverse quotes (``````) are equivalent to
:func:`repr`, but they are no longer used in modern Python code and will likely
not be in future versions of the language.

当然，还有一个问题，如何将值转化为字符串？很幸运，Python总是把任意值传入 :func:`repr` 或 :func:`str` 函数，转为字符串。反引号 (``````)等价于 :func:`repr`，未来版本的Python中将会去掉它们，这个功能不再用于现代的Python代码。

The :func:`str` function is meant to return representations of values which are
fairly human-readable, while :func:`repr` is meant to generate representations
which can be read by the interpreter (or will force a :exc:`SyntaxError` if
there is not equivalent syntax).  For objects which don't have a particular
representation for human consumption, :func:`str` will return the same value as
:func:`repr`.  Many values, such as numbers or structures like lists and
dictionaries, have the same representation using either function.  Strings and
floating point numbers, in particular, have two distinct representations.

函数 :func:`str` 用于将值转化为适于人阅读的形式，而 :func:`repr` 转化为供解释器读取的形式（如果没有等价的语法，则会发生 :exc:`SyntaxError` 异常） 某对象没有适于人阅读的解释形式的话， :func:`str` 会返回与 :func:`repr` 等同的值。很多类型，诸如数值或链表、字典这样的结构，针对各函数都有着统一的解读方式。字符串和浮点数，有不同的解读方式。

Some examples::

下面是一些示例：

   >>> s = 'Hello, world.'
   >>> str(s)
   'Hello, world.'
   >>> repr(s)
   "'Hello, world.'"
   >>> str(0.1)
   '0.1'
   >>> repr(0.1)
   '0.10000000000000001'
   >>> x = 10 * 3.25
   >>> y = 200 * 200
   >>> s = 'The value of x is ' + repr(x) + ', and y is ' + repr(y) + '...'
   >>> print(s)
   The value of x is 32.5, and y is 40000...
   >>> # The repr() of a string adds string quotes and backslashes:
   ... hello = 'hello, world\n'
   >>> hellos = repr(hello)
   >>> print(hellos)
   'hello, world\n'
   >>> # The argument to repr() may be any Python object:
   ... repr((x, y, ('spam', 'eggs')))
   "(32.5, 40000, ('spam', 'eggs'))"
   >>> # reverse quotes are convenient in interactive sessions:
   ... `x, y, ('spam', 'eggs')`
   "(32.5, 40000, ('spam', 'eggs'))"

Here are two ways to write a table of squares and cubes::

以下两种方式可以输出平方和立方表：

   >>> for x in range(1, 11):
   ...     print(repr(x).rjust(2), repr(x*x).rjust(3), end=' ')
   ...     # Note use of 'end' on previous line
   ...     print(repr(x*x*x).rjust(4))
   ...
    1   1    1
    2   4    8
    3   9   27
    4  16   64
    5  25  125
    6  36  216
    7  49  343
    8  64  512
    9  81  729
   10 100 1000

   >>> for x in range(1, 11):
   ...     print('%2d %3d %4d' % (x, x*x, x*x*x))
   ... 
    1   1    1
    2   4    8
    3   9   27
    4  16   64
    5  25  125
    6  36  216
    7  49  343
    8  64  512
    9  81  729
   10 100 1000

(Note that in the first example, one space between each column was added by the
way :func:`print` works: it always adds spaces between its arguments.)

（需要注意的是使用 print 方法时每两列之间有一个空格：它总是在参数之间加一个空格。）

This example demonstrates the :meth:`rjust` method of string objects, which
right-justifies a string in a field of a given width by padding it with spaces
on the left.  There are similar methods :meth:`ljust` and :meth:`center`.  These
methods do not write anything, they just return a new string.  If the input
string is too long, they don't truncate it, but return it unchanged; this will
mess up your column lay-out but that's usually better than the alternative,
which would be lying about a value.  (If you really want truncation you can
always add a slice operation, as in ``x.ljust(n)[:n]``.)

以上是一个 :meth:`rjust` 函数的演示，这个函数把字符串输出到一列，并通过向左侧填充空格来使其右对齐。类似的函数还有 :meth:`ljust` 和 :meth:`center`。这些函数只是输出新的字符串，并不改变什么。如果输出的字符串太长，它们也不会截断它，而是原样输出，这会使你的输出格式变得混乱，不过总强过另一种选择（截断字符串），因为那样会产生错误的输出值。（如果你确实需要截断它，可以使用切割操作，例如：``x.ljust( n)[:n]``。）

There is another method, :meth:`zfill`, which pads a numeric string on the left
with zeros.  It understands about plus and minus signs::

另一个函数 :meth:`zfill` 用于向数值的字符串表达左侧填充零。该函数可以正确理解正负号：

   >>> '12'.zfill(5)
   '00012'
   >>> '-3.14'.zfill(7)
   '-003.14'
   >>> '3.14159265359'.zfill(5)
   '3.14159265359'

Using the ``%`` operator looks like this::

可以如下这样使用 % 操作符：

   >>> import math
   >>> print('The value of PI is approximately %5.3f.' % math.pi)
   The value of PI is approximately 3.142.

If there is more than one format in the string, you need to pass a tuple as
right operand, as in this example::

如果有超过一个的字符串要格式化为一体，就需要将它们传入一个元组作为右值，如下所示：

   >>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
   >>> for name, phone in table.items():
   ...     print('%-10s ==> %10d' % (name, phone))
   ... 
   Jack       ==>       4098
   Dcab       ==>       7678
   Sjoerd     ==>       4127

Most formats work exactly as in C and require that you pass the proper type;
however, if you don't you get an exception, not a core dump. The ``%s`` format
is more relaxed: if the corresponding argument is not a string object, it is
converted to string using the :func:`str` built-in function.  Using ``*`` to
pass the width or precision in as a separate (integer) argument is supported.
The C formats ``%n`` and ``%p`` are not supported.

大多数类 C 的格式化操作都需要你传入适当的类型，不过如果你没有定义异常，也不会有什么从内核中主动的弹出来。（however, if you don't you get an exception, not a core dump）使用 %s 格式会更轻松些：如果对应的参数不是字符串，它会通过内置的 str() 函数转化为字符串。Python支持用 ``*`` 作为一个分隔（整型的）参数来传递宽度或精度。Python 不支持 C 的 ``%n`` 和 ``%p`` 操作符。

If you have a really long format string that you don't want to split up, it
would be nice if you could reference the variables to be formatted by name
instead of by position.  This can be done by using form ``%(name)format``, as
shown here::

如果你有一个非常长的格式字符串，又不想分割开，按格式中的名字引用变量会是个好主意。这可以通过使用form %(name)format 结构实现：

   >>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
   >>> print('Jack: %(Jack)d; Sjoerd: %(Sjoerd)d; Dcab: %(Dcab)d' % table)
   Jack: 4098; Sjoerd: 4127; Dcab: 8637678

This is particularly useful in combination with the new built-in :func:`vars`
function, which returns a dictionary containing all local variables.

这个技巧与新的内置函数 :func:`vars` 组合使用时非常有用，该函数返回一个包含所有局部变量的字典。

The :mod:`string` module contains a class Template which offers yet another way
to substitute values into strings.

模块 :mod:`string` 包含一个模板类型，提供了另一种将变量组合到字符串中的方法。

.. _tut-files:

Reading and Writing Files 读写文件
=========================

.. index::
   builtin: open
   object: file

:func:`open` returns a file object, and is most commonly used with two
arguments: ``open(filename, mode)``.

:func:`open` 返回一个文件，通常的用法需要两个参数： ``open(filename, mode)``。

.. % Opening files

::

   >>> f=open('/tmp/workfile', 'w')
   >>> print(f)
   <open file '/tmp/workfile', mode 'w' at 80a0960>

The first argument is a string containing the filename.  The second argument is
another string containing a few characters describing the way in which the file
will be used.  *mode* can be ``'r'`` when the file will only be read, ``'w'``
for only writing (an existing file with the same name will be erased), and
``'a'`` opens the file for appending; any data written to the file is
automatically added to the end.  ``'r+'`` opens the file for both reading and
writing. The *mode* argument is optional; ``'r'`` will be assumed if it's
omitted.

第一个参数是一个标识文件名的字符串。第二个参数是由有限的字母组成的字符串，描述了文件将会被如何使用。可选的模式 有： ``'r'`` ，此选项使文件只读； ``'w'``，此选项使文件只写（对于同名文件，该操作使原有文件被覆盖）； ``'a'`` ，此选项以追加方式打开文件； ``'r+'`` ，此选项以读写方式打开文件；如果没有指定，默认为 ``'r'`` 模式。

``'b'`` appended to the mode opens the file in binary mode, so there are
also modes like ``'rb'``, ``'wb'``, and ``'r+b'``.  Python distinguishes
between text and binary files.  Binary files are read and written without
any data transformation.  In text mode, platform-specific newline
representations are automatically converted to newlines when read and
newline characters are automatically converted to the proper
platform-specific representation when written.  This makes writing portable
code which reads or writes text files easier.  In addition, when reading
from or writing to text files, the data are automatically decoded or
encoding, respectively, using the encoding associated with the file.

'b'模式以二进制方式打开文件，所以可能会有类似于 'rb' ，'wb' ， 'r+b' 等等模式组合。Python 区分文本文件和二进制文件。二进制文件读写时不做任何数据转换。文本模式下，读的时候，自动在行末添加平台相关的换行符在结尾；写的时候，自动将其转换为平台相关的换行描述。这使得读写文本文件更方便。另外，从文本文件中读写的时候，使用文件设定的编码自动编解码。

This behind-the-scenes modification to file data is fine for text files, but
will corrupt binary data like that in :file:`JPEG` or :file:`EXE` files.  Be
very careful to use binary mode when reading and writing such files.

这种后台操作方式对文本文件没有什么问题，但是操作 JPEG 或 .EXE这样的二进制文件时就会产生破坏。在操作这些文件时一定要记得以二进制模式打开。

.. _tut-filemethods:

Methods of File Objects 文件对象方法
-----------------------

The rest of the examples in this section will assume that a file object called
``f`` has already been created.

本节中的示例都假设文件对象 f 已经创建。

To read a file's contents, call ``f.read(size)``, which reads some quantity of
data and returns it as a string.  *size* is an optional numeric argument.  When
*size* is omitted or negative, the entire contents of the file will be read and
returned; it's your problem if the file is twice as large as your machine's
memory. Otherwise, at most *size* bytes are read and returned.  If the end of
the file has been reached, ``f.read()`` will return an empty string (``""``).
::

要读取文件内容，需要调用 ``f.read(size)``，该方法读取若干数量的数据并以字符串形式返回其内容。*size* 是一个可选的数值参数。如果没有指定 size或者指定为负数，就会读取并返回整个文件。当文件大小为当前机器内存两倍时，就会给你惹麻烦。不过，应该尽可能按比较大的 *size* 读取和返回数据。如果到了文件末尾，``f.read()``会返回一个空字符串（``""``）。

   >>> f.read()
   'This is the entire file.\n'
   >>> f.read()
   ''

``f.readline()`` reads a single line from the file; a newline character (``\n``)
is left at the end of the string, and is only omitted on the last line of the
file if the file doesn't end in a newline.  This makes the return value
unambiguous; if ``f.readline()`` returns an empty string, the end of the file
has been reached, while a blank line is represented by ``'\n'``, a string
containing only a single newline.   ::

``f.readline()`` 从文件中读取单独一行，字符串结尾会自动加上一个换行符（``\n``），只有当文件最后一行没有以换行符结尾时，这一操作才会被忽略。这样返回值就不会有什么混淆不清，如果如果 ``f.readline()`` 返回一个空字符串，那就表示到达了文件末尾，如果是一个空行，就会描述为 ``'\n'`` ，一个只包含换行符的字符串：

   >>> f.readline()
   'This is the first line of the file.\n'
   >>> f.readline()
   'Second line of the file\n'
   >>> f.readline()
   ''

``f.readlines()`` returns a list containing all the lines of data in the file.
If given an optional parameter *sizehint*, it reads that many bytes from the
file and enough more to complete a line, and returns the lines from that.  This
is often used to allow efficient reading of a large file by lines, but without
having to load the entire file in memory.  Only complete lines will be returned.
::

``f.readlines()`` 返回一个列表，其中包含了文件中所有的数据行。如果给定了可选的 *sizehint* 参数，就会读入多于一行的比特数，从中返回多行文本。这个功能通常用于高效读取大型行文件，避免了将整个文件读入内存。这种操作只返回完整的行。

   >>> f.readlines()
   ['This is the first line of the file.\n', 'Second line of the file\n']

An alternative approach to reading lines is to loop over the file object. This is
memory efficient, fast, and leads to simpler code::

有个替代的方法，遍历文件读取文件对象中的行。这是内存操作，效率，快速，代码简单：

   >>> for line in f:
           print(line, end='')

   This is the first line of the file.
   Second line of the file

The alternative approach is simpler but does not provide as fine-grained
control.  Since the two approaches manage line buffering differently, they
should not be mixed.

这个替代方法很简单，但是不提供完整的控制。因为两个方法管理行缓冲的方式不同，它们不能混合。

``f.write(string)`` writes the contents of *string* to the file, returning
``None``.   ::

``f.wirte(string)`` 将 *string* 的内容写入文件，返回 ``None``。：

   >>> f.write('This is a test\n')

To write something other than a string, it needs to be converted to a string
first::

如果需要写入字符串以外的数据，就要先把这些数据转换为字符串：

   >>> value = ('the answer', 42)
   >>> s = str(value)
   >>> f.write(s)

``f.tell()`` returns an integer giving the file object's current position in the
file, measured in bytes from the beginning of the file.  To change the file
object's position, use ``f.seek(offset, from_what)``.  The position is computed
from adding *offset* to a reference point; the reference point is selected by
the *from_what* argument.  A *from_what* value of 0 measures from the beginning
of the file, 1 uses the current file position, and 2 uses the end of the file as
the reference point.  *from_what* can be omitted and defaults to 0, using the
beginning of the file as the reference point. ::

``f.tell()`` 返回一个整数，代表文件对象在文件中的指针位置，该数值计量了自文件开头到指针处的比特数。需要改变文件对象指针话话，使用 ``f.seek(offset,from_what)`` 。指针在该操作中从指定的引用位置移动 *offset* 比特，引用位置由 *from_what* 参数指定。 *from_what* 值为 0 表示自文件起初处开始，1 表示自当前文件指针位置开始，2 表示自文件末尾开始。 *from_what* 可以忽略，其默认值为零，此时从文件头开始：

   >>> f = open('/tmp/workfile', 'r+')
   >>> f.write('0123456789abcdef')
   >>> f.seek(5)     # Go to the 6th byte in the file
   >>> f.read(1)        
   '5'
   >>> f.seek(-3, 2) # Go to the 3rd byte before the end
   >>> f.read(1)
   'd'

When you're done with a file, call ``f.close()`` to close it and free up any
system resources taken up by the open file.  After calling ``f.close()``,
attempts to use the file object will automatically fail. ::

文件使用完后，调用 ``f.close()`` 可以关闭文件，释放打开文件后占用的系统资源。调用 ``f.close()`` 之后，再调用文件对象会自动引发错误。

   >>> f.close()
   >>> f.read()
   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
   ValueError: I/O operation on closed file

File objects have some additional methods, such as :meth:`isatty` and
:meth:`truncate` which are less frequently used; consult the Library Reference
for a complete guide to file objects.

文件对象还有一些不太常用的附加方法，比如 :meth:``isatty`` 和 :meth:`truncate` 在库参考手册中有文件对象的完整指南。

.. _tut-pickle:

The :mod:`pickle` Module :mod:`pickle` 模块
------------------------

.. index:: module: pickle

Strings can easily be written to and read from a file. Numbers take a bit more
effort, since the :meth:`read` method only returns strings, which will have to
be passed to a function like :func:`int`, which takes a string like ``'123'``
and returns its numeric value 123.  However, when you want to save more complex
data types like lists, dictionaries, or class instances, things get a lot more
complicated.

我们可以很容易的读写文件中的字符串。数值就要多费点儿周折，因为 :meth:`read` 方法只会返回字符串，应该将其传入 :fun:`int` 方法中，就可以将 ``'123'`` 这样的字符转为对应的数值123。不过，当你需要保存更为复杂的数据类型，例如链表、字典，类的实例，事情就会变得更复杂了。

Rather than have users be constantly writing and debugging code to save
complicated data types, Python provides a standard module called :mod:`pickle`.
This is an amazing module that can take almost any Python object (even some
forms of Python code!), and convert it to a string representation; this process
is called :dfn:`pickling`.  Reconstructing the object from the string
representation is called :dfn:`unpickling`.  Between pickling and unpickling,
the string representing the object may have been stored in a file or data, or
sent over a network connection to some distant machine.

好在用户不是非得自己编写和调试保存复杂数据类型的代码。 Python提供了一个名为 :mod:`pickle` 的标准模块。这是一个令人赞叹的模块，几乎可以把任何 Python对象 （甚至是一些 Python 代码段！）表达为为字符串，这一过程称之为*封装* （ :dfn:`pickling`）。从字符串表达出重新构造对象称之为*拆封*（ unpickling）。封装状态中的对象可以存储在文件或对象中，也可以通过网络在远程的机器之间传输。

If you have an object ``x``, and a file object ``f`` that's been opened for
writing, the simplest way to pickle the object takes only one line of code::

如果你有一个对象 ``x`` ，一个以写模式打开的文件对象 ``f``，封装对象的最简单的方法只需要一行代码：

   pickle.dump(x, f)

To unpickle the object again, if ``f`` is a file object which has been opened
for reading::

如果 ``f`` 是一个以读模式打开的文件对象，就可以重装拆封这个对象：

   x = pickle.load(f)

(There are other variants of this, used when pickling many objects or when you
don't want to write the pickled data to a file; consult the complete
documentation for :mod:`pickle` in the Python Library Reference.)

（如果不想把封装的数据写入文件，这里还有一些其它的变化可用。完整的 :mod:`pickle` 文档请见Python 库参考手册）。

:mod:`pickle` is the standard way to make Python objects which can be stored and
reused by other programs or by a future invocation of the same program; the
technical term for this is a :dfn:`persistent` object.  Because :mod:`pickle` is
so widely used, many authors who write Python extensions take care to ensure
that new data types such as matrices can be properly pickled and unpickled.

:mod:`pickle` 是存储 Python 对象以供其它程序或其本身以后调用的标准方法。提供这一组技术的是一个持久化对象。因为 :mod:`pickle` 的用途很广泛，很多 Python 扩展的作者都非常注意类似矩阵这样的新数据类型是否适合封装和拆封。
