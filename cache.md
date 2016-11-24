
###1、全页面静态化缓存

  将页面全部生成HTML静态页面，用户访问时直接访问静态页面，而不会走PHP服务器解析的流程。例如：`DEDECMS`。

  PHP页面缓存主要用到的ob系列函数，例如：`ob_start(), ob_end_flush(), ob_get_contents()`等。

  `ob_start()`: 页面缓存开始的标志，此函数一下的内容直至`ob_end_flush()`或者`ob_end_clean()`都保存在页面缓存中。

  `ob_get_contents()`：用来获取页面缓存中的内容.

  `ob_end_flush()`：表示页面缓存结束

  `ob_start();`
  ```
    *****代码运行****
    $content = ob_get_contents();
    *****将缓存内容写入HTML文件*****
    ob_end_clean();
  ```

###2、页面部分缓存
  将一个页面中不经常变得部分进行静态缓存，而经常变化的块不缓存，最后组装在一起显示，可以使用类似`ob_get_contents` 的方式实现，也可以利用类似ESI之类的页面片段缓存策略

###3、数据缓存
  就是缓存数据的一种方式，将查询到的数据缓存到一个PHP文件中，下一次再次查询同样的信息，就可以直接读取PHP文件了，就不用查询数据库了。

###4、按内容变更进行缓存
  就是当数据库内容被修改时，立即更新缓存文件。

  例如：一个人流量很大的商场，商品很多，商品表必然比较大，这表的压力也比较重，我们就可以对商品显示页进行页面缓存；当商家在后台修改这个商品的信息时，点击保存，我们同事就更新缓存文件，那么，买家访问这个商品信息时，实际上访问的是一个静态页面，而不需要再去访问数据库。



###5、内存是缓存
  例如：`Redis，Memcached` 等缓存数据库。通过缓存数据库查询结果，减少数据库访问次数，以提高动态`web`应用的速度、提高可扩展性。

  

###6、apache 缓存模块
  在安装`apache`的时候要激活`mod_cache` 的模块然后设置`httpd.conf`



###7、`php  APC` 缓存扩展
  PHP有一个`APC`缓存扩展，`windows` 下面为`php_apc.dll`， 需要先加载这个模块，然后在`php.ini`里面进行配置：
  ```
    [apc] 
         extension=php_apc.dll 
         apc.rfc1867 = on 
         upload_max_filesize = 100M 
         post_max_size = 100M 
         apc.max_file_size = 200M 
         upload_max_filesize = 1000M 
         post_max_size = 1000M 
         max_execution_time = 600 ;   每个PHP页面运行的最大时间值(秒)，默认30秒 
         max_input_time = 600 ;       每个PHP页面接收数据所需的最大时间，默认60 
         memory_limit = 128M ;       每个PHP页面所吃掉的最大内存，默认8M
  ```

###8、`Opcode`缓存

        PHP代码被解析为`Tokens`, 然后在编译为`Opcode`码，最后执行`Opcode`码返回结果，所以对于相同PHP文件，第一次运行时可以缓存器`Opcode`码，下次再执行这个页面是，直接回去找到缓存下的`opcode`码，直接执行最后一步，而不需要中间的步骤了。
