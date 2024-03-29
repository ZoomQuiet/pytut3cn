.. _tut-classes:

*******
Classes 类
*******

Python's class mechanism adds classes to the language with a minimum of new
syntax and semantics.  It is a mixture of the class mechanisms found in C++ and
Modula-3.  As is true for modules, classes in Python do not put an absolute
barrier between definition and user, but rather rely on the politeness of the
user not to "break into the definition."  The most important features of classes
are retained with full power, however: the class inheritance mechanism allows
multiple base classes, a derived class can override any methods of its base
class or classes, and a method can call the method of a base class with the same
name.  Objects can contain an arbitrary amount of private data.

Python 在尽可能不增加新的语法和语义的情况下加入了类机制。这种机制是 C++ 和 Modula-3 的混合。Python中的类没有在用户和定义之间建立一个绝对的屏障，而是依赖于用户自觉的不去“破坏定义”。然而，类机制最重要的功能都完整的保留下来：类继承机制允许多继承，派生类可以覆盖（override）基类中的任何方法，方法中可以调用基类中的同名方法。对象可以包含任意数量的私有成员。

In C++ terminology, normally class members (including the data members) are 
*public* (except see below :ref:`tut-private`),
and all member functions are *virtual*.  There are no special constructors or
destructors.  As in Modula-3, there are no shorthands for referencing the
object's members from its methods: the method function is declared with an
explicit first argument representing the object, which is provided implicitly by
the call.  As in Smalltalk, classes themselves are objects, albeit in the wider
sense of the word: in Python, all data types are objects.  This provides
semantics for importing and renaming.  Unlike  C++ and Modula-3, built-in types
can be used as base classes for extension by the user.  Also, like in C++ but
unlike in Modula-3, most built-in operators with special syntax (arithmetic
operators, subscripting etc.) can be redefined for class instances.

用 C++ 术语来讲，所有的类成员（包括数据成员）都是*公有*（参见 :ref:`tut-private` ）的，所有的成员函数都是*虚*（ virtual ）的。没有特定的构造和析构函数。像 Modula-3 一样，在成员方法中没有什么简便的方式可以引用对象的成员：方法函数在定义时需要以引用的对象做为第一个参数，调用时则会隐式引用对象。像 Smalltalk 一样，类本身就是对象，在更为广义的范围上理解： Python 中一切数据类型都是对象。这样就形成了语义上的引入和重命名。但是，像 C++ 而非 Modula-3 中那样，大多数带有特殊语法的内置操作符（算法运算符、下标等）都可以针对类的需要重新定义。


.. _tut-terminology:

A Word About Terminology 术语漫谈
========================

Lacking universally accepted terminology to talk about classes, I will make
occasional use of Smalltalk and C++ terms.  (I would use Modula-3 terms, since
its object-oriented semantics are closer to those of Python than C++, but I
expect that few readers have heard of it.)

由于没有什么关于类的通用术语，我从 Smalltalk 和 C++ 中借用一些（我更希望用 Modula-3 的，因为它的面向对象机制比 C++更接近Python，不过我想没多少读者听说过它）。

Objects have individuality, and multiple names (in multiple scopes) can be bound
to the same object.  This is known as aliasing in other languages.  This is
usually not appreciated on a first glance at Python, and can be safely ignored
when dealing with immutable basic types (numbers, strings, tuples).  However,
aliasing has an (intended!) effect on the semantics of Python code involving
mutable objects such as lists, dictionaries, and most types representing
entities outside the program (files, windows, etc.).  This is usually used to
the benefit of the program, since aliases behave like pointers in some respects.
For example, passing an object is cheap since only a pointer is passed by the
implementation; and if a function modifies an object passed as an argument, the
caller will see the change --- this eliminates the need for two different
argument passing mechanisms as in Pascal.

对象是被特化的，多个名字（在多个作用域中）可以绑定同一个对象。这相当于其它语言中的别名。通常对 Python 的第一印象中会忽略这一点，使用那些不可变的基本类型（数值、字符串、元组）时也可以很放心的忽视它。然而，在 Python 代码调用字典、链表之类可变对象，以及大多数涉及程序外部实体（文件、窗体等等）的类型时，这一语义就会有影响。这通用有助于优化程序，因为别名的行为在某些方面类似于指针。例如，很容易传递一个对象，因为在行为上只是传递了一个指针。如果函数修改了一个通过参数传递的对象，调用者可以接收到变化－－在 Pascal 中这需要两个不同的参数传递机制。

.. _tut-scopes:

Python Scopes and Name Spaces 作用域和命名空间
=============================

Before introducing classes, I first have to tell you something about Python's
scope rules.  Class definitions play some neat tricks with namespaces, and you
need to know how scopes and namespaces work to fully understand what's going on.
Incidentally, knowledge about this subject is useful for any advanced Python
programmer.

在介绍类之前，我首先介绍一些有关 Python 作用域的规则：类的定义非常巧妙的运用了命名空间，要完全理解接下来的知识，需要先理解作用域和命名空间的工作原理。另外，这一切的知识对于任何高级 Python 程序员都非常有用。

Let's begin with some definitions.

我们从一些定义开始。

A *namespace* is a mapping from names to objects.  Most namespaces are currently
implemented as Python dictionaries, but that's normally not noticeable in any
way (except for performance), and it may change in the future.  Examples of
namespaces are: the set of built-in names (functions such as :func:`abs`, and
built-in exception names); the global names in a module; and the local names in
a function invocation.  In a sense the set of attributes of an object also form
a namespace.  The important thing to know about namespaces is that there is
absolutely no relation between names in different namespaces; for instance, two
different modules may both define a function "maximize" without confusion ---
users of the modules must prefix it with the module name.

*命名空间*是从命名到对象的映射。当前命名空间主要是通过 Python 字典实现的，不过通常不关心具体的实现方式（除非出于性能考虑），以后也有可能会改变其实现方式。以下有一些命名空间的例子：内置命名（像 :func:`abs` 这样的函数，以及内置异常名）集，模块中的全局命名，函数调用中的局部命名。某种意义上讲对象的属性集也是一个命名空间。关于命名空间需要了解的一件很重要的事就是不同命名空间中的命名没有任何联系，例如两个不同的模块可能都会定义一个名为“maximize”的函数而不会发生混淆－－用户必须以模块名为前缀来引用它们。

By the way, I use the word *attribute* for any name following a dot --- for
example, in the expression ``z.real``, ``real`` is an attribute of the object
``z``.  Strictly speaking, references to names in modules are attribute
references: in the expression ``modname.funcname``, ``modname`` is a module
object and ``funcname`` is an attribute of it.  In this case there happens to be
a straightforward mapping between the module's attributes and the global names
defined in the module: they share the same namespace!  [#]_

顺便提一句，我习惯称 Python 中任何一个“.”之后的命名为*属性*－－例如，表达式 z.real 中的 real 是对象 z 的一个属性。严格来讲，从模块中引用命名是引用属性：表达式 ``modname.funcname`` 中， ``modname`` 是一个模块对象，``funcname`` 是它的一个属性。因此，模块的属性和模块中的全局命名有直接的映射关系：它们共享同一命名空间！[#]_

Attributes may be read-only or writable.  In the latter case, assignment to
attributes is possible.  Module attributes are writable: you can write
``modname.the_answer = 42``.  Writable attributes may also be deleted with the
:keyword:`del` statement.  For example, ``del modname.the_answer`` will remove
the attribute :attr:`the_answer` from the object named by ``modname``.

属性可以是只读或可写的。后一种情况下，可以对属性赋值。你可以这样作：``modname.the_answer = 42``。可写的属性也可以用 :keyword:`del` 语句删除。例如：``del modname.the_answer`` 会从 ``modname`` 对象中删除 ``the_answer`` 属性。

Name spaces are created at different moments and have different lifetimes.  The
namespace containing the built-in names is created when the Python interpreter
starts up, and is never deleted.  The global namespace for a module is created
when the module definition is read in; normally, module namespaces also last
until the interpreter quits.  The statements executed by the top-level
invocation of the interpreter, either read from a script file or interactively,
are considered part of a module called :mod:`__main__`, so they have their own
global namespace.  (The built-in names actually also live in a module; this is
called :mod:`builtins`.)

在不同的时刻创建的命名空间，有不同的生存期。包含内置命名的命名空间在 Python 解释器启动时创建，会一直保留，不被删除。模块的全局命名空间在模块定义被读入时创建，通常，模块命名空间也会一直保存到解释器退出。由解释器在最高层调用执行的语句，不管它是从脚本文件中读入还是来自交互式输入，都是 `__main__` 模块的一部分，所以它们也拥有自己的命名空间。（内置命名也同样被包含在一个模块中，它被称作 :mod:`__builtin__` 。）

The local namespace for a function is created when the function is called, and
deleted when the function returns or raises an exception that is not handled
within the function.  (Actually, forgetting would be a better way to describe
what actually happens.)  Of course, recursive invocations each have their own
local namespace.

当函数被调用时创建一个局部命名空间，函数反正返回过抛出一个未在函数内处理的异常时删除。（实际上，说是遗忘更为贴切）。当然，每一个递归调用拥有自己的命名空间。

A *scope* is a textual region of a Python program where a namespace is directly
accessible.  "Directly accessible" here means that an unqualified reference to a
name attempts to find the name in the namespace.

*作用域*是Python程序中一个命名空间可以直接访问的正文区域。“直接访问”在这里的意思是查找命名时无需引用命名前缀。

Although scopes are determined statically, they are used dynamically. At any
time during execution, there are at least three nested scopes whose namespaces
are directly accessible: the innermost scope, which is searched first, contains
the local names; the namespaces of any enclosing functions, which are searched
starting with the nearest enclosing scope; the middle scope, searched next,
contains the current module's global names; and the outermost scope (searched
last) is the namespace containing built-in names.

尽管作用域是静态定义，在使用时他们都是动态的。每次执行时，至少有三个命名空间可以直接访问的作用域嵌套在一起：包含局部命名的使用域在最里面，首先被搜索；其次搜索的是中层的作用域，这里包含了同级的函数；最后搜索最外面的作用域，它包含内置命名。

If a name is declared global, then all references and assignments go directly to
the middle scope containing the module's global names. Otherwise, all variables
found outside of the innermost scope are read-only (an attempt to write to such
a variable will simply create a *new* local variable in the innermost scope,
leaving the identically named outer variable unchanged).

如果一个命名声明为全局的，那么所有的赋值和引用都直接针对包含模全局命名的中级作用域。另外，从外部访问到的所有内层作用域的变量都是只读的。（试图写这样的变量只会在内部作用域创建一个*新*局部变量，外部标示命名的那个变量不会改变）。

Usually, the local scope references the local names of the (textually) current
function.  Outside functions, the local scope references the same namespace as
the global scope: the module's namespace. Class definitions place yet another
namespace in the local scope.

从字面意义上讲，局部作用域引用当前函数的命名。在函数之外，局部作用域与全局使用域引用同一命名空间：模块命名空间。类定义也是局部作用域中的另一个命名空间。

It is important to realize that scopes are determined textually: the global
scope of a function defined in a module is that module's namespace, no matter
from where or by what alias the function is called.  On the other hand, the
actual search for names is done dynamically, at run time --- however, the
language definition is evolving towards static name resolution, at "compile"
time, so don't rely on dynamic name resolution!  (In fact, local variables are
already determined statically.)

重要的是作用域决定于源程序的文本：一个定义于某模块中的函数的全局作用域是该模块的命名空间，而不是该函数的别名被定义或调用的位置，了解这一点非常重要。另一方面，命名的实际搜索过程是动态的，在运行时确定的——然而，Python 语言也在不断发展，以后有可能会成为静态的“编译”时确定，所以不要依赖动态解析！（事实上，局部变量已经是静态确定了。）

A special quirk of Python is that assignments normally go into the innermost
scope.  Assignments do not copy data --- they just bind names to objects.  The
same is true for deletions: the statement ``del x`` removes the binding of ``x``
from the namespace referenced by the local scope.  In fact, all operations that
introduce new names use the local scope: in particular, import statements and
function definitions bind the module or function name in the local scope.

Python 的一个特别之处在于其赋值操作总是在最里层的作用域。赋值不会复制数据——只是将命名绑定到对象。删除也是如此："del x" 只是从局部作用域的命名空间中删除命名 x 。事实上，所有引入新命名的操作都作用于局部作用域。特别是 import 语句和函数定将模块名或函数绑定于局部作用域。（可以使用 global 语句将变量引入到全局作用域。）

The :keyword:`global` statement can be used to indicate that particular
variables live in the global scope and should be rebound there; the
:keyword:`nonlocal` statement indicates that particular variables live in
an enclosing scope and should be rebound there.

关键字 :keyword:`global` 可以用于从全局命名域引入个别变量并重绑定它；:keyword:`nonlocal` 语句检索外围的作用域并进行重绑定。

.. _tut-scopeexample:

Scopes and Namespaces Example 作用域和命名空间示例
-----------------------------

This is an example demonstrating how to reference the different scopes and
namespaces, and how :keyword:`global` and :keyword:`nonlocal` affect variable
binding::

以下示例演示了如何引用不同的作用域和命名空间，以及如何使用 :keyword:`global` 和 :keyword:`nonlocal` 影响变量绑定::

   def scope_test():
       def do_local():
           spam = "local spam"
       def do_nonlocal():
           nonlocal spam
           spam = "nonlocal spam"
       def do_global():
           global spam
           spam = "global spam"
   
       spam = "test spam"
       do_local()
       print("After local assignment:", spam)
       do_nonlocal()
       print("After nonlocal assignment:", spam)
       do_global()
       print("After global assignment:", spam)

   scope_test()
   print("In global scope:", spam)

The output of the example code is::

示例代码输出如下::

   After local assignment: test spam
   After nonlocal assignment: nonlocal spam
   After global assignment: nonlocal spam
   In global scope: global spam

Note how the *local* assignment (which is default) didn't change *scope_test*\'s
binding of *spam*.  The :keyword:`nonlocal` assignment changed *scope_test*\'s
binding of *spam*, and the :keyword:`global` assignment changed the module-level
binding.

注意 *局部* 赋值（默认的） 不会改变 *sope_test* 在 *spam* 上的绑定。 :keyword:`nonlocal` 赋值改变了 *scope_test* 在 *spam* 上的绑定， 而 :keyword:`global` 赋值改变了模块级绑定。

You can also see that there was no previous binding for *spam* before the
:keyword:`global` assignment.

你可以观察到没有进行 :keyword:`global` 赋值之前 *spam* 上的绑定没有改变。

.. _tut-firstclasses:

A First Look at Classes 初识类
=======================

Classes introduce a little bit of new syntax, three new object types, and some
new semantics.

类引入了一点新的语法，三种新的对象类型，以及一些新的语义。

.. _tut-classdefinition:

Class Definition Syntax 类定义语法
-----------------------

The simplest form of class definition looks like this::

最简单的类定义形式如下::

   class ClassName:
       <statement-1>
       .
       .
       .
       <statement-N>

Class definitions, like function definitions (:keyword:`def` statements) must be
executed before they have any effect.  (You could conceivably place a class
definition in a branch of an :keyword:`if` statement, or inside a function.)

类的定义就像函数定义（:keyword:`def` 语句），要先执行才能生效。（你当然可以把它放进 :keyword:`if` 语句的某一分支，或者一个函数的内部。）

In practice, the statements inside a class definition will usually be function
definitions, but other statements are allowed, and sometimes useful --- we'll
come back to this later.  The function definitions inside a class normally have
a peculiar form of argument list, dictated by the calling conventions for
methods --- again, this is explained later.

习惯上，类定义语句的内容通常是函数定义，不过其它语句也可以，有时会很有用——后面我们再回过头来讨论。类中的函数定义通常包括了一个特殊形式的参数列表，用于方法调用约定——同样我们在后面讨论这些。

When a class definition is entered, a new namespace is created, and used as the
local scope --- thus, all assignments to local variables go into this new
namespace.  In particular, function definitions bind the name of the new
function here.

进入类定义后，会创建一个新的命名空间，就像使用一个局部使用域－－因此，所有对局部变量的赋值都会处于这个新的命名空间。此时函数定义绑定这这里的新函数名上。

When a class definition is left normally (via the end), a *class object* is
created.  This is basically a wrapper around the contents of the namespace
created by the class definition; we'll learn more about class objects in the
next section.  The original local scope (the one in effect just before the class
definition was entered) is reinstated, and the class object is bound here to the
class name given in the class definition header (:class:`ClassName` in the
example).

类定义完成时（正常退出），就创建了一个类对象。基本上它是对类定义创建的命名空间进行了一个包装；我们在下一节进一步学习类对象的知识。原始的局部作用域（类定义引入之前生效的那个）得到恢复，类对象在这里绑定到类定义头部的类名（例子中是 :class:`ClassName` ）。

.. _tut-classobjects:

Class Objects 类对象
-------------

Class objects support two kinds of operations: attribute references and
instantiation.

类对象支持两种操作：属性引用和实例化。

*Attribute references* use the standard syntax used for all attribute references
in Python: ``obj.name``.  Valid attribute names are all the names that were in
the class's namespace when the class object was created.  So, if the class
definition looked like this::

*属性引用*使用和 Python 中所有的属性引用一样的标准语法： ``obj.name``。类对象创建后，类命名空间中所有的命名都是有效属性名。所以如果类定义是这样::

   class MyClass:
       "A simple example class"
       i = 12345
       def f(self):
           return 'hello world'

then ``MyClass.i`` and ``MyClass.f`` are valid attribute references, returning
an integer and a function object, respectively. Class attributes can also be
assigned to, so you can change the value of ``MyClass.i`` by assignment.
:attr:`__doc__` is also a valid attribute, returning the docstring belonging to
the class: ``"A simple example class"``.

那么 ``MyClass.i`` 和 ``MyClass.f`` 是有效的属性引用，分别返回一个整数和一个方法对象。也可以对类属性赋值，你可以通过给 ``MyClass.i`` 赋值来修改它。 :attr:`__doc__` 也是一个有效的属性，返回类的文档字符串： "A simple example class"。

Class *instantiation* uses function notation.  Just pretend that the class
object is a parameterless function that returns a new instance of the class.
For example (assuming the above class)::

类的实例化使用函数符号。只要将类对象看作是一个返回新的类实例的无参数函数即可。例如（假设沿用前面的类）：

   x = MyClass()

creates a new *instance* of the class and assigns this object to the local
variable ``x``.

以上创建了一个新的类*实例*并将该对象赋给局部变量 ``x``。

The instantiation operation ("calling" a class object) creates an empty object.
Many classes like to create objects with instances customized to a specific
initial state. Therefore a class may define a special method named
:meth:`__init__`, like this::

这个实例化操作（“调用”一个类对象）来创建一个空的对象。很多类都倾向于将对象创建为有初始状态的。因此类可能会定义一个名为 :meth:`__init__` 的特殊方法，像下面这样：

   def __init__(self):
       self.data = []

When a class defines an :meth:`__init__` method, class instantiation
automatically invokes :meth:`__init__` for the newly-created class instance.  So
in this example, a new, initialized instance can be obtained by::

类定义了 :meth:`__init__` 方法的话，类的实例化操作会自动为新创建的类实例调用 :meth:`__init__` 方法。所以在下例中，可以这样创建一个新的实例：

   x = MyClass()

Of course, the :meth:`__init__` method may have arguments for greater
flexibility.  In that case, arguments given to the class instantiation operator
are passed on to :meth:`__init__`.  For example, ::

当然，出于灵活的需要， :meth:`__init__` 方法可以有参数。事实上，参数通过 :meth:`__init__` 传递到类的实例化操作上。例如::

   >>> class Complex:
   ...     def __init__(self, realpart, imagpart):
   ...         self.r = realpart
   ...         self.i = imagpart
   ... 
   >>> x = Complex(3.0, -4.5)
   >>> x.r, x.i
   (3.0, -4.5)


.. _tut-instanceobjects:

Instance Objects 实例对象
----------------

Now what can we do with instance objects?  The only operations understood by
instance objects are attribute references.  There are two kinds of valid
attribute names, data attributes and methods.

现在我们可以用实例对象作什么？实例对象唯一可用的操作就是属性引用。有两种有效的属性名。

*data attributes* correspond to "instance variables" in Smalltalk, and to "data
members" in C++.  Data attributes need not be declared; like local variables,
they spring into existence when they are first assigned to.  For example, if
``x`` is the instance of :class:`MyClass` created above, the following piece of
code will print the value ``16``, without leaving a trace::

*数据属性*相当于 Smalltalk 中的“实例变量”或 C++ 中的“数据成员”。和局部变量一样，数据属性不需要声明，第一次使用时它们就会生成。例如，如果 x 是前面创建的 :class:`MyClass` 实例，下面这段代码会打印出 ``16`` 而不会有任何多余的残留：

   x.counter = 1
   while x.counter < 10:
       x.counter = x.counter * 2
   print(x.counter)
   del x.counter

The other kind of instance attribute reference is a *method*. A method is a
function that "belongs to" an object.  (In Python, the term method is not unique
to class instances: other object types can have methods as well.  For example,
list objects have methods called append, insert, remove, sort, and so on.
However, in the following discussion, we'll use the term method exclusively to
mean methods of class instance objects, unless explicitly stated otherwise.)

另一种引用属性是*方法*。方法是“属于”一个对象的函数。（在 Python 中，方法不止是类实例所独有：其它类型的对象也可有方法。例如，链表对象有 append，insert，remove，sort 等等方法。然而，在后面的介绍中，除非特别说明，我们提到的方法特指类方法）

.. index:: object: method

Valid method names of an instance object depend on its class.  By definition,
all attributes of a class that are function  objects define corresponding
methods of its instances.  So in our example, ``x.f`` is a valid method
reference, since ``MyClass.f`` is a function, but ``x.i`` is not, since
``MyClass.i`` is not.  But ``x.f`` is not the same thing as ``MyClass.f`` --- it
is a *method object*, not a function object.

实例对象的有效名称依赖于它的类。按照定义，类中所有（用户定义）的函数对象对应它的实例中的方法。所以在我们的例子中，x.f 是一个有效的方法引用，因为 MyClass.f 是一个函数。但 x.i 不是，因为 MyClass.i 是不是函数。不过 x.f 和 MyClass.f 不同－－它是一个方法对象，不是一个函数对象。

.. _tut-methodobjects:

Method Objects 方法对象
--------------

Usually, a method is called right after it is bound::

通常，方法通过右绑定调用::

   x.f()

In the :class:`MyClass` example, this will return the string ``'hello world'``.
However, it is not necessary to call a method right away: ``x.f`` is a method
object, and can be stored away and called at a later time.  For example::

在 :class:`MyClass` 示例中，这会返回字符串 ``hello world`` 。然而，也不是一定要直接调用方法。 ``x.f`` 是一个方法对象，它可以存储起来以后调用。例如：

   xf = x.f
   while True:
       print(xf())

will continue to print ``hello world`` until the end of time.

会不断的打印 "hello world" 。

What exactly happens when a method is called?  You may have noticed that
``x.f()`` was called without an argument above, even though the function
definition for :meth:`f` specified an argument.  What happened to the argument?
Surely Python raises an exception when a function that requires an argument is
called without any --- even if the argument isn't actually used...

调用方法时发生了什么？你可能注意到调用 ``x.f()`` 时没有引用前面标出的变量，尽管在 :meth:`f` 的函数定义中指明了一个参数。这个参数怎么了？事实上如果函数调用中缺少参数，Python 会抛出异常－－甚至这个参数实际上没什么用……

Actually, you may have guessed the answer: the special thing about methods is
that the object is passed as the first argument of the function.  In our
example, the call ``x.f()`` is exactly equivalent to ``MyClass.f(x)``.  In
general, calling a method with a list of *n* arguments is equivalent to calling
the corresponding function with an argument list that is created by inserting
the method's object before the first argument.

实际上，你可能已经猜到了答案：方法的特别之处在于实例对象作为函数的第一个参数传给了函数。在我们的例子中，调用 ``x.f`` 相当于 ``MyClass.f(x)`` 。通常，以 *n* 个参数的列表去调用一个方法就相当于将方法的对象插入到参数列表的最前面后，以这个列表去调用相应的函数。

If you still don't understand how methods work, a look at the implementation can
perhaps clarify matters.  When an instance attribute is referenced that isn't a
data attribute, its class is searched.  If the name denotes a valid class
attribute that is a function object, a method object is created by packing
(pointers to) the instance object and the function object just found together in
an abstract object: this is the method object.  When the method object is called
with an argument list, it is unpacked again, a new argument list is constructed
from the instance object and the original argument list, and the function object
is called with this new argument list.

如果你还是不理解方法的工作原理，了解一下它的实现也许有帮助。引用非数据属性的实例属性时，会搜索它的类。如果这个命名确认为一个有效的函数对象类属性，就会将实例对象和函数对象封装进一个抽象对象：这就是方法对象。以一个参数列表调用方法对象时，它被重新拆封，用实例对象和原始的参数列表构造一个新的参数列表，然后函数对象调用这个新的参数列表。

.. _tut-remarks:

Random Remarks 一些说明
==============

.. % [These should perhaps be placed more carefully...]

Data attributes override method attributes with the same name; to avoid
accidental name conflicts, which may cause hard-to-find bugs in large programs,
it is wise to use some kind of convention that minimizes the chance of
conflicts.  Possible conventions include capitalizing method names, prefixing
data attribute names with a small unique string (perhaps just an underscore), or
using verbs for methods and nouns for data attributes.

同名的数据属性会覆盖方法属性，为了避免可能的命名冲突－－这在大型程序中可能会导致难以发现的 bug －－最好以某种命名约定来避免冲突。可选的约定包括方法的首字母大写，数据属性名前缀小写（可能只是一个下划线），或者方法使用动词而数据属性使用名词。

Data attributes may be referenced by methods as well as by ordinary users
("clients") of an object.  In other words, classes are not usable to implement
pure abstract data types.  In fact, nothing in Python makes it possible to
enforce data hiding --- it is all based upon convention.  (On the other hand,
the Python implementation, written in C, can completely hide implementation
details and control access to an object if necessary; this can be used by
extensions to Python written in C.)

数据属性可以由方法引用，也可以由普通用户（客户）调用。换句话说，类不能实现纯的数据类型。事实上 Python 中没有什么办法可以强制隐藏数据－－一切都基本约定的惯例。（另一方法讲，Python 的实现是用 C 写成的，如果有必要，可以用 C 来编写 Python 扩展，完全隐藏实现的细节，控制对象的访问。）

Clients should use data attributes with care --- clients may mess up invariants
maintained by the methods by stamping on their data attributes.  Note that
clients may add data attributes of their own to an instance object without
affecting the validity of the methods, as long as name conflicts are avoided ---
again, a naming convention can save a lot of headaches here.

客户应该小心使用数据属性－－客户可能会因为随意修改数据属性而破坏了本来由方法维护的数据一致性。需要注意的是，客户只要注意避免命名冲突，就可以随意向实例中添加数据属性而不会影响方法的有效性－－再次强调，命名约定可以省去很多麻烦。

There is no shorthand for referencing data attributes (or other methods!) from
within methods.  I find that this actually increases the readability of methods:
there is no chance of confusing local variables and instance variables when
glancing through a method.

从方法内部引用数据属性（或者方法！）没有什么快捷的方式。我认为这事实上增加了方法的可读性：即使粗略的浏览一个方法，也不会有混淆局部变量和实例变量的机会。

Often, the first argument of a method is called ``self``.  This is nothing more
than a convention: the name ``self`` has absolutely no special meaning to
Python.  (Note, however, that by not following the convention your code may be
less readable to other Python programmers, and it is also conceivable that a
*class browser* program might be written that relies upon such a convention.)

通常方法的第一个参数命名为 ``self`` 。这仅仅是一个约定：对 Python 而言，``self`` 绝对没有任何特殊含义。（然而要注意的是，如果不遵守这个约定，别的 Python 程序员阅读你的代码时会有不便，而且有些*类浏览器*程序也是遵循此约定开发的。）

Any function object that is a class attribute defines a method for instances of
that class.  It is not necessary that the function definition is textually
enclosed in the class definition: assigning a function object to a local
variable in the class is also ok.  For example::

类属性中的任何函数对象在类实例中都定义为方法。不是必须要将函数定义代码写进类定义中，也可以将一个函数对象赋给类中的一个变量。例如::

   # Function defined outside the class
   def f1(self, x, y):
       return min(x, x+y)

   class C:
       f = f1
       def g(self):
           return 'hello world'
       h = g

Now ``f``, ``g`` and ``h`` are all attributes of class :class:`C` that refer to
function objects, and consequently they are all methods of instances of
:class:`C` --- ``h`` being exactly equivalent to ``g``.  Note that this practice
usually only serves to confuse the reader of a program.

现在 ``f``, ``g`` 和 ``h`` 都是类 :class:`C` 的属性，引用的都是函数对象，因此它们都是 :class:`C`0 实例的方法－－ ``h`` 严格等于 ``g``。要注意的是这种习惯通常只会迷惑程序的读者。

Methods may call other methods by using method attributes of the ``self``
argument::

通过 self 参数的方法属性，方法可以调用其它的方法：

   class Bag:
       def __init__(self):
           self.data = []
       def add(self, x):
           self.data.append(x)
       def addtwice(self, x):
           self.add(x)
           self.add(x)

Methods may reference global names in the same way as ordinary functions.  The
global scope associated with a method is the module containing the class
definition.  (The class itself is never used as a global scope!)  While one
rarely encounters a good reason for using global data in a method, there are
many legitimate uses of the global scope: for one thing, functions and modules
imported into the global scope can be used by methods, as well as functions and
classes defined in it.  Usually, the class containing the method is itself
defined in this global scope, and in the next section we'll find some good
reasons why a method would want to reference its own class!

方法可以像引用普通的函数那样引用全局命名。与方法关联的全局作用域是包含类定义的模块。（类本身永远不会做为全局作用域使用！）尽管很少有好的理由在方法中使用全局数据，全局作用域确有很多合法的用途：其一是方法可以调用导入全局作用域的函数和方法，也可以调用定义在其中的类和函数。通常，包含此方法的类也会定义在这个全局作用域，在下一节我们会了解为何一个方法要引用自己的类！

.. _tut-inheritance:

Inheritance 继承
===========

Of course, a language feature would not be worthy of the name "class" without
supporting inheritance.  The syntax for a derived class definition looks like
this::

当然，如果一种语言不支持继承就，“类”就没有什么意义。派生类的定义如下所示::

   class DerivedClassName(BaseClassName):
       <statement-1>
       .
       .
       .
       <statement-N>

The name :class:`BaseClassName` must be defined in a scope containing the
derived class definition.  In place of a base class name, other arbitrary
expressions are also allowed.  This can be useful, for example, when the base
class is defined in another module::

命名 :class:`BaseClassName`（示例中的基类名）必须与派生类定义在一个作用域内。除了类，还可以用表达式，基类定义在另一个模块中时这一点非常有用::

   class DerivedClassName(modname.BaseClassName):

Execution of a derived class definition proceeds the same as for a base class.
When the class object is constructed, the base class is remembered.  This is
used for resolving attribute references: if a requested attribute is not found
in the class, the search proceeds to look in the base class.  This rule is
applied recursively if the base class itself is derived from some other class.

派生类定义的执行过程和基类是一样的。构造派生类对象时，就记住了基类。这在解析属性引用的时候尤其有用：如果在类中找不到请求调用的属性，就搜索基类。如果基类是由别的类派生而来，这个规则会递归的应用上去。

There's nothing special about instantiation of derived classes:
``DerivedClassName()`` creates a new instance of the class.  Method references
are resolved as follows: the corresponding class attribute is searched,
descending down the chain of base classes if necessary, and the method reference
is valid if this yields a function object.

派生类的实例化没有什么特殊之处：``DerivedClassName()`` （示列中的派生类）创建一个新的类实例。方法引用按如下规则解析：搜索对应的类属性，必要时沿基类链逐级搜索，如果找到了函数对象这个方法引用就是合法的

Derived classes may override methods of their base classes.  Because methods
have no special privileges when calling other methods of the same object, a
method of a base class that calls another method defined in the same base class
may end up calling a method of a derived class that overrides it.  (For C++
programmers: all methods in Python are effectively :keyword:`virtual`.)

派生类可能会覆盖其基类的方法。因为方法调用同一个对象中的其它方法时没有特权，基类的方法调用同一个基类的方法时，可能实际上最终调用了派生类中的覆盖方法。（对于 C++ 程序员来说，Python中的所有方法本质上都是 :keyword:`virtual` 方法。）

An overriding method in a derived class may in fact want to extend rather than
simply replace the base class method of the same name. There is a simple way to
call the base class method directly: just call ``BaseClassName.methodname(self,
arguments)``.  This is occasionally useful to clients as well.  (Note that this
only works if the base class is defined or imported directly in the global
scope.)

派生类中的覆盖方法可能是想要扩充而不是简单的替代基类中的重名方法。有一个简单的方法可以直接调用基类方法，只要调用：``BaseClassName.methodname(self, arguments)``。有时这对于客户也很有用。（要注意的中只有基类在同一全局作用域定义或导入时才能这样用。）

.. _tut-multiple:

Multiple Inheritance 多继承
--------------------

Python supports a form of multiple inheritance as well.  A class definition with
multiple base classes looks like this::

Python同样有限的支持多继承形式。多继承的类定义形如下例：

   class DerivedClassName(Base1, Base2, Base3):
       <statement-1>
       .
       .
       .
       <statement-N>

For most purposes, in the simplest cases, you can think of the search for
attributes inherited from a parent class as depth-first, left-to-right, not
searching twice in the same class where there is an overlap in the hierarchy.
Thus, if an attribute is not found in :class:`DerivedClassName`, it is searched
for in :class:`Base1`, then (recursively) in the base classes of :class:`Base1`,
and if it was not found there, it was searched for in :class:`Base2`, and so on.

对于通常的应用，最简单的场合，你可以认为在父类中搜索继承属性的过程是深度优先，从左向右，交叉点上的同一个类不会被搜索两次。因此，如果 :class:`DerivedClassName` 找不到某个属性，它会搜索 :class:`Base1` ，然后（递归的）是 :class:`Base1` ，如果找不到，它再去搜索 :class:`Base2` 依次类推。

In fact, it is slightly more complex than that; the method resolution order
changes dynamically to support cooperative calls to :func:`super`.  This
approach is known in some other multiple-inheritance languages as
call-next-method and is more powerful than the super call found in
single-inheritance languages.

实际上，这比看上去要复杂的多；解决动态顺序变更，支持协作调用的方法称为 :func:`super`。作为 call-next-method，这也适用于已知的其它多继承语言，比单继承语言中的super调用更强大。

Dynamic ordering is necessary because all cases of multiple inheritance exhibit
one or more diamond relationships (where at least one of the parent classes
can be accessed through multiple paths from the bottommost class).  For example,
all classes inherit from :class:`object`, so any case of multiple inheritance
provides more than one path to reach :class:`object`.  To keep the base classes
from being accessed more than once, the dynamic algorithm linearizes the search
order in a way that preserves the left-to-right ordering specified in each
class, that calls each parent only once, and that is monotonic (meaning that a
class can be subclassed without affecting the precedence order of its parents).
Taken together, these properties make it possible to design reliable and
extensible classes with multiple inheritance.  For more detail, see
http://www.python.org/download/releases/2.3/mro/.

动态排序是必要的，因为多继承场景中总会有一到多个菱形继承（从最底部的类向上，至少会有一个祖先类可以通过多条路径访问到）。如所有的类都继承自 :class:`object`，所以在多继承应用中总会有超过一条路径可以到达 :class:`object`。为了确保基类可以多次访问，动态算法将搜索顺序从左到右线性化，每个祖先只调用一次，这是单调的（意味着一个类型被继承不会影响它的祖先类的优先级）。合起来看，这些东西使得它可以通过多继承设计可靠和可扩展的类型。要了解详细内容，参见 http://www.python.org/download/releases/2.3/mro/。

.. _tut-private:

Private Variables 私有变量
=================

There is limited support for class-private identifiers.  Any identifier of the
form ``__spam`` (at least two leading underscores, at most one trailing
underscore) is textually replaced with ``_classname__spam``, where ``classname``
is the current class name with leading underscore(s) stripped.  This mangling is
done without regard to the syntactic position of the identifier, so it can be
used to define class-private instance and class variables, methods, variables
stored in globals, and even variables stored in instances. private to this class
on instances of *other* classes.  Truncation may occur when the mangled name
would be longer than 255 characters. Outside classes, or when the class name
consists of only underscores, no mangling occurs.

Python 对类的私有成员提供了有限的支持。任何形如 ``__spam`` （以至少双下划线开头，至多单下划线结尾）随即都被替代为 ``_classname__spam`` ，去掉前导下划线的 ``classname`` 即当前的类名。这种混淆不关心标识符的语法位置，所以可用来定义私有类实例和类变量、方法，以及全局变量，甚至于将*其它*类的实例保存为私有变量。混淆名长度超过255个字符的时候可能会发生截断。在类的外部，或类名只包含下划线时，不会发生截断。

Name mangling is intended to give classes an easy way to define "private"
instance variables and methods, without having to worry about instance variables
defined by derived classes, or mucking with instance variables by code outside
the class.  Note that the mangling rules are designed mostly to avoid accidents;
it still is possible for a determined soul to access or modify a variable that
is considered private.  This can even be useful in special circumstances, such
as in the debugger, and that's one reason why this loophole is not closed.
(Buglet: derivation of a class with the same name as the base class makes use of
private variables of the base class possible.)

命名混淆意在给出一个在类中定义“私有”实例变量和方法的简单途径，避免派生类的实例变量定义产生问题，或者与外界代码中的变量搞混。要注意的是混淆规则主要目的在于避免意外错误，被认作为私有的变量仍然有可能被访问或修改。在特定的场合它也是有用的，比如调试的时候，这也是一直没有堵上这个漏洞的原因之一（小漏洞：派生类和基类取相同的名字就可以使用基类的私有变量。）

Notice that code passed to ``exec()`` or ``eval()`` does not
consider the classname of the invoking  class to be the current class; this is
similar to the effect of the  ``global`` statement, the effect of which is
likewise restricted to  code that is byte-compiled together.  The same
restriction applies to ``getattr()``, ``setattr()`` and ``delattr()``, as well
as when referencing ``__dict__`` directly.

要注意的是传入 ``exec()``，``eval()`` 的代码不会将调用它们的类视作当前类，这与 ``global`` 语句的情况类似，``global`` 的作用局限于“同一批”进行字节编译的代码。同样的限制也适用于 ``getattr()``，``setattr()`` 和 ``delattr()`` ，以及直接引用 ``__dict__`` 的时候。

.. _tut-odds:

Odds and Ends  补充
=============

Sometimes it is useful to have a data type similar to the Pascal "record" or C
"struct", bundling together a few named data items.  An empty class definition
will do nicely::

有时类似于Pascal中“记录（record）”或C中“结构（struct）”的数据类型很有用，它将一组已命名的数据项绑定在一起。一个空的类定义可以很好的实现这它::

   class Employee:
       pass

   john = Employee() # Create an empty employee record

   # Fill the fields of the record
   john.name = 'John Doe'
   john.dept = 'computer lab'
   john.salary = 1000

A piece of Python code that expects a particular abstract data type can often be
passed a class that emulates the methods of that data type instead.  For
instance, if you have a function that formats some data from a file object, you
can define a class with methods :meth:`read` and :meth:`readline` that get the
data from a string buffer instead, and pass it as an argument.

某一段 Python 代码需要一个特殊的抽象数据结构的话，通常可以传入一个类，事实上这模仿了该类的方法。例如，如果你有一个用于从文件对象中格式化数据的函数，你可以定义一个带有 :meth:`read` 和 :meth:`readline` 方法的类，以此从字符串缓冲读取数据，然后将该类的对象作为参数传入前述的函数。

.. % (Unfortunately, this
.. % technique has its limitations: a class can't define operations that
.. % are accessed by special syntax such as sequence subscripting or
.. % arithmetic operators, and assigning such a ``pseudo-file'' to
.. % \code{sys.stdin} will not cause the interpreter to read further input
.. % from it.)
.. % （很不幸，这个技术有它的极限：类不能通过特殊符号定义序列下标或算术运算符之类的操作符，并且``伪装`` \code{sys.stdin} 不会引发解释器从中读取输入。）

Instance method objects have attributes, too: ``m.__self__`` is the instance
object with the method :meth:`m`, and ``m.__func__`` is the function object
corresponding to the method.

方法对象实例也有属性，``m.__self__`` 是调用 :meth:`m` 方法的实例对象， ``m.__func__``是这个方法对应的函数对象。

.. _tut-exceptionclasses:

Exceptions Are Classes Too 异常也是类
==========================

User-defined exceptions are identified by classes as well.  Using this mechanism
it is possible to create extensible hierarchies of exceptions.

用户自定义异常也可以是类。利用这个机制可以创建可扩展的异常体系。

There are two valid (semantic) forms for the raise statement::

以下是两种新的有效（语义上的）异常抛出形式：

   raise Class

   raise Instance

In the first form, ``Class`` must be an instance of :class:`type` or of a
class derived from it.  The first form is a shorthand for::

第一种形式中，``Class`` 必须是 :class:`type` 或其派生类的一个实例。第一种形式是以下形式的简写：

   raise Class()

A class in an except clause is compatible with an exception if it is the same
class or a base class thereof (but not the other way around --- an except clause
listing a derived class is not compatible with a base class).  For example, the
following code will print B, C, D in that order::

发生的异常其类型如果是 except 子句中列出的类，或者是其派生类，那么它们就是相符的（但是不能反过来说－－ except 子句列出的类型如果是其子类，不能作为判别依据）。例如，以下代码会按顺序打印B，C，D：

   class B:
       pass
   class C(B):
       pass
   class D(C):
       pass

   for c in [B, C, D]:
       try:
           raise c()
       except D:
           print("D")
       except C:
           print("C")
       except B:
           print("B")

Note that if the except clauses were reversed (with ``except B`` first), it
would have printed B, B, B --- the first matching except clause is triggered.

要注意的是如果异常子句的顺序颠倒过来（ "execpt B" 在最前），它就会打印B，B，B－－第一个匹配的异常被触发。

When an error message is printed for an unhandled exception, the exception's
class name is printed, then a colon and a space, and finally the instance
converted to a string using the built-in function :func:`str`.

打印一个异常类的错误信息时，先打印类名，然后是一个空格、一个冒号，然后是用内置函数 str() 将类转换得到的完整字符串。

.. _tut-iterators:

Iterators 迭代器
=========

By now you have probably noticed that most container objects can be looped over
using a :keyword:`for` statement::

现在你可能注意到大多数容器对象都可以用 :keyword:`for` 遍历::

   for element in [1, 2, 3]:
       print(element)
   for element in (1, 2, 3):
       print(element)
   for key in {'one':1, 'two':2}:
       print(key)
   for char in "123":
       print(char)
   for line in open("myfile.txt"):
       print(line)

This style of access is clear, concise, and convenient.  The use of iterators
pervades and unifies Python.  Behind the scenes, the :keyword:`for` statement
calls :func:`iter` on the container object.  The function returns an iterator
object that defines the method :meth:`__next__` which accesses elements in the
container one at a time.  When there are no more elements, :meth:`__next__`
raises a :exc:`StopIteration` exception which tells the :keyword:`for` loop to
terminate.  You can call the :meth:`__next__` method using the :func:`next`
builtin; this example shows how it all works::

这种形式的访问清晰、简洁、方便。迭代器的用法在 Python 中普遍而且统一。在后台，:keyword:`for` 语句在容器对象中调用 :func:`iter` 。 该函数返回一个定义了 :meth:`__next__` 方法的迭代器对象，它在容器中逐一访问元素。没有后续的元素时，:meth:`__next__` 抛出一个 :exc:`StopIteration` 异常通知 :keyword:`for` 语句循环结束。你可以使用内置函数 :func:`next` 调用 :meth:`__next__` 方法。以下是其工作原理的示例：

   >>> s = 'abc'
   >>> it = iter(s)
   >>> it
   <iterator object at 0x00A1DB50>
   >>> next(it)
   'a'
   >>> next(it)
   'b'
   >>> next(it)
   'c'
   >>> next(it)

   Traceback (most recent call last):
     File "<stdin>", line 1, in ?
       next(it)
   StopIteration

Having seen the mechanics behind the iterator protocol, it is easy to add
iterator behavior to your classes.  Define a :meth:`__iter__` method which
returns an object with a :meth:`__next__` method.  If the class defines
:meth:`__next__`, then :meth:`__iter__` can just return ``self``::

了解了迭代器协议的后台机制，就可以很容易的给自己的类添加迭代器行为。定义一个 :meth:`__iter__` 方法，使其返回一个带有 :meth:`__next` 方法的对象。如果这个类已经定义了 :meth:`__next__`，那么 :meth:`__iter__` 只需要返回``self``：

   class Reverse:
       "Iterator for looping over a sequence backwards"
       def __init__(self, data):
           self.data = data
           self.index = len(data)
       def __iter__(self):
           return self
       def __next__(self):
           if self.index == 0:
               raise StopIteration
           self.index = self.index - 1
           return self.data[self.index]

   >>> for char in Reverse('spam'):
   ...     print(char)
   ...
   m
   a
   p
   s


.. _tut-generators:

Generators 生成器
==========

:term:`Generator`\s are a simple and powerful tool for creating iterators.  They
are written like regular functions but use the :keyword:`yield` statement
whenever they want to return data.  Each time :func:`next` is called on it, the
generator resumes where it left-off (it remembers all the data values and which
statement was last executed).  An example shows that generators can be trivially
easy to create::

:term:`生成器`是创建迭代器的简单而强大的工具。它们写起来就像是正则函数，需要返回数据的时候使用 :keyword:`yield` 语句。每次 :func:`next` 被调用时，生成器回复它脱离的位置（它记忆语句最后一次执行的位置和所有的数据值）。以下示例演示了生成器便捷的创建方式：

   def reverse(data):
       for index in range(len(data)-1, -1, -1):
           yield data[index]

   >>> for char in reverse('golf'):
   ...     print(char)
   ...
   f
   l
   o
   g	

Anything that can be done with generators can also be done with class based
iterators as described in the previous section.  What makes generators so
compact is that the :meth:`__iter__` and :meth:`__next__` methods are created
automatically.

前一节中描述了基于类的迭代器，它能作的每一件事生成器也能作到。因为自动创建了 :meth:`__iter__` 和 :meth:`next` 方法，生成器显得如此简洁。

Another key feature is that the local variables and execution state are
automatically saved between calls.  This made the function easier to write and
much more clear than an approach using instance variables like ``self.index``
and ``self.data``.

另外一个关键的功能是两次调用之间的局部变量和执行情况都自动保存了下来。这样函数编写起来就比手动调用 ``self.index`` 和 ``self.data`` 这样的类变量容易的多。

In addition to automatic method creation and saving program state, when
generators terminate, they automatically raise :exc:`StopIteration`. In
combination, these features make it easy to create iterators with no more effort
than writing a regular function.

除了创建和保存程序状态的自动方法，当发生器终结时，还会自动抛出 :exc:`StopIteration` 异常。综上所述，这些功能使得编写一个正规函数成为创建迭代器的最简单方法。

.. _tut-genexps:

Generator Expressions 生成器表达式
=====================

Some simple generators can be coded succinctly as expressions using a syntax
similar to list comprehensions but with parentheses instead of brackets.  These
expressions are designed for situations where the generator is used right away
by an enclosing function.  Generator expressions are more compact but less
versatile than full generator definitions and tend to be more memory friendly
than equivalent list comprehensions.

有时简单的生成器可以用简洁的方式调用，就像不带中括号的链表推导式。这些表达式是为函数调用生成器而设计的。生成器表达式比完整的生成器定义更简洁，但是没有那么多变，而且通常比等价的列表推导式更容易记。

Examples::

   >>> sum(i*i for i in range(10))                 # sum of squares
   285

   >>> xvec = [10, 20, 30]
   >>> yvec = [7, 5, 3]
   >>> sum(x*y for x,y in zip(xvec, yvec))         # dot product
   260

   >>> from math import pi, sin
   >>> sine_table = dict((x, sin(x*pi/180)) for x in range(0, 91))

   >>> unique_words = set(word  for line in page  for word in line.split())

   >>> valedictorian = max((student.gpa, student.name) for student in graduates)

   >>> data = 'golf'
   >>> list(data[i] for i in range(len(data)-1, -1, -1))
   ['f', 'l', 'o', 'g']



.. rubric:: Footnotes

.. [#] Except for one thing.  Module objects have a secret read-only attribute called
   :attr:`__dict__` which returns the dictionary used to implement the module's
   namespace; the name :attr:`__dict__` is an attribute but not a global name.
   Obviously, using this violates the abstraction of namespace implementation, and
   should be restricted to things like post-mortem debuggers.
   有个例外。模块对象有一个隐秘的只读属性，叫 :attr:`__dict__`，返回组成模块的命名空间； :attr:`__dict__` 这个名字是一个属性而非全局命名。显然，这违反了命名空间实现概念，应该严格限制于调试之类的场合。

