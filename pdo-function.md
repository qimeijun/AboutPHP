##PHP 函数PDO

> 简介

PHP数据对象扩展为PHP访问数据库定义了一个轻量级的一致接口。 实现PDO接口的每个数据库驱动可以公开具体数据库的特性所谓标准扩展功能。

PDO提供了一个数据访问抽象层，意味着，不管使用哪种数据库，都可以使用相同的函数（方法）来查询和获取数据。

> 安装

1、PDO和所有主要的驱动作为共享扩展随PHP一起发布，要激活他们只需简单地编辑 `php.ini`文件。
`extension=php_pdo.dll`

2、选择其他具体数据库的DLL文件，然后要么在运行时用`dll()`载入，要么在`php.ini`中的`php_pdo.dll`后面启用。
`extension=php_pdo.dll
extension=php_pdo_firebird.dll
extension=php_pdo_informix.dll
extension=php_pdo_mssql.dll
extension=php_pdo_mysql.dll
extension=php_pdo_oci.dll
extension=php_pdo_oci8.dll
extension=php_pdo_odbc.dll
extension=php_pdo_pgsql.dll
extension=php_pdo_sqlite.dll  
`

> 连接与连接管理

链接是通过创建PDO基类的实例而建立的。不管使用哪种驱动程序，都是用 PDO 类名。构造函数接收用于指定数据库源（所谓的 DSN）以及可能还包括用户名和密码（如果有的话）的参数。

```
<?php 
 $db = new PDO('mysql:host=localhost;dbname=test', $user, $pass);
```

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     foreach ($db -> query("select * from user") as $row) {
         print_r($row);
     }
     $db = null;// 关闭数据库连接
 } catch(e) {
     print "Error:".$e->getMessage().'<br/>';
     die();
 }
```

如果不手动关闭链接，那么PDO对象的生成周期一直保持活动，知道PHP脚本结束。

> 事物与自动提交

事务支持四大特性：原子性、一致性、隔离性、持久性。一个事务中执行的任何操作，即使是分阶段执行的，也能保证安全地应用于数据库，并在提交时不会受到来自其他链接的干扰。事务操作也可以根据请求自动撤销。

如果需要一个事务，则必须用`PDO::beginTransaction()`方法来启动，如果底层驱动不支持事务，则抛出一个`PDOException`异常。一旦开始了事务，可用`PDO::commit()`或`PDO::rollback()`来完成，这取决于事务中的代码是否运行成功。

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     echo "Connected<br/>";
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 
 try {
     $db -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
     $db -> beginTransaction();
     $db -> exec("insert into staff (id, first, last) values (23, 'Joe', 'Bloggs')");
     $db -> exec("insert into salarychange (id, amount, changedate) 
      values (23, 50000, NOW())");
     $db -> commit();
 } catch (e) {
     $db -> rollback();
     echo "Failed: " . $e->getMessage();
 }
```

> 预处理语句与存储过程

预处理语句的两大好处：
  1、查询仅需解析（或预处理）一次，但可以用相同或不同的参数执行多次。与距离语句占用更少的资源，因而运行得更快。
2、提供给预处理语句的参数不需要用引号括起来，驱动程序会自动处理。如果应用程序只使用预处理语句，可以确保不会发生SQL注入。

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     echo "Connected<br/>";
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 
$stmt = $db -> prepare("INSERT INTO REGISTRY (name, value) VALUES (:name, :value)");
$stmt -> bindParam(":name", $name);
$stmt -> bindParam(":value", $value);

$name = 'one';
$value = 1;
$stmt -> execute();

$name = 'two';
$value = 2;
$stmt -> execute();
```

使用预处理语句获取数据

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     echo "Connected<br/>";
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 
$stmt = $db -> prepare("SELECT * FROM REGISTRY where name = ?");
if ($stmt -> execute(array($_GET['name'])) {
    while ($row = $stmt -> fetch()) {
        print_r($row);
    }
}
```

调用存储过程
```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     echo "Connected<br/>";
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 
$stmt = $db->prepare("CALL sp_returns_string(?)");
$stmt -> bindParam(1, $return_value, PDO::PARAM_STR, 4000);
$stmt -> execute();
print 'procedure returned $return_value<br/>';
```

> 错误与错误处理

PDO提供了三种不同的错误处理模式。
1、`PDO::ERRMODE_SILENT`
默认模式。PDO将值简单地设置错误码，可使用`PDO::errorCode()`和`PDO::errorInfo()`方法来检查语句和数据库对象，如果错误是由于对语句对象的调用而产生，那么可以调用那个对象的`PDOStatement::errorCode()`或`PDOStatement::errorinfo()`方法。

2、`PDO::ERRMODE_WARNING`
除设置错误码之外，PDO还讲发出一条传统的`E_WARNING`信息。如果只是想看看发生了什么问题且不终端应用程序的流程，那么次设置在调试/测试期间非常有用。

3、`PDO::ERRMODE_EXCEPTION`
除设置错误码之外， PDO还讲抛出一个`PDOException`异常类并设置它的属性来反射错误码和错误信息。次设置在调试期间也非常有用，因为他会有效地放大脚本中产生错误的点，从而可以非常快速地指出代码中有问题的潜在区域。

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
     $db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
```


> 大对象 (LOBs)

应用程序在某一个刻可能需要在数据库中存储“大数据”，“大”通常意味着“大约4KB或以上”，尽管某些数据库在数据达到“大”之前可以轻松地处理多大32KB的数据。大对象本质上可能是文本或二进制。在`PDOStatement::bindParam()`或`PDOStatement::bindColumn()`调用中使用`PDO::PARAM_LOB`类型码可以让PDO使用大数据类型。大对象 (LOBs)`PDO::PARAM_LOB`告诉 PDO 作为流来映射数据，以便能使用 PHP Streams API来操作。


```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 $stmt = $db->prepare("select contenttype, imagedata from images where id=?");
 $stmt->execute(array($_GET['id']));
 $stmt->bindColumn(1, $type, PDO::PARAM_STR, 256);
 $stmt->bindColumn(2, $lob, PDO::PARAM_LOB);
 $stmt->fetch(PDO::FETCH_BOUND);
 header("Content-Type:$type");
 fpassthru($lob);
```

插入一张图片到数据库

```
<?php 
 try {
     $db = new PDO("mysql:host=localhost;dbname=test", $user, $pass);
 } catch (e) {
     die("Unable to connect :" . $e -> getMessage());
 }
 $stmt = $db->prepare("insert into images (id, contenttype, imagedata) values (?, ?, ?)");
 $id = '20123';
 // 假设处理一个文件上传
 $fp = fopen($$_FILES['file']['tmp_name'], 'rb');
 $stmt->bindParam(1, $id);
 $stmt->bindParam(2, $$_FILES['file']['type']);
 $stmt->bindParam(3, $fp, PDO::PARAM_LOB);
 $db->beginTransaction();
 $stmt->execute();
 $db->commit();
```
