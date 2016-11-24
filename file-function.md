# PHP文件函数

1、`basename(string $path[, string $suffix])` 返回路径中的文件名部分

```
<?php 
echo basename('/etc/sudoers.d', '.d').'<br/>';
echo basename('/etc/passwd').'<br/>';
echo basename('/etc/sdkdf.s').'<br/>';

// 输出
sudoers
passwd
sdkdf.s
```

2、`chmod(string $filename, int $mode)` 改变文件模式, 返回值为`true` or `false`

```
<?php 
chmod('/somedir/somefile', 755); // 十进制数，可能不对
chmod('/somedir/somefile', 'u+rwx,go+rx'); //字符串，不对
chmod('/somedir/somefile', 0755); // 八进制数，正确
```

3、`clearstatcache([ bool $clear_realpath_cache  = false [, string $filename ]])` 清除文件状态缓存；` clear_realpath_cache`  是否清除真实路径缓存； `filename `清除文件名指定的文件的真实路径缓存，只有在`clear_realpath_cache`  为`true`是启用。

4、`copy(string $source, string $dest [, resource $context])`  拷贝文件，返回`TRUE` or `FALSE`
`copy('file1.php', 'copy.php');`

5、`dirname(string $path)` 返回路径中的目录部分
```
<?php 
echo dirname('/etc/passwd'); // 输出: /etc 
echo dirname('/etc/'); // 输出：(or \ on Windows)
```

6、`disk_total_space(string $directory)` 返回一个目录的磁盘总大小
```
<?php 
echo disk_total_space('/');
echo disk_total_space('C:');
echo disk_total_space('D:');
```

7、`fclose(resource $handle)` 关闭一个已打开的文件指针，返回`TRUE` 或者 `FALSE`
`handle` 文件指针必须有效，并且通过`fopen()`或`fsockopen()`成功打开的。
```
<?php 
$handle = fopen('somefile.txt', 'r');
fclose($handle);
```

8、`feof(resouce $handle)` 测试文件指针是否到了文件结束的位置 ，返回`TRUE`或者`FALSE`
`handle`：文件指针必须是有效的，必须指向由`fopen()`或`fsockopen()`成功打开的文件（并还未由`fclose()`关闭）
如果服务器没有关闭由`fsockopen()`所打开的连接，`feof()`会一直等待直到超时。

如果传递的文件指针无效可能会陷入无限循环中，因为`feof()`不会返回`TRUE`

9、`fflush(resource $handle)` 将缓冲内容输出到文件，返回`TRUE` 或者`FALSE`
`handle` 文件指针必须是有效的，必须指向由 `fopen()` 或 `fsockopen()` 成功打开的文件(并还未由 `fclose()` 关闭)。
```
<?php 
$filename = 'bar.txt';
$file = fopen($filename, 'r+');
rewind($file);
fwrite($file, 'Foo');
fflush($file);
ftruncate($file, ftell($file));
fclose($file);
```

10、`fgetc(resource $handle)` 从文件指针中读取字符，返回字符串
`handle` 文件指针必须是有效的，必须指向由 `fopen()` 或 `fsockopen()` 成功打开的文件(并还未由 `fclose()` 关闭)。
```
<?php 
$fp = fopen('bar.txt', 'r');
if (!$fp) {
    echo 'Could not read this file!';
}
while (false != ($char = fgetc($fp))) {
    echo $char . '<br/>';
}
 
