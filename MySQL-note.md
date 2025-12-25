# 1. MySQL的客户端/服务端架构

## 1.1 如何使用MySQL

1. 启动MySQL服务端程序
2. 启动MySQL客户端程序
3. 客户端程序连接到服务端
4. 在客户端输入命令作为请求发送到服务端，服务端接收请求，处理数据，将结果返回给客户端

## 1.2 bin目录的可执行文件

MySQL的大部分安装包都包含了服务器程序和客户端程序，使用官方提供的安装包进行安装之后，`MySQL`的服务器程序和客户端程序都会被安装到我们的机器上。

MySQL的安装目录下有一个bin目录，其内部放着许多重要的可执行文件。如何在命令行执行这些可执行文件？

* 使用可执行文件的相对／绝对路径

  假设我们现在所处的工作目录是`MySQL`的安装目录，也就是`/usr/local/mysql`，我们想启动`bin`目录下的`mysqld`这个可执行文件，可以使用相对路径来启动：

  ```bash
  ./bin/mysqld
  ```

  或者直接输入`mysqld`的绝对路径也可以：

  ```bash
  /usr/local/mysql/bin/mysqld
  ```

  

* 将该`bin`目录的路径加入到环境变量`PATH`中

  每次执行一个文件都要输入一串长长的路径名贼麻烦，可以把该`bin`目录所在的路径添加到环境变量`PATH`中。

  环境变量`PATH`是一系列路径的集合，各个路径之间使用冒号`:`隔离开，比方说我的机器上的环境变量`PATH`的值就是：

  ```bash
  /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
  ```

  这个环境变量`PATH`的值表明：当在命令行输入一个命令时，系统便会在`/usr/local/bin`、`/usr/bin`、`/bin`、`/usr/sbin`、`/sbin`这些目录下依次寻找是否存在我们输入的那个命令对应的可执行文件，如果寻找成功，则执行该可执行文件。

  如果我们把`MySQL`安装目录下的`bin`目录的路径也加入到`PATH`中，在我的机器上修改后的环境变量`PATH`的值为：

  ```bash
  /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/mysql/bin
  ```

  这样现在不论我们所处的工作目录是啥，我们都可以直接输入可执行文件的名字就可以启动它，比如这样：

  ```
  mysqld
  ```

## 1.3 启动MySQL服务器/客户端程序

==MySQL服务端进程默认的进程名为`mysqld`，MySQL客户端默认的进程名为`mysql`。==

### 1.3.1 启动MySQL服务器程序

MySQL安装目录下的bin目录有很多可执行文件用于启动MySQL服务端程序：

* mac系统「具体区别不详细展开」

  * mysqld

  * mysqld_safe

  * mysql.server

  * mysqld_multi

* windows系统

  * mysqld

  * 注册为windows服务来运行mysql

    **什么是windows服务？**

    答：如果无论是谁正在使用这台计算机，我们都需要长时间的运行某个程序，而且需要在计算机启动的时候便启动它，一般我们都会把它注册为一个`Windows 服务`，操作系统会帮我们管理它。

    **如何将一个服务注册为windows服务？**

    答：

    ```css
    "完整的可执行文件路径" --install [-manual] [服务名]
    ```

    不加-manual表示windows系统启动的时候自动启动该服务；加上-manual表示不自动启动，需要手动启动。

    如果不加服务名则默认为`MySQL`

    因此如果我们想把MySQL服务器程序注册为Windows服务，可以这样写：

    ```css
    "C:\Program Files\MySQL\MySQL Server 5.7\bin\mysqld" --install
    ```

    **如何手动启动/关闭windows服务？**

    答：

    ```css
    net start [服务名]
    net stop [服务名]
    ```

    **手动开启/关闭MySQL服务器程序？**

    答：

    ```css
    net start [MySQL]
    net stop [MySQL]
    ```

### 1.3.2 启动MySQL客户端程序

bin目录下的`mysql`可执行文件用于启动MySQL客户端程序并和服务端程序建立连接。

启动命令：

```css
mysql -h主机名  -u用户名 -p密码
```

各个参数的意义如下：

| 参数名 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| `-h`   | 表示服务器进程所在计算机的域名或者IP地址，如果服务器进程就运行在本机的话，可以省略这个参数，或者填`localhost`或者`127.0.0.1`。也可以写作 `--host=主机名`的形式。 |
| `-u`   | 表示用户名。也可以写作 `--user=用户名`的形式。               |
| `-p`   | 表示密码。也可以写作 `--password=密码`的形式。               |

连接成功后的界面如图：

```css
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.21 Homebrew

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

然后就可以不断输入命令发给服务端了。

如果要断开和服务端的连接并关闭客户端，可以用以下命令：

1. quit
2. exit
3. \q

例如：

```css
mysql> quit
Bye
```

输出了`Bye`说明客户端程序已经关掉了。

注意，这是关闭客户端程序的方式，不是关闭服务器程序的方式。

## 1.4 客户端和服务端的进程通信方式

客户端和服务端程序本质上都是计算机上的一个进程，因此客户端向服务端发送请求并得到回复的过程本质上是一个进程间通信的过程。

`MySQL`支持以下三种客户端进程和服务器进程的通信方式。

* TCP/IP
* 命名管道和共享内存
* Unix域套接字文件



TCP/IP这种方式是使用得最多的，因此展开讨论一下：

MySQL采用TCP协议作为服务端和客户端之间的网络通信协议。

MySQL服务器启动的时候会默认监听3306端口，如果`3306`端口号已经被别的进程占用了或者单纯想自定义该服务端监听的端口号，那我们可以在启动服务器程序的命令行里添加`-P`参数来明确指定一下端口号：

```css
mysqld -P3307
```

如果服务器进程监听的端口号不是3306，客户端向服务端发起连接的时候，需要用-P参数明确指定要连接的端口号：

```css
mysql -h127.0.0.1 -uroot -P3307 -p
```

## 1.5 服务端处理客户端请求全流程

服务端程序处理来自客户端的查询请求需要经过三个部分，分别是`连接管理`、`解析与优化`、`存储引擎`。

![image-20251225194827575](/Users/yutinglai/Documents/note/MySQL-note/assets/image-20251225194827575.png)

### 1.5.1 连接管理

每当有一个客户端进程连接到服务器进程时，服务器进程都会创建一个线程来专门处理与这个客户端的交互。

当该客户端退出时会与服务器断开连接，服务器并不会立即把与该客户端交互的线程销毁掉，而是把它缓存起来，在另一个新的客户端再进行连接时，把这个缓存的线程分配给该新客户端。这样就起到了不频繁创建和销毁线程的效果，从而节省开销。

「`MySQL`服务器会为每一个连接进来的客户端分配一个线程，但是线程分配的太多了会严重影响系统性能，所以我们也需要限制一下可以同时连接到服务器的客户端数量。」

### 1.5.2 解析与优化

MySQL客户端和服务端成功建立连接后，客户端向服务端发送请求。此时`MySQL`服务器已经获得了文本形式的请求，在请求执行之前，还要进行以下三步的处理：

1. 查询缓存
2. 语法解析
3. 查询优化

#### 1️⃣ 查询缓存

* MySQL会将处理过的查询请求及其结果缓存起来，如果下一次有相同的查询请求过来，则会直接返回缓存的结果。
* 查询缓存在不同的客户端之间共享，例如客户端A刚刚查询了一个语句，而客户端B之后发送了同样的查询请求，那么会直接给客户端B返回客户端A的查询请求所缓存的数据。
* 两个查询请求的SQL语句必须完全相同，才能够命中缓存。空格、注释、大小写等有任何不同都无法命中。
* 如果查询请求中包含某些系统函数、用户自定义变量和函数、一些系统表，如 mysql 、information_schema、 performance_schema 数据库中的表，那这个请求就不会被缓存。例如函数NOW，每次调用都应该返回最新的当前时间，因此这样的请求结果是不会被缓存的。
* 只要缓存涉及的表被修改，则该表的所有缓存都会失效。
* 虽然查询缓存有时可以提升系统性能，但也会造成额外开销，从MySQL 5.7.20开始，不推荐使用查询缓存，并在MySQL 8.0中删除。

#### 2️⃣ 语法解析

如果查询缓存没有命中，接下来就需要进入正式的查询阶段了。

因为客户端程序发送过来的请求只是一段文本而已，所以`MySQL`服务器程序首先要对这段文本做分析：

1. 检查SQL语句语法是否正确
2. 从文本中提取出要查询的表、列、查询条件等信息

#### 3️⃣ 查询优化

此时服务端已经获得到执行请求所需的信息，例如要查询的表、列、搜索条件等。

在真正之前之前，mysql还会帮我们将SQL语句进行优化，以保证能够以最高的效率执行，例如：外连接转换为内连接、表达式简化、子查询转为连接等等。

优化之后，会生成一个执行计划，这个执行计划表明了应该使用哪些索引进行查询，表之间的连接顺序是啥样的等信息。

### 1.5.3 存储引擎

* 截止到服务器程序完成了查询优化为止，只是生成了执行计划，还没有真正的去访问真实的数据表。
* 数据的存储、修改、提取等操作由`存储引擎`负责。
* `存储引擎`负责记录在物理上如何表示记录，怎么从表中读取数据，怎么把数据写入具体的物理存储器上。
* 为了实现不同的功能，`MySQL`提供了各式各样的`存储引擎`，不同`存储引擎`管理的表具体的存储结构可能不同，采用的存取算法也可能不同。
* 为什么叫`引擎`呢？因为这个名字更拉风～ 其实这个存储引擎以前叫做`表处理器`，后来可能人们觉得太土，就改成了`存储引擎`的叫法，它的功能就是接收上层传下来的指令，然后对表中的数据进行提取或写入操作。
* 为了管理方便，人们把`连接管理`、`查询缓存`、`语法解析`、`查询优化`这些并不涉及真实数据存储的功能划分为`MySQL server`的功能，把真实存取数据的功能划分为`存储引擎`的功能。
* MySQL官方定义了存储引擎API接口，包含了几十个底层函数，像"读取索引第一条内容"、"读取索引下一条内容"、"插入记录"等等。各大存储引擎厂商去实现接口，不同的厂商可以有不同的实现。这样可以保证，不管下层的存储引擎怎么变，MySQL server调用的是同一套API。 虽然 API 是统一的，但存储引擎可以有不同的实现方式，甚至可以选择不实现某些高级功能。例如事务相关API，InnoDB 实现了它们，实现了 ACID；而 MyISAM 的实现往往是“空函数”或者直接报错，告诉 Server 它不支持事务。
* 在`MySQL server`完成了查询优化后，只需按照生成的执行计划调用底层存储引擎提供的API，获取到数据后返回给客户端就好。
* 总结：MySQL Server是大脑，负责连接管理、理解指令，优化指令，生成执行策略。存储引擎是四肢，负责数据真正的存储以及执行MySQL Server下达的指令。

#### 常用的存储引擎

InnoDB、MyISAM、Memory

==InnoDB是MySQL默认存储引擎==

#### 存储引擎相关命令

* **查看当前服务器程序支持的存储引擎**

  ```sql
  SHOW ENGINES;
  ```

  调用效果

  ```sql
  mysql> SHOW ENGINES;
  +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
  | Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
  +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
  | InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
  | MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
  | MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
  | BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
  | MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
  | CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
  | ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
  | PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
  | FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
  +--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
  9 rows in set (0.00 sec)
  
  mysql>
  
  ```

  * `Support`列表示该存储引擎是否可用。`DEFAULT`值代表是当前服务器程序的默认存储引擎。
  * `Comment`列是对存储引擎的一个描述。
  * `Transactions`列代表该存储引擎是否支持事务处理。
  * `XA`列代表着该存储引擎是否支持分布式事务。
  * `Savepoints`代表着该存储引擎是否支持部分事务回滚。

* **设置表的存储引擎**

  可以为不同的表设置不同的存储引擎，也就是说不同的表可以有不同的物理存储结构，不同的提取和写入方式。

  * **创建表时指定存储引擎**

    如果不显式指定，会使用默认的存储引擎InnoDB。

    ```sql
    CREATE TABLE 表名(
        建表语句;
    ) ENGINE = 存储引擎名称;
    ```

    ```sql
    mysql> CREATE TABLE engine_demo_table(
        ->     i int
        -> ) ENGINE = MyISAM;
    Query OK, 0 rows affected (0.02 sec)
    
    mysql>
    ```

  * **修改表的存储引擎**

    ```sql
    ALTER TABLE 表名 ENGINE = 存储引擎名称;
    ```

    ```sql
    mysql> ALTER TABLE engine_demo_table ENGINE = InnoDB;
    Query OK, 0 rows affected (0.05 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    
    mysql>
    ```

    ```sql
    # 检查存储引擎是否更改成功
    mysql> SHOW CREATE TABLE engine_demo_table\G
    *************************** 1. row ***************************
           Table: engine_demo_table
    Create Table: CREATE TABLE `engine_demo_table` (
      `i` int(11) DEFAULT NULL
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8
    1 row in set (0.01 sec)
    
    mysql>
    ```