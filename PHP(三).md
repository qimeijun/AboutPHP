# PHP基础（三）


##一、PHP命名空间

> 命名空间概述

命名空间就是一种封装事物的方法。关键字`namespace`

在PHP中，命名空间用来解决在编写类库或应用程序时创建可重用的代码如类或函数时碰到的两类问题：
  1、用户编写的代码与PHP内部的类/函数/常量或第三方类/函数/常量之间的名字冲突。

  2、为很长的标识符名称（通常是为了缓解第一类问题而定义的）创建一个别名的名称，提高源代码的可读性。

```
<?php
namespace my\name; // 参考 "定义命名空间" 小节

class MyClass {}
function myfunction() {}
const MYCONST = 1;

$a = new MyClass;
$c = new \my\name\MyClass; // 参考 "全局空间" 小节

$a = strlen('hi'); // 参考 "使用命名空间：后备全局函数/常量" 小节

$d = namespace\MYCONST; // 参考 "namespace操作符和__NAMESPACE__常量" 小节

$d = __NAMESPACE__ . '\MYCONST';
echo constant($d); // 参考 "命名空间和动态语言特征" 小节
?> 
```

> 定义命名空间

只有类（包含抽象类和trait）、接口、函数和常量才受命名空间的影响。

命名空间通过关键字`namespace`来声明，如果一个文件中包含命名空间，他必须在其他所有代码之前声明命名空间，除了一个以外：`declare`关键字。

```
<?php 
namespace MyProject;
const CONNECT_OK = 1;
class Connection { /* ... */ }
function connect() { /* ... */  }
```
`在声明命名空间之前唯一合法的代码是用于定义源文件编码方式的`declare`语句。另外，所有非PHP代码包括空白符都不能出现在命名空间的声明之前。`

> 定义子命名空间

与目录和文件的关系很象，PHP 命名空间也允许指定层次化的命名空间的名称。因此，命名空间的名字可以使用分层次的方式定义。

```
<?php
namespace MyProject\Sub\Level;
const CONNECT_OK = 1;
class Connection { /* ... */ }
function connect() { /* ... */  }
```

> 在同一个文件中定义多个命名空间

也可以在同一个文件中定义多个命名空间。在同一个文件中定义多个命名空间有两种语法形式。 

```
<?php 
namespace MyProject;
const CONNECT_OK = 1;
class Connection { /* ... */ }
function connect() { /* ... */  }

namespace AnotherProject;
const CONNECT_OK = 1;
class Connection { /* ... */ }
function connect() { /* ... */  }
```
或者

```
<?php
namespace MyProject {
    const CONNECT_OK = 1;
    class Connection { /* ... */ }
    function connect() { /* ... */  }
}

namespace AnotherProject {
    const CONNECT_OK = 1;
    class Connection { /* ... */ }
    function connect() { /* ... */  }
}
```

*建议使用大括号的形式定义多个命名空间。*

*实际编码中，非常不提倡在同一个文件中定义多个命名空间，这种方式的主要用于将多个PHP脚本合并在同一个文件中。*

除了开始的`declare`语句外，命名空间的括号外不得有任何PHP代码。

>  使用命名空间：基础

PHP 命名空间中的元素使用同样的原理。例如，类名可以通过三种方式引用： 
1. 非限定名称，或不包含前缀的类名称，例如 $a=new foo(); 或 foo::staticmethod();。如果当前命名空间是 currentnamespace，foo 将被解析为 currentnamespace\foo。如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，则 foo 会被解析为foo。 警告：如果命名空间中的函数或常量未定义，则该非限定的函数名称或常量名称会被解析为全局函数名称或常量名称。详情参见 使用命名空间：后备全局函数名称/常量名称。 
2. 限定名称,或包含前缀的名称，例如 $a = new subnamespace\foo(); 或 subnamespace\foo::staticmethod();。如果当前的命名空间是 currentnamespace，则 foo 会被解析为 currentnamespace\subnamespace\foo。如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，foo 会被解析为subnamespace\foo。 
3. 完全限定名称，或包含了全局前缀操作符的名称，例如， $a = new \currentnamespace\foo(); 或 \currentnamespace\foo::staticmethod();。在这种情况下，foo 总是被解析为代码中的文字名(literal name)currentnamespace\foo

file1.php
```
<?php 
namespace Foo\Bar\subnamespace;
const Foo = 1;
function foo() {}
class foo {
	static function staticmethod() {}
}
```

```
<?php
namespace Foo\Bar;
include 'file1.php';

const Foo = 1;
function foo () {}
class foo {
    static function staticmethod() {};
}

// 非限定名称
foo(); // 解析为Foo\Bar\foo方法
foo::staticmethod(); // 解析为 Foo\Bar\foo的静态方法staticmethod
echo Foo; // 解析为Foo\Bar\Foo 常量

// 限定名称
subnamespace\foo(); // 解析函数为Foo\bar\subnamespace\foo方法
subnamespace\foo::staticmethod(); // 解析为 Foo\Bar\subnamespace\foo函数
echo subnamespace\Foo; // 解析为 Foo\Bar\subnamespace\Foo 常量

// 完全限定名称
\Foo\Bar\foo(); // 解析为 Foo\Bar\foo 函数
\Foo\Bar\foo::staticmethod(); //解析为Foo\Bar\foo 以及类的方法staticmethod
echo \Foo\Bar\Foo; // 解析为 Foo\Bar\Foo 常量
```

注意访问任何全局类、函数或常量，都可以使用完全限定名称，例如`\strlen()`或`\exception` 或`\INI_ALL`.

> 命名空间和动态语言特征

PHP命名空间的实现收到其语言自身的动态特征的影响。

```
<?php
class classname
{
    function __construct()
    {
        echo __METHOD__,"\n";
    }
}
function funcname()
{
    echo __FUNCTION__,"\n";
}
const constname = "global";

$a = 'classname';
$obj = new $a; // prints classname::__construct
$b = 'funcname';
$b(); // prints funcname
echo constant('constname'), "\n"; // prints global
```
必须使用完全限定名称。注意因为在动态的类名称、函数名称或常量名称中，限定名称和完全限定名称没有区别，因此其前导的反斜杠是不必要的。

> `namespace`关键字和`__NAMESPACE__`常量

PHP支持两种朝向的访问当前命名空间内部元素的方法， `__NAMESPACE__`魔术常量和`namespace`关键字。

常量`__NAMESPACE__`的值是包含当前命名空间名称的字符串。在全局的，不包括在任何命名空间中的代码，它包含一个空的字符串。 

```
<?php
namespace MyProject;
echo '"', __NAMESPACE__, '"'; // 输出 "MyProject"
```

`namespace`可用来显示访问当前命名空间或子命名空间中的元素，它等价于类中的`self`操作符。

```
<?php
namespace MyProject;
use blah\blah as mine; // see "Using namespaces: importing/aliasing"
blah\mine(); // calls function MyProject\blah\mine()
namespace\blah\mine(); // calls function MyProject\blah\mine()
namespace\func(); // calls function MyProject\func()
namespace\sub\func(); // calls function MyProject\sub\func()
namespace\cname::method(); // calls static method "method" of class MyProject\cname
$a = new namespace\sub\cname(); // instantiates object of class MyProject\sub\cname
$b = namespace\CONSTANT; // assigns value of constant MyProject\CONSTANT to $b
```

> 使用命名空间：别名\导入

允许通过别名引用或导入外部的完全限定名称，是命名空间的一个重要特征。

所有支持命名空间的PHP版本支持三种别名或导入方式：为类名称使用别名、为接口使用别名或未命名空间名称使用别名。PHP5.6开始允许导入函数或常量或者为它们设置别名。

在PHP中，别名是通过操作符`use`来实现的。

```
<?php 
namespace foo;
use My\Full\Classname as Another;
use My\Full\NSname;
use ArrayOjbect;// 导入一个全局类
use function My\Full\functionName; //导入一个函数
use function My\Full\functionName as func; // 为导入的函数取别名
use const My\Full\CONSTANT; // 导入一个常量
$obj = new namespace\Another; // 实例化foo\Another对象
$obj = new Another; //实例化My\Full\Classname 对象
NSname\subns\func(); // 调用函数My\Full\NSname\subns\func 
$a = new ArrayObject(array(1)); // 实例化ArrayObject对象
// 如果不使用'use \ArrayObject', 则实例化一个foo\ArrayObject 对象
func(); //调用函数 My\Full\functionName
echo CONSTANT; // My\Full\CONSTANT
```

> 全局空间

如果没有定义任何命名空间，所有的类与函数的定义都是在全局空间，与PHP引入命名空间概念前一样。在名称前加上前缀 \表示该名称是全局空间中的名称，即使该名称位于其它的命名空间中时也是如此。 

```
<?php
namespace A\B\C;
/* 这个函数是 A\B\C\fopen */
function fopen() { 
     /* ... */
     $f = \fopen(...); // 调用全局的fopen函数
     return $f;
}
```

> 使用命名空间：后备全局函数/常量

在一个命名空间中，当PHP遇到一个非限定的类、函数、常量名称时，它使用不同的优先策略来解析该名称。类名称总是解析到当前命名空间中的名称。因此在访问系统内部或不包含在命名空间中的类名称时，必须使用完全限定名称。

```
<?php
namespace A\B\C;
class Exception extends \Exception {}
$a = new Exception('hi'); // $a 是类 A\B\C\Exception 的一个对象
$b = new \Exception('hi'); // $b 是类 Exception 的一个对象
$c = new ArrayObject; // 致命错误, 找不到 A\B\C\ArrayObject 类
```

对于函数和常量来说，如果当前命名空间中不存在该函数或常量，PHP 会退而使用全局空间中的函数或常量。

>  名称解析规则

1、对完全限定名称的函数、类、常量的调用在编译时解析
2、所有的非限定名称和限定名称根据当前的导入规则在编译时进行转换。
3、在命名空间内部，所有的没有根据导入规则转换的限定名称俊辉再起前面加上当前的命名空间名称。
4、非限定类名根据当前的导入规则在编译时转换
5、在命名空间内部，对非限定名称的函数调用是在运行时解析。
6、在命名空间（例如A\B）内部对非限定名称或限定名称类（非完全限定名称）的调用是在运行时解析的。
