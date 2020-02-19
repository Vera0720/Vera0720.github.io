---
title: 浅谈phpCI框架
date: 2017-03-14 23:57
tags: php
---
作为前端开发人员,掌握一门后端语言是必不可少的,PHP的CI框架是一个快速开发框架,基于MVC,比较接近原生PHP,在原有的PHP代码上封装了许多类,易上手,容易扩展,适用于小项目,并且CI的文档及案例及其丰富,我用了5天时间从一头雾水照猫画虎用CI框架写了一个个人博客,顺路也看了看数据库,所以CI框架针对新手及小项目还是非常友好的.学完这个我由衷的明白了为什么论坛上的人将PHP奉为世界上最好的语言.

推荐的书籍:PHP与MySQL程序设计

<!-- more -->

### 我们所关心的为XAMPP下的这几部分:

1.Apache---web服务器,客户端发送request到服务器端,服务器返回response给客户端

2.Mysql

3.Php运行环境

注意:将项目文件夹放在xampp下的htdocs下,将ci框架直接下载到项目文件夹里

CI框架分为几部分:

1.Models模型   简单来说就是所有放跟数据库有关的操作以及其他用来摆放实体类工具类的地方

2.Controls控制器  起到一个连接的作用,将用户界面和后台数据库将View中的用户界面显示,并调用Models里的操作,实现相关操作

3.View视图   用户界面,主要用html等前端语言来写

小提示:

隐藏文件.htaccess是用来做访问控制的

System下的文件基本不需要改动

Application下config目录是配置目录,根据所需修改相关配置

### CI环境配置
去官网上下载相应的压缩包，其中application、system、index.php放到项目的根目录中，启动服务器，如果看到欢迎界面，说明成功。

application文件夹中：
config：配置信息
controllers：控制器层
models：模型层
views：视图层

system文件夹：系统文件，一般情况下不需要修改
index.php 入口文件


controller: class User extends CI_Controller，如果不指定方法默认进入index方法

autoload.php:
$autoload['libraries'] = array('database', 'session');
$autoload['helper'] = array('url'); <?php echo site_url();?>
config.php:
$config['index_page'] = '';
$config['encryption_key'] = 'jsdoijfodskjksldjkj';
database.php
$db['default']['hostname'] = 'localhost';
$db['default']['username'] = 'root';
$db['default']['password'] = '';
$db['default']['database'] = 'ci10';

在项目的根目录下：.htaccess

### 编码流程示例:

1.在view下创建一个html文件名为login.php,简单写一下代码

    <form id="frm_login" action="welcome/check_login" method="POST"">
    
            <table>
    
                <tbody>
    
                    <tr>
    
                        <th nowrap="nowrap">邮箱 或 账号：</th>
    
                        <td><input name="username" type="text"></td>
    
                    </tr>
    
                    <tr>
    
                        <th>登录密码：</th>
    
                         <td><input name="password" type="password"></td>
    
                    </tr>
    
                    <tr class="buttons">
    
                        <td>
    
                            <input value="现在登录" class="BUTTON SUBMIT" type="submit"/>
    
                        </td>
    
                    </tr>
    
                </tbody>
    
            </table>
    
    </form>

2.在控制器里先加载界面

    public function login()
    
    {
    
       $this->load->view('login');
    
    }

通过在地址栏下输入地址localhost:/myblog/welcome/login就能看到登录界面

 

3.当输入账号密码后点击登录按钮,会通过表单post来提交数据,前端与后端的联系是通过name传值.所以将所需传值的变量设置name,input name="username"以及input name="password",并利用表单通过action跳转到相应控制器下的方法,这里为action="welcome/check_login" ,控制器下check_login代码如下

    public function check_login()
    
        {
    
            //1.接收数据
    
            $username = $this->input->post('username');
    
            $password = $this->input->post('password');
    
            //2.验证
    
            //3.数据库操作
    
            $this->load->model('user_model');//加载model文件
    
            $result = $this->user_model->get_by_name_pwd($username, $password);//调用model文件下的方法并传值
    
            if ($result) {
    
                echo 'ok';
    
            } else {
    
                echo 'fail';
    
            }
    
        }

控制器中的代码有3个步骤,接收数据,验证,数据库操作(包括加载model文件和用model文件下的方法并传值,然后根据返回结果写所需代码)

4.操作model,主要为数据库操作,建数据库表的过程省略

    public function get_by_name_pwd($name,$pwd){
    
            $query = $this -> db -> get_where('t_user',array(
    
                'username' => $name,
    
                'password' => $pwd
    
            ));
    
            return $query -> row();
    
    }

接收传过来的值并在数据库中查询,将结果返回到上一步控制器中,然后继续进行

CI框架简单易上手,拿它做个毕业设计什么的简直跟玩一样,哈哈哈
