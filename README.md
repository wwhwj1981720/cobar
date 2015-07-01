# cobar
阿里开源项目cobar
cobar的安装步骤：
1、	从 网址 http://code.taobao.org/p/cloud-cobar/src/ 上使用svn下载 cloud-cobar 代码。(版本选 默认 head)
2、 使用myeclipse（或者 eclipse）c从  菜单File->import->General->Existing projects Into WorkSpace 中选择 cobra的下载路径
3、导入之后 会有 cobar-build、cobar-common、cobar-config、cobar-manager、cobar-net、cobar-parser、cobar-route、cobar-server等子工程；
4、找到cobra-build工程目录下面 resources->build.xml文件，在该文件上面点击鼠标右键 选择 runas ->ant build(有两个选上面的)，后还需要运行（runas ->ant build） cobar-common、cobar-config、cobar-manager、cobar-net、cobar-parser、cobar-route、cobar-server工程下面的build->build.xml文件，否则会报错。，这时整个项目会按照依赖关系编译依次编译（最后出现build failure 不用管）
5、 编译之后在cobar-build\resources\server 下面会有四个文件夹分别是bin、conf、jar、logs，需要重命名文件夹 jar为lib，不然后续会报找不到路径的错误
6、conf文件夹下修改 schema.xml和Server.xml 中 dataSource 标签下面的数据库设置和用户名、设置按照如下设置。
       <dataSource name="dsTest" type="mysql">
                <property name="location">
                          <location>127.0.0.1:3306/dbtest1</location>
                          <location>127.0.0.1:3306/dbtest2</location>
                          <location>127.0.0.1:3306/dbtest3</location>
                </property>
                <property name="user">root</property>
                <property name="password">root</property>
                <property name="sqlMode">STRICT_TRANS_TABLES</property>
       </dataSource>
   
Server.xml 文件 的配置按照如下设置
<user name="test">
    <property name="password">test</property>
  </user>
7、安装mysql5.1版本数据库，使用navcat客户端进行连接
   在 MySQL中 首先建立 用户root,密码root.在该用户下面建立
   dbtest1，dbtest2，dbtest3三个库。在dbtest1下面建立 tb1表，dbtest2下面建立tb2, dbtest3下面也建立tb2表
   sql语句为
                        
#创建dbtest1
drop database if exists dbtest1;
create database dbtest1;
use dbtest1;
#在dbtest1上创建tb1
create table tb1(
id    int not null,
gmt   datetime);

#创建dbtest2
drop database if exists dbtest2;
create database dbtest2;
use dbtest2;
#在dbtest2上创建tb2
create table tb2(
id    int not null,
val   varchar(256));

#创建dbtest3
drop database if exists dbtest3;
create database dbtest3;
use dbtest3;
#在dbtest3上创建tb2
create table tb2(
id    int not null,
val   varchar(256));
       

      8、首先在 .\cobar-build\resources\server\bin路径下面双击 startup.bat命令；正常启动之后
      9、在cmd命令行中 输入  mysql –h127.0.0.1 –utest –ptest –P8066 –Ddbtest;(注意 端口大写P 密码小写p，不然会让你郁闷的，端口是8066不是3306)
10、成功之后会出现  MySQL> ,然后  执行 insert into tb2 (id,val) values (2,”part1”),(517,”part2”) 
11、成功后执行 select * from tb2  两条语句，
12、在后台用 navcat查看 dbtest2中tb2表有一条 （2，“part”），dbtest3中有一条（517，“part2”） 记录，按照rule.xml中 id是否大于512进行分库路由插入 
13、还可实验 updata,delete等语句，注意 这里 都是用逻辑表 tb2 .    
大家可以参考 http://blog.csdn.net/sunboy7890/article/details/7802514，就是 schema.xml 和 server.xml必须按照我上面的配置。谢谢

