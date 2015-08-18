目的：加快开发速度，总结使用方法：
menu list:

 - custom controller 
 - custom 模块使用 
 - custom model 
 - custom middleware  
 - custom
 - service

## 关于启动 ##：
----

config/connection -- db connection
入口: app.js
config/bootstrap - 启动入口app.js 后的
config/policies 配置
bin/*.json


## 关于DIY ##：
----
config/router
路由与对应的controller处理:
用命令行 & controller 变化
wade-mac:fin_server_invest mac$ sails generate controller mail sendmaillog
info: Created a new controller ("mail") at api/controllers/MailController.js!
路由总汇总：config/routes.js 
    想往常一样加：, 'GET /make/a/mail':"MailController.sendmaillog"

模块的使用： －－  与以前一样

     /**
     * MailController
     *
     * @description :: Server-side logic for managing mails
     * @help        :: See http://links.sailsjs.org/docs/controllers
     */
    
    module.exports = {
        
    
    
      /**
       * `MailController.sendmaillog()`
       */
      sendmaillog: function (req, res) {
        var log4js = require('log4js');
        var logger = log4js.getLogger();
        logger.debug("Some debug messages");
    
        return res.json({
          todo: 'sendmaillog() is not implemented yet!'
        });
      }
    };

model command ：
    [https://www.digitalocean.com/community/tutorials/how-to-create-an-node-js-app-using-sails-js-on-an-ubuntu-vps][1]

    $sails generate model user name:string email:string password:string
    $sails generate controller user index show edit delete

middleware：
[https://gist.github.com/mikermcneil/6255295][2]
look at config/http.js


    module.exports.http = {
    
      /****************************************************************************
      *                                                                           *
      * Express middleware to use for every Sails request. To add custom          *
      * middleware to the mix, add a function to the middleware config object and *
      * add its key to the "order" array. The $custom key is reserved for         *
      * backwards-compatibility with Sails v0.9.x apps that use the               *
      * `customMiddleware` config option.                                         *
      *                                                                           *
      ****************************************************************************/
    
      middleware: {
    
      /***************************************************************************
      *                                                                          *
      * The order in which middleware should be run for HTTP request. (the Sails *
      * router is invoked by the "router" middleware below.)                     *
      *                                                                          *
      ***************************************************************************/
    
        order: [
           'startRequestTimer',
           'cookieParser',
           'session',
           'myRequestLogger',
           'bodyParser',
           'handleBodyParserError',
           'compress',
           'methodOverride',
           'poweredBy',
           '$custom',
           'router',
           'www',
           'favicon',
           '404',
           '500'
        ],
    
      /****************************************************************************
      *                                                                           *
      * Example custom middleware; logs each request to the console.              *
      *                                                                           *
      ****************************************************************************/
    
        myRequestLogger: function (req, res, next) {
             console.log("Requested :: ", req.method, req.url);
             return next();
        },
    
    
      /***************************************************************************
      *                                                                          *
      * The body parser that will handle incoming multipart HTTP requests. By    *
      * default as of v0.10, Sails uses                                          *
      * [skipper](http://github.com/balderdashy/skipper). See                    *
      * http://www.senchalabs.org/connect/multipart.html for other options.      *
      *                                                                          *
      ***************************************************************************/
    
        bodyParser: require('skipper')
    
      },
    
      /***************************************************************************
      *                                                                          *
      * The number of seconds to cache flat files on disk being served by        *
      * Express static middleware (by default, these files are in `.tmp/public`) *
      *                                                                          *
      * The HTTP static cache is only active in a 'production' environment,      *
      * since that's the only time Express will cache flat-files.                *
      *                                                                          *
      ***************************************************************************/
    
      cache: 31557600000
    };

上面的是所有的路由都经过的middleware
疑问：控制某个路由/a  经过middleware: [a, b, c ] , 某个路由/b 经过middleware: [a, c ] 

一些文件夹
service
lib
data

CRUD
CRUD -- model
CRUD -- native
速度
复杂操作
CRUD -- 坑
findandmodify
Order.native(function (err, collection) {
 collection.findAndModify(query, null, {$set: update_data}, {'new': false}, function (err, newResult, detail) {

update  upsert
User_account_cashflow.native(function(err, colletion){
 colletion.update(query, {$set: cashflow },{upsert:true, multi:false} , function(err, effectNum, result){
   callback(err, cashflow, result.updatedExisting);
 });
});

.findorcreate
multi write: 注意速度，mongodb 会lock db
query 0-10 ms
write 0-100ms
nodejs
坑－－大数组（>5000） setImmediate,"args"        : "['--stack-size=32000']",

callback hell
transaction : two phase commit; mongoose-transaction
long Procedure in async.waterfall
use event:
event_util.emit("cashflow_over_quota", 'create', JSON.stringify(cashflow));


Testing 
makefile, -- make coverage
grunt
mocha
bad code
all in controller
good code
pass to service/lib/
node-modules


  [1]: https://www.digitalocean.com/community/tutorials/how-to-create-an-node-js-app-using-sails-js-on-an-ubuntu-vps
  [2]: https://gist.github.com/mikermcneil/6255295
