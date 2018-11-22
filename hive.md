1、mysql 与 hive区别  
	
	1）mysql增删改强，hive主要查询
	2)hive数据再HDFS,mysql在保存在块设备或者本地文件系统中。
	3）hive查询主要通过mapreduce执行，mysql为自己的执行引擎。
	4）hive 慢， mysql快。

2.hive 优缺点
	优点：采用mysql语句，开发快；Hive支持用户自定义函数；
	缺点:延时高，采用mapreduce计算；hive 的HQL表达能力有限有的场景需要编写mapreduce；hive调优有限，粒度有限；
	试用场景：大数据分析，实时性要求不高的场合。如日活、用户回流量等定期计算场景；
