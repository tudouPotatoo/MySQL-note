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



# 2. MySQL的启动选项&系统变量&状态变量

启动选项：启动MySQL程序时指定的设置项称为启动选项，这些选项控制着程序启动后的行为。

系统变量：指的是MySQL运行过程中会用到的影响程序行为的变量，例如允许同时接入的客户端数量、默认的存储引擎。大部分系统变量可以通过启动选项传入，部分系统变量是程序运行过程中自动生成的，例如timestamp系统变量，代表当前时间戳，这个是MySQL运行过程中自动生成的，无法通过启动选项传入。

状态变量：MySQL运行过程中维护的和程序运行状态相关的变量，以便于我们更好地了解程序的运行情况，例如当前和服务器建立连接的客户端的数量。



类比：

在游戏中，你可以配置你要携带的武器，你的坐骑，你的服装。你所配置的这些东西在游戏开始时会生效，影响着你的攻击力、攻击方式、速度、行为等。因此你的武器、坐骑、服装，都属于系统变量，因为这些值都影响着你在游戏的表现。而这些系统变量，你可以在游戏开始之前配置（启动选项），也可以在游戏中途暂停进行配置。在游戏的过程中，你会有精力值、血条，这些并不是你能主动控制的值，而是游戏系统计算出来代表你当前角色状况的变量，这些就叫做状态变量。

## 2.1 如何配置启动选项

### 命令行方式

**启动MySQL程序时**，可以通过命令行设置启动选项。

其格式为

```css
可执行文件名 --启动选项1[=值1] --启动选项2[=值2] ... --启动选项n[=值n]
```

示例：

* 禁止MySQL服务器通过TCP/IP方式和客户端进行通信

  ```css
  mysqld --skip-networking
  # 或
  mysqld --skip_networking
  ```

  

* 修改默认使用的存储引擎

  ```css
  mysqld --default-storage-engine=MyISAM
  ```

  

==注意事项：==

* 通过命令行设置的启动选项只对当次启动生效。

* 为方便，很多启动选项不仅有长形式也具有短形式，例如以下列出的一些：

  | 长形式       | 短形式 | 含义     |
  | ------------ | ------ | -------- |
  | `--host`     | `-h`   | 主机名   |
  | `--user`     | `-u`   | 用户名   |
  | `--password` | `-p`   | 密码     |
  | `--port`     | `-P`   | 端口     |
  | `--version`  | `-V`   | 版本信息 |

* 如果选项名是长形式，则前面加`--`，如果选项名由多个单词构成的，它们之间可以由短划线`-`连接起来，也可以使用下划线`_`连接起来，例如`--skip-networking`和`--skip_networking`是等价的；

  短形式选项名前仅需要一个 `-` 前缀；

* 长形式指定启动选项时，选项名、=、选项值之间不可以有空白字符，例如`--default-storage-engine = MyISAM`是错误的；

  短形式指定启动选项时，选项名和选项值之间可以没有间隙，或者用空白字符隔开（`-p`选项有些特殊，`-p`和密码值之间不能有空白字符）。例如`mysqld -P3307`和`mysqld -P 3307`等价；

* 每个MySQL程序都提供`--help`选项以查看程序支持的所有启动选项及其默认值。例如`mysql --help`、`mysqld_safe --help`，只有mysqld比较特殊`mysqld --verbose -help`；

* 选项名是区分大小写的，比如`-p`和`-P`选项拥有完全不同的含义；



### 配置文件方式

**1. 配置文件在哪**

以下路径任何一个都可以当作配置文件。

* windows

  | 路径名                                | 备注                         |
  | ------------------------------------- | ---------------------------- |
  | `%WINDIR%\my.ini`， `%WINDIR%\my.cnf` |                              |
  | `C:\my.ini`， `C:\my.cnf`             |                              |
  | `BASEDIR\my.ini`， `BASEDIR\my.cnf`   |                              |
  | `defaults-extra-file`                 | 命令行指定的额外配置文件路径 |
  | `%APPDATA%\MySQL\.mylogin.cnf`        | 登录路径选项（仅限客户端）   |

* 类unix

  | 路径名                | 备注                                 |
  | --------------------- | ------------------------------------ |
  | `/etc/my.cnf`         |                                      |
  | `/etc/mysql/my.cnf`   |                                      |
  | `SYSCONFDIR/my.cnf`   |                                      |
  | `$MYSQL_HOME/my.cnf`  | 特定于服务器的选项（仅限服务器）     |
  | `defaults-extra-file` | 命令行指定的额外配置文件路径         |
  | `~/.my.cnf`           | 用户特定选项                         |
  | `~/.mylogin.cnf`      | 用户特定的登录路径选项（仅限客户端） |



**2. 配置文件的内容**

```css
[server]
(具体的启动选项...)
option1     #这是option1，该选项不需要选项值
option2 = value2      #这是option2，该选项需要选项值

[mysqld]
(具体的启动选项...)

[mysqld_safe]
(具体的启动选项...)

[client]
(具体的启动选项...)

[mysql]
(具体的启动选项...)

[mysqladmin]
(具体的启动选项...)
```

* 配置文件中定义了多个组，每个组下面可以定义该组的启动选项。

  例如[mysqld]组定义的启动选项应用于mysqld服务器程序

  例如[mysql]组定义的启动选项应用于mysql客户端程序

* 用于启动服务端的程序都会读取[server]组的配置

  用于启动客户端的程序都会读取[client]组的配置

  各个启动命令及其能够读取的组信息如下：

  | 启动命令       | 类别       | 能读取的组                               |
  | -------------- | ---------- | ---------------------------------------- |
  | `mysqld`       | 启动服务器 | `[mysqld]`、`[server]`                   |
  | `mysqld_safe`  | 启动服务器 | `[mysqld]`、`[server]`、`[mysqld_safe]`  |
  | `mysql.server` | 启动服务器 | `[mysqld]`、`[server]`、`[mysql.server]` |
  | `mysql`        | 启动客户端 | `[mysql]`、`[client]`                    |
  | `mysqladmin`   | 启动客户端 | `[mysqladmin]`、`[client]`               |
  | `mysqldump`    | 启动客户端 | `[mysqldump]`、`[client]`                |

  例如配置文件内容如下：

  ```ini
  [server]
  skip-networking
  default-storage-engine=MyISAM
  ```

  使用`mysqld`命令启动服务器程序

  则最终`skip-networking`和`default-storage-engine=MyISAM`这两个选项会生效。

* 配置文件中只能使用启动选项的长形式
* 启动选项不允许加`--`前缀
* 每行只指定一个选项
* `=`周围可以有空白字符
* 可以使用`#`来添加注释



**3. 配置文件优先级**

* 若多个配置文件配置了相同的启动选项，以最后一个文件为准

  MySQL会以上面表中配置文件的顺序来扫描各个配置文件。如果在多个配置文件中设置了相同的启动选项，那以最后一个配置文件中的为准。

  比方说`/etc/my.cnf`文件的内容是这样的：

  ```ini
  [server]
  default-storage-engine=InnoDB
  ```

  而`~/.my.cnf`文件中的内容是这样的：

  ```ini
  [server]
  default-storage-engine=MyISAM
  ```

  因为`~/.my.cnf`比`/etc/my.cnf`顺序靠后，所以以`~/.my.cnf`中的为准，所以`MySQL`服务器程序启动之后，`default-storage-engine`的值就是`MyISAM`。



* 同一个配置文件中多个组配置了相同的启动选项，以最后一个组的为准

  我们知道同一个启动命令可以访问配置文件中的多个组，比如`mysqld`可以访问`[mysqld]`、`[server]`组。

  如果在同一个配置文件中，多个组出现了同样的配置项，比如这样：

  ```ini
  [server]
  default-storage-engine=InnoDB
  
  [mysqld]
  default-storage-engine=MyISAM
  ```

  那么，将以最后一个出现的组中的启动选项为准，例子中`default-storage-engine`既出现在`[mysqld]`组也出现在`[server]`组，因为`[mysqld]`组在`[server]`组后边，就以`[mysqld]`组中的配置项为准。

  

==注意事项==：

* 通过配置文件设置的启动选项是长久生效的。
* 如果同一个启动选项既出现在命令行中，又出现在配置文件中，那么以命令行中的启动选项为准。



## 2.2 系统变量

每个系统变量都有默认值，大多数系统变量可以在程序启动时通过启动选项设置，也可以在程序运行过程中修改。

### 系统变量的作用范围

* `GLOBAL`：全局变量。
* `SESSION` / `LOCAL`：会话变量，作用范围为单个客户端。

==注意事项：==

- 并不是所有系统变量都具有`GLOBAL`和`SESSION`的作用范围。
  - 有一些系统变量只具有`GLOBAL`作用范围，比方说`max_connections`，表示服务器程序支持同时最多有多少个客户端程序进行连接。
  - 有一些系统变量只具有`SESSION`作用范围，比如`insert_id`，表示在对某个包含`AUTO_INCREMENT`列的表进行插入时，该列初始的值。
  - 有一些系统变量的值既具有`GLOBAL`作用范围，也具有`SESSION`作用范围，比如我们前边用到的`default_storage_engine`，而且其实大部分的系统变量都是这样的，



### 设置系统变量

通过启动选项设置，见2.1。

启动选项设置的系统变量的作用范围都是`GLOBAL`的，也就是对所有客户端都有效的。



### 修改系统变量

```ini
SET [GLOBAL|SESSION] 系统变量名 = 值;
# 或
SET [@@(GLOBAL|SESSION).]var_name = XXX;
```

比如我们想在服务器运行过程中把作用范围为`GLOBAL`的系统变量`default_storage_engine`的值修改为`MyISAM`，也就是想让之后新连接到服务器的客户端都用`MyISAM`作为默认的存储引擎。

设置：

```ini
语句一：SET GLOBAL default_storage_engine = MyISAM;
语句二：SET @@GLOBAL.default_storage_engine = MyISAM;
```

如果只想对本客户端生效，也可以选择下边三条语句中的任意一条来进行设置：

```ini
语句一：SET SESSION default_storage_engine = MyISAM;
语句二：SET @@SESSION.default_storage_engine = MyISAM;
语句三：SET default_storage_engine = MyISAM;
```

从上边的`语句三`也可以看出，如果在设置系统变量的语句中省略了作用范围，默认的作用范围就是`SESSION`。也就是说`SET 系统变量名 = 值`和`SET SESSION 系统变量名 = 值`是等价的。



==注意事项：==

* 如果某个客户端改变了某个系统变量在`GLOBAL`作用范围的值，并不会影响该系统变量在当前已经连接的客户端作用范围为`SESSION`的值，只会影响后续连入的客户端在作用范围为`SESSION`的值。

* 如果我当前session使用的存储引擎是innodb，运行一段时间后修改为MyISAM，那已有的表的结构会发生改变吗，还是只会影响新创建的表的行为？

  简单直接的回答是：**已有表的结构完全不会发生改变，只会影响之后新创建的表的行为。**

  我们可以把 `default_storage_engine` 这个系统变量理解为**“新建表的模版选项”**。修改它，只是换了一套新的默认模版，并不会去回溯修改已经盖好的“房子”。

- 有些系统变量是只读的，并不能设置值。

  比方说`version`，表示当前`MySQL`的版本，我们客户端是不能设置它的值的，只能在`SHOW VARIABLES`语句里查看。



### 查看系统变量

**指定要查看某个作用范围的系统变量**

命令：

```sql
SHOW [GLOBAL|SESSION] VARIABLES [LIKE 匹配的模式];
```

示例：

```sql
mysql> SHOW SESSION VARIABLES LIKE 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
+------------------------+--------+
1 row in set (0.00 sec)

mysql> SHOW GLOBAL VARIABLES LIKE 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
+------------------------+--------+
1 row in set (0.00 sec)

mysql> SET SESSION default_storage_engine = MyISAM;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW SESSION VARIABLES LIKE 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | MyISAM |
+------------------------+--------+
1 row in set (0.00 sec)

mysql> SHOW GLOBAL VARIABLES LIKE 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
+------------------------+--------+
1 row in set (0.00 sec)

mysql>

```



**不指定作用范围，默认是查看SESSION作用范围的系统变量**

```sql
SHOW VARIABLES [LIKE 匹配的模式];
```

例子

```sql
mysql> SHOW VARIABLES LIKE 'default%';
+-------------------------------+-----------------------+
| Variable_name                 | Value                 |
+-------------------------------+-----------------------+
| default_authentication_plugin | mysql_native_password |
| default_password_lifetime     | 0                     |
| default_storage_engine        | InnoDB                |
| default_tmp_storage_engine    | InnoDB                |
| default_week_format           | 0                     |
+-------------------------------+-----------------------+
5 rows in set (0.01 sec)

mysql>
```





## 2.3 状态变量

由于`状态变量`是用来显示服务器程序运行状况的，所以它们的值只能由服务器程序自己来设置，程序员是不能设置的。

`状态变量`也有`GLOBAL`和`SESSION`两个作用范围，查看`状态变量`的语句如下：

```ini
SHOW [GLOBAL|SESSION] STATUS [LIKE 匹配的模式];
```

类似的，如果我们不写作用范围，默认的作用范围是`SESSION`，比方说这样：

```sql
mysql> SHOW STATUS LIKE 'thread%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_cached    | 0     |
| Threads_connected | 1     |
| Threads_created   | 1     |
| Threads_running   | 1     |
+-------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

所有以`Thread`开头的`SESSION`作用范围的状态变量就都被展示出来了。