DemoUserAuthorization
=====================

MVC WebApi 用户权限验证及授权DEMO

前言：Web 用户的身份验证，及页面操作权限验证是B/S系统的基础功能，一个功能复杂的业务应用系统，通过角色授权来控制用户访问，本文通过Form认证，Mvc的Controller基类及Action的权限验证来实现Web系统登录，Mvc前端权限校验以及WebApi服务端的访问校验功能。


1 Web Form认证介绍

Web应用的访问方式因为是基于浏览器的Http地址请求，所以需要验证用户身份的合法性。目前常见的方式是Form认证，其处理逻辑描述如下：

1) 用户首先要在登录页面输入用户名和密码，然后登录系统，获取合法身份的票据，再执行后续业务处理操作；

2) 用户在没有登录的情况下提交Http页面访问请求，如果该页面不允许匿名访问，则直接跳转到登录页面；

3) 对于允许匿名访问的页面请求，系统不做权限验证，直接处理业务数据，并返回给前端；

4) 对于不同权限要求的页面Action操作，系统需要校验用户角色，计算权限列表，如果请求操作在权限列表中，则正常访问，如果不在权限列表中，则提示“未授权的访问操作”到异常处理页面。


2 WebApi 服务端Basic 方式验证

WebApi服务端接收访问请求，需要做安全验证处理，验证处理步骤如下：

1) 如果是合法的Http请求，在Http请求头中会有用户身份的票据信息，服务端会读取票据信息，并校验票据信息是否完整有效，如果满足校验要求，则进行业务数据的处理，并返回给请求发起方；

2) 如果没有票据信息，或者票据信息不是合法的，则返回“未授权的访问”异常消息给前端，由前端处理此异常。

3 登录及权限验证流程

1) 用户打开浏览器，并在地址栏中输入页面请求地址，提交；
2) 浏览器解析Http请求，发送到Web服务器；Web服务器验证用户请求，首先判断是否有登录的票据信息；

3) 用户没有登录票据信息，则跳转到登录页面；

4) 用户输入用户名和密码信息；

5) 浏览器提交登录表单数据给Web服务器；

6) Web服务需要验证用户名和密码是否匹配，发送api请求给api服务器；

7) api用户账户服务根据用户名，读取存储在数据库中的用户资料，判断密码是否匹配；

7.1）如果用户名和密码不匹配，则提示密码错误等信息，然该用户重新填写登录资料；

7.2）如果验证通过，则保存用户票据信息；

8) 接第3步，如果用户有登录票据信息，则跳转到用户请求的页面；

9) 验证用户对当前要操作的页面或页面元素是否有权限操作，首先需要发起api服务请求，获取用户的权限数据；

10). api用户权限服务根据用户名，查找该用户的角色信息，并计算用户权限列表，封装为Json数据并返回；

11). 当用户有权限操作页面或页面元素时，跳转到页面，并由页面Controller提交业务数据处理请求到api服务器；
   如果用户没有权限访问该页面或页面元素时，则显示“未授权的访问操作”，跳转到系统异常处理页面。
   
12). api业务服务处理业务逻辑，并将结果以Json 数据返回；

13). 返回渲染后的页面给浏览器前端，并呈现业务数据到页面；

14). 用户填写业务数据，或者查找业务数据；

15). 当填写或查找完业务数据后，用户提交表单数据；

16). 浏览器脚本提交get，post等请求给web服务器，由web服务器再次解析请求操作，重复步骤2的后续流程；

17). 当api服务器验证用户身份是，没有可信用户票据，系统提示“未授权的访问操作”，跳转到系统异常处理页面。
