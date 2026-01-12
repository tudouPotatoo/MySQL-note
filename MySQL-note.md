# e 1. MySQL的客户端/服务端架构

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



# 3. 字符集 & 比较规则

## 3.1 字符集

### 是什么

我们知道计算机只能存储二进制数据，那如何存储字符呢？就是通过建立字符和二进制数据的映射关系，要建立这种映射关系，需要确定两件事：

1. 给哪些字符建立映射关系

   即确定字符的范围

2. 如何建立映射关系

   将一个字符映射为二进制数据的过程，称为编码。

   将一个二进制数据映射为字符的过程，称为解码。



>题外话
>
>既然计算机只能存储二进制数据，那是怎么在屏幕中显示出字符的样子？
>
>在 ASCII 中，大写字母 **'A'** 的编号是 `65`。分析一下怎么从硬盘的65转化为屏幕上的A。
>
>计算机从硬盘读到二进制数据 -> 计算得出数字为65 -> 字库中记录着每一个编号及其对应的形状描述（**点阵字体：** 像拼图一样，用一个个小方块（像素点）拼出形状。）-> 显卡驱动程序根据绘图指令，计算出屏幕上哪些像素点应该发光，哪些不发光。 -> 人眼看到A。



人们抽象出一个`字符集`的概念来描述某个字符范围的编码规则。比方说我们来自定义一个名称为`xiaohaizi`的字符集，它包含的字符范围和编码规则如下：

- 包含字符`'a'`、`'b'`、`'A'`、`'B'`。

- 编码规则如下：

  采用1个字节编码一个字符的形式，字符和字节的映射关系如下：

  ```css
  'a' -> 00000001 (十六进制：0x01)
  'b' -> 00000010 (十六进制：0x02)
  'A' -> 00000011 (十六进制：0x03)
  'B' -> 00000100 (十六进制：0x04)
  ```

有了`xiaohaizi`字符集，我们就可以用二进制形式表示一些字符串了，下边是一些字符串用`xiaohaizi`字符集编码后的二进制表示：

```css
'bA' -> 00000010 00000011  (十六进制：0x0203)
'baB' -> 00000010 00000001 00000100  (十六进制：0x020104)
'cd' -> 无法表示，字符集xiaohaizi不包含字符'c'和'd'
```



### 常见的字符集

* `ASCII`字符集

  * 共收录128个字符，包括空格、标点符号、**数字**、**大小写字母**和一些不可见字符。

  * 使用1个字节来进行编码。

  * 我们看一些字符的编码方式：

    ```css
    'L' ->  01001100（十六进制：0x4C，十进制：76）
    'M' ->  01001101（十六进制：0x4D，十进制：77）
    ```

* `ISO 8859-1`字符集（别名`latin1`）

  * 共收录256个字符，是在`ASCII`字符集的基础上又扩充了128个西欧常用字符(包括德法两国的字母)
  * 使用1个字节来进行编码。

* `GB2312`字符集

  * 收录了**汉字**以及拉丁字母、希腊字母、日文平假名及片假名字母、俄语西里尔字母。其中收录汉字6763个，其他文字符号682个。

  * 兼容`ASCII`字符集，编码方式如下：

    - 如果该字符在`ASCII`字符集中，则采用1字节编码。

    - 否则采用2字节编码。

    - 这种表示一个字符需要的字节数可能不同的编码方式称为`变长编码方式`。比方说字符串`'爱u'`，其中`'爱'`需要用2个字节进行编码，编码后的十六进制表示为`0xB0AE`，`'u'`需要用1个字节进行编码，编码后的十六进制表示为`0x75`，所以拼合起来就是`0xB0AE75`。

    - 问题：怎么区分某个字节代表一个单独的字符还是代表某个字符的一部分呢？

      首先，GB2312字符集保证，如果是单字节字符，最高位是0。如果是多字节字符，最高位是1。

      第二，计算机解析GB2312文本流的时候，是从左往右解析的，它在读取到一个字节的时候，会先检查最高位是不是1，如果是1，则会直接认为是多字节字符，会连续读两个字节。如果不是1，就认为是单字节字符，只读一个字节。

       所以，就算出现了多字节字符第二个字节值在范围在0-127之间，也没关系，因为它会根据第一个字节的最高位来确定第二个字节是多字节字符的第二个字节还是仅仅是单字节字符。

* `GBK`字符集

  * `GBK`字符集只是在收录字符范围上对`GB2312`字符集作了扩充，编码方式上兼容`GB2312`。

* `utf8`字符集

  * 收录地球上能想到的所有字符，而且还在不断扩充。这种字符集兼容`ASCII`字符集，采用变长编码方式，编码一个字符需要使用1～4个字节。

  * 准确的说，utf8只是Unicode字符集的一种编码方案，Unicode字符集可以采用utf8、utf16、utf32这几种编码方案，utf8使用1～4个字节编码一个字符，utf16使用2个或4个字节编码一个字符，utf32使用4个字节编码一个字符。

  * 由于常用的字符使用1-3个字节表示就足够了，MySQL为了性能考虑，定义了utfmb3和utfmb4：

    * `utf8mb3`：阉割过的`utf8`字符集，只使用1～3个字节表示字符。
    * `utf8mb4`：正宗的`utf8`字符集，使用1～4个字节表示字符。

    在MySQL中`utf8`是`utf8mb3`的别名。



## 3.2 比较规则

是什么：我们一个字符集中可以有很多个字符，比较规则就是用来比较字符之间的大小的。

作用：通常体现在比较字符串大小的表达式 & 对某个字符串列进行***排序***。

> 在对字符串做比较或者对某个字符串列做排序操作时没有得到想象中的结果，可以思考一下是不是`比较规则`的问题



## 3.3 MySQL支持的字符集 & 比较规则

### 1️⃣查看当前MySQL支持的字符集

```sql
SHOW (CHARACTER SET|CHARSET) [LIKE 匹配的模式];
```

示例：

* Charset列：表示字符集的名称。
* Default collation列：表示这种字符集默认使用的`比较规则`。
* MaxLen列：表示字符集表示一个字符最多需要几个字节。

```sql
mysql> SHOW CHARSET;
+----------+---------------------------------+---------------------+--------+
| Charset  | Description                     | Default collation   | Maxlen |
+----------+---------------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese        | big5_chinese_ci     |      2 |
...
| latin1   | cp1252 West European            | latin1_swedish_ci   |      1 |
| latin2   | ISO 8859-2 Central European     | latin2_general_ci   |      1 |
...
| ascii    | US ASCII                        | ascii_general_ci    |      1 |
...
| gb2312   | GB2312 Simplified Chinese       | gb2312_chinese_ci   |      2 |
...
| gbk      | GBK Simplified Chinese          | gbk_chinese_ci      |      2 |
| latin5   | ISO 8859-9 Turkish              | latin5_turkish_ci   |      1 |
...
| utf8     | UTF-8 Unicode                   | utf8_general_ci     |      3 |
| ucs2     | UCS-2 Unicode                   | ucs2_general_ci     |      2 |
...
| latin7   | ISO 8859-13 Baltic              | latin7_general_ci   |      1 |
| utf8mb4  | UTF-8 Unicode                   | utf8mb4_general_ci  |      4 |
| utf16    | UTF-16 Unicode                  | utf16_general_ci    |      4 |
| utf16le  | UTF-16LE Unicode                | utf16le_general_ci  |      4 |
...
| utf32    | UTF-32 Unicode                  | utf32_general_ci    |      4 |
| binary   | Binary pseudo charset           | binary              |      1 |
...
| gb18030  | China National Standard GB18030 | gb18030_chinese_ci  |      4 |
+----------+---------------------------------+---------------------+--------+
41 rows in set (0.01 sec)
```



### 2️⃣查看当前MySQL支持的比较规则

```sql
SHOW COLLATION [LIKE 匹配的模式];
```

示例：

* Collation列：比较规则的名称。

  * 比较规则名称以与其关联的字符集的名称开头

  * 字符集名称后紧跟着作用的语言，比如`utf8_polish_ci`表示以波兰语的规则比较，`utf8_spanish_ci`是以西班牙语的规则比较，`utf8_general_ci`是一种通用的比较规则

  * 名称后缀意味着该比较规则是否区分语言中的重音、大小写等，具体可以用的值如下：

    | 后缀   | 英文释义             | 描述             |
    | ------ | -------------------- | ---------------- |
    | `_ai`  | `accent insensitive` | 不区分重音       |
    | `_as`  | `accent sensitive`   | 区分重音         |
    | `_ci`  | `case insensitive`   | 不区分大小写     |
    | `_cs`  | `case sensitive`     | 区分大小写       |
    | `_bin` | `binary`             | 以二进制方式比较 |

    比如`utf8_general_ci`这个比较规则是以`ci`结尾的，说明不区分大小写。

* Charset列：可以用于的字符集。

* Default列：`Default`列的值为`YES`的就是该字符集的默认比较规则。因此utf8字符集默认的比较规则为utf8_general_ci。

```sql
mysql> SHOW COLLATION LIKE 'utf8\_%';
+--------------------------+---------+-----+---------+----------+---------+
| Collation                | Charset | Id  | Default | Compiled | Sortlen |
+--------------------------+---------+-----+---------+----------+---------+
| utf8_general_ci          | utf8    |  33 | Yes     | Yes      |       1 |
| utf8_bin                 | utf8    |  83 |         | Yes      |       1 |
| utf8_unicode_ci          | utf8    | 192 |         | Yes      |       8 |
| utf8_icelandic_ci        | utf8    | 193 |         | Yes      |       8 |
| utf8_latvian_ci          | utf8    | 194 |         | Yes      |       8 |
| utf8_romanian_ci         | utf8    | 195 |         | Yes      |       8 |
| utf8_slovenian_ci        | utf8    | 196 |         | Yes      |       8 |
| utf8_polish_ci           | utf8    | 197 |         | Yes      |       8 |
| utf8_estonian_ci         | utf8    | 198 |         | Yes      |       8 |
| utf8_spanish_ci          | utf8    | 199 |         | Yes      |       8 |
| utf8_swedish_ci          | utf8    | 200 |         | Yes      |       8 |
| utf8_turkish_ci          | utf8    | 201 |         | Yes      |       8 |
| utf8_czech_ci            | utf8    | 202 |         | Yes      |       8 |
| utf8_danish_ci           | utf8    | 203 |         | Yes      |       8 |
| utf8_lithuanian_ci       | utf8    | 204 |         | Yes      |       8 |
| utf8_slovak_ci           | utf8    | 205 |         | Yes      |       8 |
| utf8_spanish2_ci         | utf8    | 206 |         | Yes      |       8 |
| utf8_roman_ci            | utf8    | 207 |         | Yes      |       8 |
| utf8_persian_ci          | utf8    | 208 |         | Yes      |       8 |
| utf8_esperanto_ci        | utf8    | 209 |         | Yes      |       8 |
| utf8_hungarian_ci        | utf8    | 210 |         | Yes      |       8 |
| utf8_sinhala_ci          | utf8    | 211 |         | Yes      |       8 |
| utf8_german2_ci          | utf8    | 212 |         | Yes      |       8 |
| utf8_croatian_ci         | utf8    | 213 |         | Yes      |       8 |
| utf8_unicode_520_ci      | utf8    | 214 |         | Yes      |       8 |
| utf8_vietnamese_ci       | utf8    | 215 |         | Yes      |       8 |
| utf8_general_mysql500_ci | utf8    | 223 |         | Yes      |       1 |
+--------------------------+---------+-----+---------+----------+---------+
27 rows in set (0.00 sec)
```



### 3️⃣MySQL各级别的字符集 & 比较规则

`MySQL`有4个级别的字符集和比较规则，分别是：

- 服务器级别
- 数据库级别
- 表级别
- 列级别

#### 服务器级别

* `MySQL`提供了两个系统变量来表示服务器级别的字符集和比较规则：

  | 系统变量               | 描述                 |
  | ---------------------- | -------------------- |
  | `character_set_server` | 服务器级别的字符集   |
  | `collation_server`     | 服务器级别的比较规则 |

  查看这两个系统变量的值：

  ```sql
  mysql> SHOW VARIABLES LIKE 'character_set_server';
  +----------------------+-------+
  | Variable_name        | Value |
  +----------------------+-------+
  | character_set_server | utf8  |
  +----------------------+-------+
  1 row in set (0.00 sec)
  
  mysql> SHOW VARIABLES LIKE 'collation_server';
  +------------------+-----------------+
  | Variable_name    | Value           |
  +------------------+-----------------+
  | collation_server | utf8_general_ci |
  +------------------+-----------------+
  1 row in set (0.00 sec)
  ```

  可以通过启动选项/配置文件/程序运行过程中用SET语句来修改这些变量的值。



#### 数据库级别

* 如果不显式指定，将使用服务器级别的字符集和比较规则作为数据库的字符集和比较规则。

* 数据库级别的字符集和比较规则对应的系统变量

  | 系统变量                 | 描述                 |
  | ------------------------ | -------------------- |
  | `character_set_database` | 当前数据库的字符集   |
  | `collation_database`     | 当前数据库的比较规则 |

  查看这两个系统变量的值：

  ```sql
  mysql> USE charset_demo_db;
  Database changed
  
  mysql> SHOW VARIABLES LIKE 'character_set_database';
  +------------------------+--------+
  | Variable_name          | Value  |
  +------------------------+--------+
  | character_set_database | gb2312 |
  +------------------------+--------+
  1 row in set (0.00 sec)
  
  mysql> SHOW VARIABLES LIKE 'collation_database';
  +--------------------+-------------------+
  | Variable_name      | Value             |
  +--------------------+-------------------+
  | collation_database | gb2312_chinese_ci |
  +--------------------+-------------------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  ***character_set_database*** 和 ***collation_database*** 这两个系统变量是只读的，我们不能通过修改这两个变量的值而改变当前数据库的字符集和比较规则。

  

* 创建数据库时指定字符集和比较规则

  ```sql
  CREATE DATABASE 数据库名
      [[DEFAULT] CHARACTER SET 字符集名称]
      [[DEFAULT] COLLATE 比较规则名称];
  ```

* 修改数据库的字符集和比较规则

  ```sql
  ALTER DATABASE 数据库名
      [[DEFAULT] CHARACTER SET 字符集名称]
      [[DEFAULT] COLLATE 比较规则名称];
  ```

​	

#### 表级别

* 如果创建表的语句中没有指明字符集和比较规则，将使用该表所在数据库的字符集和比较规则作为该表的字符集和比较规则。

* 我们也可以在创建和修改表的时候指定表的字符集和比较规则，语法如下：

  ```sql
  CREATE TABLE 表名 (列的信息)
      [[DEFAULT] CHARACTER SET 字符集名称]
      [COLLATE 比较规则名称]]
  
  ALTER TABLE 表名
      [[DEFAULT] CHARACTER SET 字符集名称]
      [COLLATE 比较规则名称]
  ```

  

#### 列级别

* 对于某个列来说，如果在创建和修改的语句中没有指明字符集和比较规则，将使用该列所在表的字符集和比较规则作为该列的字符集和比较规则。

* 对于存储字符串的列，同一个表中的不同的列也可以有不同的字符集和比较规则。我们在创建和修改列定义的时候可以指定该列的字符集和比较规则，语法如下：

  ```sql
  CREATE TABLE 表名(
      列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称],
      其他列...
  );
  
  ALTER TABLE 表名 MODIFY 列名 字符串类型 [CHARACTER SET 字符集名称] [COLLATE 比较规则名称];
  ```

* 在转换列的字符集时需要注意，如果转换前列中存储的数据不能用转换后的字符集进行表示会发生错误。比方说原先列使用的字符集是utf8，列中存储了一些汉字，现在把列的字符集转换为ascii的话就会出错，因为ascii字符集并不能表示汉字字符。



==注意事项：==

* 仅修改字符集或仅修改比较规则

  由于字符集和比较规则是互相有联系的，如果我们只修改了字符集，比较规则也会跟着变化，如果只修改了比较规则，字符集也会跟着变化，具体规则如下：

  - 只修改字符集，则比较规则将变为修改后的字符集默认的比较规则。

  - 只修改比较规则，则字符集将变为修改后的比较规则对应的字符集。



## 3.4 服务端 & 客户端通信过程使用的字符集

### 为什么会出现乱码

如果客户端用字符集A编码对字符进行编码，而服务端用字符集B对字符进行解码，最终效果就是会出现乱码。

例如：

`我` --- utf8编码 ---> `E68891`（十六进制）

`E68891` --- GBK解码 ---> `鎴?`



### MySQL服务端 & 客户端通信过程的字符集转换

客户端发送请求给服务端，本质上是发了一个字符串给服务端，这个字符串也是通过二进制的方式编码之后传输到服务端的。

在服务端，这个字符串需要经过经过多次字符集的转换，这会涉及三个服务端的系统变量：

| 系统变量                   | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `character_set_client`     | 服务器解码请求时使用的字符集                                 |
| `character_set_connection` | 服务器处理请求时会把请求字符串从`character_set_client`转为`character_set_connection` |
| `character_set_results`    | 服务器向客户端返回数据时使用的字符集                         |

具体的转换过程如下：

以客户端发送`SELECT * FROM t WHERE s = '我';`请求为例介绍

1. 客户端将整个请求字符串进行编码，发送给服务端

   客户端使用的字符集和客户端OS相关，如下：

   - 类`Unix`系统使用的是`utf8`
   - `Windows`使用的是`gbk`

2. 服务端接收到二进制形式的请求，它会认为这个二进制字节采用的是character_set_client对应的字符集编码，因此服务端会以相同的字符集进行解码。

   然后服务端会以character_set_connection字符集进行编码

3. 如果s列使用的字符集和character_set_connection字符集不一致，则还需要转换为s列使用的字符集
4. 查询到结果之后，会从s列使用的字符集解码，用character_set_results字符集编码发送给客户端
5. 最后客户端再对结果进行解码

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/image-20251227205525372.png" alt="image-20251227205525372" style="zoom:50%;" />

==注意事项：==

* `character_set_client`、`character_set_connection`、`character_set_results`三个系统变量既有GLOBAL范围，也有SESSION范围。

* `character_set_connection`包含的字符范围一定要 >= `character_set_client`包含的字符范围。

  比如你把`character_set_connection`设置成`ascii`，把`character_set_client`设置为`utf8`，那么此时你如果从客户端发送一个汉字到服务器，那么服务器无法使用`ascii`字符集来编码这个汉字，就会向用户发出一个警告。

* 为了避免进行过多无谓的转换，通常都把 `character_set_client`*** 、***`character_set_connection`、`character_set_results`这三个系统变量设置成和客户端使用的字符集一致的情况。

  * 服务端设置

    为了方便设置，`MySQL`提供了一条非常简便的语句：
  
    ```sql
    SET NAMES 字符集名;
    ```

    这一条语句产生的效果和我们执行这3条的效果是一样的：
  
    ```sql
    SET character_set_client = 字符集名;
    SET character_set_connection = 字符集名;
    SET character_set_results = 字符集名;
    ```
  
  * 客户端设置
  
    如果你想在启动客户端的时候就把`character_set_client`、`character_set_connection`、`character_set_results`这三个系统变量的值设置成一样的，那我们可以在启动客户端的时候指定一个叫`default-character-set`的启动选项，比如在配置文件里可以这么写：
  
    ```sql
    [client]
    default-character-set=utf8
    ```
  
    它起到的效果和执行一遍`SET NAMES utf8`是一样的，都会将那三个系统变量的值设置成`utf8`。

* 为什么要拆成入口、处理、出口三个系统变量，而不是直接统一它们的值？

  * ==***注意：这三个系统变量99%的情况下都可以统一，只有极端情况下才需要分开设置。***==

  * 为什么character_set_client和character_set_results不能合并为一个？

    举例：

    入口：HR通过浏览器录入新员工信息，现代浏览器和网页前端都是 **UTF-8**，则网页把“张三”两个字通过UTF8编码发给了MySQL服务器存储。

    出口：财务部门需要将所有员工的工资条导出一个 **CSV 文件**，然后在他们本地的 **旧版 Excel** 软件里打开。由于这个旧版的Excel软件不支持UTF编码，仅支持gbk。如果MySQL服务端直接返回UTF-8编码的员工信息内容，用该Excel打开会乱码。

    因此需要财务的下载脚本在请求数据时，告诉数据库：`SET character_set_results = gbk`。这样MySQL返回的是gbk格式的数据，用Excel打开的时候才不会乱码。

    因此入口和出口拆开是因为入口和出口支持的编码方式并不一定一致。

  * 为什么character_set_client和character_set_connection不能合并为一个？

    本质上会先从character_set_client转化为character_set_connection再转化为表/列使用的字符集

    表/列使用的字符集可能和character_set_client是不同的，因此是需要转化的。

    那为什么不能直接从character_set_client转化到表/列使用的字符集，而需要一个中间人character_set_connection？

    我认为是为了便利于内部逻辑的计算。



# 4. InnoDB记录结构

我们知道，存储引擎是真正负责数据的存储和读写工作的。

MySQL服务器支持不同类型的存储引擎，例如InnoDB、MyISAM、Memory等，不同的存储引擎是为实现不同特性而开发的。

数据在不同的存储引擎中存放的格式各不相同，由于InnoDB是MySQL默认存储引擎，也是被最广泛使用的存储引擎，因此本章介绍数据在InnoDB存储引擎的具体存储格式。

## 4.1 行格式是什么

记录在磁盘中存放的方式称为行格式 / 记录格式。

InnoDB有四种可选的行格式，它们的原理大体是相同的：

* Compact（MySQL 5.7.9之前默认行格式）
* Redundant
* Dynamic（MySQL 5.7.9及其之后的版本的默认行格式）
* Compressed



Compact和Dynamic大致原理相同，主要区别在于行溢出的处理方式，下面会介绍到。



## 4.2 行格式相关SQL命令

```sql
# 创建表时指定行格式
CREATE TABLE 表名 (列的信息) ROW_FORMAT=行格式名称
# 修改表的行格式
ALTER TABLE 表名 ROW_FORMAT=行格式名称
```

示例：

在xiaohaizi数据库中创建一个record_format_demo表，并制定其行格式

```sql
mysql> USE xiaohaizi;
Database changed

mysql> CREATE TABLE record_format_demo (
    ->     c1 VARCHAR(10),
    ->     c2 VARCHAR(10) NOT NULL,
    ->     c3 CHAR(10),
    ->     c4 VARCHAR(10)
    -> ) CHARSET=ascii ROW_FORMAT=COMPACT;
Query OK, 0 rows affected (0.03 sec)
```



## 4.3 Compact行格式

> 我们向4.2示例中的record_format_demo表中插入两条数据，作为例子介绍Compact行格式
>
> ```sql
> mysql> INSERT INTO record_format_demo(c1, c2, c3, c4) VALUES('aaaa', 'bbb', 'cc', 'd'), ('eeee', 'fff', NULL, NULL);
> Query OK, 2 rows affected (0.02 sec)
> Records: 2  Duplicates: 0  Warnings: 0
> 
> mysql> SELECT * FROM record_format_demo;
> +------+-----+------+------+
> | c1   | c2  | c3   | c4   |
> +------+-----+------+------+
> | aaaa | bbb | cc   | d    |
> | eeee | fff | NULL | NULL |
> +------+-----+------+------+
> 2 rows in set (0.00 sec)
> 
> mysql>
> ```



从图中可以看出，一条完整的记录可以分为 `记录的额外信息` 和 `记录的真实数据` 两大部分。

![image-20251228145129897](/Users/yutinglai/Documents/note/MySQL-note/assets/image-20251228145129897.png)

### 1) 记录的额外信息

#### 1️⃣变长字段长度列表

##### 是什么

MySQL支持一些变长的数据类型，比如`VARCHAR(M)`、`VARBINARY(M)`、`TEXT`、`BLOB`，这些数据类型对应的列称为`变长字段`。

由于变长字段占用的字节数是不确定的，因此需要单独记录下来。

一个变长字段占用的存储空间分为两部分：

1. 真正的数据内容
2. 占用的**字节数**（*注意，是占用的字节数，而不是字段长度*）

在`Compact`行格式中，所有变长字段实际占用的字节长度都存放在记录的开头部位，形成一个变长字段长度列表，各变长字段数据占用的字节数按照列的顺序***逆序*** 存放。



我们拿`record_format_demo`表中的第一条记录举例。因为`record_format_demo`表的`c1`、`c2`、`c4`列都是`VARCHAR(10)`类型的，也就是变长的数据类型，所以这三个列的值的长度都需要保存在记录开头处，因为`record_format_demo`表中的各个列都使用的是`ascii`字符集，所以每个字符只需要1个字节来进行编码，来看一下第一条记录各变长字段内容的长度：

| 列名 | 存储内容 | 内容长度（十进制表示） | 内容长度（十六进制表示） |
| ---- | -------- | ---------------------- | ------------------------ |
| `c1` | `'aaaa'` | `4`                    | `0x04`                   |
| `c2` | `'bbb'`  | `3`                    | `0x03`                   |
| `c4` | `'d'`    | `1`                    | `0x01`                   |



又因为这些长度值需要按照列的逆序存放，所以最后`变长字段长度列表`的字节串用十六进制表示的效果就是（各个字节之间实际上没有空格，用空格隔开只是方便理解）：

```
01 03 04 
```

把这个字节串组成的`变长字段长度列表`填入上边的示意图中的效果就是：

![image-20251228201319160](/Users/yutinglai/Documents/note/MySQL-note/assets/image-20251228201319160.png)

##### 变长字段长度用1字节还是2字节表示

由于第一行记录中`c1`、`c2`、`c4`列中的字符串都比较短，其长度用1个字节就可以表示。

但是如果变长列内容比较长，其长度可能需要用2个字节表示（最多不会超过2个字节，因为一行记录占用字节数不能超过65535=2^16-1）。

那具体是用一个字节还是两个字节来表示变长字段内容的长度，InnoDB规则如下：

* 设W为字符集表示一个字符需要的最大字节数，例如`utf8`字符集中的`W`就是`3`，`gbk`字符集中的`W`就是`2`，`ascii`字符集中的`W`就是`1`。
* 设M为变长字段最多能够存储多少个字符，例如`VARCHAR(10)`表示能存储最多`10`个字符
* 设L为变长字段实际占用的字节数

判断规则

- 如果`M×W <= 255`，那么使用1个字节来表示真正字符串占用的字节数。

- 如果`M×W > 255`，则分为两种情况：

  - 如果`L <= 127`，则用1个字节来表示真正字符串占用的字节数。
  - 如果`L > 127`，则用2个字节来表示真正字符串占用的字节数

总结：如果该可变字段允许存储的最大字节数（`M×W`）超过255字节并且真实存储的字节数（`L`）超过127字节，则使用2个字节，否则使用1个字节。

在读记录的变长字段长度列表时，对于一个字节，如何确定是一个单独的字段长度还是半个字段长度？

如果最高位为0，则说明是一个单独的字节长度。如果最高位是1，则说明是半个字段长度，和后面一个字节两个字节共同表示这个可变长字段的长度。



##### 如果变长字段值为NULL，变长字段长度列表会存储其长度吗

变长字段长度列表中只存储值为 ***非NULL*** 的列内容占用的长度，值为 ***NULL*** 的列的长度是不储存的 。

也就是说对于第二条记录来说，因为`c4`列的值为`NULL`，所以第二条记录的`变长字段长度列表`只需要存储`c1`和`c2`列的长度即可。其中`c1`列存储的值为`'eeee'`，占用`4`字节数，`c2`列存储的值为`'fff'`，占用`3`字节数。数字`4`可以用1个字节表示，`3`也可以用1个字节表示，所以整个`变长字段长度列表`共需2个字节。



填充完`变长字段长度列表`的两条记录的对比图如下：

![image_1c9grq2b2jok1062t8tov21lqjbj.png](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e8fe4ee6b0~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



##### 变长字段长度列表是必须的吗

并不是所有记录都有这个 变长字段长度列表 部分，比方说表中所有的列都不是变长的数据类型的话，这一部分就不需要有。



##### CHAR(M)列的存储格式

结论：

* 如果CHAR(M)列的字符集是定长字符集（例如ascii，每个字符都用固定一个字节表示），则字段占用的长度不会存到变长字段长度列表。
* 如果CHAR(M)列的字符集是**非**定长字符集（例如utf8，用1-3字节表示一个字符），则字段占用的长度也会存到变长字段长度列表。

`record_format_demo`表的`c1`、`c2`、`c4`列的类型是`VARCHAR(10)`，而`c3`列的类型是`CHAR(10)`。

`record_format_demo`表采用的是`ascii`字符集，则其变长字段长度列表如图：

![image_1c9jdkga71kegkjs14o111ov1ce3kn.png-12.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e985c8d9a7~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



`record_format_demo`表的字符集修改为utf8：

```sql
mysql> ALTER TABLE record_format_demo MODIFY COLUMN c3 CHAR(10) CHARACTER SET utf8;
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

修改后新的变长字段长度列表如图：

![image_1c9jeb6defgf1o981lgfciokjl4.png-43.1kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e973ff4fde~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

原因：**变长字段长度列表**的本质作用是记录那些长度不固定的列到底占了多少空间。

1. 为什么ASCII编码的CHAR(10)不需要在变长字段长度列表记录长度？

   因为其长度是完全固定的，例如CHAR(10)就一定会占10字节，所以没有必要再额外存一个10，浪费空间。

2. 为什么UTF8编码的CHAR(10)需要在变长字段长度列表记录长度？

   是因为UTF8编码的字符可能有1-3个字节编码，因此CHAR(10)实际占用的字节长度在10-30这个区间。虽然底层可以根据编码规则，推测出来每一个字符是用几个字节编码，从而推测出整个字符串占用的字节长度，但是非常影响性能。

   因此直接在变长字段长度列表存储其占用的字节数是一种以空间换时间的做法。



#### 2️⃣NULL值列表

**是什么**

我们知道表中某些列可能会存锤NULL值，但是如果真的把NULL值存储起来，会非常浪费空间。

Compact行格式用一个单独的NULL值列表来处理对NULL值列信息的存储。

1. 统计「允许存NULL值」的列有哪些

   除了以下两种情况其他都允许存NULL

   * 主键列不允许存NULL
   * 被NOT NULL修饰的列不允许

2. 如果不存在「允许存NULL值」的列

   则不需要NULL值列表，不会占用任何空间

3. 如果存在「允许存NULL值」的列

   将每一个「允许存NULL值」的列，都用一个二进制位表示：

   - 1 - 为NULL值
   - 0 - 非NULL值

   最终按照列顺序**逆序**存放在NULL值列表中

​	NULL值列表的长度必须是字节的整数倍，如果不足字节的整数倍，则在前面补0



因为表`record_format_demo`有3个值允许为`NULL`的列，所以这3个列和二进制位的对应关系就是这样：

![image_1c9g88mtt1tj51ua1qh51vjo12pg5k.png-10.4kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e9018133f7~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



NULL值列表的长度必须是字节的整数倍，因此在前面补5个0：

![image_1c9g8g27b1bdlu7t187emsc46s61.png-19.4kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e901669f45~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

对于第一条记录来说，`c1`、`c3`、`c4`这3个列的值都不为`NULL`，所以它们对应的二进制位都是`0`，画个图就是这样：

![image_1c9g8m05b19ge1c8v2bf163djre6e.png-21.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e901befeac~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

所以第一条记录的`NULL值列表`用十六进制表示就是：`0x00`。

对于第二条记录来说，`c1`、`c3`、`c4`这3个列中`c3`和`c4`的值都为`NULL`，所以这3个列对应的二进制位的情况就是：

![image_1c9g8ps5c1snv1bhj3m48151sfl6r.png-20.6kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e944a8af0c~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

所以第二条记录的`NULL值列表`用十六进制表示就是：`0x06`。

所以这两条记录在填充了`NULL值列表`后的示意图就是这样：

![image_1c9grs9m4co8134u1t2rjhm1q6rc0.png-39kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e95903144f~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



#### 3️⃣记录头信息

由固定的`5`个字节，即`40`个二进制位组成。

不同的位代表不同的意思，如图：

![image_1c9geiglj1ah31meo80ci8n1eli8f.png-29.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e97718ef01~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

这些二进制位代表的详细信息如下表：

| 名称           | 大小（单位：bit） | 描述                                                         |
| -------------- | ----------------- | ------------------------------------------------------------ |
| `预留位1`      | `1`               | 没有使用                                                     |
| `预留位2`      | `1`               | 没有使用                                                     |
| `delete_mask`  | `1`               | 标记该记录是否被删除                                         |
| `min_rec_mask` | `1`               | B+树的每层非叶子节点中的最小记录都会添加该标记               |
| `n_owned`      | `4`               | 表示当前记录拥有的记录数                                     |
| `heap_no`      | `13`              | 表示当前记录在记录堆的位置信息                               |
| `record_type`  | `3`               | 表示当前记录的类型，`0`表示普通记录，`1`表示B+树非叶子节点记录，`2`表示最小记录，`3`表示最大记录 |
| `next_record`  | `16`              | 表示下一条记录的相对位置                                     |

>如果看不懂各个位的概念可以先略过，需要用到再深入研究。



`record_format_demo`中的两条记录的`头信息`分别是什么：

![img](/Users/yutinglai/Documents/note/MySQL-note/assets/17075b82cb070959~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



### 2) 记录的真实数据

#### 隐藏列

每一行记录除了我们自己定义的列，MySQL还会默认的添加一些列（也称为`隐藏列`）：

| 列名             | 是否必须 | 占用空间 | 描述                   |
| ---------------- | -------- | -------- | ---------------------- |
| `row_id`         | 否       | `6`字节  | 行ID，唯一标识一条记录 |
| `transaction_id` | 是       | `6`字节  | 事务ID                 |
| `roll_pointer`   | 是       | `7`字节  | 回滚指针               |

`InnoDB`表主键的生成策略：

* 用户定义了主键，则使用用户定义的主键
* 用户没有定义主键，则选择一个`Unique`键作为主键
* 如果没有`Unique`键，则默认添加一个名为`row_id`的隐藏列作为主键

这就是为什么`row_id`列不是必须的，而其他两个列是必须的。



因为表`record_format_demo`并没有定义主键，所以`MySQL`服务器会为每条记录增加上述的3个列。

现在看一下加上`记录的真实数据`的两个记录长什么样吧：

![image_1c9h256f9nke14311adhtu61ie2dn.png-92kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e973b70372~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

注意事项：

1. 表`record_format_demo`使用的是`ascii`字符集，所以`0x61616161`就表示字符串`'aaaa'`，`0x626262`就表示字符串`'bbb'`，以此类推。
2. 注意第1条记录中`c3`列的值，它是`CHAR(10)`类型的，它实际存储的字符串是：`'cc'`，而`ascii`字符集中的字节表示是`'0x6363'`，虽然表示这个字符串只占用了2个字节，但整个`c3`列仍然占用了10个字节的空间，除真实数据以外的8个字节的统统都用空格字符填充，空格字符在`ascii`字符集的表示就是`0x20`。
3. 注意第2条记录中`c3`和`c4`列的值都为`NULL`，它们被存储在了前边的`NULL值列表`处，在记录的真实数据处就不再冗余存储，从而节省存储空间。



## 4.4 VARCHAR(M) M的最大取值

MySQL 规定一行中，除了BLOB、TEXT类型的列之外，其他所有列（不包括隐藏列和记录头）占用的总长度不能超过 **65,535 字节**



假设一个表中只有一个`VARCHAR(M)`类型的字段，其中的`M`代表该类型最多存储的字符个数，那M的取值最大可以是多少呢？

*这个表只有一个VARCHAR(M)类型的列*，那么一行数据需要占用3部分存储空间（不包括隐藏列和记录头）：

1. 真实数据
2. 数据占用字节的长度（最多2字节，因为2^8-1=65535，两字节**所能表示的最大数值范围**刚好覆盖了 MySQL 一行记录的最大限制）
3. NULL值标识，如果该列有NOT NULL属性，则不占用这部分存储空间（NOT NULL 则0字节；可为NULL 则1字节；）



综上：

* NOT NULL：则真实数据最多可以占用65535 - 2 = 65533

  ```sql
  # M=65535 报错
  mysql> create table varchar_size_demo( c varchar(65535) NOT NULL) CHARSET=ascii ROW_FORMAT=compact;
  ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
  # M=655354报错
  mysql> create table varchar_size_demo( c varchar(65534) NOT NULL) CHARSET=ascii ROW_FORMAT=compact;
  ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
  # M=65533 报错
  mysql> create table varchar_size_demo( c varchar(65533) NOT NULL) CHARSET=ascii ROW_FORMAT=compact;
  Query OK, 0 rows affected (0.010 sec)
  ```

  

* 可为NULL：则真实数据最多可以占用65535 - 2 -1 = 65532

  ```sql
  # M=65535 报错
  mysql> create table varchar_size_demo( c varchar(65535) ) CHARSET=ascii ROW_FORMAT=compact;
  ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
  # M=65533 报错
  mysql> create table varchar_size_demo( c varchar(65533) ) CHARSET=ascii ROW_FORMAT=compact;
  ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
  # M=65532可以成功创建表
  mysql> create table varchar_size_demo( c varchar(65532) ) CHARSET=ascii ROW_FORMAT=compact;
  Query OK, 0 rows affected (0.011 sec)
  ```

  

上面的数据基于数据表使用的是ascii字符集，如果使用的不是ascii字符集会怎么样？

VARCHAR(M)中的M表示的是该字段最多存储的字符数量，当该列允许为NULL的情况下：

ascii字符集（1个字符占1字节）：M最大取值 = 65532 / 1 = 65532

gbk字符集（1个字符占1-2字节）：M最大取值 = 65532 / 2 =32766

```sql
mysql> CREATE TABLE varchar_size_demo( c VARCHAR(32767) ) CHARSET=gbk ROW_FORMAT=Compact;
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
mysql> CREATE TABLE varchar_size_demo( c VARCHAR(32766) ) CHARSET=gbk ROW_FORMAT=Compact;
Query OK, 0 rows affected (0.007 sec)
```

utf字符集（一个字符占1-3字节）：M最大取值 =65532 / 3 = 21844



## 4.5 行溢出

MySQL将整个存储空间划分为若干个页，每个页的大小为16KB，即`16384`字节。

而一行记录最大不能超过 **65,535 字节**，也就是说可能出现***一个页放不下一条记录***的情况。

在`Compact`和`Redundant`行格式中，对于占用存储空间非常大的列，在`记录的真实数据`处只会存储该列的一部分数据，把剩余的数据分散存储在几个其他的页中，然后`记录的真实数据`处用20个字节存储指向这些页的地址（当然这20个字节中还包括这些分散在其他页面中的数据的占用的字节数），从而可以找到剩余数据所在的页，如图所示：

![image_1d48e3imu1vcp5rsh8cg0b1o169.png-149kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e9aab47ea5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

从图中可以看出来，对于`Compact`和`Redundant`行格式来说，如果某一列中的数据非常多的话，在本记录的真实数据处只会存储该列的前`768`个字节的数据和一个指向其他页的地址，然后把剩下的数据存放到其他页中，这个过程也叫做`行溢出`，存储超出`768`字节的那些页面也被称为`溢出页`。画一个简图就是这样：

![image_1conbskr7apj19ns1d194vs1buo1t.png-35.8kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e9a5d5637a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

***VARCHAR(M)*** 、***TEXT***、***BLOB*** 类型的列在存储数据非常多的时候都会发生`行溢出`。

不用太关注行溢出的临界点在哪里，只要知道如果我们一条记录的某个列中存储的数据占用的字节数非常多时，该列就可能成为`溢出列`。



## 4.6 Dynamic & Compressed行格式

`Dynamic`和`Compressed`和`Compact`行格式的区别主要在于`行溢出`的处理方式不同：

`Dynamic`和`Compressed`行格式不会在记录的真实数据处存储字段真实数据的前`768`个字节，而是把所有的字节都存储到其他页面中，只在记录的真实数据处存储其他页面的地址，就像这样：

![image_1conbtnmr1sg1hao1nf41pi1eb72a.png-29.9kB](/Users/yutinglai/Documents/note/MySQL-note/assets/169710e9b2c2b71e~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

`Compressed`行格式和`Dynamic`不同的一点是，`Compressed`行格式会采用压缩算法对页面进行压缩，以节省空间。





## 4.7 注意事项

* CHAR(10)和VARCHAR(10)的区别是什么

  **CHAR(10)**：

  - **定长分配**：无论你存入的是 1 个字符还是 10 个字符，它在磁盘上始终占据 **10 个字符** 宽度的空间。
  - **空格填充**：如果存入内容不满 10 个字符，MySQL 会在末尾用空格填充至 10 位。

  **VARCHAR(10)**：

  - **变长存储**：它是“按需分配”。如果你存入 3 个字符，它就只占用 3 个字符的空间。
  - **额外开销**：由于长度不固定，它必须额外占用 **1 字节**（长度 < 255）或 **2 字节**（长度 > 255）来存储数据的实际长度。

  怎么选？

  如果字段长度非常固定（如 UUID、MD5、身份证号、国家/状态缩写）选CHAR(M)，如果字段长度跨度很大（如 用户名、个人简介**、**评论）选VARCHAR(M)。

* 变长字符集的`CHAR(M)`类型的列要求至少占用`M`个字节，而`VARCHAR(M)`却没有这个要求。

  例如如果使用`utf8`字符集，`CHAR(10)`列存储的数据字节长度的范围是10～30个字节。即使我们向该列中存储一个空字符串也会占用`10`个字节，这是怕将来更新该列的值的字节长度大于原有值的字节长度而小于10个字节时，可以在该记录处直接更新，而不是在存储空间中重新分配一个新的记录空间，导致原有的记录空间成为碎片。

* 为什么记录的额外信息数据都要逆序放

  提高 CPU 读取记录的性能。

  

# 5. InnoDB的数据页结构

* 页是InnoDB管理存储空间的基本单位，一个页的大小一般为16KB。

* InnoDB为了不同的目的设计了很多不同类型的页，例如存undo log信息的页、溢出页、系统页、索引页（又称数据页）...

  其中「数据页」是专门用于存放表记录的页，这一节讨论「数据页」的具体结构。



## 5.1 数据页结构概览

![img](/Users/yutinglai/Documents/note/MySQL-note/assets/16f13ee1e2dfac7c~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

| 名称                 | 中文名             | 占用空间大小 | 简单描述                 |
| -------------------- | ------------------ | ------------ | ------------------------ |
| `File Header`        | 文件头部           | `38`字节     | 页的一些通用信息         |
| `Page Header`        | 页面头部           | `56`字节     | 数据页专有的一些信息     |
| `Infimum + Supremum` | 最小记录和最大记录 | `26`字节     | 两个虚拟的行记录         |
| `User Records`       | 用户记录           | 不确定       | 实际存储的行记录内容     |
| `Free Space`         | 空闲空间           | 不确定       | 页中尚未使用的空间       |
| `Page Directory`     | 页面目录           | 不确定       | 页中的某些记录的相对位置 |
| `File Trailer`       | 文件尾部           | `8`字节      | 校验页是否完整           |

> 下面按照按照便于理解的顺序展开介绍。



## 5.2 Infimum & Supremum

这是两条伪记录（虚拟记录），InnoDB中每页都默认有的两条记录，分别代表该页的最小记录和最大记录。

其构造如图，都是由5字节大小的`记录头信息`和8字节大小的一个固定的部分组成的：

![image_1c9ra45eam7t1mil9o1h3ucqdhv.png-50.4kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c100ff0ccc2~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

## 5.3 User Records

* 我们存储的记录会按照我们指定的行格式存储到User Records部分。
* 一开始并没有`User Records`这个部分，每当我们插入一条记录，都会从`Free Space`部分，也就是尚未使用的存储空间中申请一个记录大小的空间划分到`User Records`部分，当`Free Space`部分的空间全部被`User Records`部分替代掉之后，也就意味着这个页使用完了，如果还有新的记录插入的话，就需要去申请新的页了，这个过程的图示如下：

![image_1cosvi1in9st476cdqfki1n39m.png-133.8kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c0fe86555ed~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

* User Records中的记录是如何组织起来的？

  通过记录头信息组织起来的。

  > 为了详细介绍一下记录是如何通过记录头信息组织起来的，这里创建一张表，并插入一些数据作为示例：
  >
  > ```sql
  > mysql> CREATE TABLE page_demo(
  >     ->     c1 INT,
  >     ->     c2 INT,
  >     ->     c3 VARCHAR(10000),
  >     ->     PRIMARY KEY (c1)
  >     -> ) CHARSET=ascii ROW_FORMAT=Compact;
  > Query OK, 0 rows affected (0.03 sec)
  > 
  > mysql> INSERT INTO page_demo VALUES(1, 100, 'aaaa'), (2, 200, 'bbbb'), (3, 300, 'cccc'), (4, 400, 'dddd');
  > Query OK, 4 rows affected (0.00 sec)
  > Records: 4  Duplicates: 0  Warnings: 0
  > ```

  回顾一下记录头信息包含哪些属性（Compact行格式）:

  | 名称           | 大小（单位：bit） | 描述                                                         |
  | -------------- | ----------------- | ------------------------------------------------------------ |
  | `预留位1`      | `1`               | 没有使用                                                     |
  | `预留位2`      | `1`               | 没有使用                                                     |
  | `delete_mask`  | `1`               | 标记该记录是否被删除                                         |
  | `min_rec_mask` | `1`               | B+树的每层非叶子节点中的最小记录都会添加该标记               |
  | `n_owned`      | `4`               | 表示当前记录拥有的记录数                                     |
  | `heap_no`      | `13`              | 表示当前记录在记录堆的位置信息                               |
  | `record_type`  | `3`               | 表示当前记录的类型，`0`表示普通记录，`1`表示B+树非叶节点记录，`2`表示最小记录，`3`表示最大记录 |
  | `next_record`  | `16`              | 表示下一条记录的相对位置                                     |

  原本完整的Compact行格式如图：

  ![image_1c9o2eib2vl11qnf1dfl1d2lco313.png-76.4kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c0feca77be3~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  为了专注于我们关注的记录头信息，这里对行格式示意图进行简化，只关注记录头信息以及`c1`、`c2`、`c3`列的信息：

  ![image_1c9o52lt41v5c7vk1vm91fsm174b2d.png-49.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c0ff1ae5364~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  则我们插入的记录的行格式示意图如下：

  ![image_1c9qs0j281knc16hc1hqsgj01v0o2c.png-82.8kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c0ff83f9870~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  > 注意：各条记录在`User Records`中存储的时候并没有空隙，这里为便于观看才把每条记录单独画在一行中。

  对照图来看看记录头信息中各个属性的含义：

  * `delete_mask`

    标记当前记录是否被删除，占1bit。

    - 0 - 没被删除
    - 1 - 已被删除

    

    为什么只是打一个删除标记，而不是直接从磁盘删除？

    因为如果直接从磁盘删除，则剩余的记录需要在磁盘中重新排列，这样会造成性能消耗。

    只打一个标记相对来说对性能更加友好。而且被打删除标记的记录会组成一个垃圾链表，这个链表占用的空间为可重用空间。如果后续要插入新记录，则可能从垃圾链表中能找到可重用空间，直接将被删除的记录给覆盖掉。

  * `min_rec_mask`

    B+树的每层非叶子节点中的最小记录都会添加该标记。

    图中四条记录的`min_rec_mask`都为0，说明它们都不是`B+`树的非叶子节点中的最小记录。

  * `n_owned`

    记录所在组拥有的记录条数，只有组中最后一条记录会记录该属性值。

  * `heap_no`

    标记当前记录在本页中的位置。

    从图中可以看出来，我们插入的4条记录在本页中的位置分别是：`2`、`3`、`4`、`5`。

    为什么没有0、1位置的记录呢？

    因为被Infimum & Supremum记录占用，Infimum的`heap_no`为0，Supremum的`heap_no`为1。

    ![image_1c9qs1mn2t3j1nt344116nk15uf2p.png-119.7kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c10773d8cee~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  * `record_type`

    标识当前记录的类型。

    - `0`表示普通记录
    - `1`表示B+树非叶节点记录
    - `2`表示最小记录
    - `3`表示最大记录

    从图中我们也可以看出来，我们自己插入的记录就是普通记录，它们的`record_type`值都是`0`，而最小记录和最大记录的`record_type`值分别为`2`和`3`。

  * `next_record`

    用于找到下一条记录的指针。

    * `next_record`记录从当前记录的真实数据到下一条记录的真实数据的地址偏移量。比方说第一条记录的`next_record`值为`32`，意味着从第一条记录的真实数据的地址处向后找`32`个字节便是下一条记录的真实数据。

    * 规定 ***Infimum记录（也就是最小记录）*** 的下一条记录就是本页中主键值最小的用户记录，而本页中主键值最大的用户记录的下一条记录就是 ***Supremum记录（也就是最大记录）***

      ![image_1cot1r96210ph1jng1td41ouj85c13.png-120.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c1084c440b4~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

    * 删除记录，链表的变化

      ```sql
      mysql> DELETE FROM page_demo WHERE c1 = 2;
      Query OK, 1 row affected (0.02 sec)
      ```

      删掉第2条记录后示意图为：

      ![image_1cul8slbp1om0p31b3u1be11gco9.png-119.6kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c108ee1da43~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

    * 将记录重新插回到表中，链表的变化

      ```sql
      mysql> INSERT INTO page_demo VALUES(2, 200, 'bbbb');
      Query OK, 1 row affected (0.00 sec)
      ```

      ![image_1cot2j9n94a511jd15clrrfp6p1t.png-137.8kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c109f25d55d~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

    * 当数据页中存在多条被删除掉的记录时，这些记录的`next_record`属性将会把这些被删除掉的记录组成一个垃圾链表，以备之后重用这部分存储空间。

## 5.4 Page Directory（页目录）

一个页中可能有很多条记录，那我们怎么搜索到目标记录？

方法1：遍历整个页，直到找到目标记录 - 效率低

方法2：给这个页增加一个页目录，通过目录查找 - 效率高「页目录顾名思义就是页的目录」



这个页目录是怎么制作的呢？

1. 将所有正常的记录（包括最大和最小记录，不包括标记为已删除的记录）划分为几个组。

2. 每个组的最后一条记录（即组内主键值最大的那条记录）的头信息中的`n_owned`属性记录该组拥有多少条记录。

   **一个组能拥有多少条记录？**

   * 最小记录所在分组只能有1条记录
   * 最大记录所在分组可以有1-8条记录
   * 剩余分组可以有4-8条记录

3. 将「每个组的最后一条记录的地址偏移量」单独提取出来按顺序存储到`页目录`。页目录中的这些地址偏移量被称为`槽`（英文名：`Slot`），所以这个页面目录就是由`槽`组成的。



**分组的过程是怎么样的？**

- 初始情况下一个数据页里只有最小记录和最大记录两条记录，它们分属于两个分组。
- 之后每插入一条记录，都会从`页目录`中找到主键值比本记录的主键值大、并且差值最小的槽，然后把该槽对应的记录的`n_owned`值加1，表示本组内又添加了一条记录，直到该组中的记录数等于8个。
- 在一个组中的记录数等于8个后再插入一条记录时，会将组中的记录拆分成两个组，一个组中4条记录，另一个5条记录。这个过程会在`页目录`中新增一个`槽`来记录这个新增分组中最大的那条记录的偏移量。

因此我们page_demo表的页目录如图：

最小记录单独作为一组，其地址偏移量作为`slot0`被放入页目录。由于仅剩下5条记录，因此剩余的5条记录作为一个组，最大记录的地址偏移量作为`slot1`被放入页目录。

![image_1couate3jr19gc18gl1cva1fcg34.png-100.8kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c10f2e61ad5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



为了演示通过页目录能够高效查找的过程，再往`page_demo`表中添加一些记录：

```sql
mysql> INSERT INTO page_demo VALUES(5, 500, 'eeee'), (6, 600, 'ffff'), (7, 700, 'gggg'), (8, 800, 'hhhh'), (9, 900, 'iiii'), (10, 1000, 'jjjj'), (11, 1100, 'kkkk'), (12, 1200, 'llll'), (13, 1300, 'mmmm'), (14, 1400, 'nnnn'), (15, 1500, 'oooo'), (16, 1600, 'pppp');
Query OK, 12 rows affected (0.00 sec)
Records: 12  Duplicates: 0  Warnings: 0
```

现在页里边就一共有18条记录了（包括最小和最大记录），这些记录被分成了5个组，如图所示（为突出重点，把不必要的属性省略了）：

![image_1d6g64af2sgj1816ktl1q22dehp.png-189.1kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c10e3449897~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

由于页目录中的槽代表的记录的主键值都是递增排序的，因此可以通过二分查找快速搜索记录。

假设我们想找主键值=6的记录，过程如下：

初始low=0，high=4

1. 计算中间槽位置 `(0 + 4) / 2 = 2`，由slot2对应的记录的主键值=8，6 < 8，因此high=2
2. 计算中间槽位置 `(0 + 2) / 2 = 1`，由slot1对应的记录的主键值=4，6 > 4，因此low=1
3. 又因为high-low=1，因此主键值=6的记录在slot2对应的组。因为slot2存储的是该组最后一条记录的偏移量，为了能够找到该组第一条记录的位置，我们通过slot2找到slot1（各个槽之间是相邻的），slot1指向的记录的下一条记录就是slot2所在组的第一条记录，然后遍历该组，可以找到主键=6的记录。

总结在一个数据页中根据主键值搜索记录的过程：

1. 根据二分查找定位到主键值所在槽
2. 找到槽对应的组的第一条记录
3. 根据next_record属性遍历组内的记录以找到match的那条记录

## 5.5 Page Header（页面头部）

Page Header专门用于记录「数据页」中存储的记录的状态信息，例如本页中已经存储了多少条记录，第一条记录的地址是什么，页目录中存储了多少个槽等等。

| 名称                | 占用空间大小 | 描述                                                         |
| ------------------- | ------------ | ------------------------------------------------------------ |
| `PAGE_N_DIR_SLOTS`  | `2`字节      | 在页目录中的槽数量                                           |
| `PAGE_HEAP_TOP`     | `2`字节      | 还未使用的空间最小地址，也就是说从该地址之后就是`Free Space` |
| `PAGE_N_HEAP`       | `2`字节      | 本页中的记录的数量（包括最小和最大记录以及标记为删除的记录） |
| `PAGE_FREE`         | `2`字节      | 第一个已经标记为删除的记录地址（各个已删除的记录通过`next_record`也会组成一个单链表，这个单链表中的记录可以被重新利用） |
| `PAGE_GARBAGE`      | `2`字节      | 已删除记录占用的字节数                                       |
| `PAGE_LAST_INSERT`  | `2`字节      | 最后插入记录的位置                                           |
| `PAGE_DIRECTION`    | `2`字节      | 最后一次插入相对于前一次插入的位置关系。<br />如果新插入的记录主键值比上一次插入的记录主键值大，则认为插入方向是右边，反之则是左边。 |
| `PAGE_N_DIRECTION`  | `2`字节      | 一个方向连续插入的记录数量。<br />假设连续几次插入新记录的方向都是一致的，`InnoDB`会把沿着同一个方向插入记录的条数记下来，这个条数就用`PAGE_N_DIRECTION`这个状态表示。当然，如果最后一条记录的插入方向改变了的话，这个状态的值会被清零重新统计。 |
| `PAGE_N_RECS`       | `2`字节      | 该页中记录的数量（不包括最小和最大记录以及被标记为删除的记录） |
| `PAGE_MAX_TRX_ID`   | `8`字节      | 修改当前页的最大事务ID，该值仅在二级索引中定义               |
| `PAGE_LEVEL`        | `2`字节      | 当前页在B+树中所处的层级                                     |
| `PAGE_INDEX_ID`     | `8`字节      | 索引ID，表示当前页属于哪个索引                               |
| `PAGE_BTR_SEG_LEAF` | `10`字节     | B+树叶子段的头部信息，仅在B+树的Root页定义                   |
| `PAGE_BTR_SEG_TOP`  | `10`字节     | B+树非叶子段的头部信息，仅在B+树的Root页定义                 |



## 5.6 File Header（文件头部）

Page Header专门用来记录数据页的各种状态信息，只有数据页包含这个模块。

File Header用于记录各种页的通用的信息，比方说这个页的编号是多少，它的上一个页、下一个页是谁等等，因此所有页都包含这个模块。

| 名称                               | 占用空间大小 | 描述                                                         |
| ---------------------------------- | ------------ | ------------------------------------------------------------ |
| `FIL_PAGE_SPACE_OR_CHKSUM`         | `4`字节      | 页的校验和（checksum值）<br />通过算法来计算一个比较短的值来代表这个很长的字符串，这个比较短的值就是校验和。这样在比较两个很长的字节串之前先比较这两个长字节串的校验和，如果校验和都不一样两个长字节串肯定是不同的，所以省去了直接比较两个比较长的字节串的时间损耗。 |
| `FIL_PAGE_OFFSET`                  | `4`字节      | 页号<br />每一个`页`都有一个单独的页号，就跟身份证号码一样，`InnoDB`通过页号来可以唯一定位一个`页`。 |
| `FIL_PAGE_PREV`                    | `4`字节      | 上一个页的页号                                               |
| `FIL_PAGE_NEXT`                    | `4`字节      | 下一个页的页号                                               |
| `FIL_PAGE_LSN`                     | `8`字节      | 页面被最后修改时对应的日志序列位置（英文名是：Log Sequence Number） |
| `FIL_PAGE_TYPE`                    | `2`字节      | 该页的类型<br />例如undo日志页、系统页、溢出页、数据页等等   |
| `FIL_PAGE_FILE_FLUSH_LSN`          | `8`字节      | 仅在系统表空间的一个页中定义，代表文件至少被刷新到了对应的LSN值 |
| `FIL_PAGE_ARCH_LOG_NO_OR_SPACE_ID` | `4`字节      | 页属于哪个表空间                                             |



注意：InnoDB以页为单位存储数据，有时例如我们一张表中有成千上万条数据，一个页存不下，需要用多个页存储，这些页逻辑上相邻但在物理上不一定相邻。`FIL_PAGE_PREV`和`FIL_PAGE_NEXT`分别记录当前页的上一页和下一页，通过这两个指针，所有的页能够形成一个双向链表。

![image_1ca00fhg418pl1f1a1iav1uo3aou9.png-90.9kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a95c10eb9d61ce~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

## 5.7 File Trailer

File Trailer也是所有页共有的，不是数据页特有的。 

由8个字节组成，分为两部分：

- 前4个字节代表页的校验和

  这部分和`File Header`中的校验和是相同的。

  因为我们的页如果在内存中被修改了，则会定期刷盘到磁盘。

  刷盘的过程是从页头开始刷到页尾，如果说刷到一半断电了，就会出现问题，也就是磁盘中这一页前半部份是最新的数据，后半部份是旧的数据。

  为了解决这个问题，在`File Trailer`中也会记录校验和。

  刷盘之前，会将校验和计算出来，然后再开始刷。如果刷到一半断电了，则最终磁盘的页的`File Header`中的校验和（新值）与`File Trailer`的校验和（旧值）不一致，则可以发现刷盘过程出现了问题。

- 后4个字节代表页面被最后修改时对应的日志序列位置（LSN）

  用于校验页的完整性。



# 6. B+树索引

## 6.1 没有索引如何进行查找

### 在一个页中的查找

* 以主键为搜索条件

  根据页目录使用二分查找快速定位到组

  遍历组中的记录以找到目标记录

* 以非主键列作为搜索条件

  没有页目录，只能从页的第一条记录开始逐条遍历

### 在多个页中的查找

在很多个页中的查找分为两步：

1. 定位到目标页
2. 在页中进行查找

由于无法确定目标记录在哪一页，所以只能从第一页开始沿着双向链表往下找，因此效率很低。



总结，如果没有索引，以下两个场景搜索效率低：

1. 以非主键列作为搜索条件时
2. 在很多个页中进行查找时（大部分情况下都会在很多个页中进行查找，因为数据量大）

为了解决以上问题，需要引入索引。

## 6.2 索引概念介绍

### 是什么

我们知道，如果没有索引，以下两个场景搜索效率低：

1. 以非主键列作为搜索条件时
2. 在很多个页中进行查找时

我们现在先专注于第2个场景，要解决的问题是，如何快速定位到目标页？

我们可以参考一下页目录是如何帮助我们快速定位到目标记录的：将整个页划分为若干个组，将每个组的最后一个元素的偏移量作为slot存储在页目录，则可以通过二分查找快速搜索。

为什么可以进行二分查找？因为页目录中的slot指向的记录的主键值是递增的。

那是不是能够利用类似的方法来为我们的所有页也建立一个目录？

完全可以。

每个数据页本身可以作为一组，又因为所有数据页存储的记录主键值实际上也是递增的，所以我们可以将每一个数据页的第一条记录（主键值最小的记录）作为参考点，据此进行二分查找。

我们建立一个目录，每一页对应一个目录项，目录项包含两项信息：

1. 页的第一条记录的主键值（即页的用户记录的最小主键值），用key表示。
2. 页号，用page_no表示。

假设一个页就能够存储完整的目录信息，则在这个目录的页中进行二分查找就能够定位到目标页。

> 注意，页目录的示意图只展示以下部分，以节省空间。
>
> <img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd1b0cad198~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1caacokob6ne1nv41meda0s7vk3g.png-68.1kB" style="zoom: 67%;" />

创建一个表作为示例：

```sql
mysql> CREATE TABLE index_demo(
    ->     c1 INT,
    ->     c2 INT,
    ->     c3 CHAR(1),
    ->     PRIMARY KEY(c1)
    -> ) ROW_FORMAT = Compact;
Query OK, 0 rows affected (0.03 sec)
```

我们往index_demo表中插入若干条数据之后，其效果如下：

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd282d6b9b9~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1caba0afo11fa1cli1nu070m16bg1j.png-119.1kB" style="zoom:67%;" />

那我们如果要搜索主键值`c1=20`的记录如何搜索：

1. 定位到目标页：

   在目录页根据key进行二分查找：

   * low=1 high=4 mid=(1+4)/2=2 由于目录项2的key=5<20，因此low=2
   * low=2 high=4 mid=(2+4)/2=3 由于目录项3的key=12<20，因此low=3
   * 由于low=3 high=4 high-low=1，因此可以确定目标页为page_no=9

2. 在页中定位到目标记录

   通过页目录使用二分查找，可以定位到目标记录。



那目录信息应该用什么类型的页来存储呢？

存储目录的信息的页由目录项组成，每个目录项对应一个数据页，每个目录项包含两项信息：

1. 页的用户记录的最小主键值，即页的第一条记录的主键值，用key表示。
2. 页号，用page_no表示。

就类似于数据页，每个表只有两列，分别存主键值和页号信息。

因此，目录信息也用数据页来存即可。

![image_1caahuomf15m11e5k19v1bf21inq9.png-145.9kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd295fd42b5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

因此数据页既可以用来存储用户记录数据，也可以用来存储目录信息。为区分它们，我们将专门用于存目录信息的页称为「目录页」。

目录页的特点是：

* 记录头信息里的`record_type`属性，它的各个取值代表的意思如下：

  - `0`：普通的用户记录
  - `1`：目录项记录
  - `2`：最小记录
  - `3`：最大记录

  如果是普通用户记录，则`record_type = 0`，如果是目录项记录，则`record_type = 1`

  所以可以看到上图中，页30中所有目录项记录的最上面的块（`record_type`属性）填写的值为1，其他页存储的用户记录的的最上面的块（`record_type`属性）填写的值为0。

* `目录项记录`只有主键值和页的编号两个列，而普通的用户记录的列包含用户自定义的列和隐藏列。
* 记录头信息`min_rec_mask`属性，只有在存储`目录项记录`的页中的主键值最小的`目录项记录`的`min_rec_mask`值为`1`，其他别的记录的`min_rec_mask`值都是`0`。（图中没有画出）



如果存的数据越来越多了，一个数据页存不下目录信息了怎么办？

多整几个目录页。

![image_1cacabsrh17a5133q1otf725gi92q.png-135.7kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd29ebc7a4c~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

那如果目录页特别多，是不是又回到要遍历所有目录页的尴尬局面？

给目录再加一层目录。

![image_1cacafpso19vpkik1j5rtrd17cm3a.png-158.1kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2a6c7a65f~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

其最终的效果就像一棵树：

所有的用户记录都放在叶子节点，目录项记录都放在非叶子节点，这就是B+树的结构。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2a6fb9126~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1ca80gps314u9121u1rdp9r7md8cm.png-55.6kB" style="zoom:67%;" />



假设所有存放目录项记录的数据页可以存放1000条目录项记录，所有存放用户记录的数据页可以存放100条用户记录，那么：

- 如果`B+`树只有1层，也就是只有1个用于存放用户记录的节点，最多能存放`100`条记录。
- 如果`B+`树有2层，最多能存放`1000×100=100000`条记录。
- 如果`B+`树有3层，最多能存放`1000×1000×100=100000000`条记录。
- 如果`B+`树有4层，最多能存放`1000×1000×1000×100=100000000000`条记录。

因此，一般情况下，我们用到的`B+`树都不会超过4层，那我们通过主键值去查找某条记录最多只需要做4个页面内的查找（查找3个目录项页和一个用户记录页），又因为在每个页面内有`Page Directory`（页目录），所以在页面内也可以通过二分法实现快速定位记录。



### 聚簇索引

我们上边介绍的`B+`树就是聚簇索引。它有两个特点：

1. 使用记录主键值的大小进行记录和页的排序，这包括三个方面的含义：

   - 页内的记录是按照主键的大小顺序排成一个单向链表。
   - 各个存放用户记录的页也是根据页中用户记录的主键大小顺序排成一个双向链表。
   - 存放目录项记录的页分为不同的层次，在同一层次中的页也是根据页中目录项记录的主键大小顺序排成一个双向链表。

2. `B+`树的叶子节点存储的是完整的用户记录。

   所谓完整的用户记录，就是指这个记录中存储了所有列的值（包括隐藏列）。



* 聚簇索引不需要我们手动创建，InnoDB存储引擎会自动帮我们创建
* InnoDB存储引擎中，聚簇索引既是索引，也是数据（所有用户记录都存在了叶子节点）。

### 二级索引

通过聚簇索引，我们可以解决「在很多个页中根据主键值进行查找时效率低」的问题，还有一个问题没有解决「以非主键列作为搜索条件时效率低」。

这是因为我们前面的索引都是根据主键值升序排序来建立的，对于非主键列，依然是无序的。

那么如何解决这个问题呢？好办，给非主键列也建立索引。

例如上面的例子都是给`index_demo`表的`c1`主键列建立索引，我们给`c2`列建立索引。

![image_1cactc8jg14j91likvmd1h8cn3o4h.png-161.6kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2a89adfa5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

它和聚簇索引的区别在于：

* 使用c2列值的大小对记录和页进行排序

* 叶子节点中不会存储完整的用户记录数据，只会存储索引列的值和主键列值（即`c2`、`c1`列）。

  如果我们要根据c2列查询一条数据，需要在c2列的索引树上定位到目标项，获得其主键列值之后，再回到聚簇索引进行查询才能获得完整数据，这个过程称为「回表」。

这种根据非主键列值建立的索引，称为二级索引。



为什么在二级索引的叶子节点不存储完整数据，这不就不需要回表了吗？

如果在所有索引都存储一遍完整数据，就太浪费存储空间了。

### 联合索引

前面例子中的索引都是基于一个列来建立索引的，如果我想同时用多个列来作为排序规则呢？

例如我想让`B+`树按照`c2`和`c3`列的大小进行排序，这个包含两层含义：

- 先把各个记录和页按照`c2`列进行排序。
- 在记录的`c2`列相同的情况下，采用`c3`列进行排序

为`c2`和`c3`列建立的索引的示意图如下：

![image_1d80rmun21al711ok1tvo1i161rnpp.png-172.2kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2b0b70d72~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

如图所示，我们需要注意一下几点：

- 每条`目录项记录`都由`c2`、`c3`、`页号`这三个部分组成，各条记录先按照`c2`列的值进行排序，如果记录的`c2`列相同，则按照`c3`列的值进行排序。
- `B+`树叶子节点处的用户记录由`c2`、`c3`和主键`c1`列组成。
- 联合索引本质也是二级索引。



### 创建/删除索引相关语句

`InnoDB`和`MyISAM`会自动为主键或者声明为`UNIQUE`的列建立`B+`树索引，但是如果我们想为其他的列建立索引就需要我们手动创建。

**创建索引**

```sql
CREATE TALBE 表名 (
    各种列的信息 ··· , 
    [KEY|INDEX] 索引名 (需要被索引的单个列或多个列)
)
```

**修改索引**

```sql
ALTER TABLE 表名 ADD [INDEX|KEY] 索引名 (需要被索引的单个列或多个列);
```

**删除索引**

```sql
ALTER TABLE 表名 DROP [INDEX|KEY] 索引名;
```

示例：

```sql
# 在index_demo表为c2和c3列添加联合索引
CREATE TABLE index_demo(
    c1 INT,
    c2 INT,
    c3 CHAR(1),
    PRIMARY KEY(c1),
    INDEX idx_c2_c3 (c2, c3)
);
# 删除该索引
ALTER TABLE index_demo DROP INDEX idx_c2_c3;
```

**索引命名原则**

以`idx_`为前缀，后边跟着需要建立索引的列名，多个列名之间用下划线`_`分隔开，例如`idx_c2_c3`。



### 注意事项

* 一个B+树索引的根节点自诞生之日起，便不会再移动。这样只要我们对某个表建立一个索引，那么它的`根节点`的页号便会被记录到某个地方，然后凡是`InnoDB`存储引擎需要用到这个索引的时候，都会从那个固定的地方取出`根节点`的页号，从而来访问这个索引。

* 我们上面的例子中，二级索引的目录项记录由「二级索引列+页号」两部分组成。

  假设我们二级索引列的值并不是Unique的，`index_demo`表为例，假设这个表中的数据是这样的：

  | `c1` | `c2` | `c3` |
  | ---- | ---- | ---- |
  | 1    | 1    | 'u'  |
  | 3    | 1    | 'd'  |
  | 5    | 1    | 'y'  |
  | 7    | 1    | 'a'  |

  那我们为`c2`列建立的二级索引会是这样的

  ![image_1cp9vthl71h9n8091dkdjek16qg1j.png-58.6kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2c5b9ef01~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  假设此时要插入一条新数据`c1=9`, `c2=1`, `c3='c'`，在找到目录页页3之后，这两项目录项记录记录的c2列都是1，那到底应该将新数据插入到页4还是页5呢？无法确定。

  为了解决这种情况，==**二级索引真正的目录项记录由`「二级索引列 + 主键列 + 页号」`组成**==，这样能够保证`「二级索引列 + 主键列」`这个元组是唯一值。

  ![image_1cpb919suginpp7lbgsk0147f20.png-58.6kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a01bd2c92fbca0~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

  那插入一条新数据`c1=9`, `c2=1`, `c3='c'`的时候，可以先比较二级索引列，如果索引列都一致，则比较主键列，由于`9>7`，因此能够确定应该插入到页5。

  鉴于联合索引也属于二级索引，因此联合索引也遵循相同的原则。在 InnoDB 中，联合索引的**目录项记录**（非叶子节点）包含的内容是： `「所有联合索引列 + 主键列 + 页号」`。

  如果这个二级索引的索引列是unique，目录项记录内容也必须加上主键列吗？

  是，对于 Unique 索引，在物理存储上，InnoDB 的目录项记录依然会包含主键。

  虽然从逻辑上讲，`Unique` 约束已经保证了索引列的唯一性，不需要主键来辅助排序，但 InnoDB 依然保留了这种结构。这样做的好处是处理逻辑统一，不需要为 `Unique` 和 `Non-Unique` 索引维护两套完全不同的分裂与合并算法。

* 在 InnoDB 存储引擎中，一个数据页至少要存储两条记录。

  如果一个数据页只存储一条记录，每条记录由一个指针指向下一条记录，则B+树就会退化成一个线性链表，会存在以下问题：

  1. 节点数量无敌多，查找要进行IO的次数很多。
  2. 无法使用二分查找提效。

​	这里的至少要存储两条记录，其中不包含最小记录和最大记录，只包含用户记录。

​	表刚建的时候如果只插入了一条数据，这种情况可以只存储一条记录。

* 为啥不自动为每个列都建立个索引呢，这样不是查询效率更高吗？

  因为每建立一个索引都会建立一棵`B+`树，每插入一条记录都要维护各个记录、数据页的排序关系，这是很费性能和存储空间的。

  因为每一个索引，都对应着一颗B+树，就意味着每次增删改一条记录，都要维护涉及到的所有索引结构，既浪费存储空间，也影响性能。



## 6.3 索引的使用

### 6.3.1 使用索引的代价

* 空间上的代价

  每个索引都对应一颗B+树。

  B+树的每个节点都是一个页，每个页的大小为16KB。

  一个索引由很多个页构成，占用一定的存储空间。维护的索引越多，占用的存储空间越多。

* 时间上的代价

  B+树的特性是：

  1. 每一个页中的记录是按照索引列大小升序排序形成单链表
  2. 一层中的每一个页也是按照索引列的大小升序排列，组成双向链表

  因此每次对表中的数据进行增删改操作，都需要去维护所有索引B+树，存储引擎可能需要进行额外的页面分裂、页面回收来保持上述特性，因此如果建立很多索引，对性能也会有影响。



综上，索引不是越多越好，而是应该按照一定的原则建立又好又少的索引，这样一方面能提升我们搜索的效率，另一方面保证时间和空间的消耗比较小。

### 6.3.2 索引的使用原则

创建一个`person_info`表作为示例来展开后续的介绍：

```sql
CREATE TABLE person_info(
    id INT NOT NULL auto_increment,
    name VARCHAR(100) NOT NULL,
    birthday DATE NOT NULL,
    phone_number CHAR(11) NOT NULL,
    country varchar(100) NOT NULL,
    PRIMARY KEY (id),
    KEY idx_name_birthday_phone_number (name, birthday, phone_number)
);
```

该表的索引信息如下：

* 主键是`id`列，会自动递增。InnoDB存储引擎会为`id`列自动创建聚簇索引。

* 额外定义了二级索引`idx_name_birthday_phone_number`，由`name、birthday、phone_number`三列构成，索引会先按照`name`排序，`name`相同情况下按照`birthday`排序，`birthday`相同情况下按照`phone_number`排序。

  ![image_1dmo2n5c11ij019unpjtpf21tdr9.png-121.1kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16db02bc665cf0b1~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

#### 全值匹配

搜索条件列和索引列完全一致，且是等值匹配，此时索引能够生效。

示例：

该sql查询语句，联合索引的`name、birthday、phone_number`三列的都会用到：

```sql
SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday = '1990-09-27' AND phone_number = '15123983239';
```



注意：

***如果查询条件中三列的顺序调换了，对结果没有影响，因为MySQL的查询优化器，会自动对sql语句进行优化。***

也就是说下面这个sql语句和上面的，效果完全一致。

```sql
SELECT * FROM person_info WHERE birthday = '1990-09-27' AND phone_number = '15123983239' AND name = 'Ashburn';
```



#### 最左匹配原则

`<name, birthday, phone_number>`要让这个索引生效，查询条件不一定要包含所有列，可以仅包含部分列，但一定是最左边的列。

示例：

这两条sql语句`<name, birthday, phone_number>`索引都可以生效。

```sql
SELECT * FROM person_info WHERE name = 'Ashburn';
SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday = '1990-09-27';
```

但是下面这条sql语句就无法生效。

```sql
SELECT * FROM person_info WHERE birthday = '1990-09-27';
```

因为`<name, birthday, phone_number>`这个索引的数据，是先按照`name`列排序，`name`列相同的情况下，再按照`birthday`列排序，`<name, birthday>`都相同的情况下，再按照`phone_number`列排序。

对于下面这条sql语句，只能用到联合索引的`name`列，因为`name = 'Ashburn'`的所有记录中，`phone_number`列并不一定是有序的，因此无法使用索引。

```sql
SELECT * FROM person_info WHERE name = 'Ashburn' AND phone_number = '15123983239';
```



#### 最左模糊匹配原则

`<name, birthday, phone_number>`这个索引会先对`name`列进行排序，这里的排序意思是，先根据第一个字符排序，第一个字符相同的情况下再对第二个字符排序...***这里字符的排序规则就是根据为该表设置的字符集及比较规则来确定了***。

因此我们的sql语句如果使用模糊匹配，最左的元素不能是模糊的，例如：

这个sql可以使用`<name, birthday, phone_number>`索引

```sql
SELECT * FROM person_info WHERE name LIKE 'As%';
```

而这个sql不会走索引，而是会进行全表查询。

```sql
SELECT * FROM person_info WHERE name LIKE '%As%';
```

因为以`As`开头的字符串是有序的，可以走索引。字符串中间有`'As'`的字符串并不是有序的，所以只能全表扫描。



#### 范围匹配

下面这个范围匹配是可以走`<name, birthday, phone_number>`索引的，因为`name`列本身就是有序的。

```sql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow';
```



如果我们对左边的列进行范围匹配，则对其右边的列索引就不会生效了。

例如下面这条sql语句，由于`name`列是有序的，因此执行`name > 'Asa' AND name < 'Barlow' `这个查询条件是可以走索引的。

但是查询出来的所有满足`name > 'Asa' AND name < 'Barlow' `的记录，它们的`birthday`列并不是有序的，因此对birthday列无法使用索引。

```sql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow' AND birthday > '1980-01-01';
```



如果我们对左边的列进行精确匹配，则对右边的列可以进行范围查询

```sql
SELECT * FROM person_info 
WHERE name = 'Ashburn' 
AND birthday > '1980-01-01' AND birthday < '2000-12-31' 
AND phone_number > '15100000000';
```

由于`name`列记录是有序的，因此对name查询条件可以使用索引。

由于`name = 'Ashburn'`的记录其`birthday`列是有序的，因此`birthday`的查询条件也可以走索引。

但满足`name = 'Ashburn' AND birthday > '1980-01-01' AND birthday < '2000-12-31'`的记录，其`phone_number`是无序的，因此对`phone_number`列无法使用索引。



#### Order By

下面这条sql语句，要求先根据`name`列排序，`name`相等的时候根据`birthday`排序，`birthday`相等的时候根据`phone_number`排序。

```sql
SELECT * FROM person_info ORDER BY name, birthday, phone_number LIMIT 10;
```

这和`<name, birthday, phone_number>`索引的特点是完全一致的。

所以我们不需要额外进行排序，直接从这个二级索引中获取前10条数据即可。



如果没有索引，`order by`命令需要把所有记录都加载到内存，再用比如快速排序、归并排序等的排序算法在内存中对这些记录进行排序。

有的时候可能查询的结果集太大以至于不能在内存中进行排序的话，还可能暂时借助磁盘的空间来存放中间结果，排序操作完成后再把排好序的结果集返回到客户端。

在`MySQL`中，把这种在内存中或者磁盘上进行排序的方式统称为文件排序（filesort），***Filesort 的本质是“无法利用索引顺序，而必须在内存或磁盘中进行的额外排序操作”。***这种排序方式速度非常慢。相对来说如果有直接可用的索引，速度则是快多了。



注意：

* Order By的顺序必须和联合索引的顺序一致。如果顺序不一致，就无法使用索引了。

  下面这个sql语句就无法使用`<name, birthday, phone_number>`索引

  ```sql
  SELECT * FROM person_info ORDER BY birthday, name, phone_number LIMIT 10;
  ```

  

* 下面这些情况都会走直接索引，无须filesort

  ```sql
  SELECT * FROM person_info ORDER BY name LIMIT 10;
  
  SELECT * FROM person_info ORDER BY name, birthday LIMIT 10;
  
  SELECT * FROM person_info WHERE name = 'A' ORDER BY birthday, phone_number LIMIT 10;
  ```

* 下面

  ```sql
  # 这个也会走索引，能够获得name有序的列表
  # 但是最终依然要进行全表的filesort，因为phone_number是无序的
  # 例如有100条数据name都一样，phone_number不一样，则需要将这100条数据全部排序才能获得最终结果
  # 它相比于真正的全表扫描还是有一点点优化，因为至少name是有序的，MySQL 在进行 filesort 时，其内部的排序算法（如堆排序）可以利用这种局部有序性来稍微减轻一点点计算压力。
  # 但本质上，它需要进行全表的filesort。只是比没有索引稍微好一点。
  # 下面这条sql语句
  SELECT * FROM person_info ORDER BY name, phone_number LIMIT 10;
  ```

  



#### Group By

这个sql语句，它做了什么：

```sql
SELECT name, birthday, phone_number, COUNT(*) FROM person_info GROUP BY name, birthday, phone_number
```

1. 根据`name`进行分组
2. `name`相同的分组中，再根据`birthday`进行分组，得到小分组
3. `name`、`birthday`相同的小分组中，再根据`phone_number`分组，得到小小分组
4. COUNT(*)表示统计各个小小分组的包含的记录条数

由于`<name, birthday, phone_number>`索引本身就是按照`GROUP BY name, birthday, phone_number`这种分组方式的顺序排序的，因此可以直接使用该索引，无需重新分组。



### 6.3.3 索引失效的情况

#### ASC、DESC混用

这个`<name, birthday, phone_number>`索引，是先根据`name`升序排序，再根据`birthday`升序排序，最后根据`phone_number`升序排序。

进行`ORDER BY`排序的时候，如果想使用该联合索引，必须保证`ORDER BY`后的各个列排序方式一致，索引才会生效。

示例：

* `ORDER BY name, birthday LIMIT 10` （不显式指定，默认是ASC升序排序）

  直接从索引最左边往右读10条记录即可。

* `ORDER BY name DESC, birthday DESC LIMIT 10`

  直接从索引最右边往左读10条记录即可。



但是如果各个列排序方式不一致，例如要求按照name升序排序，再按照birthday降序排序：

`SELECT * FROM person_info ORDER BY name, birthday DESC LIMIT 10;`（name列不显式指定，默认是ASC升序排序）

如果依然使用`<name, birthday, phone_number>`索引，其查找方式为：

1. 先从索引的最左边开始找，找到name列的最小值。
2. 找到name值为最小值的所有记录。
3. 从这些记录的最右边开始往左找10条记录。
4. 如果name值为最小值的记录数<10条，则继续往右找name列的第二小值，重复上过程，直到找到10条记录为止。



这个过程非常复杂，所以为了避免采取更复杂的算法，MySQL遇到这样的情况会直接进行全量排序。

> 这里可能有疑问，一个页中的记录是根据单向链表相连接的，那一个页中的记录如何实现从右往左读呢？
>
> gemini解释的所谓从右往左读的流程：
>
> 1. 宏观定位：找到“书”的最后一页
>
> MySQL 首先利用 B+ 树的非叶子节点直接定位到索引叶子节点链表的**最后一个 Page（最右侧的页）**。
>
> 2. 页内“倒着数”：利用槽（Slot）跳跃
>
> 进入这个 16KB 的数据页后，记录确实是 `A -> B -> C` 这样单向连接的。为了找最后的 10 条，MySQL 的动作如下：
>
> ​	1). **访问页目录（Page Directory）**：页目录就像是这一页的“快捷书签”。它存储在页的尾部。
>
> ​	2). **定位最后一个槽（Last Slot）**：页目录的最后一个槽指向的是本页最大的记录（通常是内建的 `Supremum` 伪记录）。
>
> ​	3). **获取该组数据**：每个槽管理一组记录（通常 4-8 条）。MySQL 找到最后一个槽后，就拿到了这组记录的起始位置，然后**正向遍历**这 4-8 条，得到本页最末尾的几条。
>
> ​	4). **跨槽跳跃**：如果这一组只有 6 条，不够 10 条，MySQL 就会看**倒数第二个槽**。
>
> ​				它跳转到倒数第二个槽指向的记录。
>
> ​				从那里开始正向读，补齐剩下的 4 条。
>
> 3. 跨页“回头看”：利用 Prev Page 指针
>
> 如果这最后一页一共才 8 条记录（没填满），不够 10 条怎么办？
>
> - MySQL 会看当前页头（File Header）里的 `FIL_PAGE_PREV` 指针。
> - 直接“闪现”到物理上的**前一个页面**。
> - 在这个新页面里，重复“看最后一个槽”的动作，直到数满 10 条为止。
>
> 4. 关键结论：这依然是“顺序 IO”
>
> 虽然在页内部有“小碎步”的正向遍历，但这种操作是在内存中完成的，速度极快。
>
> - **对比 Filesort**：Filesort 是“先把 4 万行全部加载并重排”，这叫**全量计算**。
> - **对比这种扫描**：它只触碰了最后 1-2 个页面，读取了大概 10-20 条记录。这叫**流式读取**。



#### Order By排序列包含非同一个索引的列

下面`name`和`country`就不属于同一个索引，name列属于`<name, birthday, phone_number>`索引，country列不属于任何索引，因此会直接走全量排序，而不会走索引。

```sql
SELECT * FROM person_info ORDER BY name, country LIMIT 10;
```



#### 使用了复杂表达式

假设表中有一个整数列`my_col`，我们为这个列建立了索引。下边的两个`WHERE`子句虽然语义是一致的，但是在效率上却有差别：

1. `WHERE my_col * 2 < 4`
2. `WHERE my_col < 4/2`

第1个`WHERE`子句中`my_col`列并不是以单独列的形式出现的，而是以`my_col * 2`这样的表达式的形式出现的，存储引擎会依次遍历所有的记录，计算这个表达式的值是不是小于`4`，所以这种情况下是使用不到为`my_col`列建立的`B+`树索引的。

而第2个`WHERE`子句中`my_col`列并是以单独列的形式出现的，这样的情况可以直接使用`B+`树索引。

所以结论就是：如果索引列在比较表达式中不是以单独列的形式出现，而是以某个表达式，或者函数调用形式出现的话，是用不到索引的。

因为索引是按照`my_col`列创建的，而按照不是`my_col`参与表达式后的结果/`my_col`参与函数调用之后的结果来创建的。



### 6.3.4 回表

#### 回表的代价

以`<name, birthday, phone_number>`二级索引为例，看该sql语句是如何执行的

```sql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow';
```

1. 首先，根据二级索引找到所有满足` name > 'Asa' AND name < 'Barlow'`的记录
2. 由于`SELECT *`，而二级索引不包含`country`列信息，因此需要回表到聚簇索引查询完整的记录信息。



满足` name > 'Asa' AND name < 'Barlow'`的二级索引记录在磁盘中是连续存储的，也就是说这些记录会集中分布在一到多个数据页，所以在二级索引查询该数据我们只需要进行`顺序I/O`。

但我们在二级索引查询到的这些记录的`id主键`并不一定是连续有序的，因此这些记录可能散落在聚簇索引的各个页中，因此回表即在聚簇索引查询的过程我们需要进行`随机I/O`。

`随机I/O`的性能实际上是非常低的。

因此，需要回表的记录个数越多，使用二级索引的性能就越低，MySQL就越倾向于进行全表查询。

例如假设满足` name > 'Asa' AND name < 'Barlow'`的记录占全表的90%，如果还使用二级索引，对这90%的记录都需要回表，那还不如直接进行全表扫描了。



那什么时候采用全表扫描的方式，什么时候使用采用`二级索引 + 回表`的方式去执行查询呢？

查询优化器会进行统计，大体来说：需要回表的记录数越多，就越倾向于使用全表扫描，反之倾向于使用`二级索引 + 回表`的方式。

下面这个sql由于需要查询的记录条数少，因此回表次数少，则MySQL更倾向于使用`二级索引 + 回表`的方式。

```sql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow' LIMIT 10;
```



下面这个sql语句，如果使用二级索引，则对100%的记录都需要进行回表，那还不如直接全表扫描再进行filesort。

```sql
SELECT * FROM person_info ORDER BY name, birthday, phone_number;
```



如果我们增加了limit子句，这样需要回表的记录特别少，优化器就会倾向于使用`二级索引 + 回表`的方式执行查询。

```sql
SELECT * FROM person_info ORDER BY name, birthday, phone_number LIMIT 10;
```



#### 覆盖索引-避免回表

之所以需要回表是因为我们查询的列的范围超过二级索引包含的列的范围，

例如 `*` 包含`id, name, birthday, phone_number, country`五列，而`<name, birthday, phone_number>`二级索引不包含country列，所以需要回到聚簇索引查询完整的信息。（注意二级索引也是包含主键id列的哦）

```sql
SELECT * FROM person_info ORDER BY name, birthday, phone_number;
```



如果我们查询的列的范围在索引包含的列的范围之内，那么就不需要回表了，例如这两条sql语句：

```sql
SELECT name, birthday, phone_number FROM person_info WHERE name > 'Asa' AND name < 'Barlow'

SELECT name, birthday, phone_number  FROM person_info ORDER BY name, birthday, phone_number;
```



因此为了性能考虑，不推荐使用 `*` 作为查询列表，推荐将需要的列依次写出。



### 6.3.5 索引创建原则

#### 只为搜索、排序、分组的列创建索引

只为`WHERE`/`ORDER BY`/`GROUP BY`子句中的列创建索引。

例如该sql子句，我们只为`name`列创建索引即可，无须为`birthday`、`country`列创建索引，这两列可以回表查询得到。

```sql
SELECT birthday, country FROM person_name WHERE name = 'Ashburn';
```



#### 为大基数的列创建索引

列的基数表示这一列中不同的值的个数，例如某个列包含值`2, 5, 8, 2, 5, 8, 2, 5, 8`，虽然有`9`条记录，但该列的基数却是`3`，只有`2、5、8`三个不同的值。

如果一个列基数为1，则说明这个列的所有值都一样，那为这个列创建索引是没有任何效率提升的，还会带来额外的维护开销。

如果一个列的基数越大，则说明他的区分度越大，为其创建索引的收益更大，因为根据索引能够更快定位到局部。



#### 索引列的类型尽量取小

如果我们一个列的类型为整数类型，可以选择的类型有`TINYINT`、`MEDIUMINT`、`INT`、`BIGINT`，它们表示的范围从左到右依次增大。

我们倾向于如果索引列的数据类型能取小，就取小。原因如下：

1. 数据类型越小，在查询时进行的比较操作越快（CPU层面）
2. 数据类型越小，一条记录占用的空间越小，一个页能存储的记录条数越多，搜索时需要查询的页越少，磁盘IO次数更少，读写效率更高。
3. 对于主键来说更加适用，因为聚簇索引、二级索引中都会存储一份主键。如果主键使用更小的数据类型，也就意味着节省更多的存储空间和更高效的`I/O`。



#### 字符串可以根据前缀创建索引

字符串是由字符构成的，一个字符根据选用的字符集不同，可能占用不同的字节数。

例如如果使用utf8字符集，则一个字符需要占用1-3字节不等。

如果说我们的字符串很长的话，则其占用的字节数也会很大。

假设我们需要为类型为字符串的某列建立索引，如果这个列存储的字符串通常都非常长，那我们为该列创建的索引会存在如下问题：

1. 这个索引会非常占用空间，因为其索引列占用的字节数多。
2. 查询比较的时候耗时长，因为要一个个字符对比。



是一种折中的方案，是仅根据前缀创建索引，例如我们只为这列的前10个字符创建二级索引，则：

1. 二级索引不用存储完整的字符串，节省了存储空间。
2. 也能够大致实现排序功能，查询速度比没有索引的时候快多了
3. 如果我们要查询完整的字符串，可以定位到匹配的前缀，然后回表查询。



比方说我们在建表语句中只对`name`列的前10个字符进行索引可以这么写：

```sql
CREATE TABLE person_info(
    name VARCHAR(100) NOT NULL,
    birthday DATE NOT NULL,
    phone_number CHAR(11) NOT NULL,
    country varchar(100) NOT NULL,
    KEY idx_name_birthday_phone_number (name(10), birthday, phone_number)
);    
```

`name(10)`就表示在建立的`B+`树索引中只保留记录的前`10`个字符的编码。

这种只索引字符串值的前缀的策略是我们非常鼓励的，尤其是在字符串类型存储的字符比较多的时候。



使用列前缀索引的方式，无法支持使用`Order By`索引排序。

例如我们创建一个这个索引`<name(10), birthday, phone_number>`，下面这个sql语句无法使用该索引进行排序，因为该索引只能保证`name(10)`有序，无法保证完整的`name`列有序，MySQL 无法确信索引里的顺序就是真实的顺序，因此它会放弃索引排序，转而进行全量排序filesort。

```sql
SELECT * FROM person_info ORDER BY name LIMIT 10;
```



#### 尽量使用AUTO_INCREMENT自动生成主键

假设某个数据页存储的记录已经满了，它存储的主键值在`1~100`之间：

![image_1capq3r1o1geqdck1cnc1fkihj39.png-28.1kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16db02bc7153fee5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

如果此时再插入一条主键值为`9`的记录，那它插入的位置就如下图：

![image_1capq7nnv13en8b31lvtj2i1e8lm.png-35.3kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16db02bc77059366~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

我们需要把当前页面分裂成两个页面，把本页中的一些记录移动到新创建的这个页中。

因此如果我们频繁在索引的中间插入输入，则会造成频繁的页分裂和记录移位，导致产生大量的性能损耗。

所以对于主键列，我们尽量使用AUTO_INCREMENT修饰，让其每插入一条新的数据自动递增生成主键。



#### 避免创建冗余索引

下面这个建表语句创建了idx_name_birthday_phone_number、idx_name两个二级索引。

```sql
CREATE TABLE person_info(
    id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    birthday DATE NOT NULL,
    phone_number CHAR(11) NOT NULL,
    country varchar(100) NOT NULL,
    PRIMARY KEY (id),
    KEY idx_name_birthday_phone_number (name(10), birthday, phone_number),
    KEY idx_name (name(10))
);    
```

实际上idx_name这个索引是完全冗余的，因为通过`idx_name_birthday_phone_number`索引就可以对`name`列进行快速搜索。

下面这种情况是极易容易忽略的：

```sql
CREATE TABLE repeat_index_demo (
    c1 INT PRIMARY KEY,
    c2 INT,
    UNIQUE uidx_c1 (c1),
    INDEX idx_c1 (c1)
);  
```

`c1`列本身是主键，MySQL会自动为其创建聚簇索引。

`UNIQUE uidx_c1 (c1)`又给c1列创建了唯一索引。

`INDEX idx_c1 (c1)`又给c1列创建了普通索引。

所以定义的唯一索引和普通索引都是冗余的。



定义了冗余的索引，不会提升查询性能，还会占用存储空间，带来额外的维护成本，所以一定要避免定义冗余的索引。



# 7. MySQL的数据目录

> 本书的知识点以MySQL5.7为准

## 7.1 是什么

`数据目录`是用来存储`MySQL`在运行过程中产生的数据。

包含「用户创建的数据库、插入的记录等用户数据」以及「MySQL为程序能够更好的运行创建的一些额外数据」。

## 7.2 在哪里

数据目录位置就对应着一个系统变量`datadir`，通过下面的命令可以查询其位置：

```sql
mysql> SHOW VARIABLES LIKE 'datadir';
+---------------+--------------------------+
| Variable_name | Value                    |
+---------------+--------------------------+
| datadir       | /opt/homebrew/var/mysql/ |
+---------------+--------------------------+
1 row in set (0.011 sec)
```

## 7.3 数据目录的结构

数据目录针对数据库信息、表信息、视图信息等有不同的存储方式，下面分别展开介绍。

### 7.3.1 数据库信息

当我们使用`CREATE DATABASE 数据库名`语句创建一个数据库的时候，MySQL会做两件事：

1. 在数据目录下创建一个和数据库名同名的子目录。
2. 在数据库同名子目录下创建一个名为`db.opt`文件，这个文件存储数据库的各种属性，例如数据库使用的字符集和比较规则信息等。



演示：

```sql
mysql> SHOW DATABASES;                                                                                                                                                                                   
+--------------------+
| Database           |
+--------------------+
| dahaizi            |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| xiaohaizi          |
+--------------------+
6 rows in set (0.001 sec)
```

/opt/homebrew/var/mysql/目录：

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/image-20260103211006186.png" alt="image-20260103211006186" style="zoom:50%;" />

可以看到，除了`information_schema`这个特殊的系统数据库外，其他的数据库在`数据目录`下都有对应的文件夹。



### 7.3.2 表信息

表信息包含两部分：

1. 表结构信息（例如表名、表的所有列信息、表的索引、表的字符集和比较规则...）
2. 表中的数据



#### InnoDB

**表结构信息的存储**

当我们在一个数据库中创建新表之后，InnoDB和MyISAM都会在对应的数据库子目录下创建一个名为`表名.frm`的文件，专门用于存储表描述信息。

示例：

比方说我们在`dahaizi`数据库下创建一个名为`test`的表：

```sql
mysql> USE dahaizi;
Database changed

mysql> CREATE TABLE test (
    ->     c1 INT
    -> );
Query OK, 0 rows affected (0.03 sec)
```

那在数据库`dahaizi`对应的子目录下就会创建一个名为`test.frm`的用于描述表结构的文件。注意：这个后缀名为.frm的文件是以二进制格式存储的，直接打开会是乱码的。



**表中的数据的存储**

InnoDB中使用表空间来管理页。

表空间是一个抽象的概念，每个表空间可以被划分为很多个页，表数据就存放在某个表空间的某些页里面。

表空间有不同的类型：

* 系统表空间

  从`MySQL5.5.7`到`MySQL5.6.6`之间的各个版本中，我们表中的数据都会被默认存储到这个 ***系统表空间***。

  系统表空间就对应着数据目录下名为`ibdata1`的文件。

  ![image-20260103201906180](/Users/yutinglai/Documents/note/MySQL-note/assets/image-20260103201906180.png)

  

* 独立表空间

  在`MySQL5.6.6`及之后的版本中，`InnoDB`不会默认的把各个表的数据存储到系统表空间中，而是为每一个表建立一个独立表空间，也就是说我们创建了多少个表，就有多少个独立表空间。

  如果使用独立表空间存储表数据，会在数据库对应的子目录创建一个`表名.ibd`文件，这就是独立表空间对应的文件。

  <img src="/Users/yutinglai/Documents/note/MySQL-note/assets/image-20260103202132984.png" alt="image-20260103202132984" style="zoom:67%;" />

  

* 其它类型的表空间

  * 通用表空间（general tablespace）

  * undo表空间（undo tablespace）

  * 临时表空间（temporary tablespace）

    

* 注意

  * 想手动指定数据是存储到系统表空间/独立表空间，可以通过启动参数`innodb_file_per_table`控制

    该命令只对新建的表起作用，对于已经分配了表空间的表并不起作用。

    ```ini
    # 0 - 使用系统表空间
    # 1 - 使用独立表空间
    [server]
    innodb_file_per_table=0
    ```

    

  * 将已经存于系统表空间中的表转移到独立表空间

    ```sql
    ALTER TABLE 表名 TABLESPACE [=] innodb_file_per_table;
    # 或
    ALTER TABLE 表名 TABLESPACE innodb_file_per_table;  # = 号可省略
    ```

    

  * 已经存在独立表空间的表转移到系统表空间

    ```sql
    ALTER TABLE 表名 TABLESPACE [=] innodb_system;
    # 或
    ALTER TABLE test TABLESPACE innodb_system;  # = 号可省略
    ```

    

#### MyISAM

`MyISAM`的数据和索引是分开存放的，因此`MyISAM`是用不同的文件来存储数据信息和索引信息。

`MyISAM`没有表空间的概念。

假设我们在`xiaohaizi`目录创建了`test`表，则在数据目录下的`xiaohaizi`数据库对应的子目录中，会创建这三个文件：

```ini
# 表结构信息
test.frm
# 表用户记录
test.MYD
# 表索引
test.MYI
```



### 7.3.3 视图信息

**视图是什么**

视图本质就是一个查询语句的别名

```sql
-- 将复杂的关联查询定义为视图
CREATE VIEW v_order_details AS
SELECT o.id, u.name, p.item_name, (o.price * o.count) AS total_fee
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN products p ON o.product_id = p.id;

-- 以后只需要一句话就能查到结果
SELECT * FROM v_order_details WHERE name = '张三';
```



**如何存储视图信息**

如果我们创建了一个视图，则会以`视图名.frm`文件的形式存放在所属数据库的子目录下。



### 7.3.4 其它

上面所说的是用户自定义的数据如何存放在文件系统。

为了更好地运行程序，数据目录还会存放以下几种文件：

* 服务器进程文件

  当运行一个MySQL服务器程序的时候，MySQL服务器会把自己的进程ID写入到一个文件中。

* 服务器日志文件

  服务器运行过程中产生的各种日志对应的文件。

* SSL密钥文件

  用于客户端和服务端安全通信。



## 7.4 MySQL系统数据库

我们在执行`SHOW DATABASES;`的时候，除了自己创建的数据库，还有一些自带的系统数据库，这些数据库中会存储MySQL运行过程中所需的信息以及运行状态信息。

```sql
mysql> SHOW DATABASES;                                                                                                                                                                                   
+--------------------+
| Database           |
+--------------------+
| dahaizi            |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| xiaohaizi          |
+--------------------+
6 rows in set (0.001 sec)
```

下面分别介绍下系统数据库各自存储的内容：

* `mysql`
  * MySQL的用户帐户和权限信息。
  * 运行过程中产生的日志信息。
  * 帮助信息。
  * 时区信息。
  * ...
* `information_schema`
  * MySQL服务器维护的所有其他数据库的信息，比如有哪些表、哪些视图、哪些触发器、哪些列、哪些索引等等。
* `performance_schema`
  * MySQL服务器运行过程中产生的状态信息，包括统计最近执行了哪些语句，在执行过程的每个阶段都花费了多长时间，内存的使用情况等等信息。
* `sys`
  * 通过视图的形式把`information_schema `和`performance_schema`结合起来，让程序员可以更方便的了解`MySQL`服务器的一些性能信息。



## 7.5 总结

1. 数据目录是MySQL存储「用户创建的数据」以及「MySQL运行时需要的数据」的目录。

   简单来说就是MySQL用于「存储运行过程中产生的数据」的地方。

2. 用`SHOW VARIABLES LIKE 'datadir';`即可查询MySQL服务器的数据目录在哪里。

3. 数据目录存储的信息包含数据库信息、表信息、视图信息这种「用户创建的数据」，以及「MySQL运行时需要的数据」。

   分别是这样存储的（InnoDB）：

   * 数据库信息：当我们创建一个新的数据库，MySQL会1. 在数据目录下创建一个子目录，该子目录以数据库名命名。2. 会在该子目录创建`db.opt`文件，存储数据库的元信息（例如字符集、比较规则等）。

   * 表信息：表信息包含两部分 1. 表结构信息 2. 用户记录。

     1. 表结构信息：当我们创建一个新的表，会在数据库子目录下创建一个`表名.frm`文件，存储表的结构信息（例如表名、所所有列信息等）。

     2. 用户记录：存储在表空间。表空间分为系统表空间/独立表空间。

        ​		`MySQL5.6.6`之前默认用系统表空间存储，则所有用户记录存储在数据目录的`ibdata1`文件中。

        ​		`MySQL5.6.6`及之后默认用独立表空间存储，则在数据库子目录中，会创建`表名.ibd`文件，用于存储用户记录。

   * 视图信息：会以`视图名.frm`文件形式存储在数据库子目录中。

   >  注意：
   >
   > 本书都是基于MySQL5.7介绍。
   >
   > MySQL8.0开始，数据库、表和视图的元数据都不再存储于db.opt和.frm文件中，而是存储于数据目录下一个名为`mysql.ibd` 的系统表空间。可以理解为8.0版本之后，.frm文件已经彻底消失。
   >
   > 8.0版本之后用户记录依然存储于.ibd文件中。

4. 除了用户自定义的数据库，还有`mysql`、`information_schema`、`performance_schema`、`sys`这种自带的系统数据库，用于存储MySQL运行过程中需要的信息（例如用户帐户和权限），以及运行过程中产生的状态信息（例如最近执行的语句花费了所长时间）等等。



# 8. InnoDB表空间

## 8.1 表空间是什么

`表空间`是一个抽象的概念。

对于系统表空间来说，对应着文件系统中一个或多个实际文件。

对于每个独立表空间来说，对应着文件系统中一个名为`表名.ibd`的实际文件。

可以把表空间想象成被切分为很多个`页`的池子，当我们想为某个表插入一条记录的时候，就从池子中捞出一个对应的页来把数据写进去。



## 8.2 前置基础

### 页面类型

可以看到所有页面的类型都以`FIL_PAGE`或`FIL_PAGE_TYPE`作为前缀，为更简便，后面在描述页面类型的时候会将前缀省略。

| 类型名称                  | 十六进制 | 描述                             |
| ------------------------- | -------- | -------------------------------- |
| `FIL_PAGE_TYPE_ALLOCATED` | 0x0000   | 最新分配，还没使用               |
| `FIL_PAGE_UNDO_LOG`       | 0x0002   | Undo日志页                       |
| `FIL_PAGE_INODE`          | 0x0003   | 段信息节点                       |
| `FIL_PAGE_IBUF_FREE_LIST` | 0x0004   | Insert Buffer空闲列表            |
| `FIL_PAGE_IBUF_BITMAP`    | 0x0005   | Insert Buffer位图                |
| `FIL_PAGE_TYPE_SYS`       | 0x0006   | 系统页                           |
| `FIL_PAGE_TYPE_TRX_SYS`   | 0x0007   | 事务系统数据                     |
| `FIL_PAGE_TYPE_FSP_HDR`   | 0x0008   | 表空间头部信息                   |
| `FIL_PAGE_TYPE_XDES`      | 0x0009   | 扩展描述页                       |
| `FIL_PAGE_TYPE_BLOB`      | 0x000A   | BLOB页                           |
| `FIL_PAGE_INDEX`          | 0x45BF   | 索引页，也就是我们所说的`数据页` |

### 页的通用部分

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f33c338667~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crjupisqne61uer17ikh6l1v8k9.png-44.9kB" style="zoom:67%;" />

任何类型的页都包含这两部分：

- `File Header`：记录页面的一些通用信息
- `File Trailer`：校验页是否完整，保证从内存到磁盘刷新时内容的一致性

`File Header`的各个部分如下表格所示：

| 名称                               | 占用空间大小 | 描述                                                         |
| ---------------------------------- | ------------ | ------------------------------------------------------------ |
| `FIL_PAGE_SPACE_OR_CHKSUM`         | `4`字节      | 页的校验和（checksum值）                                     |
| `FIL_PAGE_OFFSET`                  | `4`字节      | 页号                                                         |
| `FIL_PAGE_PREV`                    | `4`字节      | 上一个页的页号                                               |
| `FIL_PAGE_NEXT`                    | `4`字节      | 下一个页的页号                                               |
| `FIL_PAGE_LSN`                     | `8`字节      | 页面被最后修改时对应的日志序列位置（英文名是：Log Sequence Number） |
| `FIL_PAGE_TYPE`                    | `2`字节      | 该页的类型                                                   |
| `FIL_PAGE_FILE_FLUSH_LSN`          | `8`字节      | 仅在系统表空间的一个页中定义，代表文件至少被刷新到了对应的LSN值 |
| `FIL_PAGE_ARCH_LOG_NO_OR_SPACE_ID` | `4`字节      | 页属于哪个表空间                                             |

注意：

- 表空间中的每一个页都对应着一个页号`FIL_PAGE_OFFSET`，占4个字节组成，即32个比特位，所以一个表空间最多可以拥有2³²个页。

  一个页默认占16KB，因此一个表空间最多支持64TB的数据。

  表空间的第一个页的页号为0，之后的页号分别是1，2，3...依此类推

- 数据页会根据`FIL_PAGE_PREV`和`FIL_PAGE_NEXT`两个字段来建立双向链表。



## 8.3 独立表空间结构

### 全局图

![独立表空间.drawio](/Users/yutinglai/Documents/note/MySQL-note/assets/独立表空间.drawio-7679238.svg)

### 区（extent）

#### 1️⃣是什么

表空间中的页太多了，为了便于管理，提出了区的概念。

每个页的大小为16KB，==***连续64个页为一个区***==，即一个区默认占用1MB。

不论是系统表空间还是独立表空间，都可以看成是由若干个区组成的。



#### 2️⃣为什么要引入区的概念

在引入区概念之前

当要插入一条记录的时候，如果已有的页空间不足，就会申请一个新的页然后在新的页插入数据。这个新的页和已有的页不一定是物理相邻，但是可以根据`FIL_PAGE_PREV`和`FIL_PAGE_NEXT`这两个页头属性来和已有的页建立逻辑相邻关系。

如果进行范围查询，我们可以定位到符合条件的最左边或最右边的记录，然后沿着双向链表往下找就能够找到所有符合条件的记录。

由于逻辑相邻的页并不是物理相邻，因此在进行范围查询的时候就会需要进行大量的随机I/O，相比顺序I/O，随机I/O效率是非常低的。

在引入区概念之后

当一个表的数据量很大的时候，MySQL给索引分配空间的时候，会以区为单位分配空间，区就是物理上连续的64个页。当表中数据特别多的时候，还可以一次性分配多个连续的区。虽然可能会造成一定的空间浪费（数据不足填充满整个区），但是消除了很多随机I/O，因此此时逻辑相邻的页也是物理相邻的，能够大大提升性能。



#### 4️⃣XDES Entry

全称为Extent Descriptor Entry

每个区都对应一个XDES Entry结构，该结构记录了对应的区的属性。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f343654829~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crre79uq9971bsdj9s1i0j11en8a.png-96.2kB" style="zoom:80%;" />

* `Segment ID`

  该区所在的段的ID。

  当该区被分配给某个段时，该字段的值才有意义。否则该字段的值没有意义。

* `List Node`

  如果我们想定位表空间内的某一个位置的话，只需指定「页号」以及该位置在指定页号中的「页内偏移量」即可。所以：

  - `Pre Node Page Number`和`Pre Node Offset`的组合就是指向前一个`XDES Entry`的指针
  - `Next Node Page Number`和`Next Node Offset`的组合就是指向后一个`XDES Entry`的指针。
  
* `State`

  这个字段表明区的状态，可选值：

  | 状态名      | 含义                 |
  | ----------- | -------------------- |
  | `FREE`      | 空闲的区             |
  | `FREE_FRAG` | 有剩余空间的碎片区   |
  | `FULL_FRAG` | 没有剩余空间的碎片区 |
  | `FSEG`      | 附属于某个段的区     |

  > 处于`FREE`、`FREE_FRAG`以及`FULL_FRAG`这三种状态的区都是直属于表空间
>
  > 处于`FSEG`状态的区是附属于某个段的

* `Page State Bitmap`

  一个区默认有64个页。

  `Page State Bitmap`占用16字节，即128bits，这128bits被分为64部分，每部分2bits，对应区中的一个页。

  第一个bit表示对应的页是否是空闲的，第二个bit预留的，还没有用。

  

#### 5️⃣XDES Entry链表

通过XDES Entry的`List Node`属性，InnoDB会将所有XDES Entry根据状态连接成不同链表：

* `Free`链表：所有状态为`FREE`的`XDES Entry`连接而成的链表。
* `FREE_FRAG`链表：所有状态为`FREE_FRAG`的`XDES Entry`连接而成的链表。
* `FULL_FRAG`链表：所有状态为`FULL_FRAG`的`XDES Entry`连接而成的链表。

这三个链表是直属于表空间的三个链表。

****

除了会维护这三个链表之外，InnoDB还会为每个***段***维护三个链表：

- `FREE`链表：同一个段中，所有页面都是空闲的区对应的`XDES Entry`结构会被加入到这个链表。

  注意这和直属于表空间的`FREE`链表是两个链表，此处的`FREE`链表是附属于某个段的。

- `NOT_FULL`链表：同一个段中，仍有空闲空间的区对应的`XDES Entry`结构会被加入到这个链表。

- `FULL`链表：同一个段中，已经没有空闲空间的区对应的`XDES Entry`结构会被加入到这个链表。

一个索引对应两个段，每个段都会维护上述的三个链表。

****

示例：

```sql
CREATE TABLE t (
    c1 INT NOT NULL AUTO_INCREMENT,
    c2 VARCHAR(100),
    c3 VARCHAR(100),
    PRIMARY KEY (c1),
    KEY idx_c2 (c2)
)ENGINE=InnoDB;
```

这个t表包含了两个索引：聚簇索引以及二级索引`idx_c2`。每个索引都对应两个段，因此一共包含四个段，也就是会维护`4 * 3 = 12`个段链表。加上直属于表空间的三个链表。整个独立表空间需要维护15个链表。

****

上述所说的每个链表，都对应一个`List Base Node`结构，这是链表的基节点，存储了链表的信息。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f388927e1c~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crrehf6i1jsq1j5cubj1mdoh77a4.png-81.6kB" style="zoom:67%;" />

- `List Length`表明该链表一共有多少节点。
- `First Node Page Number`和`First Node Offset`共同组成链表头节点的指针。
- `Last Node Page Number`和`Last Node Offset`共同组成链表尾节点的指针。

某个链表对应的`List Base Node`结构一般会放置在表空间中固定的位置，以便于快速定位到某一个链表。



#### 6️⃣插入数据的过程

刚开始往一个表中插入数据的时候，如果数据量比较少，会先从表空间的`FREE_FRAG`链表尝试获取一个状态为`FREE_FRAG`的区，也就是有空闲空间的碎片区。

如果找到了，那么从该区中取一些零散的页把数据插进去；

否则到表空间下申请一个状态为`FREE`的区，也就是空闲的区，把该区的状态变为`FREE_FRAG`，然后从该新申请的区中取一些零散的页把数据插进去。

之后不同的段使用零散页的时候都会从该区中取，直到该区中没有空闲空间，然后该区的状态就变成了`FULL_FRAG`，然后移到`FULL_FRAG`链表中。

***当段中数据已经占满了32个零散的页后***，就直接申请完整的区来插入数据了。

再插入数据时，会先获取属于段的`NOT_FULL`链表的头节点，直接把数据插入这个头节点对应的区中即可。如果该区的空间已经被用完，就把该节点移到`FULL`链表中。

如果`NOT_FULL`链表已经为空，则从属于段的`FREE`链表取一个区，将其状态变更为`NOT_FULL`，然后移至`NOT_FULL`链表，然后就可以将数据插入到这个区中。



### 组

==***每256个区被划分成一组。***==

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f33c4a1c3a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1cri1nutcorp5ghf5c7vqagt1j.png-71.4kB" style="zoom:77%;" />

### 段（segment）

#### 1️⃣为什么要有段

如果所有产生的非叶子节点索引数据以及叶子节点用户记录数据都存在相同的区中，会存在这样的情况：

* insert一些数据，有一个非叶子节点页，以及若干叶子节点页，因此会申请一个区，然后非叶子节点页和叶子节点页都顺序存在这个区中。
* 随着insert的数据变多，需要进行页分裂/增加层级，则需要一个新的非叶子节点，则会从同一个区中获取下一个空闲页存储。
* 继续insert数据，需要更多的叶子页，继续从同一个区中获取下一个空闲页存储。

最终这个区的存储页信息会呈现：非叶子->叶子->叶子->非叶子->叶子...这样的交错状态。

那进行范围扫描的时候，就会呈现「读叶子页 A -> **跳过**非叶子页 B -> 读叶子页 C -> **跳过**非叶子页 D...」这样的效果，范围扫描的效率大大降低。

为了避免出现这样的问题，InnoDB对B+树的叶子节点和非叶子节点分开存储。叶子节点有单独的区，非叶子节点也有单独的区。***存放叶子节点的区的集合为一个段，存放非叶子节点的区的集合也为一个段***，也就是说一个索引会生成2个段，一个叶子节点段，一个非叶子节点段。



#### 2️⃣段以区为单位申请存储空间会造成空间浪费

我们知道一个索引，会生成两个段，一个是用来存储叶子节点数据的段，一个是用来存储非叶子节点数据的段。

段以区为单位申请存储空间，一个区默认占用1MB。

也就是说一个索引，至少会占用2MB。就算表中只存了几条数据，也会占用2MB，这会造成很大的空间浪费。

为什么会出现这样的问题，是因为段以区为单位申请存储空间，一个区的所有页都只能存储该段的数据，不管该段的数据是否能填满这个区。

为了解决上述的问题，InnoDB提出了碎片（fragment）区的概念。碎片区中的页可以用于不同目的，比如有些页用于段A，有些页用于段B，有些页甚至哪个段都不属于。碎片区直属于表空间，并不属于任何一个段。

此后为某个段分配存储空间的策略是这样的：

- 在刚开始向表中插入数据的时候，段是从某个碎片区***以单个页面为单位***来分配存储空间的。
- 当某个段已经占用了32个碎片区页面之后，就会以完整的区为单位来分配存储空间。

**所以现在段不能仅定义为是某些区的集合，更精确的应该是某些零散的页面以及一些完整的区的集合。**



#### 3️⃣INODE Entry

就像是每一个区都有一个`XDES Entry`来记录该区的属性一样，每个段有一个`INODE Entry`记录其属性信息。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f4087c4a56~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crrju0cnji91a2fhv91ijb15hgb1.png-111.4kB" style="zoom:85%;" />

* `Segment ID`：本INODE Entry对应的段的ID。

* `NOT_FULL_N_USED`：NOT_FULL链表中已经使用了多少个页面。

* `List Base Node`：分别为段的FREE链表、NOT_FULL链表、FULL链表的List Base Node。

* `Magic Number`：标记这个INODE Entry是否已经被初始化了（初始化的意思就是把各个字段的值都填进去了）。如果这个数字是97937874，表明该INODE Entry已经初始化，否则没有被初始化。

* `Fragment Array Entry`：因为一个段由若干零散的页面和一些完整的区组成，这些区的信息可以通过三个链表获取到，而Fragment Array Entry就是用于存储这些零散页面的信息。每个Fragment Array Entry结构都对应着一个零散的页面，这个结构占4个字节，记录一个零散页面的页号。

  

### 各类型页面详细介绍

整个表空间由很多个区组成，每256个区划分为一组。

所有组的头几个页面都是用于存储组的属性信息，如图：

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f33df9307a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crjo0hl4q8u1dkdofe187b10fa9.png-105.2kB" style="zoom:80%;" />

* 表空间的第0组的前3个页是固定的，即extent 0这个区的前三个页：
  * `FSP_HDR`类型：存储 1. 整个表空间的整体属性 2. 本组所有区的属性。每个表空间只有一个FSP_HDR页面。
  * `IBUF_BITMAP`类型：存储本组所有的区的所有页面关于INSERT BUFFER的信息。
  * `INODE`类型：存储INODE Entry信息。
* 其余各个组的前两个页是固定的：
  * `XDES`类型：存储本组所有区的属性。FSP_HDR页面和XDES页面作用类似，只不过FSP_HDR页面还会额外存储所在表空间的整体属性的信息。
  * `IBUF_BITMAP`类型：上面介绍过。



下面对各个类型的页面展开介绍。



#### 1️⃣FSP_HDR

这是第0个组的第0个页面，也是表空间的第0个页面，其页号为0。

它存储了「表空间的一些整体属性」以及「第一个组内256个区的对应的XDES Entry结构」。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f4733af475~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crmfvigk938c8h1hahglr15329.png-146.8kB" style="zoom:80%;" />

从图中可以看出，一个完整的FSP_HDR类型的页面大致由5个部分组成：

| 名称                | 中文名       | 占用空间大小 | 简单描述                       |
| ------------------- | ------------ | ------------ | ------------------------------ |
| `File Header`       | 文件头部     | `38`字节     | 页的一些通用信息               |
| `File Space Header` | 表空间头部   | `112`字节    | 表空间的一些整体属性信息       |
| `XDES Entry`        | 区描述信息   | `10240`字节  | 存储本组256个区对应的属性信息  |
| `Empty Space`       | 尚未使用空间 | `5986`字节   | 用于页结构的填充，没啥实际意义 |
| `File Trailer`      | 文件尾部     | `8`字节      | 校验页是否完整                 |

重点看File Space Header和XDES Entry这两个部分：

##### File Space Header部分

该部分用于存储表空间的整体属性，如图：

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f47508ede5~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crrp2qp310rc10fd33ch716hcp.png-148.1kB" style="zoom: 77%;" />

| 名称                                      | 占用空间大小 | 描述                                                         |
| ----------------------------------------- | ------------ | ------------------------------------------------------------ |
| `Space ID`                                | `4`字节      | 表空间的ID                                                   |
| `Not Used`                                | `4`字节      | 这4个字节未被使用，可以忽略                                  |
| `Size`                                    | `4`字节      | 当前表空间占有的页面数                                       |
| `FREE Limit`                              | `4`字节      | 尚未被初始化的最小页号，大于或等于这个页号的区对应的XDES Entry结构都没有被加入FREE链表 |
| `Space Flags`                             | `4`字节      | 表空间的一些占用存储空间比较小的属性                         |
| `FRAG_N_USED`                             | `4`字节      | FREE_FRAG链表中已使用的页面数量                              |
| `List Base Node for FREE List`            | `16`字节     | FREE链表的基节点                                             |
| `List Base Node for FREE_FRAG List`       | `16`字节     | FREE_FRAG链表的基节点                                        |
| `List Base Node for FULL_FRAG List`       | `16`字节     | FULL_FRAG链表的基节点                                        |
| `Next Unused Segment ID`                  | `8`字节      | 当前表空间中下一个未使用的 Segment ID                        |
| `List Base Node for SEG_INODES_FULL List` | `16`字节     | SEG_INODES_FULL链表的基节点                                  |
| `List Base Node for SEG_INODES_FREE List` | `16`字节     | SEG_INODES_FREE链表的基节点                                  |

* List Base Node for FREE List、List Base Node for FREE_FRAG List、List Base Node for FULL_FRAG List

  属于表空间的三个链表的基节点。

* FREE Limit

  在一开始创建表空间的时候，可以为表空间申请一个很大的空间，InnoDB只把部分空闲区加入FREE链表，等FREE链表中的XDES Entry结构对应的区不够用的时候，再把之前没有加入FREE链表的空闲区对应的XDES Entry结构加入FREE链表。

  FREE Limit 这个字段表示的页号之前的区都被初始化了，之后的区尚未被初始化。

* Next Unused Segment ID

  当前表空间最大段ID的下一个ID，当创建一个新段的时候，其ID设为该值即可。

* Space Flags

  用4字节记录了一些占用空间非常小的属性

  | 标志名称        | 占用的空间（单位：bit） | 描述                                     |
  | --------------- | ----------------------- | ---------------------------------------- |
  | `POST_ANTELOPE` | 1                       | 表示文件格式是否大于`ANTELOPE`           |
  | `ZIP_SSIZE`     | 4                       | 表示压缩页面的大小                       |
  | `ATOMIC_BLOBS`  | 1                       | 表示是否自动把值非常长的字段放到BLOB页里 |
  | `PAGE_SSIZE`    | 4                       | 页面大小                                 |
  | `DATA_DIR`      | 1                       | 表示表空间是否是从默认的数据目录中获取的 |
  | `SHARED`        | 1                       | 是否为共享表空间                         |
  | `TEMPORARY`     | 1                       | 是否为临时表空间                         |
  | `ENCRYPTION`    | 1                       | 表空间是否加密                           |
  | `UNUSED`        | 18                      | 没有使用到的比特位                       |

* List Base Node for SEG_INODES_FULL List、List Base Node for SEG_INODES_FREE List

  表空间中所有段的INODE Entry结构会集中存放到一个类型为INODE的页中。

  如果表空间中的段特别多，则会有很多个INODE Entry结构，可能一个页放不下，这些INODE类型的页会组成两种列表：

  - `SEG_INODES_FULL`链表，该链表中的INODE类型的页面都已经被INODE Entry结构填充满了，没空闲空间存放额外的INODE Entry了。
  - `SEG_INODES_FREE`链表，该链表中的INODE类型的页面仍有空闲空间来存放INODE Entry结构。



##### XDES Entry部分

一个XDES Entry结构的大小是40字节，但是一个页面的大小有限，只能存放有限个XDES Entry结构，所以才把256个区划分成一组，在每组的第一个页面中存放256个XDES Entry结构。



#### 2️⃣XDES类型

XDES类型和FSP_HDR类型的页面是类似的。

XDES类型和FSP_HDR类型都是组的第0个页，表空间的第0个组的第0个页是FSP_HDR类型，其余所有组的第0个页都是XDES类型。

XDES类型和FSP_HDR类型都会存储组中的所有区的XDES Entry结构。

区别只在于由于FSP_HDR类型是整个表空间的第0个页，因此还需要额外存储表空间整体属性即File Space Header部分。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f475c0ec2a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1cs3vmoii1h971aje1iveack1l109.png-149.5kB" style="zoom:67%;" />



#### 3️⃣IBUF_BITMAP类型

每个组的第1个页面都是IBUF_BITMAP类型，用于记录有关Change Buffer的信息。



#### 4️⃣INODE类型

表空间第0个分组的第2个页面是INODE类型。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16ef3a8df380813e~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="img" style="zoom:57%;" />

一个`INODE`类型的页面是由这几部分构成的：

| 名称                            | 中文名       | 占用空间大小 | 简单描述                                   |
| ------------------------------- | ------------ | ------------ | ------------------------------------------ |
| `File Header`                   | 文件头部     | `38`字节     | 页的一些通用信息                           |
| `List Node for INODE Page List` | 通用链表节点 | `12`字节     | 存储上一个INODE页面和下一个INODE页面的指针 |
| `INODE Entry`                   | 段描述信息   | `16320`字节  |                                            |
| `Empty Space`                   | 尚未使用空间 | `6`字节      | 用于页结构的填充，没啥实际意义             |
| `File Trailer`                  | 文件尾部     | `8`字节      | 校验页是否完整                             |

重点关注`List Node for INODE Page List`和`INODE Entry`这两个部分：

* `INODE Entry`

  我们知道每个段都对应着一个INODE Entry来存储其属性信息。

  INODE页面就用于存储表空间中的所有段的INODE Entry。

  每个INODE Entry占用192字节，一个页面里可以存储`85`个这样的结构。

  不够用时，InnoDB 会再申请一个新的 INODE 页面，并通过链表把它们串起来。

  <img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f4087c4a56~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crrju0cnji91a2fhv91ijb15hgb1.png-111.4kB" style="zoom:85%;" />

* `List Node for INODE Page List`

  因为INODE类型的页只能存85个INODE Entry，当一个表空间的段数超过85，InnoDB 会再申请一个新的 INODE 页面，并通过链表把它们串起来。

  为了便于管理，InnoDB把这些INODE类型的页面串联成两个链表：

  - `SEG_INODES_FULL`链表：该链表中的INODE页面中已经没有空闲空间来存储额外的INODE Entry了。
  - `SEG_INODES_FREE`链表：该链表中的INODE页面中还有空闲空间来存储额外的INODE Entry了。

  这两个链表的基节点是存在了FSP_HDR页面的File Space Header部分中。

  `List Node for INODE Page List`内部就是存储了两个指针，分别指向当前INODE页面的前一个页面和后一个页面。

  以后每当我们新创建一个段（创建索引时就会创建段）时，都会创建一个INODE Entry与之对应，存储INODE Entry的大致过程就是这样的：

  - 先看看`SEG_INODES_FREE`链表是否为空，如果不为空，直接从该链表中获取一个节点，然后把该INODE Entry结构放到该页面中。当该页面中无剩余空间时，就把该页放到`SEG_INODES_FULL`链表中。
  - 如果`SEG_INODES_FREE`链表为空，则需要从表空间的FREE_FRAG链表中申请一个页面，修改该页面的类型为`INODE`，把该页面放到`SEG_INODES_FREE`链表中，与此同时把该INODE Entry结构放入该页面。



### 怎么找到索引的段的INODE Entry

如果我们想要往一个索引插入数据，定位到根节点之后，通过二分查找，一层层定位到最终的叶子节点。

如果这个叶子节点空间不足，则需要申请新的页来存放数据。

申请新页，就需要定位到索引对应的段的INODE Entry，找到其中的`Fragment Array Entry`以及三个链表的List Base Node，才能够获得空页，来存放数据。

我们只知道INODE Entry存放在INODE页中，那我们怎么找到一个索引对应的INODE Entry呢？难道遍历整个INODE页？

当然不是，在B+树的根节点，存放了两个指针：

`INDEX类型`的页的`Page Header`部分包含这两个属性：

| 名称                | 占用空间大小 | 描述                                       |
| ------------------- | ------------ | ------------------------------------------ |
| ...                 | ...          | ...                                        |
| `PAGE_BTR_SEG_LEAF` | `10`字节     | B+树叶子段的头部信息，仅在B+树的根页定义   |
| `PAGE_BTR_SEG_TOP`  | `10`字节     | B+树非叶子段的头部信息，仅在B+树的根页定义 |

这两个指针是`Segment Header`结构

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f48c4472a3~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1d6a74gu41fuqcqm1htri771d1k16.png-65.1kB" style="zoom: 50%;" />

| 名称                              | 占用字节数 | 描述                              |
| --------------------------------- | ---------- | --------------------------------- |
| `Space ID of the INODE Entry `    | `4`        | INODE Entry结构所在的表空间ID     |
| `Page Number of the INODE Entry ` | `4`        | INODE Entry结构所在的页面页号     |
| `Byte Offset of the INODE Ent`    | `2`        | INODE Entry结构在该页面中的偏移量 |

`PAGE_BTR_SEG_LEAF`记录着叶子节点段的INODE Entry结构的具体地址：是哪个表空间的哪个页面的哪个偏移量。

`PAGE_BTR_SEG_TOP`记录着非叶子节点段的INODE Entry结构的具体地址。



也就是说，在索引的根节点中，就可以获得对应的段的`INODE Entry`结构的地址。



### 独立表空间对应的文件大小

独立表空间对应着数据目录下名为`表名.ibd`的文件，这是自拓展文件，随着表中数据的增多，表空间对应的文件也逐渐增大。



## 8.4 系统表空间

系统表空间的结构和独立表空间基本类似，只不过由于整个MySQL进程只有一个系统表空间，在系统表空间中会额外记录一些「有关整个系统信息」的页面，所以会比独立表空间多出一些记录这些信息的页面。

系统表空间的`表空间 ID`（Space ID）是`0`。

### 全局图

![系统表空间.drawio](/Users/yutinglai/Documents/note/MySQL-note/assets/系统表空间.drawio-7679249.svg)

### 系统表空间的整体结构

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f4911220d8~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1csbied27ohe1rgg32gquulplm.png-147.4kB" style="zoom:75%;" />

系统表空间和独立表空间的前三个页面（页号分别为`0`、`1`、`2`，类型分别是`FSP_HDR`、`IBUF_BITMAP`、`INODE`）的类型是一致的，只是页号为`3`～`7`的页面是系统表空间特有的：

| 页号 | 页面类型  | 英文描述               | 描述                        |
| ---- | --------- | ---------------------- | --------------------------- |
| `3`  | `SYS`     | Insert Buffer Header   | 存储Insert Buffer的头部信息 |
| `4`  | `INDEX`   | Insert Buffer Root     | 存储Insert Buffer的根页面   |
| `5`  | `TRX_SYS` | Transction System      | 事务系统的相关信息          |
| `6`  | `SYS`     | First Rollback Segment | 第一个回滚段的页面          |
| `7`  | `SYS`     | Data Dictionary Header | 数据字典头部信息            |

除此之外，系统表空间的`extent 1`和`extent 2`这两个区被称为`Doublewrite buffer`双写缓冲区。



### InnoDB数据字典

#### 1️⃣数据字典是什么

存了什么：它主要记录了数据库对象的元数据，包括但不限于：

- **表的信息：** 表名、列数、所属表空间 ID（Space ID）。
- **列的信息：** 列名、数据类型、长度、是否为空、列的顺序。
- **索引信息：** 索引名、索引类型（聚簇索引还是二级索引）、***每个索引对应的根节点（Root Page）的页号***。
- **段（Segment）信息：** 每个索引对应的叶子节点段和非叶子节点段的 INODE 位置。



#### 2️⃣数据字典是如何存储的

存储在内部系统表。InnoDB存储引擎定义了一些内部系统表（internal system table）来记录这些这些`元数据`，这些系统表也被称为`数据字典`：

| 表名               | 描述                                                       |
| ------------------ | ---------------------------------------------------------- |
| `SYS_TABLES`       | 整个InnoDB存储引擎中所有的表的信息                         |
| `SYS_COLUMNS`      | 整个InnoDB存储引擎中所有的列的信息                         |
| `SYS_INDEXES`      | 整个InnoDB存储引擎中所有的索引的信息                       |
| `SYS_FIELDS`       | 整个InnoDB存储引擎中所有的索引对应的列的信息               |
| `SYS_FOREIGN`      | 整个InnoDB存储引擎中所有的外键的信息                       |
| `SYS_FOREIGN_COLS` | 整个InnoDB存储引擎中所有的外键对应列的信息                 |
| `SYS_TABLESPACES`  | 整个InnoDB存储引擎中所有的表空间信息                       |
| `SYS_DATAFILES`    | 整个InnoDB存储引擎中所有的表空间对应文件系统的文件路径信息 |
| `SYS_VIRTUAL`      | 整个InnoDB存储引擎中所有的虚拟生成列的信息                 |

最核心的四张表分别是：

1. **`SYS_TABLES`**: 存储表名与 Space ID 的对应关系。
2. **`SYS_COLUMNS`**: 存储列的定义。
3. **`SYS_INDEXES`**: 存储索引信息，包括该索引根节点所在的 **Page Number**。
4. **`SYS_FIELDS`**: 存储索引具体包含哪些列。



##### SYS_TABLES表

| 列名         | 描述                                             |
| ------------ | ------------------------------------------------ |
| `NAME`       | 表的名称                                         |
| `ID`         | InnoDB存储引擎中每个表都有一个唯一的ID           |
| `N_COLS`     | 该表拥有列的个数                                 |
| `TYPE`       | 表的类型，记录了一些文件格式、行格式、压缩等信息 |
| `MIX_ID`     | 已过时，忽略                                     |
| `MIX_LEN`    | 表的一些额外的属性                               |
| `CLUSTER_ID` | 未使用，忽略                                     |
| `SPACE`      | 该表所属表空间的ID                               |

这个`SYS_TABLES`表有两个索引：

- 以`NAME`列为主键的聚簇索引
- 以`ID`列建立的二级索引



##### SYS_COLUMNS表

| 列名       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| `TABLE_ID` | 该列所属表对应的ID                                           |
| `POS`      | 该列在表中是第几列                                           |
| `NAME`     | 该列的名称                                                   |
| `MTYPE`    | main data type，主数据类型，就是那堆INT、CHAR、VARCHAR、FLOAT、DOUBLE之类的东东 |
| `PRTYPE`   | precise type，精确数据类型，就是修饰主数据类型的那堆东东，比如是否允许NULL值，是否允许负数啥的 |
| `LEN`      | 该列最多占用存储空间的字节数                                 |
| `PREC`     | 该列的精度，不过这列貌似都没有使用，默认值都是0              |

这个`SYS_COLUMNS`表只有一个聚集索引：

- 以`(TABLE_ID, POS)`列为主键的聚簇索引



##### SYS_INDEXES表

| 列名              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `TABLE_ID`        | 该索引所属表对应的ID                                         |
| `ID`              | InnoDB存储引擎中每个索引都有一个唯一的ID                     |
| `NAME`            | 该索引的名称                                                 |
| `N_FIELDS`        | 该索引包含列的个数                                           |
| `TYPE`            | 该索引的类型，比如聚簇索引、唯一索引、更改缓冲区的索引、全文索引、普通的二级索引等等各种类型 |
| `SPACE`           | 该索引根页面所在的表空间ID                                   |
| `PAGE_NO`         | 该索引根页面所在的页面号                                     |
| `MERGE_THRESHOLD` | 如果页面中的记录被删除到某个比例，就把该页面和相邻页面合并，这个值就是这个比例 |

这个`SYS_INDEXES`表只有一个聚集索引：

- 以`(TABLE_ID, ID)`列为主键的聚簇索引



##### SYS_FIELDS表

| 列名       | 描述                         |
| ---------- | ---------------------------- |
| `INDEX_ID` | 该索引列所属的索引的ID       |
| `POS`      | 该索引列在某个索引中是第几列 |
| `COL_NAME` | 该索引列的名称               |

这个`SYS_FIELDS`表只有一个聚集索引：

- 以`(INDEX_ID, POS)`列为主键的聚簇索引



只要有了上述4个内部系统表，就可以获取其他系统表以及用户定义的表的所有元数据。

比方说我们想看看`SYS_TABLESPACES`这个系统表里存储了哪些表空间以及表空间的属性，那就可以：

- 到`SYS_TABLES`表中根据表名定位到具体的记录，就可以获取到`SYS_TABLESPACES`表的`TABLE_ID`
- 使用这个`TABLE_ID`到`SYS_COLUMNS`表中就可以获取到属于该表的所有列的信息。
- 使用这个`TABLE_ID`还可以到`SYS_INDEXES`表中获取所有的索引的信息，索引的信息中包括对应的`INDEX_ID`，还记录着该索引对应的`B+`数根页面是哪个表空间的哪个页面。
- 使用`INDEX_ID`就可以到`SYS_FIELDS`表中获取所有索引列的信息。



#### 3️⃣一个数据字典具体是怎么发挥作用的例子

如果我们要执行select语句

1. 要根据索引名去SYS_INDEXES内部系统表确定索引根节点的页号
2. 拿到根节点的页号之后，才能开展后续的二分查找，找到最终目标的叶子节点



#### 4️⃣怎么找到数据字典

通过数据字典我们可以找到其他所有表的信息，那怎么找到数据字典？

类似于 我根据地图能找到路，那在哪里可以找到地图呢？

数据字典是所有数据的元数据，那问题就转化为怎么找到元数据的元数据？

其实数据字典的元数据就是所谓的数据字典头部，数据字典头部中记录了数据字典这几张表的根节点地址等信息。

数据字典头部是一个具体的存储页面（Page），它固定在 系统表空间（ibdata1） 的 **Page 7**（也就是第 8 个页面，从 0 开始算）。

因为它的位置是硬编码在代码里的，所以 MySQL 启动时不需要找任何人，直接读取 `ibdata1` 的偏移量 16KB×7 就能找到它。



#### 5️⃣ 数据字典头部页面

Data Dictionary Header页面，是系统表空间页号为7的页，用于存储数据字典头部信息。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16efe1e5a01aeac9~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="img" style="zoom: 50%;" />

Data Dictionary Header页面的组成：

| 名称                     | 中文名           | 占用空间大小 | 简单描述                                                     |
| ------------------------ | ---------------- | ------------ | ------------------------------------------------------------ |
| `File Header`            | 文件头部         | `38`字节     | 页的一些通用信息                                             |
| `Data Dictionary Header` | 数据字典头部信息 | `56`字节     | 记录一些基本系统表的根页面位置以及InnoDB存储引擎的一些全局信息 |
| `Segment Header`         | 段头部信息       | `10`字节     | 记录本页面所在段对应的INODE Entry位置信息                    |
| `Empty Space`            | 尚未使用空间     | `16272`字节  | 用于页结构的填充，没啥实际意义                               |
| `File Trailer`           | 文件尾部         | `8`字节      | 校验页是否完整                                               |

可以看到这个页面里有`Segment Header`部分，意味着InnoDB把Data Dictionary Header页面当成一个段来分配存储空间，可以称为`数据字典段`。由于目前我们需要记录的数据字典信息非常少（可以看到`Data Dictionary Header`部分仅占用了56字节），所以该段只有一个碎片页，也就是页号为`7`的这个页。



`Data Dictionary Header`部分的各个字段：

- `Max Row ID`：如果我们不定义主键，也没有定义Unique列，那么`InnoDB`存储引擎会自动生成一个名为`row_id`的列作为主键。

  因为它是主键，所以每条记录的`row_id`列的值不能重复。原则上只要一个表中的`row_id`列不重复就可以了，也就是说表a和表b拥有一样的`row_id`列也没关系，不过InnoDB只提供了这个`Max Row ID`字段，不论哪个拥有`row_id`列的表插入一条记录时，该记录的`row_id`列的值就是`Max Row ID`对应的值，然后再把`Max Row ID`对应的值加1，也就是说这个`Max Row ID`是全局共享的。

- `Max Table ID`：InnoDB存储引擎中的所有的表都对应一个唯一的ID，每次新建一个表时，就会把本字段的值作为该表的ID，然后自增本字段的值。

- `Max Index ID`：InnoDB存储引擎中的所有的索引都对应一个唯一的ID，每次新建一个索引时，就会把本字段的值作为该索引的ID，然后自增本字段的值。

- `Max Space ID`：InnoDB存储引擎中的所有的表空间都对应一个唯一的ID，每次新建一个表空间时，就会把本字段的值作为该表空间的ID，然后自增本字段的值。

-   `Mix ID Low(Unused)`：这个字段没用，跳过。

- `Root of SYS_TABLES clust index`：本字段代表`SYS_TABLES`表聚簇索引的根页面的页号。

- `Root of SYS_TABLE_IDS sec index`：本字段代表`SYS_TABLES`表为`ID`列建立的二级索引的根页面的页号。

- `Root of SYS_COLUMNS clust index`：本字段代表`SYS_COLUMNS`表聚簇索引的根页面的页号。

- `Root of SYS_INDEXES clust index`本字段代表`SYS_INDEXES`表聚簇索引的根页面的页号。

- `Root of SYS_FIELDS clust index`：本字段代表`SYS_FIELDS`表聚簇索引的根页面的页号。

- `Unused`：这4个字节没用，跳过。



## 8.5 information_schema系统数据库

用户是不能直接访问`InnoDB`的这些内部系统表的，为了用户能够查看表的内容以便于分析问题，系统数据库`information_schema`中提供了一些以`innodb_sys`开头的表：

```sql
mysql> USE information_schema;
Database changed

mysql> SHOW TABLES LIKE 'innodb_sys%';
+--------------------------------------------+
| Tables_in_information_schema (innodb_sys%) |
+--------------------------------------------+
| INNODB_SYS_DATAFILES                       |
| INNODB_SYS_VIRTUAL                         |
| INNODB_SYS_INDEXES                         |
| INNODB_SYS_TABLES                          |
| INNODB_SYS_FIELDS                          |
| INNODB_SYS_TABLESPACES                     |
| INNODB_SYS_FOREIGN_COLS                    |
| INNODB_SYS_COLUMNS                         |
| INNODB_SYS_FOREIGN                         |
| INNODB_SYS_TABLESTATS                      |
+--------------------------------------------+
10 rows in set (0.00 sec)
```

在`information_schema`数据库中的这些以`INNODB_SYS`开头的表并不是真正的内部系统表（内部系统表是前面提到的以`SYS`开头的那些表）。

存储引擎启动时会读取这些以`SYS`开头的系统表，然后填充到这些以`INNODB_SYS`开头的表中。

可以理解为，你不能看内部系统表。但是为了便于你排查问题，InnoDB提供了一个供外部访问的系统表，InnoDB会在启动的时候读内部系统表中的内容然后填充到供外部访问的系统表。

> 外部系统表也只可读，为什么还要专门搞个外部系统表？而不是直接对内部系统表只读即可？
>
> 1. 语言不通。
>
>    内部系统表是底层C++的结构体，并不是SQL表格形式存储。
>
>    所以需要转化为外部系统表SQL表格形式存储才能便于给用户看。
>
> 2. 并发问题。
>
>    内部系统表会被存储引擎频繁读写，它记录的是SQL任务的实时状态。
>
>    如果用户频繁查询，则为了保证读到的数据的准确性，涉及到加锁的问题，影响性能。





## 8.7 查询、修改 玩转表空间

> 在最新版本的 MySQL（包括广泛使用的 8.0、8.4 以及 9.0+）中，默认情况下都是使用「独立表空间」来存储用户表的数据和索引的，因此下面以独立表空间为例。



### INSERT全流程

如果使用的是独立表空间，INSERT执行的全流程：

```sql
INSERT INTO table (id, name) VALUES (100, 'Gemini');
```

1. 根据数据字典找到table表聚簇索引的根页面页号

2. 根据主键值在页目录二分查找，找到下一层节点的页号

3. 沿着指针层层向下，直到到达最底层的叶子节点

4. 到达目标叶子节点页后，InnoDB 检查该页的 `Free Space`

5. 如果空间充足：直接在页内插入记录

6. 如果空间不足，则需要申请新页

7. 从根节点页面的`Page Header`部分，获取`PAGE_BTR_SEG_LEAF`叶子段和`PAGE_BTR_SEG_TOP`非叶子段两个段的`INODE Entry`地址。

8. 先去`INODE Entry`的`Fragment Array`看有没有还有空闲空间的页可以用，如果有，则直接使用。

9. 如果没有，则前往表空间的第0个区的第0个页`FSP_HDR`页，找到`File Space Header`的`List Base Node for FREE_FRAG List`，即`FREE_FRAG`链表的基节点。

10. 尝试从`FREE_FRAG`链表获取状态为`FREE_FRAG`的区，即有空闲空间的碎片区。如果找到了，则从该区中取出一些零碎的页存储插入的数据。

11. 如果找不到，则找到`File Space Header`的`List Base Node for FREE List`部分，即`FREE`链表的基节点。

12. 从`FREE`链表找到一个区，将其状态转化为`FREE_FRAG`，并转移到`FREE_FRAG`链表。从该区中取出一些零碎的页存储插入的数据。

    之后不同的段需要获取碎片区都可以从该区中获取，直到这个区没有空闲空间，则将其状态变为`FULL_FRAG`，并移入到`FULL_FRAG List`。

13. 将碎片页的页号填入`INODE Entry`中的 `Fragment Array`。

    注意，不同段是不能共享同一个碎片页的。虽然共享一个碎片区，但是页是只能属于某个段的。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f4087c4a56~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crrju0cnji91a2fhv91ijb15hgb1.png-111.4kB" style="zoom:80%;" />

14. 在对应的 `XDES Entry` 的 `Page State BitMap` 中，将该页对应的位标记为 1（已占用）。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a739f343654829~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1crre79uq9971bsdj9s1i0j11en8a.png-96.2kB" style="zoom:80%;" />

15. 随着数据越来越多，该段已经占用了32个碎片页。
16. 此时继续插入数据，会从`FSP_HDR`的 `FREE List`（空闲区链表）中直接申请一个完整的Extent（区）。
17. 将该区的`XDES Entry`的 `State` 从 `FREE` 改为 `SEG_INODE`（表示现在它归属于某个段了）。
18. 将该`XDES Entry`挂到`INODE Entry`里的 `FREE List` 链表下（注意：这里是段内部的空闲链表）。
19. 当需要新页时，MySQL 从属于段的 `FREE List` 区里取出一个页。同时更新该区`XDES Entry`的 `Page State BitMap`。
20. 一旦该区用了一部分页，它在`INODE Entry`里的位置就会从 `FREE List` 移到到 `NOT_FULL List`。
21. 从申请到的区中取出一个空闲页（Page）。
22. 为保证B+ 树平衡：将原页中的部分记录移动到新页（分裂）。修改原页的 `Next Page` 指向新页，新页的 `Prev Page` 指向原页。
23. 在父节点（非叶子节点）中增加一个目录项，指向这个新页。如果父节点也满了，则递归向上分裂，甚至可能导致根节点分裂（树增高）。
24. 以上所有对 `FSP_HDR`、`XDES`、`INODE` 以及数据页的修改，都会产生 Redo Log，确保即便断电，空间分配记录也不会丢失。



### SELECT全流程

如果使用的是独立表空间，SELECT执行的全流程：

```sql
SELECT * FROM table WHERE id = 100
```

1. 访问数据字典获取table表所在聚簇索引的根节点页号。
2. 将根节点读到Buffer Pool
3. 在根节点内通过 页目录（Page Directory） 二分查找，找到 `id=100` 所在的下一层子节点的页号。
4. 重复此过程。如果是三层 B+ 树，会从根节点（Root） -> 中间节点（Internal Node） -> 叶子节点（Leaf Node）。
5. 定位到目标叶子节点之后，通过页目录二分查找以及`next_record` 链表找到 `id=100` 的行数据。
6. 如果是范围查询： `id > 100`，会通过 File Header 中的 `PREV PAGE` 和 `NEXT PAGE` 指针在叶子节点之间横向移动。



# 9. MySQL如何执行单表查询

单表查询有两种执行方式：

1. 使用全表扫描：把整张表（即整个聚簇索引）扫描一遍，将满足条件的记录加入结果集。
2. 使用索引进行查询：利用索引来提升查询速率。

MySQL执行查询语句的方式称为访问方法，有const、ref、range等访问方法，下面展开介绍。



为便于展开介绍，创建single_table表作为示例：

```sql
CREATE TABLE single_table (
    id INT NOT NULL AUTO_INCREMENT,
    key1 VARCHAR(100),
    key2 INT,
    key3 VARCHAR(100),
    key_part1 VARCHAR(100),
    key_part2 VARCHAR(100),
    key_part3 VARCHAR(100),
    common_field VARCHAR(100),
    PRIMARY KEY (id),
    KEY idx_key1 (key1),
    UNIQUE KEY idx_key2 (key2),
    KEY idx_key3 (key3),
    KEY idx_key_part(key_part1, key_part2, key_part3)
) Engine=InnoDB CHARSET=utf8;
```

为这个`single_table`表建立了1个聚簇索引和4个二级索引，分别是：

- 为`id`列建立的聚簇索引。
- 为`key1`列建立的`idx_key1`二级索引。
- 为`key2`列建立的`idx_key2`二级索引，而且该索引是唯一二级索引。
- 为`key3`列建立的`idx_key3`二级索引。
- 为`key_part1`、`key_part2`、`key_part3`列建立的`idx_key_part`二级索引，这也是一个联合索引。



## 9.1 const

是什么：通过「主键」或「唯一二级索引列」与常数的***等值比较***来定位一条记录。如果「主键」或「唯一二级索引列」是由多个列构成的话，索引中的每一个列都需要与常数进行等值比较（这是因为只有该索引中全部列都采用等值比较才可以定位唯一的一条记录）。

为什么叫const：这种查询方式速度非常快，因此命名为`const`，意思是常数级别的，代价是可以忽略不计的。

如果是通过「主键」与常数的等值比较定位一条记录，则仅需要通过二分查找就能在聚簇索引快速定位到目标叶子节点以及目标记录。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a7b843dec61b4e~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1ctendl4319v659s1dfoj6lssl16.png-36.4kB" style="zoom:67%;" />

如果是通过「唯一二级索引列」与常数的等值比较定位一条记录，则仅需要通过二分查找就能在二级索引快速定位到目标记录，然后回表在聚簇索引也能够通过二分查找快速定位到目标记录。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a7b843e05c8e33~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1cthurrlpbhlotsjru1dsjrrl30.png-110.2kB" style="zoom:67%;" />



注意：

* 对唯一二级索引，查询该列为NULL值的情况比较特殊。

  由于唯一二级索引列并不限制 NULL 值的数量，因此该sql语句可能定位到多条记录，则不属于const访问方法，而属于ref访问方法。

  ```sql
  SELECT * FROM single_table WHERE key2 IS NULL;
  ```

  

## 9.2 ref

是什么：「普通二级索引列」与常数进行***等值比较***。

什么时候采用ref：由于「普通二级索引列」与常数进行等值比较，查询到的结果数量可能大于1，如果不满足索引覆盖，则需要进行回表。所以ref的效率取决于在二级索引中查询到的记录条数，如果条数越少，说明回表次数越少，效率越高。如果条数越多，说明回表次数越多，则mysql越倾向于进行全表扫描。

![image_1ctf14vso11cdclsmc6ac8pru9h.png-109.5kB](/Users/yutinglai/Documents/note/MySQL-note/assets/16a7b843e5e227f1~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

注意：

* 不论是普通的二级索引，还是唯一二级索引，它们的索引列对包含NULL值的数量并不限制，所以key IS NULL这种形式的搜索条件最多只能使用ref的访问方法，而不是const的访问方法。

* 对于某个包含多个索引列的二级索引来说，只要是最左边的连续索引列是与常数的等值比较就可能采用`ref`的访问方法，比方说下边这几个查询：

  ```sql
  SELECT * FROM single_table WHERE key_part1 = 'god like';
  
  SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 = 'legendary';
  
  SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 = 'legendary' AND key_part3 = 'penta kill';
  ```

  但是如果最左边的连续索引列并不全部是等值比较的话，它的访问方法就不是ref了，而是range：

  ```sql
  SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 > 'legendary';
  ```



## 9.3 ref_or_null

是什么：通过二级索引找到二级索引列值等于某个常数值或者该二级索引列值为NULL的记录。

示例：该sql语句使用的就是`ref_or_null`访问方法

```sql
SELECT * FROM single_table WHERE key1 = 'abc' OR key1 IS NULL;
```

该查询相当于先分别从`idx_key1`索引对应的`B+`树中找出`key1 IS NULL`和`key1 = 'abc'`的两个连续的记录范围，然后根据这些二级索引记录中的`id`值再回表查找完整的用户记录。

<img src="/Users/yutinglai/Documents/note/MySQL-note/assets/16a7b843e8927bee~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png" alt="image_1ctf21uu8113m1ajm1rcitgf5eeco.png-122.5kB" style="zoom:90%;" />

注意：

* InnoDB中通常将 `NULL` 视为**最小的值**，因此二级索引列为 `NULL` 的记录在索引树中也是连续排列的。



## 9.4 range

是什么：利用索引进行范围查询。（可以是聚簇索引，也可以是二级索引）。

示例：该sql语句的访问方法就属于range。其中1438、6328属于单点区间、[38，79]属于连续范围区间。

```sql
SELECT * FROM single_table WHERE key2 IN (1438, 6328) OR (key2 >= 38 AND key2 <= 79);
```



## 9.5 index

是什么：在这个sql语句中，由于`key_part2`不是`idx_key_part`索引最左边的索引列，因此无法使用ref访问方法。

```sql
SELECT key_part1, key_part2, key_part3 FROM single_table WHERE key_part2 = 'abc';
```

可以观察到查询的`key_part1, key_part2, key_part3`三个列都在`idx_key_part`索引中，而且查询条件`key_part2`也在`idx_key_part`索引中。我们完全可以通过扫描`idx_key_part`索引树，找到满足条件的记录数据，而且还不需要回表。

由于二级索引只需要存储索引列和主键列，而聚簇索引需要完整的用户数据数据还有隐藏列等，所以***直接遍历二级索引比直接遍历聚簇索引的成本要小很多。***

这种遍历整个二级索引来查询的方式称为index。



## 9.6 all

全表扫描，直接扫描整个聚簇索引。



## 9.7 注意

### 1️⃣执行查询一般只会用一个二级索引

例如这个sql语句：

```sql
SELECT * FROM single_table WHERE key1 = 'abc' AND key2 > 1000;
```

查询优化器会识别到这个查询中的两个搜索条件：

- `key1 = 'abc'`
- `key2 > 1000`

优化器会根据表的统计数据，来判断使用哪一个二级索引需要扫描的数据条数更少。一般来说等值查询需要扫描的数据条数更少，但也不一定，因为有可能`key1 = 'abc'`的记录特别多。

假设这里使用了`idx_key1`这个二级索引，查询到所有满足`key1 = 'abc'`的记录之后，需要根据主键回表查询完整的用户记录，然后再根据其余的`WHERE`条件即`key2 > 1000`过滤记录。



### 2️⃣复杂搜索条件怎么简化

例如这个sql语句，如何分析应该使用哪个索引？ 

```sql
SELECT * FROM single_table WHERE 
        (key1 > 'xyz' AND key2 = 748 ) OR
        (key1 < 'abc' AND key1 > 'lmn') OR
        (key1 LIKE '%suf' AND key1 > 'zzz' AND (key2 < 8000 OR common_field = 'abc')) ;
```

分析策略：

1. 首先查看`WHERE`子句中的搜索条件都涉及到了哪些列，哪些列可能使用到索引。

   这个查询的搜索条件涉及到了`key1`、`key2`、`common_field`这3个列。

   `key1`列有普通的二级索引`idx_key1`，`key2`列有唯一二级索引`idx_key2`。

2. 对于那些可能用到的索引，分析它们的范围区间。

   * 假设使用`idx_key1`索引进行查询

     * 将用不到该索引的搜索条件暂时移除掉 ➡️ 用TRUE替代这些搜索条件

       > 之所以把用不到索引的搜索条件替换为TRUE，是因为我们不打算使用这些条件进行在该索引上进行过滤，所以不管索引的记录满不满足这些条件，我们都把它们选取出来，待到之后回表的时候再使用它们过滤。

       由于`key2`、`common_field`用不到该索引，且`key1 LIKE '%suf'`也用不到该索引，则上面的sql语句变为：

       ```sql
       SELECT * FROM single_table WHERE 
               (key1 > 'xyz' AND TRUE ) OR
               (key1 < 'abc' AND key1 > 'lmn') OR
               (TRUE AND key1 > 'zzz' AND (TRUE OR TRUE)) ;
       ```

       简化之后即为：

       ```sql
       SELECT * FROM single_table WHERE 
               (key1 > 'xyz') OR
               (key1 < 'abc' AND key1 > 'lmn') OR
               (key1 > 'zzz') ;
       ```

     * 将永远为TRUE/FALSE的条件给替换掉

       由于`key1 < 'abc' AND key1 > 'lmn'`永远为FALSE，则上面的sql等价于

       ```sql
       SELECT * FROM single_table WHERE 
               (key1 > 'xyz') OR
               FALSE OR
               (key1 > 'zzz') ;
       ```

       等价于

       ```sql
       SELECT * FROM single_table WHERE 
               (key1 > 'xyz') OR (key1 > 'zzz') ;
       ```

       `(key1 > 'xyz') OR (key1 > 'zzz')`取并集，等价于

       ```sql
       SELECT * FROM single_table WHERE key1 > 'xyz';
       ```

     所以，如果使用`idx_key1`索引进行查询，则需要先从二级索引查询到所有满足`key1 > 'xyz'`的记录，然后再回表找到完整用户记录，最后再根据其余查询条件过滤。

   * 假设使用`idx_key2`索引进行查询

     ```sql
     SELECT * FROM single_table WHERE 
             (key1 > 'xyz' AND key2 = 748 ) OR
             (key1 < 'abc' AND key1 > 'lmn') OR
             (key1 LIKE '%suf' AND key1 > 'zzz' AND (key2 < 8000 OR common_field = 'abc')) ;
     ```

     * 将用不到该索引的搜索条件暂时移除掉 ➡️ 用TRUE替代这些搜索条件

       由于该索引用不到key1、common_field字段，因此上面的sql语句等同于

       ```sql
       SELECT * FROM single_table WHERE 
               (TRUE AND key2 = 748 ) OR
               (TRUE AND TRUE) OR
               (TRUE AND TRUE AND (key2 < 8000 OR TRUE)) ;
       ```

       等同于

       ```sql
       SELECT * FROM single_table WHERE 
               (key2 = 748 ) OR
               (key2 < 8000 OR TRUE) ;
       ```

       等同于

       ```sql
       SELECT * FROM single_table WHERE 
               (key2 = 748 ) OR (TRUE) ;
       ```

       等同于

       ```sql
       SELECT * FROM single_table WHERE TRUE ;
       ```

       因此，如果我们使用`idx_key2`索引进行查询，需要扫描`idx_key2`二级索引的所有记录，然后对所有记录都要回表。没有任何优化效果，这种情况下优化器不会选择使用`idx_key2`索引。

     

### 3️⃣索引合并

使用多个索引来完成一次查询被称为index merge（索引合并）。

索引合并算法有三种：

* Intersaction合并。
* Union合并。
* Sort-Union合并。



#### Intersaction合并

**是什么：**

Intersaction指的是取交集

```sql
SELECT * FROM single_table WHERE key1 = 'a' AND key3 = 'b';
```

上面这个sql语句如果使用Intersaction合并，执行流程为：

1. 根据`idx_key1`索引查询所有满足`key1 = 'a'`的记录。
2. 根据`idx_key3`索引查询所有满足`key3 = 'b'`的记录。
3. 由于两个二级索引查询到的都是id和索引列值，则对1、2步的查询得到的记录的id值取交集。
4. 对于交集中的id再回表查询完整记录。



**为什么：**

为什么要用两个二级索引分别查询，得到的交集再回表？而不是用一个二级索引查，回表查询到完整记录，再根据剩余查询条件来过滤？

分别分析两种方式所需的成本：

只读一个二级索引的成本：

* 根据某个查询条件搜索一个二级索引
* 根据得到的主键值回表，然后再过滤其他搜索条件（*注意，这里的情况不满足索引覆盖，所以必须回表之后才能过滤剩余条件*）

读多个二级索引：

* 根据不同的查询条件搜索不同的二级索引
* 取查询结果的id交集，在回表查完整记录

如果说只读一个二级索引需要回表次数非常多，而读多个二级索引得到的查询结果交集记录数特别少，则使用Intersaction合并效率会更高。因为虽然需要读多个二级索引，但是读二级索引本身是顺序I/O，效率就很高，而回表是随机I/O，效率低。当回表造成的性能损耗比访问多个二级索引造成的性能损耗更多时，使用Intersaction合并比只读一个二级索引的方案更优。



**什么时候会使用Intersaction合并：**

当SQL语句对二级索引***都是等值匹配***的时候，则可能会使用`Intersection`索引合并。

例如：对于这个SQL语句，可能用到`idx_key1`和`idx_key_part`这两个二级索引的`Intersection`索引合并操作。

```sql
SELECT * FROM single_table WHERE key1 = 'a' AND key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c';
```

因为对于`idx_key1`二级索引，是等值匹配（`key1 = 'a'`）。

对`idx_key_part`二级索引，也是等值匹配（`key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c'`）。

下面这个SQL语句就无法使用索引合并操作。

```sql
SELECT * FROM single_table WHERE key1 > 'a' AND key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c';

SELECT * FROM single_table WHERE key1 = 'a' AND key_part1 = 'a';
```

对于第一个SQL语句来说，`idx_key1`是范围匹配；

对于第二个SQL语句来说，`idx_key_part`不是等值匹配；

为什么必须要是等值匹配才可能使用Intersaction合并？

因为二级索引会在叶子节点存储索引列+主键值，排序方式是按照索引列排序，当索引列相同的时候，再按照主键列排序。

所以当使用二级索引的时候，等值匹配能够保证搜索的结果是主键列有序的，当多个二级索引搜索结果的主键列都是有序的时候，找主键的交集的效率就非常高，时间复杂度为O(N)。

例如下面两个二级索引根据等值查询查询到的结果的主键值为：

- 从`idx_key1`中获取到已经排好序的主键值：1、3、5
- 从`idx_key2`中获取到已经排好序的主键值：2、3、4

查询交集的方式为：

1. 两个指针p、q分别指向最小值
2. p.val == q.val 则找到一个交集元素，加入集合
3. p.val != q.val 则右移值更小的指针
4. 重复该过程，当p\==null || q==null，就找完了交集中的所有元素。



以下面这个例子演示整个找交集的过程：

- 从`idx_key1`中获取到已经排好序的主键值：1、3、5
- 从`idx_key2`中获取到已经排好序的主键值：2、3、4

1. p指向`idx_key1`查询到的记录的最小主键值1，q指向`idx_key2`查询到的记录的最小主键值2。
2. 1 != 2 && 1 < 2 --> p右移 p.val=3 q.val=2
3. 3 != 2 && 3 > 2 --> q右移 p.val=3 q.val=3 加入交集
4. p.val=3 q.val=3 加入交集 --> p右移 q右移 p.val=5 q.val=4
5. 5 != 4 && 5 > 4 --> q右移 q==null 结束



为什么要这样找交集，用集合set不是也能通过O(N)的时间复杂度实现吗，而且还不需要主键值有序？

虽然时间复杂度也是O(N)，但是上面的方案空间复杂度为O(1)，而Set的方案空间复杂度为O(n)。



如果不要求等值匹配，例如使用范围匹配

```sql
SELECT * FROM single_table WHERE key1 > 'a' AND key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c';
```

使用`idx_key1`二级索引查询到的记录的主键列不一定是有序的，则需要先根据主键值排序，再找交集，效率更低。



==注意：这只是可能会发生Intersaction合并的情况，但并不一定会发生。**只有当多个索引的扫描代价之和，明显小于扫描单个索引时，MySQL 才会考虑合并。**==



#### Union合并

**是什么：**

和Intersaction合并类似，Intersaction是取交集，Union是取并集。

Intersaction处理的是AND的关系，Union处理的是OR的关系。

例如这个SQL语句，可以通过Union合并，将`idx_key1`和`idx_key3`二级索引查询到的结果的主键值取并集再回表查询。

```sql
SELECT * FROM single_table WHERE key1 = 'a' OR key3 = 'b'
```



**为什么：**

为什么要用两个二级索引分别查询，得到的并集再回表？而不是直接进行全表扫描？

分别分析两种方式所需的成本：

全表扫描所需要的成本：

* 需要扫描整个聚簇索引，一一对比每个记录是否满足`key1 = 'a' OR key3 = 'b'`。

读多个二级索引：

* 需要扫描`idx_key1`找到所有满足`key1 = 'a'`的记录
* 需要扫描`idx_key2`找到所有满足`key3 = 'b'`的记录

* 找到所有主键的并集，再回表查询完整的记录信息



虽然Union合并需要读多个二级索引，但是如果通过二级索引扫描得到的并集中记录数很少，则效率会比全表扫描高得多。



**什么时候会使用Union合并：**

* 二级索引列都是等值匹配

  例如下面这个sql语句就可以使用Union合并

  ```sql
  SELECT * FROM single_table WHERE key1 = 'a' OR ( key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c');
  ```

  下面这两个sql语句就不能用Union合并

  ```sql
  # key1不是等值匹配
  SELECT * FROM single_table WHERE key1 > 'a' OR (key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c');
  
  # 联合索引idx_key_part中的key_part2和key_part3列并没有出现在搜索条件中
  SELECT * FROM single_table WHERE key1 = 'a' OR key_part1 = 'a';
  ```

  原因和Intersaction合并讨论的一致：当等值匹配的时候才能够保证二级索引搜索到的记录的主键值是有序的，找并集的效率才会高。

* 使用`Intersection`索引合并的搜索条件

  ```sql
  SELECT * FROM single_table WHERE 
  key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c' 
  OR (key1 = 'a' AND key3 = 'b');
  ```

  * 先按照`key1 = 'a' AND key3 = 'b'`搜索条件通过`Intersection`索引合并得到一个主键集合
  * 再通过`idx_key_part`二级索引搜索`key_part1 = 'a' AND key_part2 = 'b' AND key_part3 = 'c' `得到一个主键集合
  * 由于两个主键集合都是有序的，因此取并集效率也非常高。



==注意：这只是**可能**会发生Union合并的情况，但并不一定会发生。**只有当扫描多个索引产生的性能消耗小于全表扫描的性能消耗，优化器才会考虑使用Union合并。**==



#### Sort Union合并

Union合并的要求很高，只允许在等值匹配的时候才允许进行Union合并。

对于下面这个sql语句就不允许了，之所以不允许，是因为通过`idx_key1`索引搜索`key1 < 'a'`条件得到的结果主键值不一定是有序的。对于`key3 > 'z'`条件也是同理。

```sql
SELECT * FROM single_table WHERE key1 < 'a' OR key3 > 'z'
```

为啥怕排序呢，就是因为如果记录条数很多的时候，排序的性能消耗比较大。

如果说我们通过二级索引查询到的记录的条数比较少，那对它进行主键排序关系也不大，这样的话主键有序，后续流程就和Union完全一样了。

这种通过多个二级索引查询到记录，然后对这些记录进行排序，再求并集的方式，就称为Sort Union。



> 为什么有Sort Union而没有Sort Intersaction？
>
> 思考一下什么时候会用Union什么时候会用Intersaction：
>
> Union：处理OR关系。当通过多个二级索引查询到的记录数很少，并集结果也很少，回表代价不大（至少比全表扫描代价小）的时候，才会用Union。
>
> Intersaction：处理AND关系。当仅通过一个二级索引查询到的记录很少，直接用一个二级索引查询然后回表再过滤剩余查询条件也很高效，这种情况不会用Intersaction Union。只有当仅通过一个二级索引查询到的记录很多，回表频率高的场景下，通过多个二级索引取交集的方式才有意义（减少回表次数），才会使用Intersaction合并。
>
> 因此Intersaction合并本身就只有在二级索引查询到记录数量很多的场景下才会使用，这种情况下如果还要进行Sort，效率并不高，因此没有Sort Intersaction。
>
> 而Union本身就是在通过二级索引扫描到的记录数量比较少的时候才会使用，Sort对效率影响也不高，所以有Sort Union。
>
> 
>
> 为什么 MySQL 只有 Sort-Union 而没有 Sort-Intersection？
>
> 在 MySQL 的索引合并（Index Merge）策略中，算法的选择本质上是在**“排序/合并的 CPU 消耗”**与**“回表的 I/O 消耗”**之间做权衡。
>
> 1. Union：处理OR关系。
>
> - **什么时候会用Union合并：** `OR` 查询通常用于处理多个独立条件。只有当每个条件筛选出的记录数都**较少**时，合并后的并集依然较小，此时回表的次数比较少，优化器才会选择Union合并而不是全表扫描。
> - **排序的意义：** 既然单次查询结果集小，对其主键值进行Sort的 CPU 开销就很低。 因此Sort Union在小数据量场景下性价比极高。
>
> 2. Intersection：处理AND关系。
>
> - **什么时候会用Intersection合并：** `AND` 查询只有在**“单索引过滤效果差（返回记录多），但多索引交集过滤效果好”**的极端场景下，才有必要使用 Intersection。
> - **矛盾点：**
>   - 如果单索引返回记录少，直接回表过滤即可，无需 Intersection。
>   - 如果单索引返回记录**很多**（万级以上），此时进行 **Sort（排序）** 操作的 CPU 和内存开销将变得不可接受。
> - **结论：** Intersection 只有在数据量大时才有意义，而大数据量下排序太慢。因此，MySQL 只支持 **Simple Intersection**（要求索引本身就是有序的，直接双指针扫描），而放弃了开发性价比极低的 Sort-Intersection。
