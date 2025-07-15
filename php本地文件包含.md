#php基本用法#
##php本地文件包含##
**本地文件包含是什么？**
本地文件包含，Local File Inclusion,LFI
包含的文件必须存储在本地
PHP使用函数*include(),require()*动态加载本地服务器上的文件
1.合法的本地文件包含功能
<?php
$page = $_GET["page"];  // 获取传入的文件名参数
include($page . ".php");
如果访问http://example.com/index.php?page=about
等价于执行include("about.php");
2.非法访问，LFI漏洞
如果服务器配置错误或拼接处理不当，攻击者就可能包含敏感系统文件，造成信息泄露，远程执行等安全问题
3.作为漏洞时本地文件包含（LFI）的影响
*信息泄露/目录遍历/任意代码执行/本地日志文件包含*

**PHP本地文件包含漏洞**
include($_GET['file']); *根据用户通过URL提供的参数file的值，动态包含并执行对应的PHP文件,没有任何限制*
include('/var/www/html/'.$_GET['file']); *在file数值前加上一个绝对路径*

**CTF中有价值的文件路径/PHP本地文件包含漏洞时，渗透测试中常用的可读文件清单**
/flag
/etc/hostname #主机名
/etc/passwd #Linux存放用户名的文件，可列出用户列表
/etc/shadow #Linux存放用户密码的文件，仅root权限可读
/proc/version #内核版本、编译信息
*当前进程信息*
/proc/self/cmdline #当前进程的命令行参数,Linux系统中一个特殊的虚拟文件
/proc/self/environ #环境变量
/proc/self/status #当前PHP进程的状态信息
/proc/self/cwd #指向当前进程运行目录


# 完整的LFI本地文件包含漏洞的实战演练
<img width="859" height="273" alt="image" src="https://github.com/user-attachments/assets/9440e5ab-e478-4905-9fd0-e6016c72d9c4" />
通过XAMPP搭建环境
1.打开路径 D:\XAMPP\htdocs
2.创建新文件夹，目录结构 D:\XAMPP\htdocs\lfi-demo
3.目录结构C:\xampp\htdocs\lfi-demo
├── index.php
├── page
│   ├── home.php
│   └── about.php
|——
4.文件内容
**index.php**
<?php
// 不安全版本（用于实战 LFI 演示）
$page = $_GET['page'] ?? 'home.php';
include("page/" . $page);
**page/home.php**
<h2>首页</h2>
<p>欢迎来到主页</p>
**page/about.php**
<h2>关于我们</h2>
<p>这是一个测试网站</p>
5.访问
http://localhost/lfi-demo/index.php?page=home.php
*URL参数/GET参数* ？变量名=变量值

看到首页内容
6.测试漏洞
使用绝对路径http://localhost/lfi-demo/index.php?page=C:/Windows/win.ini
通过LFI漏洞读取系统文件C:\Windows\win.ini




