---
title: 创建一个简单的API项目
top: false
cover: false
toc: true
mathjax: false
date: 2020-01-26 22:01:17
password:
summary: API（应用程序接口），我现在正在做一个安卓项目，需要将mysql数据库里的数据，通过api接口以json格式导出，然后再安卓上解析和使用。API项目基于php服务器和laravel框架，这个框架听说比较适合做api，当然由于项目简单，我做的API省去了很多验证相关的内容，普通的框架类似thinkphp也能做，这里用laravel为了方便后期补足API的相关内容。
tags: API
categories: 网站编程
---

# 0：简介

API（应用程序接口），我现在正在做一个安卓项目，需要将mysql数据库里的数据，通过api接口以json格式导出，然后再安卓上解析和使用。

API项目基于php服务器和laravel框架，这个框架听说比较适合做api，当然由于项目简单，我做的API省去了很多验证相关的内容，普通的框架类似thinkphp也能做，这里用laravel为了方便后期补足API的相关内容。

# 1：域名和LAMP

专用于api的域名建议采用类似于api.example.com这样的三级域名，LAMP这四个网站服务器是必须的。

# 2：laravel安装

使用宝塔面板安装好的php，已经自带composer，若没有composer需要先安装。

没有数据库需要先创建数据库。

然后在网站目录下例如/var/www，运行指令

`composer create-project laravel/laravel MyProject  "5.1.*"` 

安装laravel。根据提示连接数据库。

# 3：新建laravel控制器

控制器就是当我们访问url时，服务器将运行控制器内的程序，然后返回给浏览器一个内容，这个内容可以是html代码，当然也可以时xml或者json。

在laravel安装目录运行`php artisan make:controller Api/LinkController`

新建一个名叫LinkController控制器，此时在将在`app/Http/Controllers/Api`目录下生成一个LinkController.php`文件。打开文件编辑我们想要的程序。

```php
<?php]()

namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use DB;

class LinkController extends Controller
{
    //方法一，从数据库获取某一数据
    //url中有参数情况，例如https://api.example.com/linkcontroller/searchlinks?type=电影&kw=中国机长
    public function searchlinks(Request $request){
    	$type=$request->input('type');//使用request获取url中参数
    	$kw=$request->input('kw');
		$linksresult = DB::table('my_zhlinks')->where('type', $type)->get();//从已连接的数据库，找到表单“my_zhlinks”，获取type和带入参数type相同的项。将这些数据保存到linkresult变量中。
        //数据库表单my_zhlinks是我自己创建的。
    	return json_encode($linksresult);//返回一个json数组，不是json对象。
    }
    
	//方法二，url中不带参数
    public function alltype(){
    	$alltypes=DB::table('my_zhpreg')->select('type')->get();
    	return json_encode($alltypes);
    	
    }
}
```

# 4：修改路由

在上面注释中我们看到，要通过searchlinks方法返回一个json数组，url为`https://api.example.com/linkcontroller/searchlinks?type=电影&kw=中国机长`非常的长，而且不利于我们后期管理api版本。

在`routes`目录下的`api.php`是专门用来写Api接口的路由，打开它

```php
<?php]()

use Illuminate\Http\Request;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| is assigned the "api" middleware group. Enjoy building your API!
|
*/
Route::namespace('Api')->prefix('v1')->group(function(){
	Route::get('search','LinkController@searchlinks')->name('search.links');
	Route::get('type','LinkController@alltype')->name('type.all');
});
```

`namespace('Api')`指定了命名空间，控制器命名空间只要不是在`App\Http\Controllers`下，都需要进一步指明其命名空间，比如LinkController命名空间为`App\Http\Controllers\Api`。prefix为前缀，我们用来控制版本，group表示，后面的地址拥有相同的命名空间和前缀。
修改后的带参数url变为`https://api.example.com/api/v1/search?type=电影&kw=中国机长`

不带参数的url变为`https://api.example.com/api/v1/type`

# 5：总结

修改路由后，通过访问链接`https://api.example.com/api/v1/search?type=电影&kw=中国机长`就可以获得从数据库获取的值了。

下篇讲解使用okhttp和gson获取并解析api里的json数组。