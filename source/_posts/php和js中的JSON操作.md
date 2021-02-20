---
title: php和js中的JSON操作
top: false
cover: false
toc: true
mathjax: false
date: 2020-07-17 11:45:51
password:
summary: 网站开发中，前后端分离是非常常见的开发方式。在这个方式中，前后端的数据传递非常重要。我在本文中，将对我在开发过程中，对JSON数据传递的体会，进行总结。
tags: JSON
categories: 网站编程
---



# 1：基本知识

*JSON*(JavaScript Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。这种格式的特点如下：

```
{[{"name":"zhangsan","age":54}],
[{"name":"lisi","age":56}]}
```

**说明：**

- 以上JSON数据包含了一个对象，最外侧的`{}`，对象内部包含了两个数组`[]`,每个数组内都有一个结构类似的对象，内侧的`{}`。

- 对象和数组是构成JSON的基本要素，数组的出现是为了让结构相同，即键名`例如：name、age`相同的对象，排列更加整齐，也方便我们用数组的方法提取。

由于前后端数据传递的特点，**对象或者数组格式的数据不能直接从php传递到前端js**。因此，我们将php中的对象/数组数据，转为字符串格式的JSON数据，然后传递到前端，前端js再将其转为对象或数组格式。反之亦然。

# 2：php中的json处理

php中主要有两个函数处理json数据: `json_encode`、`json_decode`

## 2.1 json_encode

json_encode:将数组、对象转为json格式的字符串。

- 例如：php中有一个数组，其包含键名和键值。

```php
$arr = [
    'appid' => '121434352',
    'appkey' => '19b8b372c501e1fbedead782d46199a',
    'callback' => 'http://example.com/callback.php',
    'scope' => 'add_t,add_pic_t,del_t',
    'errorReport' => true,
    'storageType' => 'file',
    'host' => 'localhost',
    'user' => 'root',
    'password' => 'root',
    'database' => 'test'
];
```

使用将其转为字符串

```php
$str = json_encode($arr);
echo $str;
```

输出：

```json
{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}
```

可以看到，数组被转换成了一个对象样式的JSON数据。

- 如果一个数组，索引为数字。

例如：

```php
$arr = array('a' , 'b', 'c');
json_encode($arr);
```

输出：

```php
["a","b","c"]
```

可看到其输出仍为数组格式。

- 如果希望将键名键值格式的数组转换后格式仍保留。可以对其排序，排序后，会丢失键名。索引统一变为数字。

```php
$arr = array('1' => 'a' , '2' => 'b', '3' => 'c');
sort($arr);
json_encode($arr);
```

输出：

```
["a","b","c"]
```

## 2.2 json_decode

json_decode:将字符串转为对象/数组格式。

```php
$str = '{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}';
$obj = json_decode($str);
print_r($obj);
```

由于`echo`只能输出字符串格式数据，`print_r`和`var_dump`可以将数组和对象用特定的格式输出。所以这里使用了print_r。

输出：

```php
stdClass Object
(
    [appid] => 121434352
    [appkey] => 19b8b372c501e1fbedead782d46199a
    [callback] => http://example.com/callback.php
    [scope] => add_t,add_pic_t,del_t
    [errorReport] => 1
    [storageType] => file
    [host] => localhost
    [user] => root
    [password] => root
    [database] => test
)
```

可以看到，json数据被转换成了一个对象。

**也可以修改json_decode参数，将json转为数组**

```php
$str = '{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}';
$arr = json_decode($str, true);
var_dump($arr);
```

这里，再json_decode参数里添加true，表示将json转为数组。并使用var_dump来输出数组。

var_dump输出结果：

```php
array(10) {
  ["appid"]=>
  string(9) "121434352"
  ["appkey"]=>
  string(31) "19b8b372c501e1fbedead782d46199a"
  ["callback"]=>
  string(31) "http://example.com/callback.php"
  ["scope"]=>
  string(21) "add_t,add_pic_t,del_t"
  ["errorReport"]=>
  bool(true)
  ["storageType"]=>
  string(4) "file"
  ["host"]=>
  string(9) "localhost"
  ["user"]=>
  string(4) "root"
  ["password"]=>
  string(4) "root"
  ["database"]=>
  string(4) "test"
}
```

对比print_r输出结果：

```php
Array
(
    [appid] => 121434352
    [appkey] => 19b8b372c501e1fbedead782d46199a
    [callback] => http://example.com/callback.php
    [scope] => add_t,add_pic_t,del_t
    [errorReport] => 1
    [storageType] => file
    [host] => localhost
    [user] => root
    [password] => root
    [database] => test
)

```

## 2.3 对象数组操作

### 2.3.1 对象操作

php中，根据对象键名获取键值的方式是`->`

比如我们转换了以下对象，

```php
$str = '{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}';
$obj = json_decode($str);
```

现在获取appkey

```php
$appkey = $obj->appkey;
echo $appkey;
```

输出：

```php
19b8b372c501e1fbedead782d46199a
```

### 2.3.2 数组操作

转换的数组：

```php
$str = '{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}';
$arr = json_decode($str, true);
```

操作数组，获取appkey：

```php
$appkey = $arr['appkey'];
echo $appkey
```

输出：

```
19b8b372c501e1fbedead782d46199a
```

# 3：JS中的json处理

与php中处理方式相对应，存在`JSON.stringify`和`JSON.parse`两个函数

## 3.1 JSON.stringify

对象、数组转为字符串格式。

对象字符串化：

```js
var Person={};   //等同于var Person =new Object();
var Person={
name:"Jason",
age:21
}
var str=JSON.stringify(Person)
alert(str);
```

输出：

```js
{"name":"Jason","age":21}
```

数组字符串化：

```js
var mycars=new Array();
mycars[0]="Saab";
mycars[1]="Volvo";
mycars[2]="BMW";
alert(JSON.stringify(mycars));
```

输出：

```js
["Saab","Volvo","BMW"]
```

## 3.2 JSON.parse

字符串转数组、对象。

- 转对象：

```js
var str = '{"appid":"121434352","appkey":"19b8b372c501e1fbedead782d46199a","callback":"http:\/\/example.com\/callback.php","scope":"add_t,add_pic_t,del_t","errorReport":true,"storageType":"file","host":"localhost","user":"root","password":"root","database":"test"}';
var obj= JSON.parse(str);
alert(obj);
```

输出：

```js
[object Object]
```

以上说明输出的数据格式为对象。

- 转数组：

```js
var str = '["a","b","c"]';
var arr= JSON.parse(str);
alert(arr);
```

输出：

```js
a,b,c
```

## 3.3 对象数组操作

### 3.3.1 对象操作

创建对象：

```js
var Person={};   //等同于var Person =new Object();
var Person={
name:"Jason",
age:21
}
```

获取name对应的键值：

```js
var name = Person.name;
alert(name);
```

或者：

```js
var name = Person['name'];
alert(name);
```

### 3.3.2 数组操作

```js
var arr = ['a','b','c'];
alert(arr[0]);
```

