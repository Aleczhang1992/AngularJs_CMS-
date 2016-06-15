# AngularJs_CMS-

# <a name="webui">WebUI开发指南1.0</a>

## 版本

我们开发Web前端，使用了Angular，目前使用版本为V1.4.5

## <a name="introduction">AngularJS简介</a>

在Web前端开发中，通常会提到经典的MVC框架：

* M（model）指的是开发中的业务模型，通常是与项目实际需求的相关数据和功能相关；
* V(view)即页面渲染，通常是我们网站中的HTML+CSS实现的部分（不过也有针对view层的类库存在，比如ReactJS）；
* C(controller)即网站开发中的控制模块，通常由Javascript编写，用于实现用户的动态交互。

而AngualrJS就是前端开发中一个典型的MVC框架。更多[关于AngularJS](http://www.apjs.net/)。

***

_要点提示：_    

_1. 理解Angular对Html的扩展属性和功用；_     
      
_2.理解双向数据绑定和如何实现；_

## <a name="modules">网站后台管理系统常备功能</a>
* 文件更新和发布：
新闻发布、图片及其他文件上传、消息推送；
* 用户（会员）管理：
用户的信息、密码修改，删除用户及新增用户；
* 监控系统：     
监控系统数据和进程的历史记录和状况；
* 页面侧栏示例    

![](http://ww2.sinaimg.cn/mw690/7403256fgw1eytd5wsq7uj204r07sdg1.jpg)    

## <a name="develop">如何在现有框架模板上进行新的开发</a>
### <a name="login">有权限的用户登录逻辑实现</a>
任何一个后台管理系统，打开index时，首先进入的应该是登录页面，通过输入正确的用户及密码可登录。
项目中涉及到
* service/auth.js （登录模块）     
* view/login.html（登陆界面）
* controller/login_controller.js（登录控制器）    
* widget/router.js（路由配置）        
* controller/root_controller.js（body的控制器）    

**步骤说明：前三个文件即是新增对应的M,V,C各层，然后进行正确的路由配置，在整个页面body控制器中选择优先进入登陆界面**


### <a name="sidebar">侧栏中增加新栏目步骤</a>
每个栏目会对应正确的路由页面，通过在div容器中插入ng-view实现页面切换。下面以增加【新增用户】栏目为例说明。  

在widget/router.js中增加正确的路由配置

    .when('/addAuth', {
                templateUrl: 'views/addAuth.html',
                controller: 'addAuthCtrl'
            })`  

在controller中增加控制器addAuthCtrl  

            angular.module('logApp').controller('addAuthCtrl', ['$scope', '$http', function ($scope, $http) {
            ...
        $scope.updateStaticData();
        $scope.title = "添加用户";
            $http.post(url+'/madical-backend/user/addUser', {
            	    username:$scope.new_username,
            	    password:$scope.new_password,
            	    role:$scope.role
            })
            .success(function (data) {
			    if (data.code === 0) {
                    alertln(data.message);
                } else {
                    alertln("添加失败");
                }

            });
			}else{
				alert('输入密码不一致，请重新输入');
			}
        }
    }]); 

在view目录中增加addAuth.html     

在nav_controller.js中增加新的标签

    var treeData = [
           ...
                {	label: '用户添加',
                able: false,
                data: {
                description: 'addAuth'
                		}
                }
        ];
这样就可增加一个新的栏目及其功能页面
![此处输入图片的描述][1]
### <a name="steps">常用功能实现步骤</a>
经常需要增加的功能栏目有：
 - 新闻系统    
新闻发布、修改删除、置顶
 - 用户管理系统    

 新增用户 删除用户 修改用户
 - 监控系统    

 数据监控 进程监控     

任何一个新增功能都需要重复新增栏目的步骤，从服务端进行数据传输时，需要获得对应的功能接口，根据接口返回的数据格式进行正确的数据获取。

## <a name="plug">此框架中前端使用到的第三方插件</a>

    {
        "name": "log",
        "version": "0.0.0",
        "dependencies": {
        "angular": "1.4.5",
        "json3": "^3.3.0",
        "es5-shim": "^4.0.0",
        "bootstrap": "^3.2.0",
        "angular-animate": "1.4.5",
        "angular-cookies": "1.4.5",
        "angular-resource": "1.4.5",
        "angular-route": "1.4.5",
        "angular-sanitize": "1.4.5",
        "angular-touch": "1.4.5",
        "angular.treeview": "",
        underscore": "~1.7.0",
        "angular-datepicker": "1.0.12",
        "bootbox": "*",
        "angular-ueditor": "*",
        "checklist-model": "~0.1.3",
        "ng-file-upload": "~2.2.2",
        "angular-bootstrap": "0.13.4"
          },
        "devDependencies": {
            "angular-mocks": "1.4.5",
            "angular-scenario": "1.4.5"
          },
         "appPath": "app"
        }

## <a name="questions">开发中遇到的问题解答</a>    
### <a name="datafixed">html中双向绑定数据的方式</a>

比如在Ctrl中声明了一个变量

   

     $scope.helloWorld='Hello World!';

可直接写入html标签内

    <span>{{helloWorld}}</span>
    
若绑定到表单元素中，使用ng-model

    <span ng-model="helloWorld"></span>
    

###  <a name="scope">$scope的理解</a>

在控制模块中可通过$scope声明任意的变量、方法，然后在对应的html中使用。
在AngularJS，一个子scope通常原型继承于它的父scope。应用于这个规则的表达式是一个使用scope:{...}的指令，这将创建一个『孤岛』scope（非原型继承）。这种构造通常在创建一个可复用组件指令的时候使用。

Scope继承通常是垂直的，并且你通常不需要知道它的发生，直到你尝试在子scope中使用2-way数据绑定到一个在父scope中定义的原始类型。scope不起作用的原因，很多是由于在它的父类中有同名的属性。AngularJS不会这么做，这是由JavaScript原型继承自己做的。新的AngularJS开发者经常不了解的是，ng-repeat、ng-switch、ng-view、ng-include都会创建一个新的子scope，所以问题经常在这些指令中出现。

在你的model中，带有「.」将会确认原型继承在起作用。所以使用<input type="text" ng-model="someObj.prop1">比<input type="text" ng-model="prop1">更好。

如果你真的想要（或需要）使用一个原始的方式，有两项工作：

*   在子scope中，使用$parent.parentScopeProperty。这将阻止子scope创建它自己的属性。
*   在父scope上创建一个方法，并且从子scope中调用它，传递原始值到父scope中（并不总是起作用）。


###  <a name="request">发送服务端请求</a>

$http

$cmsHttp     

两种方法的区别在于对服务端响应code的判断。
### <a name="datafailed">第三方插件无法双向绑定问题</a>     
解决方法：使用到的<abn-tree>是一个Angular的控件，用于生成树状目录，但是无法实现即时的数据刷新，所以需要在 service/interface.js中定义setSelectListTreeDataAble  方法，用于手动刷新datatree。    
### <a name="cookie">$cookieStore,$location的使用</a>     
$cookieStore可用于将数据存储到本地使用；$location可以暴露页面的url，从中获取页面跳转时传的参数


  [1]: http://ww3.sinaimg.cn/mw690/7403256fgw1eym7tbmf1aj20rv0ehmyg.jpg

