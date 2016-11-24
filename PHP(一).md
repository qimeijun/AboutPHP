## PHP基础（一）

>简介

PHP(Hypertext Preprocessor, 超文本预处理器的字母缩写)，脚本语言，开源，适合web开发。

PHP代码是运行在服务端的。

PHP能够支持所有主流操作系统。

####PHP基本语法

>标记

1、````<?php ?>  ````
2、```<? ?> ```

>从HTML中分离

1、``` <?php echo 'hello PHP' ?>;```
2、```<script language="php"> echo 'Hello PHP';  </script>```
3、``` <? echo 'Hello PHP'; ?>   ```
4、```<?= expression ?> Hello PHP  "<? echo expression ?>"```
5、``` <% echo 'Hello PHP'; %>```
*官方文档推荐使用第一种*

>指令分隔符

PHP 需要在每个语句后用分号结束指令。

>注释

1、/**/  多行注释

2、//     单行注释

3、#     shell 风格的注释


####PHP语法

>简介

PHP支持8中原始数据类型：

**四种标量类型**：boolean(布尔型)、integer(整型)、float(浮点型, or  double)、string(字符串)

**两种符合类型**：array（数组）、object（对象）

**两种特殊类型**：resource（资源）、NULL（无类型）

除开这8种类型而外，另外还有几种类型叫做“**伪类型**”：mixed（混合类型）、number（数字类型）、callback（回调类型）

>Boolean（布尔类型）

最简单的类型，true 或者false，不区分大小写。

**转换**： 变量前边加上（bool）或者（boolean）。

**默认为false的情况**：

1、布尔值false本身

2、整型值为0

3、浮点型为0.0

4、空字符串以及字符串“0”

5、不包括任何元素的数组

6、特殊类型NULL（包括尚未赋值的变量）

7、从空标记生成SimpXML对象

>integer（整型）

integer包含{ ...-4, -3, -2, -1, 0, 1, 2, 3, 4.... }中的任何一个数。

整型值可以是十进制、十六进制、八进制、二进制（PHP5.4.0才支持）表示。

八进制：数字前必须加上0。

十六进制：数字前必须叫上0x

二进制：数字前必须加上ob。

**整数溢出**：如果给定的数超出了integer的范围，将会被解释成float。

**转换**：用（integer）或者（int）强制转换。如果将一个浮点数转换为整数，那么小数点后边的数将会被抹掉。

>float（浮点型）

```
<?php
 $a = 1.234; 
$b = 1.2e4r5; 
$c = 7E-10;
```

>string（字符串）

一个字符串string有一系列的字符组成。每一个字符等同于一个字节，PHP只能支持256的字符集，因此不支持Unicode。

*Note：string最大可以达到2GB*

**单引号：**定义一个字符串最简单的方法就是用单引号；要表达单引号自身，需在它的前面加上一个反斜杠（\）来转移【\'】；要表达一个反斜线，则用两个反斜线（\\）；如果想用其他转义序列例如\r或\n，并不代表任何特殊含义，就单纯是这两个字符本身。

**双引号**：如果字符串是包围在双引号（""）中，PHP将对一些特殊的字符进行解析，例如：\n, \r, \t等。

**Heredoc 结构**：第三种表达字符串的方法是heredoc结构：<<<， 在该运算符之后提供一个标识符，然后换行，接下来是字符串string本身，最后要用前面定义的标志符作为结尾标识符。
```
<?php
$string = <<<EOT
字符串字符串
EOT；
```
**变量解析：**当字符串用双引号或heredoc结构定义时，其中的变量将会被解析。

**复杂（花括号）语法**：不是因为其语法复杂而得名，而是因为它可以使用复杂的表达式。例如：echo "this is { $great }"；或者 echo "this square is { $square->width } centimeters broad";

**转换成字符串**：可以通过在其前面加上（string）或用strval() 函数转变成字符串。一个布尔值boolean 的true转换成string 的"1"， boolean的false转换成""(空字符串)。array、object、resource转成string不会得到除了其类型之外的任何有用信息，可以使用函数print_t()和var_dump()列出这些类型的内容。

**字符串类型的详解**：PHP中的string的实现方式是一个有字节组成的数组再加上一个整数执行缓冲区的长度。并无如何将字节转换成字符串的信息，有程序员来决定。字符串有什么值来组成并无限制；特别的，其值为0（null bytes）的字节可以处于字符串任何位置。

>Array数组

PHP中的数组实际山是一个有序映射，映射是一种把value关联到keys 的类型。

**语法：**`array(key => value , key2 => value2, ...)`；键（key）可是一个整数integer或者字符串string, value可以是任意类型的值。
`$array = array("foo" => "bar", "bar" => "foo"); $array2 = ["foo" => "bar", "bar" => "foo"]；`

访问数组元素：`echo $array["foo"]; echo $array[2]`;

删除整个数组或者删除数组中的某一个元素使用`unset($arr)`，`unset($array[1])`;

**转换成数组：**integer、float、string、boolean、resource 类型，如果将一个值转换为数组，将得到一个仅有一个元素的数组，其下标为0,。

>Object对象

**对象初始化：**要创建一个新的对象object ，使用new语句实例化一个类。`$obj = new OBJ`;

**转化成对象：**如果讲一个对象转化成对象，他将不会有任何改变，如果其他任何类型的值转换成对象，将会创建一个内置类stdCalss的实例。如果该值为NULL，则新的实例为空。数组转换成对象将使键名成为属性名并具有相应的值。对于任何其他的值，名为scalar的成员变量将包含该值。

>Resource资源类型

资源resource是一种特殊变量，保存了到外部资源的一个引用。

####PHP变量


>基础

PHP中的变量用一个美元符号后面跟变量名来表示。

***变量名区分大小写***

**规则：**有字母或者下划线开头，后面跟上任意数量的字母、数字、或者下划线。正常的正则表达式为`[a-zA-Z_\x7f_\xff][a-zA-Z0-9_\x7f-\xff]`。

**传值赋值**：变量默认总是传值赋值，当一个表达式的值赋予一个变量时，整个原始表达式的值被赋值到目标变量。` $a = 1; $b = 34`;

**引用赋值**：就是变量简单引用了原始变量。改动新的变量将影响到原始变量，反之亦然。例如：`$foo = 'BOb'; $bar = &$foo; $bar = 'My name is Seven'; echo $bar; echo $foo; // $foo 的值也被修改`。   * 只有有名字的变量才可以引用赋值*。

虽然在PHP中并不需要初始化变量，但对变量进行初始化是个好习惯。isset()语言结果可以用来检测一个变量是否已被初始化。

>预定义变量

PHP提供了大量的预定义变量。例如：`$GLOBALS, $_SERVER, $_GET, $_POST, $_FILE, $_COOKIE, $_SESSION, $_REQUEST, $_ENV`

超全局变量不能被用作为函数或类方法中的可变变量。

>变量范围

变量范围及它定义的上下文北京。大部分的PHP变量只有一个单独的范围，这个单独的范围跨度同样包含了include和require引入的文件。在PHP的函数中，如果要使用外部变量，必须现在函数中申明， 如`global $a, $b`; // $a, $b 在函数外边存在并且有默认值， 或者是直接使用`$GLOBALS['a'], $GLOBAL['b']`直接使用。

**静态变量**：尽在局部函数域中存在，但当程序离开此作用域时，其值并不丢失。*如果静态变量在声明中用表达式的结果对其赋值会导致解析错误。  静态变量实在编译时解析的。*声明方式：`static $a = 0`;

**全局和静态变量的引用**：变量的`static`和`global`定义是以引用的方式实现的。

>可变变量

一个变量的变量名可以动态的设置和使用。 例如：`$a = 'hello' ;  $$a = 'world'`; 

>来自PHP之外的变量

当一个表单提交给PHP脚本时，表单中的信息会自动在脚本中可用。
接收外来变量`$_POST['username'],  $_REQUEST['username']`,  如果是get提交方式，那么接收方式为`$_GET['username']`.

变量名中的点和空格被转换成下划线, 例如`<input name="a.b" />` 变成了`$_REQUEST["a_b"]`。

**HTTP  Cookies: **cookies  是一种远端浏览器存储数据并能追踪或者识别再次访问的客户的机制，可以使用`setcookie()`函数设定`cookies`。Cookies是http信息头中的一部分。

**变量名中的点**：PHP不会改变传递给脚本的变量名，但是会自动将变量名中的点替换成下划线。


####PHP常量


>语法

可以用`define()`函数来定义常量。一个常量一旦定义就不能更改或者取消。常量只能包含标量数据`int, string, float, boolean`，可以定义`resource`，但尽量避免。

***常量名默认区分大小写，通常都为大写。***

**定义形式**：`define("CONSTANT", "hello world")`; 或者 `const START = 'hello world'`；

获取常量的方式： `echo CONSTANT`; 

**与变量的不同之处**：1、常量前面没有美元（`$`）符号； 2、常量只能用`define()`函数定义，而不能通过赋值语句。3、常量可以不用理会变量的作用域而在任何地方定义和访问；4、常量一旦定义就不能被重新定义或者取消定义；5、常量的值只能是标量。

**`define()`和`const`的区别**：和使用`define()`来定义常量相反的是，使用`const`关键字定义常量必须处于最顶端的作用区域，因此用此方法实在编译时定义的，这就意味着不能再函数、循环内、以及`if`语句之内用`const`来定义常量。

>魔法常量

PHP提供了大量的预定义常量， 例如：`__LINE__， __FILE__， __DIR__， __FUNCTION__， __CLASS__， __TRAINT__， __METHOD__， __NAMESPCE__`等。

>表达式

表达式是PHP的基石。最基本的表达式形式是常量和变量。

####PHP运算符

>运算符优先级

![](http://upload-images.jianshu.io/upload_images/1644330-7c88c47c08c0f21f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

尽管 = 比其他大多数的运算符的优先级低，PHP仍旧允许类似如下偶的表达式：`if(!$a=foo())`，在此例子中foo()的返回值被赋给了 `$a`.

>算术运算符

![](http://upload-images.jianshu.io/upload_images/1644330-58dda9670de27ee1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>赋值运算

![](http://upload-images.jianshu.io/upload_images/1644330-ed5cecdebc3130ec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>位运算符

位移在PHP中是数学运算。向任何方向一出去的位都被丢弃。左移时右侧以零填充，符号位被移走时、意味着正负号不被保留，右移时左侧以符号位填充，意味着正负号被保留。

![](http://upload-images.jianshu.io/upload_images/1644330-fdcdac44dcc96675?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>比较运算符

![](http://upload-images.jianshu.io/upload_images/1644330-f035718a667a10b2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 错误控制运算符

PHP支持一个错误控制运算符：`@`。将其放置在PHP表达式之前，该表达式可能产生的任何错误都被忽略。

```
<?php
$my_file = @file('non_existent_file') or die('Failed opening file: error was '.$php_errormsg);
```

*`@`运算符只对表达式有效。 如果能从某处得到值，就能在它前面加上`@`运算符。例如： 变量、函数和`include`调用、常量等。不能放在函数或类的定义之前，也不能用于条件结构例如`if`和`foreach`等。* 

>执行运算符

PHP支持一个执行运算符：反引号(``)。PHP将尝试将反引号中的内容作为shell命令来执行，并将其输出的信息返回。使用反引号符``的效果与函数`shell_exec()`相同.
```
<?php
$test = `c:\mybat.bat`;
echo "<pre>$test</pre>";
```

>递增  /  递减运算符

*递增递减运算符不影响布尔值，递减`NULL`值也没有效果，但是递增`NULL`的结果是1。*


![运算符](http://upload-images.jianshu.io/upload_images/1644330-0062348f298d4e6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?php
for ($i = 'a'; $i <= 'z'; $i++) {
  echo $i;
}
// 输出的结果：abcdefghijklmnopqrstuvwxyzaaabacadaeafagahaiajakalamanaoapaqarasatauavawaxayazbabbbcbdbebfbgbhbibjbkblbmbnbobpbqbrbsbtbubvbwbxbybzcacbcccdcecfcgchcicjckclcmcncocpcqcrcsctcucvcwcxcyczdadbdcdddedfdgdhdidjdkdldmdndodpdqdrdsdtdudvdwdxdydzeaebecedeeefegeheiejekelemeneoepeqereseteuevewexeyezfafbfcfdfefffgfhfifjfkflfmfnfofpfqfrfsftfufvfwfxfyfzgagbgcgdgegfggghgigjgkglgmgngogpgqgrgsgtgugvgwgxgygzhahbhchdhehfhghhhihjhkhlhmhnhohphqhrhshthuhvhwhxhyhziaibicidieifigihiiijikiliminioipiqirisitiuiviwixiyizjajbjcjdjejfjgjhjijjjkjljmjnjojpjqjrjsjtjujvjwjxjyjzkakbkckdkekfkgkhkikjkkklkmknkokpkqkrksktkukvkwkxkykzlalblcldlelflglhliljlklllmlnlolplqlrlsltlulvlwlxlylzmambmcmdmemfmgmhmimjmkmlmmmnmompmqmrmsmtmumvmwmxmymznanbncndnenfngnhninjnknlnmnnnonpnqnrnsntnunvnwnxnynzoaobocodoeofogohoiojokolomonooopoqorosotouovowoxoyozpapbpcpdpepfpgphpipjpkplpmpnpopppqprpsptpupvpwpxpypzqaqbqcqdqeqfqgqhqiqjqkqlqmqnqoqpqqqrqsqtquqvqwqxqyqzrarbrcrdrerfrgrhrirjrkrlrmrnrorprqrrrsrtrurvrwrxryrzsasbscsdsesfsgshsisjskslsmsnsospsqsrssstsusvswsxsysztatbtctdtetftgthtitjtktltmtntotptqtrtstttutvtwtxtytzuaubucudueufuguhuiujukulumunuoupuqurusutuuuvuwuxuyuzvavbvcvdvevfvgvhvivjvkvlvmvnvovpvqvrvsvtvuvvvwvxvyvzwawbwcwdwewfwgwhwiwjwkwlwmwnwowpwqwrwswtwuwvwwwxwywzxaxbxcxdxexfxgxhxixjxkxlxmxnxoxpxqxrxsxtxuxvxwxxxyxzyaybycydyeyfygyhyiyjykylymynyoypyqyrysytyuyvywyxyyyz
```
*在处理字符变量的算术运算时，PHP沿袭了Perl的习惯，例如在Perl中`$a = 'Z'; $a++`, 将把`$a`变成`AA`, 而在C中， `a = 'Z', a++`, 将把a变成'[',  字符变量只能递增，不能递减。 递增、递减其他字符变量则无效，原字符串没有变化。*

> 逻辑运算符


![逻辑运算符](http://upload-images.jianshu.io/upload_images/1644330-9eefd7703ccfec98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 字符运算符

```
<?php
$a = 'hello';
$b = $a . 'world!';
echo $b; // hello world!

$a = 'hello';
$b .= 'world!'; // hello world!
```

两个字符串合并，通过 '.' 就能完成合并。


> 数组运算符


![数组运算符](http://upload-images.jianshu.io/upload_images/1644330-cf12c39ee652296e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 类型运算符

*`instanceof` 用于确定一个PHP变量是否属于某一类。*

```
class MyCalss {}
class NotMyClass {}
$a = new MyClass;

var_dump($a instanceof MyClass);
var_dump($a instanceof NotMyClass);

// 输出结果
bool(true);
bool(false)
```

*`instanceof`可用来确定一个变量是不是继承自某一父类的子类*

```
class ParentClass {}
class MyClass extends ParentClass {}

$a = new MyClass;

var_dump($a instanceof MyClass);
var_dump($a instanceof ParentClass);

// 输出的结果：
bool(true);
bool(true);
```

*`instanceof` 可用于确定一个变量是不是实现了某个接口对象。*

```
interface Myinterface {}
class MyClass implements Myinterface {}

$a = new MyClass;

var_dump($a instanceof MyClass);
var_dump($a intanceof Myinterface);

// 输出结果
bool(true);
bool(true)
```

*如果被检测的变量不是对象，`instanceof `并不发出任何错误信息，而是返回`false`*



####PHP流程控制

> 简介

任何PHP脚本都是由一系列的语句构成的。


> if 

`if`结构是很多语言包括PHP在内重要的特性之一，与C语言结构相似。

```
<?php
if (expr) {
  statement;
}
```

`if ` 语句可以无限层地嵌套在其他`if`语句中。


> else 

`else` 延伸了 `if ` 语句， 可以在`if` 语句中的表达式的值为`FALSE` 时执行语句。

```
<?php
if ($a > $b) {
  echo 'a is bigger than b';
} else {
  echo 'b is bigger than a';
}
```

> elseif / else if

`elseif` 是 `if` 和 `else` 的组合，和`else` 一样，延伸了`if`语句。

```
<?php
if ($a > $b) {
  echo 'a is bigger than b';
} else if ($a == $b) {
  echo 'a is equal to b';
} else {
  echo 'b is bigger than a';
}

// 此表达式中可以使用 `elseif()` 替代  `else if()`
```

如果用冒号来定义`if elseif` 条件，就不能用两个单词的`else if`，否则PHP会产生解析错误。

```
<?php
if ($a > $b ):
  echo 'a is bigger than b';
elseif ($a == $b):
  echo 'a is equal to b';
else:
  echo 'a is smaller than b';
endif;

// 如果此表达式中将 `elseif` 替换成`else if` ,那么程序会报错。

```


> 流程控制的替代语法

PHP提供了一些流程控制的替代语法，包括`if, while, for , foreach, switch`, 替代形式把左花括号({) 替换成冒号 (:), 把右或括号(}) 分别替换成`endif;, endwhile;, endfor;, endforeach;, endswitch`。


> while

while 循环是PHP中最简单的循环类型，与C语言中的一样。

```
while (expr) {
  statement
}
或者

while (expr):
  statement
endwhile;
```


> do-while

与`while`循环非常相似，主要区别在于`do-while`的循环语句保证会执行一次，而`while`循环中就不一定了（先检查是否满足表达式，如果满足就执行，如果不满足就永远不会执行）。

```
<?php
 $i = 0;
do {
  echo $i;
} while($i > 0)
```

> for

`for` 循环是PHP中最复杂的循环结构，和C语言相似。

```
for (expr1; expr2; expr3) {
  statement;
}
或者

for (expr1; expr2; expr3):
  statement;
endfor;
```

> foreach

`foreach`语法结构提供了遍历数组的简单方式， 仅能够应用于数组和对象。

```
foreach (array_expression as $value) {
  statement;
}

foreach (array_expression as $key => $value) {
  statement;
}
```

*当`foreach`开始执行时，数组内部的指针会自动指向第一个单元。
  由于`foreach` 依赖内部数组指针，在循环中修改其值将可能导致意外的行为*

```
<?php
$array = [1, 2, 3, 4, 5];
foreach ($array as &$value) {
  $value = $value * 2;
}
// $array  is   [2, 4, 3, 4, 10];

unset($value); // 最后取消掉引用
// $array is [1, 2, 3, 4, 5];
```

*数组最后一个元素的`$value` 引用在`foreach` 循环之后仍会保留，建议使用`unset()`将其销毁。*

*`foreach`不支持用`@` 来抑制错误信息的能力。*

```
<?php
$arr = array('one', 'two', 'three', 'four');
reset($arr);
while (list($key, $value) = each($arr)) {
  echo 'Key:'.$key.'; Value:'.$value.'<br /> \n';
}
foreach ($arr as $key => $value) {
  echo 'Key:'$key.' Value:'.$value .'<br/>\n';
}
```

**用`list()`给嵌套的数组解包：**

```
<?php
$array = [
  [1, 2], [3, 4],
];
foreach ($array as list($a, $b)) {
  echo 'A:'.$a .';  B:'.$b .'\n';
}

// 输出：
A:1; B:2
A:1; B:2
```


> break

`break` 结束当前`for, foreach, while, do-while, switch`结构的执行。

`break`可以接受一个可选的数字参数来决定跳出几重循环。
```
$arr = array('one', 'two', 'three', 'four', 'stop', 'five');
while (list (, $val) = each($arr)) {
    if ($val == 'stop') {
        break;    /* You could also write 'break 1;' here. */
    }
    echo "$val<br />\n";
}
```

> continue

`continue`在循环结构中用来跳过本次循环中剩余的代码并在条件求值为真时开始执行下一次循环。

*在PHP中`switch`语句被认为是可以使用continue的一种循环结构*。

`continue`后面可接受一个数字参数来决定跳过几重循环，默认是1.

```
<?php 
while (list ($key, $value) = each($arr)) {
    if (!($key % 2)) { // skip odd members
        continue;
    }
}
```

> switch

`switch`语句类似于具有同一个表达式的一系列if语句。 支持`int, string, float`类型，不能用数组或对象，除非他们被解除引用成为简单类型。
```
<?php
if ($i == 0) {
    echo "i equals 0";
} elseif ($i == 1) {
    echo "i equals 1";
} elseif ($i == 2) {
    echo "i equals 2";
}

switch ($i) {
    case 0:
        echo "i equals 0";
        break;
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
}
```

> declare

`declare` 结构用来设定一段代码的执行指令。

`ticks`指令在PHP5.3.0中是过时指令，将会从PHP6.0.0移除。
`encoding`是PHP5.3.0新增指令。
```
declare (directive)
  statement
```

**Ticks:**
`Tick`是一个在declare代码中解释器没执行N条科技史的低级语句就会发生的事件。N的值在`declare`中的`directive`部分用`ticks = N` 来指定的。
不是所有语句都可计时，通常条件表达式和参数表达式都不可计时。
在每个`tick`中出现的事件时有`register_tick_function()`来指定的。

例子：
```
<?php
function doTicks() {
	echo 'Ticks===';
}
register_tick_function('doTicks');
declare (ticks = 1) {
	for ($x = 1; $x < 10; ++$x) {
		echo $x * $x . '<br/>';
	}
}

// 输出结果：
1
Ticks===Ticks===4
Ticks===Ticks===9
Ticks===Ticks===16
Ticks===Ticks===25
Ticks===Ticks===36
Ticks===Ticks===49
Ticks===Ticks===64
Ticks===Ticks===81
Ticks===Ticks===Ticks===Ticks===
```
*例子解读：完整的for循环算一个语句，但必须要等循环结束后才算，因此在编译时for循环里面的echo算第一个语句。第一个ticks实在第一个echo 语句后执行的，所以1 输出后才发生第一个tick事件，在$x从1到9的循环中，每个循环包括两个语句：一个echo语句，一个for循环。在81输出后，因为echo是一条语句，因此输出第一个ticks。同时$x=9的这个for循环也结束了，这又是一条语句,输出第二个ticks；开始$x=10的循环，但这时已不满足循环条件，for循环执行结束，这个循环又是一个语句，这时输出第三个ticks。最后declare本身也算一条语句，所以又输出第四个ticks。*

*
Zend引擎中生成`ZEND_TICKS`指令的唯一函数时`zend_do_ticks`，而调用`zend_do_ticks`的三条语法规则为：
`
statement:
  unticked_statement { zend_do_ticks(TSRMLS_C); }
;
function_declaration_statement:
  unticked_function_declaration_statement { zend_do_ticks(TSRMLS_C); }
;
class_declaration_statement:
  unticked_class_declaration_statement { zend_do_ticks(TSRMLS_C); }
`
也就是说PHP会编译`statement、function_declaration_statement、class_declaration_statement`后插入`ticks`处理函数，会根据`ticks` 的参数决定在第几条语句后面插入`ticks`命令，如果`ticks = 1` ，那么就会在每一条`statement、function_declaration_statement、class_declaration_statement`后面插入一条`ticks`指令。
然而所有的`statement、function_declaration_statement、class_declaration_statement`就构成了所谓的低级语句（low-level statement）。
statement包含：简单语句（空语句，return、break、continue、throw、goto、global、static、unset、echo、内置HTML文本、分号结束的表达式等），复合语句（完整的if/elseif、while、do...while、for、foreach、switch、try...catch等），语句块（{}括出来的语句块），特殊体（declare本身也算一个语句）。
*

**Encoding:** 可以用encoding指令来对每一段脚本指定其编码方式。
```
<?php
declare(encoding = 'ISO-8859-1');
// code
```


> return 

如果在一个函数中调用return语句，表示将立即结束此函数的执行并将它的参数作为函数的值返回。`return`也会终止`eval()`语句或脚本文件的执行。


> include

被包含的文件先按参数给出的路径寻找，如果没有给出目录（只有文件名）时则按照include_path指定的目录寻找，如果在include_path下没有找到该问阿金则include最后才在调用脚本文件所在的目录和当前工作目录下寻找。如果最后仍未找到文件则include结构会发出警告.

*当一个文件被包含是，其中所包含的代码继承了`include`所在行的变量范围。如果include出现于调用文件中的一个函数里，则被调用的文件中所包含的所有代码将表现的如何它们实在该函数内部定义的一样。*

**Warning：**远程文件可能会经远程服务器处理，但必须产生出一个合法的PHP脚本，因为其将被本地服务器处理，如果来自远程服务器的文件应该在远程端运行而输出结果，用`readfile()`函数更好。

**返回值：**失败是`include`返回`false`并且发出警告，成功是返回1。我们可以给被包含文件自定义返回值，使用`return`语句来返回值并且终止后面语句的执行。

*include是一个特殊的语言结构，参数不需要括号， 在比较返回值是要注意。*

> require

`require`和`include`几乎完全一样，除了处理失败的方式不同之外。`require`在出错是产生`E_COMPLIE_ERROR`级别的错误，从而导致脚本终止，而include只产生警告`E_WARNING`，脚本会继续运行。


> include_once

`include_once` 语句在脚本执行期间包含并运行指定文件，此行为和include类似，区别在于如果该文件已经被曝韩国，则不会再次包含。

> require_once

`require_once`语句和`require`语句完全相同，区别就是PHP会检查该文件是否已经被包含过，如果是则不会再次包含。

> goto

`goto`操作符可以用来跳转到程序中的另一位置，该目标位置可以用目标名称加上冒号来标记，而跳转指令是`goto`之后接上目标位置的标记。

**限制：**PHP 中的goto有一定限制，目标位置只能在同一个文件和作用域，无法跳出一个函数或类方法，也无法跳入到另一个函数。PHP5.3以上的版本有效。

```
<?php
goto a:
echo 'Foo';

a:
echo 'Bar';

// 输出结果：Bar
```
