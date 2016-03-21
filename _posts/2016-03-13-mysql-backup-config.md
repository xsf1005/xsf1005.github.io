---
layout: post
title: mysql备份及内存配置优化参考
date: 2016-03-13
categories: mysql
description: 内存配置优化说明参考

---

### 备份及还原

	1、备份数据库
	
	写个批处理,将批处理房子mysql同目录下即可	
	echo '正在备份中,请稍后...'
	%cd%\mysql5\bin\mysqldump -h localhost -u 用户名 -p密码 -x --opt 数据库名> 备份数据库文件路径（d:\test.sql）
	echo '备份完成...'
	Pause 

	2、还原数据库
	写个批处理,将批处理房子mysql同目录下即可	
	echo '正在还原中,请稍后...'
	%cd% mysql -hip(127.0.0.1) -u用户名 -p密码 数据库名< 备份数据库文件路径（d:\test.sql）
	echo '还原完成...'
	Pause 

### 内存配置优化

    MySQL自身因素当解决了上述服务器硬件制约因素后，让我们看看MySQL自身的优化是如何操作的。
对MySQL自身的优化主要是对其配置文件my.cnf中的各项参数进行优化调整。下面我们介绍一些对性能影响较大的参数。
	
   由于my.cnf文件的优化设置是与服务器硬件配置息息相关的，因而我们指定一个假想的服务器硬件环境：
   CPU: 2颗Intel Xeon 2.4GHz 内存: 4GB DDR 硬盘: SCSI 73GB(很常见的2U服务器)。
   
   下面，我们根据以上硬件配置结合一份已经优化好的my.cnf进行说明：
   
   1、避免MySQL的外部锁定，减少出错几率增强稳定性。 
	[mysqld] 
	port = 3306 
	serverid = 1 
	socket = /tmp/mysql.sock 
	skip-locking 

    2、禁止MySQL对外部连接进行DNS解析，使用这一选项可以消除MySQL进行DNS解析的时间。
但需要注意，如果开启该选项，则所有远程主机连接授权都要使用IP地址方式，否则MySQL将无法正常处理连接请求！
	skip-name-resolve 

    3、back_log参数的值指出在MySQL暂时停止响应新请求之前的短时间内多少个请求可以被存在堆栈中。
如果系统在一个短时间内有很多连接，则需要增大该参数的值，该参数值指定到来的TCP/IP连接的侦听队列的大小。
不同的操作系统在这个队列大小上有它自己的限制。 试图设定back_log高于你的操作系统的限制将是无效的。默认值为50。
对于Linux系统推荐设置为小于512的整数。
	back_log = 384 

    4、key_buffer_size指定用于索引的缓冲区大小，增加它可得到更好的索引处理性能。
对于内存在4GB左右的服务器该参数可设置为256M或384M。注意：该参数值设置的过大反而会是服务器整体效率降低！
	key_buffer_size = 256M 

    5、查询排序时所能使用的缓冲区大小。
注意：该参数对应的分配内存是每连接独占，如果有100个连接，那么实际分配的总共排序缓冲区大小为100 × 6 ＝ 600MB。
所以，对于内存在4GB左右的服务器推荐设置为6-8M。
	max_allowed_packet = 4M 
	thread_stack = 256K 
	sort_buffer_size = 6M 

    6、读查询操作所能使用的缓冲区大小。和sort_buffer_size一样，该参数对应的分配内存也是每连接独享。
	read_buffer_size = 4M 

    7、联合查询操作所能使用的缓冲区大小，和sort_buffer_size一样，该参数对应的分配内存也是每连接独享。
	join_buffer_size = 8M 

    8、指定MySQL查询缓冲区的大小。可以通过在MySQL控制台观察。如果Qcache_lowmem_prunes的值非常大，
则表明经常出现缓冲不够的情况；如果Qcache_hits的值非常大，则表明查询缓冲使用非常频繁，
如果该值较小反而会影响效率，那么可以考虑不用查询缓冲；Qcache_free_blocks，如果该值非常大，则表明缓冲区中碎片很多。
	myisam_sort_buffer_size = 64M 
	thread_cache_size = 64 
	query_cache_size = 64M 

    9、指定MySQL允许的最大连接进程数。如果在访问论坛时经常出现Too Many Connections的错误提 示，则需要增大该参数值。
	tmp_table_size = 256M 
	max_connections = 768 

    10、指定一个请求的最大连接时间，对于4GB左右内存的服务器可以设置为5-10。 
	max_connect_errors = 10000000 
	wait_timeout = 10 

    11、该参数取值为服务器逻辑CPU数量*2，在本例中，服务器有2颗物理CPU，而每颗物理CPU又支持H.T超线程，所以实际取值为4*2=8
	thread_concurrency = 8 

  
    12、开启该选项可以彻底关闭MySQL的TCP/IP连接方式，如果WEB服务器是以远程连接的方式访问MySQL数据库服务器则不要开启该选项！
否则将无法正常连接！
	skip-networking 

    13、物理内存越大,设置就越大.默认为2408K,调到512-1024最佳 
	table_cache=1024 

    14、默认为2M 
	innodb_additional_mem_pool_size=4M 

    15、设置为0就是等到innodb_log_buffer_size列队满后再统一储存,默认为1 
	innodb_flush_log_at_trx_commit=1 

    16、#默认为1M 
	innodb_log_buffer_size=2M 

    17、默认为218，调到256最佳 
	key_buffer_size=256M 

    18、默认为16M，调到64-256最挂 
	tmp_table_size=64M 

    19、默认为64K 
	read_buffer_size=4M 

    20、默认为256K 
	read_rnd_buffer_size=16M 

    21、默认为256K 
	sort_buffer_size=32M 

    22、默认为60 
	thread_cache_size=120 

	23、很多情况需要具体情况具体分析 如果Key_reads太大，则应该把my.cnf中Key_buffer_size变大，
保持Key_reads/Key_read_requests至少1/100以上，越小越好。如果Qcache_lowmem_prunes很大，就要增加Query_cache_size的值。
	query_cache_size=32M

### mysql解释说明

从内存的使用方式MySQL 数据库的内存使用主要分为以下两类：线程独享内存 和 全局共享内存
 
1、线程独享内存

    在 MySQL 中，线程独享内存主要用于各客户端连接线程存储各种操作的独享数据，如线程栈信息，分组排序操作，数据读写缓冲，
结果集暂存等等，而且大多数可以通过相关参数来控制内存的使用量。

    线程栈信息使用内存(thread_stack)：主要用来存放每一个线程自身的标识信息，如线程id，线程运行时基本信息等等，
我们可以通过 thread_stack 参数来设置为每一个线程栈分配多大的内存。

    排序使用内存(sort_buffer_size)：MySQL用此内存区域进行排序操作（filesort），完成客户端的排序请求。
当我们设置的排序区缓存大小无法满足排序实际所需内存的时候，MySQL会将数据写入磁盘文件来完成排序。
由于磁盘和内存的读写性能完全不在一个数量级，所以sort_buffer_size参数对排序操作的性能影响绝对不可小视。

    Join操作使用内存(join_buffer_size)：应用程序经常会出现一些两表（或多表）Join的操作需求，
MySQL在完成某些 Join 需求的时候（all/indexjoin），为了减少参与Join的“被驱动表”的读取次数以提高性能，
需要使用到 Join Buffer 来协助完成 Join操作。当 Join Buffer 太小，MySQL 不会将该 Buffer 存入磁盘文件，
而是先将Join Buffer中的结果集与需要 Join的表进行 Join 操作，然后清空 Join Buffer 中的数据，继续将剩余的结果集写入此 Buffer中，
如此往复。这势必会造成被驱动表需要被多次读取，成倍增加 IO 访问，降低效率。

    顺序读取数据缓冲区使用内存(read_buffer_size)：这部分内存主要用于当需要顺序读取数据的时候，如无发使用索引的情况下的全表扫描，
全索引扫描等。在这种时候，MySQL按照数据的存储顺序依次读取数据块，每次读取的数据快首先会暂存在read_buffer_size中，
当 buffer空间被写满或者全部数据读取结束后，再将buffer中的数据返回给上层调用者，以提高效率。

    随机读取数据缓冲区使用内存(read_rnd_buffer_size)：和顺序读取相对应，当MySQL进行非顺序读取（随机读取）数据块的时候，
会利用这个缓冲区暂存读取的数据。如根据索引信息读取表数据，根据排序后的结果集与表进行Join等等。总的来说，
就是当数据块的读取需要满足一定的顺序的情况下，MySQL 就需要产生随机读取，进而使用到 read_rnd_buffer_size参数所设置的内存缓冲区。

    连接信息及返回客户端前结果集暂存使用内存(net_buffer_size)：这部分用来存放客户端连接线程的连接信息和返回客户端的结果集。
当 MySQL 开始产生可以返回的结果集，会在通过网络返回给客户端请求线程之前，会先暂存在通过net_buffer_size所设置的缓冲区中，
等满足一定大小的时候才开始向客户端发送，以提高网络传输效率。不过，net_buffer_size参数所设置的仅仅只是该缓存区的初始化大小，
MySQL 会根据实际需要自行申请更多的内存以满足需求，但最大不会超过max_allowed_packet 参数大小。

    批量插入暂存使用内存(bulk_insert_buffer_size)：当我们使用如 insert …values(…),(…),(…)… 的方式进行批量插入的时候，
MySQL会先将提交的数据放如一个缓存空间中，当该缓存空间被写满或者提交完所有数据之后，MySQL才会一次性将该缓存空间中的数据写入数据库
并清空缓存。此外，当我们进行 LOAD DATA INFILE 操作来将文本文件中的数据 Load进数据库的时候，同样会使用到此缓冲区。

    临时表使用内存(tmp_table_size)：当我们进行一些特殊操作如需要使用临时表才能完成的Order By，Group By 等等，MySQL 可能需要使用
到临时表。当我们的临时表较小（小于 tmp_table_size参数所设置的大小）的时候，MySQL 会将临时表创建成内存临时表，只有当 tmp_table_size
所设置的大小无法装下整个临时表的时候，MySQL 才会将该表创建成 MyISAM 存储引擎的表存放在磁盘上。不过，当另一个系统参数
max_heap_table_size 的大小还小于 tmp_table_size 的时候，MySQL 将使用max_heap_table_size 参数所设置大小作为最大的内存临时表大小，
而忽略 tmp_table_size 所设置的值。而且tmp_table_size 参数从 MySQL 5.1.2 才开始有，之前一直使用 max_heap_table_size。

上面所列举的 MySQL 线程独享内存仅仅只是所有线程独享内存中的部分，并不是全部，选择的原则是可能对 MySQL 的性能产生较大的影响，
且可以通过系统参数进行调节。由于以上内存都是线程独享，极端情况下的内存总体使用量将是所有连接线程的总倍数。
所以各位朋友在设置过程中一定要谨慎，切不可为了提升性能就盲目的增大各参数值，避免因为内存不够而产生 Out Of Memory 异常或者是严重的
Swap 交换反而降低整体性能。
 
2、全局共享内存

    全局共享内主要是 MySQLInstance（mysqld进程）以及底层存储引擎用来暂存各种全局运算及可共享的暂存信息，如存储查询缓存的
QueryCache，缓存连接线程的 Thread Cache，缓存表文件句柄信息的 Table Cache，缓存二进制日志的 BinLogBuffer， 缓存 MyISAM 存储引擎索
引键的 Key Buffer以及存储 InnoDB 数据和索引的 InnoDB BufferPool 等等。下面针对 MySQL 主要的共享内存进行一个简单的分析。

    查询缓存（Query Cache）：查询缓存是 MySQL 比较独特的一个缓存区域，用来缓存特定Query 的结果集（Result Set）信息，且共享给所有
客户端。通过对 Query 语句进行特定的 Hash 计算之后与结果集对应存放在Query Cache 中，以提高完全相同的 Query 语句的相应速度。
当我们打开 MySQL 的 Query Cache 之后，MySQL接收到每一个 SELECT 类型的 Query 之后都会首先通过固定的 Hash 算法得到该 Query 的 Hash 值，
然后到 QueryCache 中查找是否有对应的 Query Cache。如果有，则直接将 Cache的结果集返回给客户端。如果没有，再进行后续操作，得到对应的结
果集之后将该结果集缓存到 Query Cache中，再返回给客户端。当任何一个表的数据发生任何变化之后，与该表相关的所有 Query Cache 全部会失效，
所以 Query Cache对变更比较频繁的表并不是非常适用，但对那些变更较少的表是非常合适的，可以极大程度的提高查询效率，如那些静态资源表，
配置表等等。为了尽可能高效的利用 Query Cache，MySQL 针对 Query Cache 设计了多个 query_cache_type 值和两个 QueryHint：SQL_CACHE 和
SQL_NO_CACHE。当 query_cache_type 设置为0（或者 OFF）的时候不使用Query Cache，当设置为1（或者 ON）的时候，当且仅当 Query 中使用了
SQL_NO_CACHE 的时候 MySQL 会忽略Query Cache，当 query_cache_type 设置为2（或者DEMAND）的时候，当且仅当Query 中使用了SQL_CACHE 提示之
后，MySQL 才会针对该 Query 使用 Query Cache。可以通过 query_cache_size来设置可以使用的最大内存空间。

    连接线程缓存（Thread Cache）：连接线程是 MySQL为了提高创建连接线程的效率，将部分空闲的连接线程保持在一个缓存区以备新进连接请求
的时候使用，这尤其对那些使用短连接的应用程序来说可以极大的提高创建连接的效率。当我们通过 thread_cache_size设置了连接线程缓存池可以
缓存的连接线程的大小之后，可以通过(Connections - Threads_created) /Connections * 100% 计算出连接线程缓存的命中率。注意，这里设置的
是可以缓存的连接线程的数目，而不是内存空间的大小。

    表缓存（Table Cache）：表缓存区主要用来缓存表文件的文件句柄信息，在MySQL5.1.3之前的版本通过 table_cache 参数设置，
但从MySQL5.1.3开始改为 table_open_cache来设置其大小。当我们的客户端程序提交 Query 给 MySQL 的时候，MySQL 需要对 Query所涉及到的
每一个表都取得一个表文件句柄信息，如果没有 Table Cache，那么 MySQL就不得不频繁的进行打开关闭文件操作，无疑会对系统性能产生一定
的影响，Table Cache 正是为了解决这一问题而产生的。在有了 TableCache 之后，MySQL 每次需要获取某个表文件的句柄信息的时候，首先会
到 Table Cache中查找是否存在空闲状态的表文件句柄。如果有，则取出直接使用，没有的话就只能进行打开文件操作获得文件句柄信息。在使
用完之后，MySQL会将该文件句柄信息再放回 Table Cache池中，以供其他线程使用。注意，这里设置的是可以缓存的表文件句柄信息的数目，
而不是内存空间的大小。
   
    表定义信息缓存（Table definition Cache）：表定义信息缓存是从MySQL5.1.3 版本才开始引入的一个新的缓存区，用来存放表定义信息。
当我们的 MySQL中使用了较多的表的时候，此缓存无疑会提高对表定义信息的访问效率。MySQL 提供了 table_definition_cache参数给我们设置
可以缓存的表的数量。在 MySQL5.1.25 之前的版本中，默认值为128，从 MySQL5.1.25版本开始，则将默认值调整为 256 了，最大设置值为524288。
注意，这里设置的是可以缓存的表定义信息的数目，而不是内存空间的大小。

    二进制日志缓冲区（Binlog Buffer）：二进制日志缓冲区主要用来缓存由于各种数据变更操做所产生的Binary Log 信息。为了提高系统的性能，
MySQL 并不是每次都是将二进制日志直接写入 Log File，而是先将信息写入Binlog Buffer 中，当满足某些特定的条件（如 sync_binlog参数设置）
之后再一次写入 Log File 中。我们可以通过binlog_cache_size 来设置其可以使用的内存大小，同时通过 max_binlog_cache_size限制其最大大小
（当单个事务过大的时候 MySQL 会申请更多的内存）。当所需内存大于 max_binlog_cache_size参数设置的时候，
MySQL 会报错：“Multi-statement transaction required more than‘max_binlog_cache_size’ bytes of storage”。

    MyISAM索引缓存（Key Buffer）：MyISAM 索引缓存将 MyISAM 表的索引信息缓存在内存中，以提高其访问性能。这个缓存可以说是影响 
MyISAM 存储引擎性能的最重要因素之一了，通过 key_buffere_size 设置可以使用的最大内存空间。

    InnoDB 日志缓冲区（InnoDB Log Buffer）：这是 InnoDB存储引擎的事务日志所使用的缓冲区。类似于 Binlog Buffer，InnoDB 
在写事务日志的时候，为了提高性能，也是先将信息写入Innofb Log Buffer 中，当满足 innodb_flush_log_trx_commit参数所设置的相应条件
（或者日志缓冲区写满）之后，才会将日志写到文件（或者同步到磁盘）中。可以通过 innodb_log_buffer_size参数设置其可以使用的最大内存空间。
注：innodb_flush_log_trx_commit 参数对 InnoDB Log 的写入性能有非常关键的影响。该参数可以设置为0，1，2，解释如下：

	0：log buffer中的数据将以每秒一次的频率写入到log file中，且同时会进行文件系统到磁盘的同步操作，但是每个事务的commit并不会触发任何
log buffer 到log file的刷新或者文件系统到磁盘的刷新操作；
	1：在每次事务提交的时候将log buffer 中的数据都会写入到log file，同时也会触发文件系统到磁盘的同步；
	2：事务提交会触发log buffer 到log file的刷新，但并不会触发磁盘文件系统到磁盘的同步。此外，每秒会有一次文件系统到磁盘同步操作。
此外，MySQL文档中还提到，这几种设置中的每秒同步一次的机制，可能并不会完全确保非常准确的每秒就一定会发生同步，还取决于进程调度的问题。
实际上，InnoDB 能否真正满足此参数所设置值代表的意义正常 Recovery 还是受到了不同 OS下文件系统以及磁盘本身的限制，可能有些时候在并没
有真正完成磁盘同步的情况下也会告诉 mysqld 已经完成了磁盘同步。

    InnoDB 数据和索引缓存（InnoDB Buffer Pool）：InnoDB BufferPool 对 InnoDB 存储引擎的作用类似于 Key Buffer Cache 对 MyISAM 存储
引擎的影响，主要的不同在于InnoDB Buffer Pool 不仅仅缓存索引数据，还会缓存表的数据，而且完全按照数据文件中的数据快结构信息来缓存，
这一点和Oracle SGA 中的 database buffer cache 非常类似。所以，InnoDB Buffer Pool 对 InnoDB存储引擎的性能影响之大就可想而知了。
可以通过 (Innodb_buffer_pool_read_requests -Innodb_buffer_pool_reads) / Innodb_buffer_pool_read_requests * 100%计算得到
InnoDB Buffer Pool 的命中率。

    InnoDB 字典信息缓存（InnoDB Additional Memory Pool）:InnoDB字典信息缓存主要用来存放 InnoDB 存储引擎的字典信息以及一些
internal 的共享数据结构信息。所以其大小也与系统中所使用的InnoDB 存储引擎表的数量有较大关系。不过，如果我们通过 
innodb_additional_mem_pool_size参数所设置的内存大小不够，InnoDB 会自动申请更多的内存，并在 MySQL 的 Error Log 中记录警告信息。
这里所列举的各种共享内存，是我个人认为对 MySQL 性能有较大影响的集中主要的共享内存。实际上，除了这些共享内存之外，
MySQL 还存在很多其他的共享内存信息，如当同时请求连接过多的时候用来存放连接请求信息的back_log队列等。
