# PHP 基础（二）

##一、PHP函数

> 用户自定义函数

```
function  foo ($arg1, $arg2, $arg3, ...) {
  echo 'This is example function .';
  return $retval;
}
```
任何有效的PHP代码都可以出现在函数内部，甚至包括其他函数和类的定义。

**命名规则：**以字母或者下划线开头，后面跟字母、数字或者下划线。正则表达式为：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*`

**函数名不区分大小写**

PHP中所有函数和类都具有全局作用域，可以定义在一个函数之内而在之外调用，反之亦然。

PHP的函数支持可变数量的函数和默认参数。

> 函数的参数

通过参数列表可以传递信息到函数，即以逗号作为分隔符的表达式列表，参数从左到右求值的。

```
<?php
function takes_array() {
  echo "$input[0] + $input[1] = ". $input + $input[1];
}

```

**通过引用传递参数：**函数参数通过值传递（在函数内部改变参数的值，他并不会改变函数外部的值）。如果希望允许函数修改它的参数值，必须通过引用传递参数。

```
<?php
function add_some_extra (&$string) {
  $string .= 'and something extra';
}

$str = 'This is a string';
add_some_extra($str);
echo $str; // 输出：'This is a string, and something extra';
```

**默认参数：**
```
function makecoffee ($typ = 'cappuccino') {
  return 'Making a cup of '.$type.'\n';
}
```

*PHP可以使用数组`Array`和特殊类型`NULL`作为默认参数。默认值必须是常量表达式，不能使变量、类成员、函数调用等*

**可变数量的参数列表：**PHP在用户自定义函数中支持可变数量的参数列表。

```
<?php
function sum(...$numbers) {
  $acc = 0;
  foreach($numbers as $n) {
    $acc += $n;
  }
  return $acc;
}

echo sum(1, 2, 3, 4); // 输出结果：10
```
或者

```
<?php
function sum() {
  $acc = 0;
  foreach (func_get_args() as $n) {
    $acc += $n;
  }
  return $acc;
]
echo sum(1, 2, 3, 4); //输出：10
```

> 返回值

值通过使用返回可选的返回语句返回，可以返回数组、对象的任意类型，返回语句会终止函数的运行，如果忽略了`return`，返回值则为`NULL`.

```
<?php
function small_numbers() {
  return array(0, 1, 2);
}
list($zero, $one, $two) = small_numbers();
```

**返回类型声明:** PHP7 新添加的功能。

```
<?php
function sum($a, $b):float {
  return $a + $b;
}
var_dump(sum(1, 2)); // float(3)
```

> 可变函数

所谓的可变函数，通过变量的值来调用函数，因为变量的值是可变的，所以可以通过改变一个变量的值来实现调用不同的函数。经常会用在回调函数、函数列表、根据动态参数来调用不同的函数。可变函数的调用方法为变脸名加括号。

```
<?php
function foo() {
  echo 'In foo()';
}

function echoit ($string) {
  echo 'In echoit()' . $string
}

$func = 'foo'
$func();
$func = 'echoit';
$func('test');
// 输出结果为：
In foo();
In echoit() test
```
*可变函数不能用于例如`echo, print, unset(), isset(), empty(), include, require`以及类似的语言结构*

可变函数的语法可调用一个对象的方法。

```
<?php
class Foo
{
    function Variable()
    {
        $name = 'Bar';
        $this->$name(); // This calls the Bar() method
    }

    function Bar()
    {
        echo "This is Bar";
    }
}

$foo = new Foo();
$funcname = "Variable";
$foo->$funcname();   // This calls $foo->Variable()
```

当调用静态方法时， 函数调用要比静态属性优先。

```
<?php
class Foo
{
    static $variable = 'static property';
    static function Variable()
    {
        echo 'Method Variable called';
    }
}

echo Foo::$variable; // This prints 'static property'. It does need a $variable in this scope.
$variable = "Variable";
Foo::$variable();  // This calls $foo->Variable() reading $variable in this scope.
```

> 内部（内置）函数

PHP有很多标准的函数和结构。

> 匿名函数

匿名函数（Anonymous functions）也叫闭包函数（closures），允许临时创建一个没有指定名称的函数，经常用作回调函数（callback）参数的值。

```
<?php
echo preg_replace_callback('~-([a-z])~', function ($match) {
    return strtoupper($match[1]);
}, 'hello-world');
// 输出 helloWorld
```

闭包函数也可以作为变量的值来使用

```
<?php
$greet = function($name)
{
    printf("Hello %s\r\n", $name);
};

$greet('World');
$greet('PHP');
```

闭包函数也可以继承父类函数中的变量，任何此类变量都应该用use语言结构传递进去。

```
<?php
$message = 'hello';

$example = function () {
  var_dump($GLOBAL['message']);
};
echo $example(); // 输出：string(5) "hello"
$example = function () use ($message) {
  var_dump($message);
};

echo $example(); // 输出：string(5) "hello"

$message = 'world';
echo $exapmle() ; //输出：string(5) "hello"

$message = 'hello';
$example = function () use (&$message) {
  var_dump($message);
};
echo $example(); // hello

$message = 'world';
echo $example(); // world 

$example = function ($arg) use ($message) {
  var_dump($arg . ' ' . $message);
};

echo $example('hello'); // hello world
```

##二、类与对象

> 简介

PHP5具有完整的对象模型。PHP5新特性包括访问控制、抽象类、final类与方法、魔术方法、接口、对象复制、类型约束。

> 基本概念

**class：**每个类的定义都以关键字`class`开头，后面跟着类名，一对花括号，花括号里面包含有类的属性与方法的定义。

一个合法类名以字母或下划线开头，后面可以接任意字母、数字、下划线。正则表达式：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]`。

一个类可以包含有属于自己的变量（属性）、常量以及函数(方法)

```
<?php
class SimpleClass {
  public $var = 'a default value';
  public function displayVar () {
    echo $this->var;
  }
}
```

**new：**创建一个类的实例，必须使用关键字`new`。`$instance = new SimpleClass()`。

当把一个对象已经创建的额实例赋给一个新变量时，新变量会访问同一个实例，就和用该对象赋值一样，此行为和给函数传递入实例时一样。可以用克隆给一个已创建的对象建立一个新实例。

```
<?php
$instance = new SimpleClass();
$assigned   =  $instance;
$reference  =& $instance;
$instance->var = '$assigned will have this value';
$instance = null; // $instance and $reference become null
var_dump($instance);
var_dump($reference);
var_dump($assigned);
```

**extends:**一个类可以再声明中用`extends`关键字集成另一个类的方法和属性。
**`PHP`不支持多重集成，一个类只能继承一个基类。**

被继承的方法和属性可以通过同样的名字重新声明被覆盖，如果父类定义方法使用了`final`，则该方法不可被覆盖，可以通过`parent`来访问被覆盖的方法或属性。

当覆盖方法时，参数必须保持一致否则PHP将发出E_STRICT级别的错误信息，但构造函数例外，构造函数可在被覆盖时使用不同的参数。
```
<?php
class ExtendClass extends SimpleClass {
  function displayVar () {
    echo 'Extending class\n';
    parent::displayVar();
  }
}

$extended = new ExtendClass();
$extended -> displayVar();

//输出：
Extending class
a default value
```

**::class：**从PHP5.5起， 关键词`class`也可用于类名的解析，使用`ClassName:class`可以获取一个字符串，包含了类`ClassName`的完全限定名称。

```
<?php
namespace NS {
  class ClassName {
  }

  echo ClassName::class;
}

//输出：NS\ClassName
```

> 属性

类的变量成员叫做属性或者字段或者特征。属性声明是由关键字 `public`、`protected`、或者`private`开头（如果不写，默认为`public`），然后跟一个普通的变量声明来组成的。属性中的变量可以初始化，但是初始化的值必须是常数。

在类的成员方法里面，可以用  -> (对象运算符) : $this->property (其中property是该属性名) 访问非静态属性。 静态属性则是用 :: (双冒号) : self::$property 来访问。

```
<?php
class SimpleClass
{
   // 错误的属性声明
   public $var1 = 'hello ' . 'world';
   public $var2 = <<<EOD
hello world
EOD;
   public $var3 = 1+2;
   public $var4 = self::myStaticMethod();
   public $var5 = $myVar;

   // 正确的属性声明
   public $var6 = myConstant;
   public $var7 = array(true, false);

   //在 PHP 5.3.0 及之后，下面的声明也正确
   public $var8 = <<<'EOD'
hello world
EOD;
}
```

> 类常量

可以把在类中始终保持不变的值定义为常量，在定义和使用常量的时候不需要使用`$`符号。

常量必须是一个定值，不能是变量、类属性、数学运算的结果、函数调用。

自 PHP 5.3.0 起，可以用一个变量来动态调用类。但该变量的值不能为关键字（如 self，parent 或 static）。

```
<?php
class MyClass
{
    const constant = 'constant value';

    function showConstant() {
        echo  self::constant . "\n";
    }
}

echo MyClass::constant . "\n";

$classname = "MyClass";
echo $classname::constant . "\n"; // 自 5.3.0 起

$class = new MyClass();
$class->showConstant();

echo $class::constant."\n"; // 自 PHP 5.3.0 起
?> 
```


> 自动加载类

从PHP5 开始，就可以定义一个__autoload()函数，它会视图使用尚未被被定义的类时自动调用，通过调用此函数，脚本引擎在PHP出错失败前有了最后一个机会加载所需的类。

**`spl_autoload_register()`提供了一种更加灵活的方式来实现类的自动加载，因此不再建议使用`__autoload()`函数，以后也能会弃用。**

```
<?php
function __autoload($name) {
    var_dump($name);
}

class Foo implements ITest {
}
```
异常捕捉
```
<?php
function __autoload($name) {
    echo "Want to load $name.\n";
    throw new Exception("Unable to load $name.");
}

try {
    $obj = new NonLoadableClass();
} catch (Exception $e) {
    echo $e->getMessage(), "\n";
}
```

> 构造函数和析构函数

**构造函数：**

`void __construct ([mixed $args[, $...]])`

PHP5 允许开发者在一个类中定义一个方法作为构造函数，具有构造函数的类会在每次创建对象时先调用此方法，所以非常适合在使用对象之前做一些初始化工作。

如果子类中定义了构造函数则不会隐式调用其父类的构造函数，要执行父类的构造函数，需要在子类的构造函数中调用`parent::__construct()`。如果子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（如果没有被定义为private）。

```
<?php
class BaseClass {
	function __construct() {
		print 'In BaseClass constructor<br/>';
	}
}

class SubClass extends BaseClass {
	function __construct() {
		parent::__construct();
		print 'In SubClass constructor<br/>';
	}
}

class OtherClass extends BaseClass {
	//
}

$obj = new BaseClass();
$obj = new SubClass();
$obj = new OtherClass();

// 输出结果：
In BaseClass constructor
In BaseClass constructor
In SubClass constructor
In BaseClass constructor
```

如果PHP5在类中找不到__construct()函数并且也没有从父类继承一个的话，它就会尝试寻找旧式的构造函数，也就是和类同名的函数。当`__construct()`被与父类`__construct()`具有不同参数的方法覆盖时，PHP不会产生一个`E_STRICT`错误信息。


**析构函数：**`void __destruct(void)`
PHP5引入了析构函数，析构函数会在某个对象的所有引用都被删除或者当对象被显式销毁时执行。

```
<?php
class MyDestructableClass {
   function __construct() {
       print "In constructor<br/>";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "Destroying " . $this->name . "<br/>";
   }
}

$obj = new MyDestructableClass();
```

和构造函数一样，不会隐式调用父类的析构函数，要执行父类的析构函数，必须在子类调用`parent::__destruct()`。如果子类没有定义则会自动调用父类的析构函数。

析构函数在脚本关闭时调用。试图在析构函数（在脚本终止时被调用）中抛出一个异常会导致致命错误。 

> 访问控制 

对属性或方法的访问控制，是通过在前面添加关键字`public、protected、private`来实现的。`public`：任何地方都可以被访问。`protected`：可以被自身以及其子类和父类访问。`private`：只能被其定义所在的类访问。

**属性的访问控制：**类属性必须定义为公有、受保护、私有之一。如果用var定义，则被视为公有。
```
<?php
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private
```

**方法的访问控制：**类中的方法可以被定义为公有、私有或受保护，如果没有设置关键字，则该方法默认为公有。

```
<?php
class MyClass
{
    // 声明一个公有的构造函数
    public function __construct() { }

    // 声明一个公有的方法
    public function MyPublic() { }

    // 声明一个受保护的方法
    protected function MyProtected() { }

    // 声明一个私有的方法
    private function MyPrivate() { }

    // 此方法为公有
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}

$myclass = new MyClass;
$myclass->MyPublic(); // 这行能被正常执行
$myclass->MyProtected(); // 这行会产生一个致命错误
$myclass->MyPrivate(); // 这行会产生一个致命错误
$myclass->Foo(); // 公有，受保护，私有都可以执行
```

**其它对象的访问控制：**同一个类的对象即使不是同一个实例也可以互相访问对方的私有与受保护成员。
```
<?php
class Test
{
    private $foo;

    public function __construct($foo)
    {
        $this->foo = $foo;
    }

    private function bar()
    {
        echo 'Accessed the private method.';
    }

    public function baz(Test $other)
    {
        // We can change the private property:
        $other->foo = 'hello';
        var_dump($other->foo);

        // We can also call the private method:
        $other->bar();
    }
}

$test = new Test('test');
$test->baz(new Test('other'));

// 输出：
string(5) "hello" 
Accessed the private method.
```

> 对象继承

PHP的对象模型也使用了继承。继承将会影响到类与类，对象与对象之间的关系。

```
<?php
class foo
{
    public function printItem($string) 
    {
        echo 'Foo: ' . $string . PHP_EOL;
    }
    
    public function printPHP()
    {
        echo 'PHP is great.' . PHP_EOL;
    }
}

class bar extends foo
{
    public function printItem($string)
    {
        echo 'Bar: ' . $string . PHP_EOL;
    }
}

$foo = new foo();
$bar = new bar();
$foo->printItem('baz'); // Output: 'Foo: baz'
$foo->printPHP();       // Output: 'PHP is great' 
$bar->printItem('baz'); // Output: 'Bar: baz'
$bar->printPHP();       // Output: 'PHP is great'
```

>范围解析操作符（::）

范围解析操作符(::) 可以用于访问静态成员、类常量、覆盖类中的属性和方法。

从PHP5.3.0起，可以通过变量来引用类，该变量的值不能使关键字(如self、parent、static)

```
<?php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

$classname = 'MyClass';
echo $classname::CONST_VALUE; // 自 PHP 5.3.0 起

echo MyClass::CONST_VALUE;

// 输出结果：
A constant value
A constant value
```

self、parent、static这三个特殊的关键字是用于在类定义的内部对其属性或方法进行访问的。

```
<?php 
class MyClass {
    const CONST_VALUE = 'A constant value';
}

class OtherClass extends MyClass
{
    public static $my_static = 'static var';

    public static function doubleColon() {
        echo parent::CONST_VALUE . "<br/>";
        echo self::$my_static . "<br/>";
    }
}

$classname = 'OtherClass';
echo $classname::doubleColon(); // 自 PHP 5.3.0 起

OtherClass::doubleColon();

// 输出结果：
A constant value
static var
A constant value
static var
```

当一个子类覆盖其父类中的方法时，PHP不会调用父类中已被覆盖的方法。是否调用父类的方法取决于子类。

```
<?php
class MyClass
{
    protected function myFunc() {
        echo "MyClass::myFunc()<br/>";
    }
}

class OtherClass extends MyClass
{
    // 覆盖了父类的定义
    public function myFunc()
    {
        // 但还是可以调用父类中被覆盖的方法
        parent::myFunc();
        echo "OtherClass::myFunc()<br/>";
    }
}

$class = new OtherClass();
$class->myFunc();

// 输出结果
MyClass::myFunc()
OtherClass::myFunc()
```

> static （静态）关键字

声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）。

由于静态方法不需要通过对象即可调用，所以伪变量`$this`在静态方法中不可用。

静态属性不可以由对象通过->操作符来访问。

用静态方式调用一个非静态方法会导致一个`E_STRICT`级别的错误。

```
<?php
class Foo
{
    public static $my_static = 'foo';

    public function staticValue() {
        return self::$my_static;
    }
}

class Bar extends Foo
{
    public function fooStatic() {
        return parent::$my_static;
    }
}
print Foo::$my_static . "\n";

// 输出结果： foo
```

> Abstract 抽象类

PHP5支持抽象类和抽象方法，定义为抽象的类不能被实例化。*任何一个类，如果里面至少有一个方法是被声明为抽象的，那么这个类必须被声明为抽象的。*被定义为抽象的方法只是声明了其调用方法（参数），不能定义其具体的功能实现。

抽象类继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的访问控制必须和父类中一样（或者更为宽松。

```
<?php
abstract class AbstractClass {
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);
    
    public function printOut () {
        print $this->getValue() . '<br/>';
    }
}

class ConcreteClass1 extends AbstractClass {
    protected function getValue () {
        return "ConcreteClass1";
    }
    
    public function prefixValue ($prefix) {
        return $prefix . 'ConcreteClass1<br/>';
    }
}

class ConcreteClass2 extends AbstractClass {
    public function getValue () {
        return 'ConcreteClass2';
    }
    
    public function prefixValue ($prefix) {
        return $prefix . 'ConcreteClass2';
    }
}

$class1 = new ConcreteClass1;
$class1->printOut();
echo $class1->prefixValue('FOO_') ."<br/>";

$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') ."<br/>";

// 输出：
ConcreteClass1
FOO_ConcreteClass1
ConcreteClass2
FOO_ConcreteClass2
```


>####PHP函数

> 用户自定义函数

```
function  foo ($arg1, $arg2, $arg3, ...) {
  echo 'This is example function .';
  return $retval;
}
```
任何有效的PHP代码都可以出现在函数内部，甚至包括其他函数和类的定义。

**命名规则：**以字母或者下划线开头，后面跟字母、数字或者下划线。正则表达式为：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*`

**函数名不区分大小写**

PHP中所有函数和类都具有全局作用域，可以定义在一个函数之内而在之外调用，反之亦然。

PHP的函数支持可变数量的函数和默认参数。

> 函数的参数

通过参数列表可以传递信息到函数，即以逗号作为分隔符的表达式列表，参数从左到右求值的。

```
<?php
function takes_array() {
  echo "$input[0] + $input[1] = ". $input + $input[1];
}

```

**通过引用传递参数：**函数参数通过值传递（在函数内部改变参数的值，他并不会改变函数外部的值）。如果希望允许函数修改它的参数值，必须通过引用传递参数。

```
<?php
function add_some_extra (&$string) {
  $string .= 'and something extra';
}

$str = 'This is a string';
add_some_extra($str);
echo $str; // 输出：'This is a string, and something extra';
```

**默认参数：**
```
function makecoffee ($typ = 'cappuccino') {
  return 'Making a cup of '.$type.'\n';
}
```

*PHP可以使用数组`Array`和特殊类型`NULL`作为默认参数。默认值必须是常量表达式，不能使变量、类成员、函数调用等*

**可变数量的参数列表：**PHP在用户自定义函数中支持可变数量的参数列表。

```
<?php
function sum(...$numbers) {
  $acc = 0;
  foreach($numbers as $n) {
    $acc += $n;
  }
  return $acc;
}

echo sum(1, 2, 3, 4); // 输出结果：10
```
或者

```
<?php
function sum() {
  $acc = 0;
  foreach (func_get_args() as $n) {
    $acc += $n;
  }
  return $acc;
]
echo sum(1, 2, 3, 4); //输出：10
```

> 返回值

值通过使用返回可选的返回语句返回，可以返回数组、对象的任意类型，返回语句会终止函数的运行，如果忽略了`return`，返回值则为`NULL`.

```
<?php
function small_numbers() {
  return array(0, 1, 2);
}
list($zero, $one, $two) = small_numbers();
```

**返回类型声明:** PHP7 新添加的功能。

```
<?php
function sum($a, $b):float {
  return $a + $b;
}
var_dump(sum(1, 2)); // float(3)
```

> 可变函数

所谓的可变函数，通过变量的值来调用函数，因为变量的值是可变的，所以可以通过改变一个变量的值来实现调用不同的函数。经常会用在回调函数、函数列表、根据动态参数来调用不同的函数。可变函数的调用方法为变脸名加括号。

```
<?php
function foo() {
  echo 'In foo()';
}

function echoit ($string) {
  echo 'In echoit()' . $string
}

$func = 'foo'
$func();
$func = 'echoit';
$func('test');
// 输出结果为：
In foo();
In echoit() test
```
*可变函数不能用于例如`echo, print, unset(), isset(), empty(), include, require`以及类似的语言结构*

可变函数的语法可调用一个对象的方法。

```
<?php
class Foo
{
    function Variable()
    {
        $name = 'Bar';
        $this->$name(); // This calls the Bar() method
    }

    function Bar()
    {
        echo "This is Bar";
    }
}

$foo = new Foo();
$funcname = "Variable";
$foo->$funcname();   // This calls $foo->Variable()
```

当调用静态方法时， 函数调用要比静态属性优先。

```
<?php
class Foo
{
    static $variable = 'static property';
    static function Variable()
    {
        echo 'Method Variable called';
    }
}

echo Foo::$variable; // This prints 'static property'. It does need a $variable in this scope.
$variable = "Variable";
Foo::$variable();  // This calls $foo->Variable() reading $variable in this scope.
```

> 内部（内置）函数

PHP有很多标准的函数和结构。

> 匿名函数

匿名函数（Anonymous functions）也叫闭包函数（closures），允许临时创建一个没有指定名称的函数，经常用作回调函数（callback）参数的值。

```
<?php
echo preg_replace_callback('~-([a-z])~', function ($match) {
    return strtoupper($match[1]);
}, 'hello-world');
// 输出 helloWorld
```

闭包函数也可以作为变量的值来使用

```
<?php
$greet = function($name)
{
    printf("Hello %s\r\n", $name);
};

$greet('World');
$greet('PHP');
```

闭包函数也可以继承父类函数中的变量，任何此类变量都应该用use语言结构传递进去。

```
<?php
$message = 'hello';

$example = function () {
  var_dump($GLOBAL['message']);
};
echo $example(); // 输出：string(5) "hello"
$example = function () use ($message) {
  var_dump($message);
};

echo $example(); // 输出：string(5) "hello"

$message = 'world';
echo $exapmle() ; //输出：string(5) "hello"

$message = 'hello';
$example = function () use (&$message) {
  var_dump($message);
};
echo $example(); // hello

$message = 'world';
echo $example(); // world 

$example = function ($arg) use ($message) {
  var_dump($arg . ' ' . $message);
};

echo $example('hello'); // hello world
```

####类与对象

> 简介

PHP5具有完整的对象模型。PHP5新特性包括访问控制、抽象类、final类与方法、魔术方法、接口、对象复制、类型约束。

> 基本概念

**class：**每个类的定义都以关键字`class`开头，后面跟着类名，一对花括号，花括号里面包含有类的属性与方法的定义。

一个合法类名以字母或下划线开头，后面可以接任意字母、数字、下划线。正则表达式：`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]`。

一个类可以包含有属于自己的变量（属性）、常量以及函数(方法)

```
<?php
class SimpleClass {
  public $var = 'a default value';
  public function displayVar () {
    echo $this->var;
  }
}
```

**new：**创建一个类的实例，必须使用关键字`new`。`$instance = new SimpleClass()`。

当把一个对象已经创建的额实例赋给一个新变量时，新变量会访问同一个实例，就和用该对象赋值一样，此行为和给函数传递入实例时一样。可以用克隆给一个已创建的对象建立一个新实例。

```
<?php
$instance = new SimpleClass();
$assigned   =  $instance;
$reference  =& $instance;
$instance->var = '$assigned will have this value';
$instance = null; // $instance and $reference become null
var_dump($instance);
var_dump($reference);
var_dump($assigned);
```

**extends:**一个类可以再声明中用`extends`关键字集成另一个类的方法和属性。
**`PHP`不支持多重集成，一个类只能继承一个基类。**

被继承的方法和属性可以通过同样的名字重新声明被覆盖，如果父类定义方法使用了`final`，则该方法不可被覆盖，可以通过`parent`来访问被覆盖的方法或属性。

当覆盖方法时，参数必须保持一致否则PHP将发出E_STRICT级别的错误信息，但构造函数例外，构造函数可在被覆盖时使用不同的参数。
```
<?php
class ExtendClass extends SimpleClass {
  function displayVar () {
    echo 'Extending class\n';
    parent::displayVar();
  }
}

$extended = new ExtendClass();
$extended -> displayVar();

//输出：
Extending class
a default value
```

**::class：**从PHP5.5起， 关键词`class`也可用于类名的解析，使用`ClassName:class`可以获取一个字符串，包含了类`ClassName`的完全限定名称。

```
<?php
namespace NS {
  class ClassName {
  }

  echo ClassName::class;
}

//输出：NS\ClassName
```

> 属性

类的变量成员叫做属性或者字段或者特征。属性声明是由关键字 `public`、`protected`、或者`private`开头（如果不写，默认为`public`），然后跟一个普通的变量声明来组成的。属性中的变量可以初始化，但是初始化的值必须是常数。

在类的成员方法里面，可以用  -> (对象运算符) : $this->property (其中property是该属性名) 访问非静态属性。 静态属性则是用 :: (双冒号) : self::$property 来访问。

```
<?php
class SimpleClass
{
   // 错误的属性声明
   public $var1 = 'hello ' . 'world';
   public $var2 = <<<EOD
hello world
EOD;
   public $var3 = 1+2;
   public $var4 = self::myStaticMethod();
   public $var5 = $myVar;

   // 正确的属性声明
   public $var6 = myConstant;
   public $var7 = array(true, false);

   //在 PHP 5.3.0 及之后，下面的声明也正确
   public $var8 = <<<'EOD'
hello world
EOD;
}
```

> 类常量

可以把在类中始终保持不变的值定义为常量，在定义和使用常量的时候不需要使用`$`符号。

常量必须是一个定值，不能是变量、类属性、数学运算的结果、函数调用。

自 PHP 5.3.0 起，可以用一个变量来动态调用类。但该变量的值不能为关键字（如 self，parent 或 static）。

```
<?php
class MyClass
{
    const constant = 'constant value';

    function showConstant() {
        echo  self::constant . "\n";
    }
}

echo MyClass::constant . "\n";

$classname = "MyClass";
echo $classname::constant . "\n"; // 自 5.3.0 起

$class = new MyClass();
$class->showConstant();

echo $class::constant."\n"; // 自 PHP 5.3.0 起
?> 
```


> 自动加载类

从PHP5 开始，就可以定义一个__autoload()函数，它会视图使用尚未被被定义的类时自动调用，通过调用此函数，脚本引擎在PHP出错失败前有了最后一个机会加载所需的类。

**`spl_autoload_register()`提供了一种更加灵活的方式来实现类的自动加载，因此不再建议使用`__autoload()`函数，以后也能会弃用。**

```
<?php
function __autoload($name) {
    var_dump($name);
}

class Foo implements ITest {
}
```
异常捕捉
```
<?php
function __autoload($name) {
    echo "Want to load $name.\n";
    throw new Exception("Unable to load $name.");
}

try {
    $obj = new NonLoadableClass();
} catch (Exception $e) {
    echo $e->getMessage(), "\n";
}
```

> 构造函数和析构函数

**构造函数：**

`void __construct ([mixed $args[, $...]])`

PHP5 允许开发者在一个类中定义一个方法作为构造函数，具有构造函数的类会在每次创建对象时先调用此方法，所以非常适合在使用对象之前做一些初始化工作。

如果子类中定义了构造函数则不会隐式调用其父类的构造函数，要执行父类的构造函数，需要在子类的构造函数中调用`parent::__construct()`。如果子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（如果没有被定义为private）。

```
<?php
class BaseClass {
	function __construct() {
		print 'In BaseClass constructor<br/>';
	}
}

class SubClass extends BaseClass {
	function __construct() {
		parent::__construct();
		print 'In SubClass constructor<br/>';
	}
}

class OtherClass extends BaseClass {
	//
}

$obj = new BaseClass();
$obj = new SubClass();
$obj = new OtherClass();

// 输出结果：
In BaseClass constructor
In BaseClass constructor
In SubClass constructor
In BaseClass constructor
```

如果PHP5在类中找不到__construct()函数并且也没有从父类继承一个的话，它就会尝试寻找旧式的构造函数，也就是和类同名的函数。当`__construct()`被与父类`__construct()`具有不同参数的方法覆盖时，PHP不会产生一个`E_STRICT`错误信息。


**析构函数：**`void __destruct(void)`
PHP5引入了析构函数，析构函数会在某个对象的所有引用都被删除或者当对象被显式销毁时执行。

```
<?php
class MyDestructableClass {
   function __construct() {
       print "In constructor<br/>";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "Destroying " . $this->name . "<br/>";
   }
}

$obj = new MyDestructableClass();
```

和构造函数一样，不会隐式调用父类的析构函数，要执行父类的析构函数，必须在子类调用`parent::__destruct()`。如果子类没有定义则会自动调用父类的析构函数。

析构函数在脚本关闭时调用。试图在析构函数（在脚本终止时被调用）中抛出一个异常会导致致命错误。 

> 访问控制 

对属性或方法的访问控制，是通过在前面添加关键字`public、protected、private`来实现的。`public`：任何地方都可以被访问。`protected`：可以被自身以及其子类和父类访问。`private`：只能被其定义所在的类访问。

**属性的访问控制：**类属性必须定义为公有、受保护、私有之一。如果用var定义，则被视为公有。
```
<?php
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private
```

**方法的访问控制：**类中的方法可以被定义为公有、私有或受保护，如果没有设置关键字，则该方法默认为公有。

```
<?php
class MyClass
{
    // 声明一个公有的构造函数
    public function __construct() { }

    // 声明一个公有的方法
    public function MyPublic() { }

    // 声明一个受保护的方法
    protected function MyProtected() { }

    // 声明一个私有的方法
    private function MyPrivate() { }

    // 此方法为公有
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}

$myclass = new MyClass;
$myclass->MyPublic(); // 这行能被正常执行
$myclass->MyProtected(); // 这行会产生一个致命错误
$myclass->MyPrivate(); // 这行会产生一个致命错误
$myclass->Foo(); // 公有，受保护，私有都可以执行
```

**其它对象的访问控制：**同一个类的对象即使不是同一个实例也可以互相访问对方的私有与受保护成员。
```
<?php
class Test
{
    private $foo;

    public function __construct($foo)
    {
        $this->foo = $foo;
    }

    private function bar()
    {
        echo 'Accessed the private method.';
    }

    public function baz(Test $other)
    {
        // We can change the private property:
        $other->foo = 'hello';
        var_dump($other->foo);

        // We can also call the private method:
        $other->bar();
    }
}

$test = new Test('test');
$test->baz(new Test('other'));

// 输出：
string(5) "hello" 
Accessed the private method.
```

> 对象继承

PHP的对象模型也使用了继承。继承将会影响到类与类，对象与对象之间的关系。

```
<?php
class foo
{
    public function printItem($string) 
    {
        echo 'Foo: ' . $string . PHP_EOL;
    }
    
    public function printPHP()
    {
        echo 'PHP is great.' . PHP_EOL;
    }
}

class bar extends foo
{
    public function printItem($string)
    {
        echo 'Bar: ' . $string . PHP_EOL;
    }
}

$foo = new foo();
$bar = new bar();
$foo->printItem('baz'); // Output: 'Foo: baz'
$foo->printPHP();       // Output: 'PHP is great' 
$bar->printItem('baz'); // Output: 'Bar: baz'
$bar->printPHP();       // Output: 'PHP is great'
```

>范围解析操作符（::）

范围解析操作符(::) 可以用于访问静态成员、类常量、覆盖类中的属性和方法。

从PHP5.3.0起，可以通过变量来引用类，该变量的值不能使关键字(如self、parent、static)

```
<?php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

$classname = 'MyClass';
echo $classname::CONST_VALUE; // 自 PHP 5.3.0 起

echo MyClass::CONST_VALUE;

// 输出结果：
A constant value
A constant value
```

self、parent、static这三个特殊的关键字是用于在类定义的内部对其属性或方法进行访问的。

```
<?php 
class MyClass {
    const CONST_VALUE = 'A constant value';
}

class OtherClass extends MyClass
{
    public static $my_static = 'static var';

    public static function doubleColon() {
        echo parent::CONST_VALUE . "<br/>";
        echo self::$my_static . "<br/>";
    }
}

$classname = 'OtherClass';
echo $classname::doubleColon(); // 自 PHP 5.3.0 起

OtherClass::doubleColon();

// 输出结果：
A constant value
static var
A constant value
static var
```

当一个子类覆盖其父类中的方法时，PHP不会调用父类中已被覆盖的方法。是否调用父类的方法取决于子类。

```
<?php
class MyClass
{
    protected function myFunc() {
        echo "MyClass::myFunc()<br/>";
    }
}

class OtherClass extends MyClass
{
    // 覆盖了父类的定义
    public function myFunc()
    {
        // 但还是可以调用父类中被覆盖的方法
        parent::myFunc();
        echo "OtherClass::myFunc()<br/>";
    }
}

$class = new OtherClass();
$class->myFunc();

// 输出结果
MyClass::myFunc()
OtherClass::myFunc()
```

> static （静态）关键字

声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）。

由于静态方法不需要通过对象即可调用，所以伪变量`$this`在静态方法中不可用。

静态属性不可以由对象通过->操作符来访问。

用静态方式调用一个非静态方法会导致一个`E_STRICT`级别的错误。

```
<?php
class Foo
{
    public static $my_static = 'foo';

    public function staticValue() {
        return self::$my_static;
    }
}

class Bar extends Foo
{
    public function fooStatic() {
        return parent::$my_static;
    }
}
print Foo::$my_static . "\n";

// 输出结果： foo
```

> Abstract 抽象类

PHP5支持抽象类和抽象方法，定义为抽象的类不能被实例化。*任何一个类，如果里面至少有一个方法是被声明为抽象的，那么这个类必须被声明为抽象的。*被定义为抽象的方法只是声明了其调用方法（参数），不能定义其具体的功能实现。

抽象类继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的访问控制必须和父类中一样（或者更为宽松。

```
<?php
abstract class AbstractClass {
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);
    
    public function printOut () {
        print $this->getValue() . '<br/>';
    }
}

class ConcreteClass1 extends AbstractClass {
    protected function getValue () {
        return "ConcreteClass1";
    }
    
    public function prefixValue ($prefix) {
        return $prefix . 'ConcreteClass1<br/>';
    }
}

class ConcreteClass2 extends AbstractClass {
    public function getValue () {
        return 'ConcreteClass2';
    }
    
    public function prefixValue ($prefix) {
        return $prefix . 'ConcreteClass2';
    }
}

$class1 = new ConcreteClass1;
$class1->printOut();
echo $class1->prefixValue('FOO_') ."<br/>";

$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') ."<br/>";

// 输出：
ConcreteClass1
FOO_ConcreteClass1
ConcreteClass2
FOO_ConcreteClass2
```


>  Interface 对象接口

使用接口可以指定某个类必须实现那些方法，但不需要定义这些方法的具体内容。

接口通过`interface`关键字来定义，就像定义一个标准的类，但其中定义所有的方法都是空的。

接口中定义的所有方法都必须是公有的。

**实现(`implements`)：** 类中必须实现接口中定义的所有方法，否则会报一个致命的错误。类可以实现多个接口，用逗号来分隔多个接口的名称。

*实现多个接口时，接口中的方法不能重名。 接口可以继承，通过使用extends操作符。类要实现接口，必须使用和接口中所定义的方法完全一致的方式，否则会导致致命错误。*

**常量：**接口中也可以定义常量，接口常量和类常量的使用完全相同，但是不能被子类或者子接口所覆盖。

```
<?php
interface iTemplate {
    public function setVariable($name, $var);
    public function getHtml($template);
}

class Template implements iTemplate {
    private $var = array();
    
    public function setVariable($name, $var) {
        $this->vars[$name] = $var;
    }
    
    public function getHtml ($template) {
        foreach ($this->vars as $name => $value) {
            $template = str_replace('{'. $name . '}', $value, $template);
        }
        return $template;
    }
}
```

一个接口可以继承多个接口
```
<?php
interface a
{
    public function foo();
}

interface b
{
    public function bar();
}

interface c extends a, b
{
    public function baz();
}

class d implements c
{
    public function foo() {}

    public function bar() { }

    public function baz() { }
}
```

> `Trait`

从PHP5.4.0起， PHP实现了一种代码复用的方法，称为`trait`。

`Trait `是为类似PHP的单继承语言而准备的一种代码复用机制。`Trait`为了减少单继承语言的限制，使开发人员能够自由地不同层次结构内独立的列中复用method。`Trait`和`class`组合的语义定义了一种减少复杂性的方式，避免传统多继承和Mixin类相关典型问题。

`Trait`和`Class`相似，但仅仅只在用细粒度和一致的方式来组合功能，无法通过`trait`自身来实例化。

```
<?php 
class Base {
    public function sayHello () {
        echo 'hello';
    }
}

trait SayWorld {
    public function sayHello () {
        parent::sayHello();
        echo 'World!';
    }
}

class MyHelloWorld extends Base {
    use SayWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
// 输出：hello world!
```

**多个`trait`:**通过逗号分隔，用`use`声明列出多个`trait`，可以都插入到一个类中。

**冲突的解决：**如果两个`trait`都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。

为了解决多个trait在同一个类中的命名冲突，需要使用insteadof操作符来明确指定使用冲突方法中的哪一个

```
<?php 
trait A {
    public function smallTalk() {
        echo 'a';
    }
    public function bigTalk() {
        echo 'A';
    }
}

trait B {
    public function smallTalk() {
        echo 'b';
    }
    public function bigTalk() {
        echo 'B'
    }
}

class Talker {
    use A, B {
        B::smallTalk insteadOf A;
        A::biggTalk insteadOf B;
    }
}

class Aliased_Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        B::bigTalk as talk;
    }
}
```

**修改方法的访问控制：** 使用`as`语法可以用来调整方法的访问控制。

```
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

// 修改 sayHello 的访问控制
class MyClass1 {
    use HelloWorld { sayHello as protected; }
}

// 给方法一个改变了访问控制的别名
// 原版 sayHello 的访问控制则没有发生变化
class MyClass2 {
    use HelloWorld { sayHello as private myPrivateHello; }
}
```

**从`trait`来组成`trait`:**在trait定义时通过使用一个或多个trait，能够组合其他trait中的部分或全部成员。

```
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}
trait World {
    public function sayWorld() {
        echo 'World!';
    }
}
trait HelloWorld {
    use Hello, World;
}
class MyHelloWorld {
    use HelloWorld;
}
$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();

// 输出：Hello World!
```

**Trait的抽象成员：**
为了对使用的类施加强制要求，trait支持抽象方法的使用。

```
<?php
trait Hello {
    public function sayHelloWorld() {
        echo 'Hello'.$this->getWorld();
    }
    abstract public function getWorld();
}

class MyHelloWorld {
    private $world;
    use Hello;
    public function getWorld() {
        return $this->world;
    }
    public function setWorld($val) {
        $this->world = $val;
    }
}
```

**Trait的静态成员：**

```
<?php 
trait Counter {
    public function inc() {
        static $c = 0;
        $c = $c + 1;
        echo "$c\n";
    }
    public static function doSomething() {
        echo 'Doing something';
    }
}

class C1 {
    use Counter;
}

class C2 {
    use Counter;
}
```

**属性：** `Trait`同样可以定义属性。

```
<?php
trait PropertiesTrait {
    public $x = 1;
}

class PropertiesExample {
    use PropertiesTrait;
}

$example = new PropertiesExample;
$example->x;
```

*如果`trait`定义了一个属性，那类将不能定义同样名称的属性，否则会产生一个错误（级别E_STRICT）。*


> 匿名类

PHP7开始支持匿名类，匿名类很有用，可以创建一次性的简单对象。

```
<?php
// PHP 7 之前的代码
class Logger
{
    public function log($msg)
    {
        echo $msg;
    }
}
$util->setLogger(new Logger());

// 使用了 PHP 7+ 后的代码
$util->setLogger(new class {
    public function log($msg)
    {
        echo $msg;
    }
}); 
```
匿名类同样可以传递参数、继承(extends)、实现接口(implement)，以及像其他普通的类一样使用trait：

···
<?php
class SomeClass {}
interface SomeInterface {}
trait SomeTrait {}

var_dump(new Newclass(10) extends SomeClass implements SomeInterface {
    private $num;
    public function __construct($num) {
        $this->num = $num;
    }
    
    use SomeTrait;
});
···

匿名类被嵌套进普通`Class`后，不能访问这个外部类(`Outer class`)的`private`、`protected`方法或者属性。为了访问外部类`protected`属性或者方法，匿名类可以`extend`此外部类，为了使用外部类的`private`属性，必须通过构造器转。

```
<?php 
class Outer
{
    private $prop = 1;
    protected $prop2 = 2;

    protected function func1()
    {
        return 3;
    }

    public function func2()
    {
        return new class($this->prop) extends Outer {
            private $prop3;

            public function __construct($prop)
            {
                $this->prop3 = $prop;
            }

            public function func3()
            {
                return $this->prop2 + $this->prop3 + $this->func1();
            }
        };
    }
}
echo (new Outer)->func2()->func3(); 
```

> 重载

PHP提供的“重载”是指动态地“创建”类属性和方法。我们是通过魔术方法（mixed, __call(string $name, array $arguments)）实现的。

当调用当前环境下未定义或者不可见的类属性或方法时，重载方法会被调用。

所有的重载方法都必须被声明为`public`

**属性重载：**

`public void __set(string $name, mixed $value) 给不可访问属性赋值时调用
public mixed __get(string $name)  读取不可访问属性的值时调用
public bool __isset(string $name) 当对不可访问属性调用`isset()`或者`empty()`时调用
public void __unset(string $name) 当对不可访问属性调用`unset()`时调用
`

**方法重载：** 
  ·public mixed __call(string $name, array $arguments)  在对象中调用一个不可访问方法时，__call()被调用
public static mixed __callStatic(string $name, array $arguments) 用静态方式中调用一个不可访问方法时，__callStatic 被调用·

```
<?php 
class MethodTest {
    public function __call($name, $arguments){
        echo "Calling object method ".$name . implode(',', $arguments);
    }
    
    public static function __callStatic ($name, $arguments) {
        echo "Calling static method ".$name .implode(',', $arguments);
    } 
}

$obj = new MethodTest;
$obj -> runTest ('in object context') ;
MethodTest::runTest("in static context");

//输出：
Calling object method runTestin object context
Calling static method runTestin static context
```

> 遍历对象

`PHP5`提供了一种定义对象的方法使其可以通过单元列表来遍历，例如用`foreach`语句。默认情况下，所有可见属性都将被用于遍历。

```
<?php 
class MyClass {
    public $var1 = 'value 1';
    public $var2 = 'value 2';
    public $var3 = 'value 3';
    
    protected $protected = 'protected var';
    private $private = 'private var';
    
    function iterateVisible () {
        echo "MyClass::iterateVisible<br/>";
        foreach ($this as $key => $value) {
            print "$key => $value<br/>";
        }
    }
}

$class = new MyClass();
foreach ($class as $key => $value) {
    print "$key  => $value<br/>";
}
echo "<br/>";
$class -> iterateVisible();

// 输出成绩：
var1 => value 1
var2 => value 2
var3 => value 3
MyClass::iterateVisible
var1 => value 1
var2 => value 2
var3 => value 3
protected => protected var
private => private var
```
可以事先`Iterator`接口，可以让对象自行决定如何遍历以及每次遍历时那些值可用。

```
<?php 
class MyIterator implements Iterator {
    private $var = array();
    public function __construct($array) {
        if (is_array($array)) {
            $this -> var = $array;
        }
    }
    
    public function rewind() {
        echo "rewind<br/>";
        reset($this->var);
    }
    
    public function current () {
        $var = current($this->var);
        echo "current: $var <br/>";
        return $var;
    }
    public function key() {
        $var = key($this->var);
        echo "key: $var\n";
        return $var;
    }

    public function next() {
        $var = next($this->var);
        echo "next: $var\n";
        return $var;
    }

    public function valid() {
        $var = $this->current() !== false;
        echo "valid: {$var}\n";
        return $var;
    }
}
$values = array(1,2,3);
$it = new MyIterator($values);

foreach ($it as $a => $b) {
    print "$a: $b\n";
}
```
可以用`IteratorAggrefate`接口代替实现所有的`Iterator`方法。`Iteratoraggregate`只需要实现一个方法`IteratorAggregate::getlterator()`，其应返回一个实现了`Iterator`的类的实例


> 魔术方法

`__construct(), __destruct(), __call(), __callStatic(), __get(), __set(), __isset(), __unset()`等方法在PHP中被称为“魔术方法”，在命名自己的类方法时不能使用这些方法名，除非是想使用其魔术方法。

**`__sleep()`和`__wakeup()`：**

`public array __sleep(void) ;  void __wakeup()` 
`serialize()`函数会检查类中是否存在一个魔术方法`__sleep()`，如果存在，该方法会先被调用，然后才执行序列化操作，此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组，如果该方法未返回任何内容，则NULL被序列化，并产生一个`E_NOTICE`级别的错误。

`__sleep()`方法常用于提交未提交的数据，或类似的清理操作，反之`unserialize()`会检查是否存在一个`__wakeup()`方法，如果存在则会先调用`__wakeup()`,预先准备对象需要的资源。

```
<?php
class Connection 
{
    protected $link;
    private $server, $username, $password, $db;
    
    public function __construct($server, $username, $password, $db)
    {
        $this->server = $server;
        $this->username = $username;
        $this->password = $password;
        $this->db = $db;
        $this->connect();
    }
    
    private function connect()
    {
        $this->link = mysql_connect($this->server, $this->username, $this->password);
        mysql_select_db($this->db, $this->link);
    }
    
    public function __sleep()
    {
        return array('server', 'username', 'password', 'db');
    }
    
    public function __wakeup()
    {
        $this->connect();
    }
}
```

**`__toString()`：**   `public string __toString(void)`, 用于一个类被当成字符串时应怎样回应。

```
<?php
// Declare a simple class
class TestClass
{
    public $foo;

    public function __construct($foo) 
    {
        $this->foo = $foo;
    }

    public function __toString() {
        return $this->foo;
    }
}

$class = new TestClass('Hello');
echo $class;
```

**`__invoke()`：** `mixed __invoke([$...])` 当尝试以调用函数的方式调用对象时， `__invoke()`方法会被自动调用。

```
<?php
class CallableClass {
    function __invoke($x){
        var_dump($x);
    }
}
$obj = new CallableClass;
$obj(5);
var_dump(is_callable($obj));

// 输出结果：
int(5) 
bool(true)
```

**`__set_state()`:** `static object  __set_state(array $properties)` 自PHP5.1.0起当调用var_export()导出类时，此静态方法会被调用


> `Final`关键字

PHP5新增了一个`final`关键字，如果父类中的方法被声明了`final`，则子类无法覆盖该方法，如果一个类被声明为`final`，则不能被继承。

*属性不能被定义为`final`，只有类和方法才能被定义为`final`*

> 对象复制

对象复制可以通过`clone`关键字来完成，对象中的`__clone`方法不能直接调用。

`$copy_of_object = clone $object`

当对象被复制后，PHP5会对对象的所有属性执行一个浅复制(shallow copy)。所有的引用属性仍然会是一个指向原来的变量的引用。

`void __clone(void)`

当复制完成时，如果定义了`__clone()` 方法，则新创建对象（复制生成的对象）中的对象复制`__clone()`方法会被调用，可用于修改属性的值（如果有必要的话）。

```
<?php
class SubObject
{
    static $instances = 0;
    public $instance;

    public function __construct() {
        $this->instance = ++self::$instances;
    }

    public function __clone() {
        $this->instance = ++self::$instances;
    }
}

class MyCloneable
{
    public $object1;
    public $object2;

    function __clone()
    {
        // 强制复制一份this->object， 否则仍然指向同一个对象
        $this->object1 = clone $this->object1;
    }
}

$obj = new MyCloneable();
$obj->object1 = new SubObject();
$obj->object2 = new SubObject();

$obj2 = clone $obj;

print("Original Object:<br/>");
print_r($obj);

print("<br/>Cloned Object:<br/>");
print_r($obj2);

// 输出：
Original Object:
MyCloneable Object ( [object1] => SubObject Object ( [instance] => 1 ) [object2] => SubObject Object ( [instance] => 2 ) ) 
Cloned Object:MyCloneable Object ( [object1] => SubObject Object ( [instance] => 3 ) [object2] => SubObject Object ( [instance] => 2 ) )
```

> 对象比较

PHP5中的对象比较比PHP4中复杂， 所期望的解构更符合一个面向对象语言。

当使用比较运算符（==）比较两个对象变量时，比较的原则是：如果两个对象的属性和属性值都相等，而且两个对象时同一个类的实例，那么这两个对象变量相等。

如果使用全等运算符（===），这两个对象变量一定要指向某个类的统一实例（及同一个对象）。

```
<?php
function bool2str($bool)
{
    if ($bool === false) {
        return 'FALSE';
    } else {
        return 'TRUE';
    }
}

function compareObjects(&$o1, &$o2)
{
    echo 'o1 == o2 : ' . bool2str($o1 == $o2) . "<br/>";
    echo 'o1 != o2 : ' . bool2str($o1 != $o2) . "<br/>";
    echo 'o1 === o2 : ' . bool2str($o1 === $o2) . "<br/>";
    echo 'o1 !== o2 : ' . bool2str($o1 !== $o2) . "<br/>";
}

class Flag
{
    public $flag;

    function Flag($flag = true) {
        $this->flag = $flag;
    }
}

class OtherFlag
{
    public $flag;

    function OtherFlag($flag = true) {
        $this->flag = $flag;
    }
}

$o = new Flag();
$p = new Flag();
$q = $o;
$r = new OtherFlag();

echo "Two instances of the same class<br/>";
compareObjects($o, $p);

echo "\nTwo references to the same instance<br/>";
compareObjects($o, $q);

echo "\nInstances of two different classes<br/>";
compareObjects($o, $r);

// 输出结果：
Two instances of the same class
o1 == o2 : TRUE
o1 != o2 : FALSE
o1 === o2 : FALSE
o1 !== o2 : TRUE
Two references to the same instance
o1 == o2 : TRUE
o1 != o2 : FALSE
o1 === o2 : TRUE
o1 !== o2 : FALSE
Instances of two different classes
o1 == o2 : FALSE
o1 != o2 : TRUE
o1 === o2 : FALSE
o1 !== o2 : TRUE
```

> 类型约束

如果一个类或接口指定了类型约束，则其所有的子类或实现也都如此。

类型约束不能用于标量类型如`int`或`string`. `Traints`也不允许。

```
class MyClass {
    public function test_array(array $input_array) {
        print_r($input_array);
    }
}
```

> 静态绑定 ????

从PHP5.3.0 起，PHP增加了一个叫后期静态绑定的功能，用于在继承范围内引用静态调用的类。


>  对象和引用

在PHP5中“默认情况下对象时通过引用传递的”，其实并不完全正确。

PHP的引用是别名，就是两个不同的变量名字指向相同的内容，在PHP5，一个对象变量已经不再保存整个对象的值，只是保存一个标识符来访问真正的对象内容，当对象作为参数残敌，作为结果返回，或者复制给另外一个变量，另外一个变量跟原来的不是引用的关系，只是他们都保存着同一个标识符的拷贝，这个标识符指向同一个对象的真正内容。 

```
<?php 
class A {
    public $foo = 1;
}  

$a = new A;
$b = $a;     // $a ,$b都是同一个标识符的拷贝
             // ($a) = ($b) = <id>
$b->foo = 2;
echo $a->foo."<br/>";


$c = new A;
$d = &$c;    // $c ,$d是引用
             // ($c,$d) = <id>

$d->foo = 2;
echo $c->foo."<br/>";


$e = new A;

function foo($obj) {
    // ($obj) = ($e) = <id>
    $obj->foo = 2;
}

foo($e);
echo $e->foo."<br/>";
```

>  对象序列化

**序列化对象-在会话中存放对象：**所有PHP里面的值都可以使用函数`serialize()`来返回一个包含字节流的字符串表示。`unserialize()`函数能够重新把字符串变回PHP原来的值。序列化一个对象将会保存对象的所有变量，但是不会保存对象的方法，只会保存类的名字。

为了能够`unserialize()`一个对象，这个对象的类必须已经定义过。如果序列化类A的一个对象，将会返回一个跟类A相关，而且包含了对象所有变量值的字符串。如果要想在另外一个文件种解序列化一个对象，这个对象的类必须在解序列化之前定义，可以通过包含该类的文件或使用函数`spl_autoload_register()`来实现。

当一个应用程序使用函数`session_register()`来保存对象到会话中时，在每个页面结束的时候这些对象会自动序列化，而在每个页面开始的时候又自动解序列化。所以一旦对象被保存在会话中，整个应用程序的页面都能使用这些对象，但是，`session_register()`在PHP5.4.0之后被移除了。
