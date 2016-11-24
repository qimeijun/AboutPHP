##PHP 时间函数

> 简介

可以使用PHP函数获取服务器的日期和时间，并且把这些日期和时间格式化成不同格式的字符串。

日期和时间信息在PHP内部是以64位数字存储的，他可以覆盖当前时间前后2920亿年的时间。

> 常用函数介绍

1、`date($format, $timestamp)`获取当前时间， $format为格式、$timestamp为时间戳（可选参数）

![2016-11-15_134903.jpg](http://upload-images.jianshu.io/upload_images/1644330-4415b5abbbe78ccf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2、`time(), strtotime()`获取PHP中的unix时间戳，time()为直接获取得到，`strotime($time, $now)`为将时间转换成时间戳，$time必填

3、`strtotime($time)`用法

`strtotime('2012-03-22')`, 将2012-03-22转换成时间戳
`date('Y-m-d H:i:s',strtotime('+1 day'))`  输出结果为2016-11-16 13:55:28
`date(‘Y-m-d H:i:s’,strtotime(‘-1 day’))` 输出结果为2016-11-14 15:24:08
`date(‘Y-m-d H:i:s’,strtotime(‘+1 week’))` 输出结果为2016-11-22 15:27:25
`date(‘Y-m-d H:i:s’,strtotime(‘next Thursday’))` 输出结果为2016-11-17 00:00:00
`date('Y-m-d H:i:s',strtotime('last Thursday'))` 输出结果为2016-11-10 00:00:00

4、`microtime()` 获取PHP的毫秒数， 返回一个数组，包含两个元素：一个是秒数，一个是小数表示的毫秒数。

5、获取当前时间相差6小时解决方法
解决方法：a、在`php.ini`中找到`date.timezone`, 将它的值改成`Asia/Shanghai`,即`date.timezone = Asia/Shanghai`
b、在程序开始时添加`date_default_timezone_set('Asia/Shanghai')`即可。

5、`getdate()` 获取当前时间，返回一个数组格式：`array(11) { ["seconds"]=> int(43) ["minutes"]=> int(6) ["hours"]=> int(16) ["mday"]=> int(15) ["wday"]=> int(2) ["mon"]=> int(11) ["year"]=> int(2016) ["yday"]=> int(319) ["weekday"]=> string(7) "Tuesday" ["month"]=> string(8) "November" [0]=> int(1479197203) }`

6、`checkdate(integer month, integer day, integer year)` 检查日期是否合法 
```
<?
if(checkdate(2,29,1980))
print("日期合法!");
?>
```
7、`mktime(integer hour,integer minutes,integer seconds,integer month, integer day,integer year)` 返回给出日期的时间戳
```
<?
$currenthour=date("H");
print("50个小时后为:");
print(date("h:i A l F dS,Y",mktime($currenthour+50)));
print("<br>");
?>
```
